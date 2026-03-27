# Architecture

[Back to Developer Docs](./README.md)

---

## Overview

AI DEFRA Search is a three-tier RAG assistant deployed on DEFRA's Core Delivery Platform (CDP). All AI inference and embedding is delegated to AWS Bedrock. Authentication is handled by Microsoft Entra ID (Azure AD).

---

## 1. High-Level Service Topology

```mermaid
flowchart LR
    User(["👤 User"])

    subgraph CDP["CDP"]
        subgraph PROJ["AI Assistant"]
            FE["Frontend\nNode.js"]
            Agent["Agent Service\nPython"]
            Knowledge["Knowledge Service\nPython"]
            S3[("S3")]
        end
        Bedrock["AWS Bedrock"]
        CDPUp["CDP Uploader"]
    end

    subgraph EXT["🌐 External"]
        AzureAD["Microsoft Entra ID"]
    end

    User --> FE
    FE -->|auth| AzureAD
    FE -->|chat| Agent
    FE -->|documents & knowledge groups| Knowledge
    FE -->|initiate upload| CDPUp
    CDPUp -->|upload callback| FE
    CDPUp -->|stores files| S3
    Agent -->|LLM inference| Bedrock
    Agent -->|RAG lookup| Knowledge
    Knowledge -->|embeddings| Bedrock
    Knowledge --- S3

    style FE fill:#1e88e5,stroke:#0d47a1,color:#fff
    style Agent fill:#1e88e5,stroke:#0d47a1,color:#fff
    style Knowledge fill:#1e88e5,stroke:#0d47a1,color:#fff
    style S3 fill:#b0bec5,stroke:#546e7a,color:#000
    style Bedrock fill:#fb8c00,stroke:#e65100,color:#fff
    style CDPUp fill:#fb8c00,stroke:#e65100,color:#fff
    style AzureAD fill:#78909c,stroke:#37474f,color:#fff
```

*Source: ADR-004, Figure 1 — High Level Architecture*

---

## 2. GenAI Conversation Flow

All application functionality is driven by the conversation between the user and the AI. A user submits a prompt via the frontend, which is routed to the Agent Service. The Agent determines whether a RAG lookup is required, constructs a Bedrock request, and persists the result in MongoDB.

### RAG Decision Flow

```mermaid
flowchart TD
    A(["User submits prompt"]) --> B{RAG required?}
    B -->|Yes| C["Embed prompt\nvia Bedrock"]
    C --> D["PostgreSQL RAG lookup\nvector similarity search"]
    D --> E["Send to Bedrock\nprompt + closest matches"]
    B -->|No| F["Send to Bedrock\nprompt only"]
    E --> G(["Return response to user"])
    F --> G

    style A fill:#1e88e5,stroke:#0d47a1,color:#fff
    style G fill:#43a047,stroke:#2e7d32,color:#fff
    style C fill:#43a047,stroke:#2e7d32,color:#fff
    style D fill:#43a047,stroke:#2e7d32,color:#fff
    style E fill:#fb8c00,stroke:#e65100,color:#fff
    style F fill:#fb8c00,stroke:#e65100,color:#fff
```

*Source: ADR-004, Figure 2 — RAG Flow*

---

## 3. RAG Lookup — Sequence Diagram

The RAG implementation performs a nearest-neighbour vector similarity search against the knowledge base. This is an intentionally simple baseline — no re-ranking or query expansion is applied.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant F as Frontend
    participant A as Agent Service
    participant K as Knowledge Service
    participant V as Vector DB (PostgreSQL)
    participant B as AWS Bedrock

    U->>F: Submit prompt (with knowledge_group selected)
    F->>A: POST /chat { question, knowledge_group_ids }

    rect rgb(255, 249, 196)
        Note over A,K: knowledge_group_ids present → RAG required
        A->>K: POST /rag/search { query, knowledge_group_ids }
        K->>B: Embed query
        B-->>K: Query embedding (1024-dim vector)
        K->>V: Vector similarity search
        V-->>K: Nearest matching chunks
        K-->>A: Matched chunks + similarity scores
    end

    A->>B: converse(prompt + chunks, system prompt, model)
    B-->>A: Response
    A-->>F: Completed response
    F-->>U: Display response
```

*Source: ADR-004, RAG Lookup Flow Mermaid Code*

---

## 4. Async Conversation — Loading Spinner Pattern

LLM inference via AWS Bedrock introduces latency incompatible with a synchronous request-response model. The frontend implements an async polling pattern: the initial request queues the message and returns a conversation identifier immediately; the frontend then polls for completion.

```mermaid
sequenceDiagram
    autonumber
    participant U as User
    participant F as Frontend
    participant R as Redis
    participant A as Agent Service
    participant Q as AWS SQS
    participant M as MongoDB
    participant B as AWS Bedrock

    rect rgb(255, 249, 196)
        Note over U,B: Initial request
        U->>F: POST /start { question, modelId, knowledgeGroupId }
        F->>A: POST /chat { question, conversation_id, model_id, knowledge_group_ids }
        A->>M: Save user message (status: queued)
        A->>Q: Enqueue message
        A-->>F: 202 { conversation_id, message_id, status: queued }
        F->>R: Cache placeholder (response pending)
    end

    rect rgb(255, 249, 196)
        Note over U,B: Redirect + initial load
        F-->>U: Redirect to conversation page
        U->>F: GET /start/{conversationId}
        F->>R: Get cached conversation
        R-->>F: Placeholder message
        F-->>U: Show loading state
    end

    rect rgb(255, 249, 196)
        Note over A,Q: Worker processing (highlighted)
        Note over A,Q: Worker polls SQS continuously
        A->>Q: Poll for messages
        Q-->>A: { message_id, conversation_id, question }
        A->>Q: Claim message (queued → processing)
        A->>B: converse(history, system prompt, model)
        B-->>A: Response
        A->>M: Save response (status: completed)
        A->>Q: Delete processed message
    end

    rect rgb(230, 255, 230)
        Note over U,B: Polling for result
        U->>F: GET /start/{conversationId} (polling)
        F->>A: GET /conversations/{conversationId}
        A->>M: Fetch conversation
        M-->>A: Completed conversation
        A-->>F: Messages with status: completed
        F->>R: Update cache
        F-->>U: Display response
    end
```

*Source: ADR-004, Loading Spinner for A-Sync GenAI Requests Mermaid Code*

---

## 5. Knowledge Upload and Ingestion

Document ingestion begins when the CDP Uploader delivers a file to S3 and fires a callback to the Frontend Service.

### High-Level Ingest Flow

```mermaid
flowchart TD
    A["CDP Uploader\nFiles scanned and stored in S3\nCallback fires to Frontend"] --> B["Frontend Service\nPOST /documents → Knowledge Service"]
    B --> C["Create Document in MongoDB\nstatus: 'not_started'"]
    C --> D["Return 201 Immediately"]
    C --> E["Switch to background task\nstatus: 'in_progress'"]
    E --> F["Fetch file from S3"]
    F --> G["Select extractor by file extension\npdf → PdfChunkExtractor\ndocx → DocxChunkExtractor\npptx → PptxChunkExtractor\njsonl → JsonlChunkExtractor"]
    G --> H["Extract + chunk text\n~800 chars, 100 char overlap\nJSONL: each line is a pre-formed chunk"]
    H --> I["Generate embedding per chunk\nAWS Bedrock: amazon.titan-embed-text-v2:0\n1024-dim float vector"]
    I --> J["Insert into PostgreSQL\nknowledge_vectors"]
    J --> K["Update status → 'ready'\nchunk_count = N"]
    J --> L["On any failure:\nUpdate status → 'failed'"]

    style A fill:#7b1fa2,stroke:#4a148c,color:#fff
    style B fill:#fb8c00,stroke:#e65100,color:#fff
    style I fill:#e53935,stroke:#b71c1c,color:#fff
    style J fill:#43a047,stroke:#2e7d32,color:#fff
    style K fill:#43a047,stroke:#2e7d32,color:#fff
    style L fill:#e53935,stroke:#b71c1c,color:#fff
```

*Source: ADR-004, High Level Ingest Journey diagram*

### Detailed Ingest — Create Upload Sequence

```mermaid
sequenceDiagram
    autonumber
    participant C as Client
    participant F as Frontend
    participant K as Knowledge Service
    participant CDP as CDP Uploader

    rect rgb(255, 249, 196)
        Note over C,K: Upload Screen
        C->>F: GET /upload
        F->>K: GET /knowledge-groups (user-id header)
        K-->>F: Knowledge groups for user
        F-->>C: Uploader screen
    end

    rect rgb(255, 249, 196)
        Note over C,K: Create Knowledge Group
        C->>F: POST /upload/create-group { name, description }
        F->>K: POST /knowledge-group { name, description } (user-id header)
        K-->>F: Knowledge group
        F-->>C: Knowledge group
    end

    rect rgb(230, 255, 230)
        Note over C,CDP: Trigger Upload
        C->>F: POST /upload { knowledgeGroupId }
        Note over F: generates uploadReference (UUID)
        F->>CDP: POST /initiate { redirect, callback, s3Bucket, s3Path, metadata }
        CDP-->>F: uploadId, statusUrl
        F-->>C: Redirect to /upload/files/{uploadReference}
        C->>C: Select files
        C->>CDP: POST /upload-and-scan/{uploadId}
    end

    rect rgb(255, 230, 230)
        Note over F,CDP: Callback & Ingest
        CDP->>F: POST /upload/callback/{uploadReference} { uploadStatus, files, metadata }
        Note over F: filter complete vs rejected files
        F->>K: POST /documents (complete files only, user-id header)
        K-->>F: 201 Accepted
        CDP-->>F: 200 OK
    end
```

*Source: ADR-004, Create Upload sequence diagram*

### Detailed Ingest — Trigger Ingest Sequence

```mermaid
sequenceDiagram
    autonumber
    participant F as Frontend
    participant K as Knowledge Service
    participant M as MongoDB
    participant S as S3 Bucket
    participant B as AWS Bedrock
    participant P as PostgreSQL

    rect rgb(255, 249, 196)
        Note over F,P: Ingest (triggered by CDP callback)
        F->>K: POST /documents { file_name, s3_key, knowledge_group_id, cdp_upload_id } (user-id header)
        K->>M: Insert document, status: not_started
        K-->>F: 201 Accepted
    end

    rect rgb(255, 249, 196)
        Note over K,P: Background per document
        K->>M: Update status: in_progress
        K->>S: GET s3_key
        S-->>K: File bytes
        K->>K: Extract + chunk text
        loop Per chunk
            K->>B: Generate embedding (content)
            B-->>K: 1024-dim vector
            K->>P: INSERT knowledge_vectors (content, embedding, source_id, snapshot_id, metadata)
        end
        K->>M: Update status: ready / failed
    end
```

*Source: ADR-004, Trigger Ingest sequence diagram*

---

## 6. Persistence Layer

Two databases underpin the system. The Agent Service owns conversation state; the Knowledge Service owns document metadata and vector embeddings.

| Store | Service | Purpose |
|---|---|---|
| MongoDB | Agent Service | Conversation and message history, message status (queued / processing / completed) |
| MongoDB | Knowledge Service | Document metadata, ingest status, chunk counts |
| PostgreSQL (pgvector) | Knowledge Service | Vector embeddings for RAG retrieval |
| Redis | Frontend | Short-term conversation cache, session state |
| S3 | Knowledge Service | Uploaded document files |
