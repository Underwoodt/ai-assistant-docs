# Architecture

> **Source:** `ai-defra-search-core/ARCHITECTURE.md`, `ai-defra-search-core/compose.yml`, repo analysis

---

## System Overview

AI DEFRA Search is a microservices application built around a Retrieval-Augmented Generation (RAG) pattern. Users interact with a browser-based frontend; questions are queued and processed asynchronously by an Agent service that retrieves relevant document chunks from a Knowledge service and sends them to a large language model (LLM) via AWS Bedrock.

```mermaid
graph TD
    User([Browser])
    FE[Frontend\nNode.js / Hapi\n:3000]
    AG[Agent\nPython / FastAPI\n:8086]
    KN[Knowledge\nPython / FastAPI\n:8085]
    BK[AWS Bedrock\nor Stub :8087]
    MG[(MongoDB\n:27017)]
    PG[(PostgreSQL + pgvector\n:5432)]
    RD[(Redis\n:6379)]
    SQ[SQS Queue]
    S3[LocalStack S3]

    User -->|HTTPS| FE
    FE -->|REST + X-API-KEY| AG
    FE -->|REST + X-API-KEY| KN
    AG -->|Enqueue question| SQ
    SQ -->|Poll| AG
    AG -->|Vector search| KN
    AG -->|Converse API| BK
    AG -->|Read/write state| MG
    KN -->|Embed via Titan| BK
    KN -->|Store/query vectors| PG
    KN -->|Store source docs| S3
    FE -->|Session cache| RD
```

---

## Services

### Frontend (`ai-defra-search-frontend`)

- **Stack:** Node.js 22+, Hapi framework, Nunjucks templates, Webpack, SCSS
- **Port:** 3000 (must be accessed via `frontend.localhost` through Traefik in full-stack mode)
- **Responsibilities:** Render chat UI, manage user sessions, proxy requests to Agent and Knowledge APIs, handle file uploads via CDP uploader, cache conversation data in Redis
- **Key external dependencies:** Agent API, Knowledge API, Redis (session cache), CDP uploader service

### Agent (`ai-defra-search-agent`)

- **Stack:** Python 3.12+, FastAPI, Motor (async MongoDB), boto3, Anthropic SDK
- **Port:** 8086
- **Responsibilities:** Receive chat requests, enqueue them to SQS, poll and process the queue, retrieve context from Knowledge service, call AWS Bedrock for LLM responses, persist conversation history in MongoDB
- **Chat flow:** `POST /chat` → 202 Accepted → SQS enqueue → Worker polls SQS → Knowledge query → Bedrock invoke → MongoDB write → available on `GET /conversations/{id}`

### Knowledge (`ai-defra-search-knowledge`)

- **Stack:** Python 3.13+, FastAPI, SQLAlchemy (async), pgvector, boto3, PyMuPDF, python-docx, python-pptx
- **Port:** 8085
- **Responsibilities:** Accept document uploads, extract and chunk text, generate vector embeddings via AWS Bedrock Titan, store vectors in PostgreSQL/pgvector, serve semantic search results to the Agent
- **Supported document types:** PDF, DOCX, PPTX, JSONL

### AWS Bedrock Stub (`ai-defra-search-aws-bedrock-stub`)

- **Stack:** WireMock
- **Port:** 8087
- **Responsibilities:** Stub the AWS Bedrock Claude Converse API and Titan Embed API so the full stack can run locally without AWS credentials

---

## Infrastructure Components

| Component | Version | Role |
|---|---|---|
| PostgreSQL + pgvector | Custom Dockerfile | Vector store for document embeddings |
| MongoDB | 6.0.13 | Conversation history and state |
| Redis | 7.2.3 | Frontend session and response cache |
| LocalStack | 4.9.2 | Local AWS emulation (S3, SQS, SNS, Firehose) |
| Traefik | v3 | Reverse proxy and service routing |

---

## Network

All services communicate over an isolated Docker bridge network (`ai-defra-search` in core, `cdp-tenant` per-service). Traefik handles routing from the host and provides service discovery.

---

## Data Flow: Chat Request (End to End)

```mermaid
sequenceDiagram
    participant U as User (Browser)
    participant FE as Frontend
    participant AG as Agent
    participant SQS as SQS Queue
    participant KN as Knowledge
    participant BK as Bedrock (LLM)
    participant DB as MongoDB

    U->>FE: Submit question
    FE->>AG: POST /chat {question, model_id, knowledge_group_ids}
    AG-->>FE: 202 Accepted {message_id, conversation_id}
    AG->>SQS: Enqueue chat job
    AG->>SQS: Poll queue (worker)
    SQS-->>AG: Dequeue job
    AG->>KN: POST /rag/search {query, knowledge_group_ids}
    KN->>BK: Titan Embed (vectorise query)
    BK-->>KN: Embedding vector
    KN->>KN: pgvector similarity search
    KN-->>AG: Relevant document chunks
    AG->>BK: Claude Converse (question + chunks as context)
    BK-->>AG: LLM response
    AG->>DB: Persist message + response
    U->>FE: Poll GET /conversations/{id}
    FE->>AG: GET /conversations/{id}
    AG-->>FE: Full conversation with messages
    FE-->>U: Render response
```

---

## Authentication

All Agent and Knowledge API endpoints (except `/health` and `/docs`) require an `X-API-KEY` header. The Frontend holds the key and injects it on every backend call. Missing key returns 401; incorrect key returns 403.
