# Architecture

> 👩‍💻 **Developer Docs** — [Home](Home) › [Developer Docs](Home#developer-docs)

> **Source:** ADR-004 — AI Assistant Architecture

---

## AI Assistant

This project is a reference implementation of a Retrieval-Augmented Generation (RAG) assistant, designed to demonstrate how AI-powered search and conversational interfaces can be deployed on the Core Delivery Platform (CDP).

It is intended as a practical starting point for any teams looking to deploy similar AI features. The system prompts have not been extensively optimised, and the data structures reflect a generalised design that may require adaptation for specific use cases.

---

## Services

### Frontend Service — Node.js + Redis
- User interfaces for chat, upload, and knowledge group management
- Auth flow via Microsoft Entra ID
- Short-term conversation cache via Redis
- Triggers document upload via CDP Uploader
- Receives CDP upload callback and notifies Knowledge Service

### Agent Service — Python
- Handles all LLM inference calls to AWS Bedrock
- Persists conversation history in MongoDB
- Orchestrates RAG lookup via Knowledge Service when required

### Knowledge Service — Python
- Manages document ingestion pipeline (extract, chunk, embed, store)
- Generates embeddings via AWS Bedrock (Titan Embed v2, 1024-dim, model: `amazon.titan-embed-text-v2:0`)
- Fetches uploaded files from S3
- Stores vector embeddings in PostgreSQL (pgvector)
- Stores document metadata and ingest status in MongoDB

---

## Infrastructure

| Component | Version | Role |
|---|---|---|
| PostgreSQL + pgvector | Custom Dockerfile | 1024-dim vector storage and nearest-neighbour search |
| MongoDB | 6.0.13 | Agent: conversations & messages · Knowledge: document metadata & ingest status |
| Redis | 7.2.3 | Frontend short-term conversation cache |
| LocalStack | 4.9.2 | Local AWS emulation (S3, SQS, SNS, Firehose) |
| Traefik | v3 | Reverse proxy and service routing |
| Microsoft Entra ID | Managed | User authentication (OAuth 2.0) |
| CDP Uploader | DEFRA managed | Secure file-to-S3 upload with callback |

---

## Authentication

| Layer | Mechanism |
|---|---|
| User → Frontend | Microsoft Entra ID (OAuth 2.0) |
| Frontend → Agent API | `X-API-KEY` header |
| Frontend → Knowledge API | `X-API-KEY` header + `user-id` header |
| Services → AWS | IAM role (production) / LocalStack (local dev) |

---

## High Level Architecture

```mermaid
flowchart LR
    User(["👤 User"])

    subgraph EXT["External"]
        EntraID["Microsoft Entra ID"]
    end

    subgraph CDP["CDP"]
        subgraph AIA["AI Assistant"]
            Frontend["Frontend\nNode.js"]
            Agent["Agent Service\nPython"]
            Knowledge["Knowledge Service\nPython"]
        end
        CDPUploader["CDP Uploader"]
    end

    AWSBedrock["AWS Bedrock"]
    S3[("S3")]

    User --> Frontend
    Frontend -- auth --> EntraID
    Frontend -- chat --> Agent
    Agent -- RAG lookup --> Knowledge
    Agent -- LLM inference --> AWSBedrock
    Knowledge -- embeddings --> AWSBedrock
    Knowledge -- stores files --> S3
    Frontend -- initiate upload --> CDPUploader
    CDPUploader -- upload callback --> Frontend
    CDPUploader -- stores files --> S3
```

---

## GenAI Conversation Flow

All application functionality is driven by the conversation between the user and the AI. A user submits a prompt via the frontend, which is routed to the Agent Service for processing. The Agent Service determines whether the query requires a RAG lookup against the knowledge base, then constructs an appropriate request to AWS Bedrock.

### RAG Lookup Flow

The RAG implementation performs a vector similarity search using an embedding of the user's prompt. This is an intentionally straightforward implementation — nearest-neighbour lookup with no re-ranking or query expansion.

- **RAG path:** `knowledge_group_ids` present → embed prompt → pgvector similarity search → send prompt + matched chunks to Bedrock
- **No-RAG path:** no `knowledge_group_ids` → send prompt directly to Bedrock (LLM general knowledge only)

```mermaid
flowchart TD
    A["User submits prompt"] --> B{RAG required?}
    B -- Yes --> C["Embed prompt using Bedrock"]
    C --> D["PostgreSQL RAG lookup\nvector similarity search"]
    D --> E["Send to Bedrock\nprompt + closest matches"]
    B -- No --> F["Send to Bedrock\nprompt only"]
    E --> G["Return response to user"]
    F --> G
```

### RAG Lookup Sequence

```mermaid
sequenceDiagram
    participant U as User
    participant FE as Frontend
    participant AG as Agent Service
    participant KN as Knowledge Service
    participant VDB as Vector DB (PostgreSQL)
    participant BD as AWS Bedrock

    U->>FE: Submit prompt with knowledge group selected
    FE->>AG: POST /chat { question, knowledge_group_ids }
    Note over AG: knowledge_group_ids present — RAG required
    AG->>KN: POST /rag/search { query, knowledge_group_ids }
    KN->>BD: Embed query
    BD-->>KN: Query embedding (1024-dim vector)
    KN->>VDB: Vector similarity search
    VDB-->>KN: Nearest matching chunks
    KN-->>AG: Matched chunks with similarity scores
    AG->>BD: converse(prompt + matched chunks, system_prompt, model)
    BD-->>AG: Response
    AG-->>FE: Conversation with completed response
    FE-->>U: Display response
```

### Async GenAI Requests

LLM inference via AWS Bedrock introduces latency that is incompatible with a synchronous request-response model. The frontend implements an asynchronous polling pattern: an initial request queues the message and returns a conversation identifier immediately, while the frontend polls for completion.

```mermaid
sequenceDiagram
    participant U as User
    participant FE as Frontend
    participant R as Redis
    participant AG as Agent Service
    participant SQS as AWS SQS
    participant MDB as MongoDB
    participant BD as AWS Bedrock

    U->>FE: POST /start { question, modelId, knowledgeGroupId }
    FE->>AG: POST /chat { question, conversation_id, model_id, knowledge_group_ids }
    AG->>MDB: Save user message (status: queued)
    AG->>SQS: Enqueue message
    AG-->>FE: 202 { conversation_id, message_id, status: queued }
    FE->>R: Cache placeholder (response pending)
    FE-->>U: Redirect to conversation page
    U->>FE: GET /start/{conversationId}
    FE->>R: Get cached conversation
    R-->>FE: Placeholder message
    FE-->>U: Show loading state
    Note over AG,SQS: Worker polls SQS continuously
    SQS->>AG: { message_id, conversation_id, question }
    Note over AG: Claim message (queued → processing)
    AG->>BD: converse(history, system_prompt, model)
    BD-->>AG: Response
    AG->>MDB: Save response (status: completed)
    AG->>SQS: Delete processed message
    U->>FE: GET /start/{conversationId} (polling)
    FE->>AG: GET /conversations/{conversationId}
    AG->>MDB: Fetch conversation
    MDB-->>AG: Completed conversation
    AG-->>FE: Messages with status: completed
    FE->>R: Update cache
    FE-->>U: Display response
```

---

## Knowledge Upload and Ingestion

Document ingestion begins when the CDP Uploader delivers a file to S3 and fires a callback to the Frontend Service. The Frontend Service notifies the Knowledge Service, which takes responsibility for the remainder of the pipeline.

**Chunking:** 800 characters per chunk, 100 character overlap. JSONL = each line is a pre-formed chunk.

### High Level Ingest Journey

```mermaid
flowchart TD
    A["CDP Uploader\nFiles scanned and stored in S3\nCallback fires to Frontend"]
    --> B["Frontend Service\nPOST /documents → Knowledge Service"]
    --> C["Create Document in MongoDB\nstatus: not_started"]
    C --> D["Return 201 immediately"]
    C --> E["Switch to background task\nstatus: in_progress"]
    --> F["Fetch file from S3"]
    --> G{"Select extractor\nby file extension"}
    G -- pdf --> H1["PdfChunkExtractor"]
    G -- docx --> H2["DocxChunkExtractor"]
    G -- pptx --> H3["PptxChunkExtractor"]
    G -- jsonl --> H4["JsonlChunkExtractor"]
    H1 & H2 & H3 & H4 --> I["Extract + chunk text\n800 chars, 100 char overlap\nJSONL: each line = pre-formed chunk"]
    --> J["Generate embedding per chunk\nAWS Bedrock: amazon.titan-embed-text-v2:0\n1024-dim fixed vector"]
    --> K["Insert into PostgreSQL\nknowledge_vectors"]
    --> L["Update status → ready\nchunk_count = N"]
    K -- On any failure --> M["Update status → failed"]
```

### Create Upload

```mermaid
sequenceDiagram
    participant C as Client
    participant FE as Frontend
    participant KN as Knowledge Service
    participant CDP as CDP Uploader

    rect rgb(255, 253, 230)
        Note over C,CDP: Upload Screen
        C->>FE: GET /upload
        FE->>KN: GET /knowledge-groups (user-id header)
        KN-->>FE: Knowledge groups for user
        FE-->>C: Upload screen
    end

    rect rgb(255, 253, 230)
        Note over C,CDP: Create Knowledge Group
        C->>FE: POST /upload/create-group { name, description }
        FE->>KN: POST /knowledge-groups { name, description } (user-id header)
        KN-->>FE: Knowledge group
        FE-->>C: Knowledge group
    end

    rect rgb(255, 253, 230)
        Note over C,CDP: Trigger Upload
        Note over C: Select knowledge group
        C->>FE: POST /upload { knowledgeGroupId }
        Note over FE: generates uploadReference (UUID)
        FE->>CDP: POST /initiate { redirect, callback, s3Bucket, s3Path, metadata }
        CDP-->>FE: uploadId, statusUrl
        FE-->>C: Redirect to /upload/files/{uploadReference}
        C->>CDP: POST /upload-and-scan/{uploadId}
    end

    rect rgb(255, 253, 230)
        Note over C,CDP: Callback & Ingest
        CDP->>FE: POST /upload/callback/{uploadReference} { uploadStatus, files, metadata }
        Note over FE: filter: complete vs rejected files
        FE->>KN: POST /documents { complete files only } (user-id header)
        KN-->>FE: 201 Accepted
        FE-->>CDP: 200 OK
    end
```

### Upload Complete

```mermaid
sequenceDiagram
    participant C as Client
    participant FE as Frontend
    participant CDP as CDP Uploader

    rect rgb(255, 253, 230)
        Note over C,CDP: Upload Complete
        C->>FE: GET /upload-status/{uploadReference}
        FE->>CDP: GET {statusUrl}
        CDP-->>FE: uploadStatus: pending
        FE-->>C: Upload status: pending
        CDP->>FE: POST /upload/callback/{uploadReference}
        FE-->>CDP: 200 OK
        C->>FE: GET /upload-status/{uploadReference}
        FE->>CDP: GET {statusUrl}
        CDP-->>FE: uploadStatus: complete / error
        FE-->>C: Upload status: success / error
    end
```

### Trigger Ingest

Ingestion runs as a background task per document using `asyncio.create_task()`. Status progression: `not_started → in_progress → ready / failed`.

```mermaid
sequenceDiagram
    participant FE as Frontend
    participant KN as Knowledge Service
    participant MDB as MongoDB
    participant S3 as S3 Bucket
    participant BD as AWS Bedrock
    participant PG as PostgreSQL

    rect rgb(255, 230, 230)
        Note over FE,PG: Ingest (triggered by CDP callback)
        FE->>KN: POST /documents { file_name, s3_key, knowledge_group_id, cdp_upload_id } (user-id header)
        KN->>MDB: Insert document, status: not_started
        KN-->>FE: 201 Accepted
        Note over KN: asyncio.create_task() — background per document
        KN->>MDB: Update status: in_progress
        KN->>S3: GET s3_key
        S3-->>KN: File bytes
        Note over KN: Extract + chunk text
        loop Per chunk
            KN->>BD: Generate embedding per chunk
            BD-->>KN: 1024-dim vector
            KN->>PG: INSERT knowledge_vectors (content, embedding, source_id, snapshot_id, metadata)
        end
        KN->>MDB: Update status: ready / failed
    end
```

### Submit Prompt / RAG Lookup

```mermaid
sequenceDiagram
    participant C as Client
    participant FE as Frontend
    participant KN as Knowledge Service
    participant AG as Agent Service

    rect rgb(230, 255, 230)
        Note over C,AG: Submit Prompt
        C->>FE: GET /start
        FE->>KN: GET /knowledge-groups (user-id header)
        KN-->>FE: Knowledge groups for user
        FE->>KN: GET /models
        KN-->>FE: Available models
        FE-->>C: Chat screen
        Note over C: Enter question
        Note over C: Select knowledge group & model
        C->>FE: POST /start { question, knowledgeGroupId, modelId }
        FE->>AG: POST /chat { question, conversation_id, model_id, knowledge_group_ids } (user-id header)
        AG-->>FE: { conversation_id, message_id }
        FE-->>C: Redirect to GET /start/{conversationId}
    end
```

---

## Persistence

The Agent Service owns conversation and message state; the Knowledge Service owns document metadata and vector embeddings.

See [Data Models](Developer-Data-Models) for full field reference.

### Agent Service Data Model

```mermaid
classDiagram
    class Conversation {
        +UUID id
        +list~Message~ messages
    }
    class Message {
        +str role
        +str content
        +str model_id
        +str model_name
        +UUID message_id
        +datetime timestamp
    }
    class UserMessage {
        +str role = "user"
        +MessageStatus status
        +str error_message
    }
    class AssistantMessage {
        +str role = "assistant"
        +TokenUsage usage
        +list~Source~ sources
        +str rag_error
    }
    class KnowledgeDoc {
        +str content
        +str file_name
        +str s3_key
        +float score
    }
    class Source {
        +str name
        +str location
        +str snippet
        +float score
    }

    Conversation "1" --> "*" Message : messages
    Message <|-- UserMessage
    Message <|-- AssistantMessage
    AssistantMessage "1" --> "*" Source : sources
    KnowledgeDoc --> Source : mapped to by RAG lookup
```

### Knowledge Service Data Model

```mermaid
classDiagram
    class ChunkData {
        +str source
        +str text
    }
    class KnowledgeVector {
        +int id
        +str content
        +list~float~ embedding
        +str snapshot_id
        +str source_id
        +dict metadata
        +datetime created_at
    }
    class Document {
        +str id
        +str file_name
        +str status
        +str knowledge_group_id
        +str cdp_upload_id
        +str s3_key
        +datetime created_at
        +int chunk_count
    }
    class KnowledgeGroup {
        +str id
        +str name
        +str description
        +str information_asset_owner
        +str created_by
    }

    ChunkData --> KnowledgeVector : text embedded via Bedrock
    KnowledgeVector --> Document : source (document id)
    Document --> KnowledgeGroup : knowledge_group_id
```
