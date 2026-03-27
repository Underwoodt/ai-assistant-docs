# Service Interactions

[← Back to Integration & Deployment](README.md)



---

## 1. Chat Flow (Async Queue-and-Poll)

```mermaid
sequenceDiagram
    participant U as User (Browser)
    participant FE as Frontend
    participant AG as Agent Service
    participant SQS as AWS SQS
    participant KN as Knowledge Service
    participant VDB as PostgreSQL (pgvector)
    participant BD as AWS Bedrock
    participant MDB as MongoDB

    U->>FE: POST /chat (question, knowledge_group_ids)
    FE->>AG: POST /chat {question, conversation_id, model_id, knowledge_group_ids}
    AG->>MDB: Save message (status: queued)
    AG->>SQS: Enqueue {message_id, conversation_id, question}
    AG-->>FE: 202 {conversation_id, message_id, status: queued}
    FE-->>U: Redirect to conversation view

    loop Poll until completed
        U->>FE: GET /conversation/{id}
        FE->>AG: GET /conversations/{id}
        AG->>MDB: Load conversation
        AG-->>FE: Conversation with message status
        FE-->>U: Show loading spinner or result
    end

    Note over AG: SQS Worker (background)
    SQS->>AG: Dequeue message
    opt knowledge_group_ids present
        AG->>KN: POST /rag/search {query, knowledge_group_ids}
        KN->>VDB: Nearest-neighbour similarity search
        VDB-->>KN: Top matching chunks
        KN-->>AG: Relevant document chunks
    end
    AG->>BD: converse(history + chunks, system_prompt, model)
    BD-->>AG: Generated response
    AG->>MDB: Save AssistantMessage (status: completed)
    AG->>SQS: Delete processed message
```

---

## 2. Document Upload Flow

```mermaid
sequenceDiagram
    participant U as User (Browser)
    participant FE as Frontend
    participant CDP as CDP Uploader
    participant S3 as AWS S3
    participant KN as Knowledge Service
    participant BD as AWS Bedrock (Titan Embed)
    participant VDB as PostgreSQL (pgvector)
    participant MDB as MongoDB

    U->>FE: Select knowledge group + upload file
    FE->>CDP: POST /initiate {redirect, callback, s3Bucket, s3Path, metadata}
    CDP-->>FE: {uploadReference: UUID}
    FE-->>U: Redirect to CDP Uploader upload page
    U->>CDP: Upload file
    CDP->>S3: Store file
    CDP->>FE: Callback (upload complete, s3Key)
    FE->>KN: POST /knowledge-groups/{id}/documents {s3_key, cdp_upload_id}
    KN->>MDB: Save Document (status: not_started)

    Note over KN: asyncio.create_task() per document
    KN->>S3: Fetch file bytes
    loop Per chunk (800 chars, 100 overlap)
        KN->>BD: invoke Titan Embed v2 (chunk text)
        BD-->>KN: 1024-dim embedding vector
        KN->>VDB: INSERT KnowledgeVector
    end
    KN->>MDB: Update Document (status: ready, chunk_count)

    U->>FE: Poll document status
    FE->>KN: GET /knowledge-groups/{id}/documents/{docId}
    KN-->>FE: {status: ready}
    FE-->>U: Document ready to query
```

---

## 3. RAG Search (Agent → Knowledge — Internal)

```mermaid
sequenceDiagram
    participant AG as Agent Service
    participant BD as AWS Bedrock (Titan Embed)
    participant KN as Knowledge Service
    participant VDB as PostgreSQL (pgvector)

    AG->>BD: invoke Titan Embed v2 (user question)
    BD-->>AG: 1024-dim query embedding
    AG->>KN: POST /rag/search {query_embedding, knowledge_group_ids}
    KN->>VDB: SELECT ... ORDER BY embedding <-> $query LIMIT n
    VDB-->>KN: Top-n KnowledgeVector rows
    KN-->>AG: [{content, file_name, score}, ...]
```

---

## API Key Flow

| Caller | Called Service | Headers |
|---|---|---|
| Frontend | Agent Service | `X-API-KEY` + `user-id` |
| Frontend | Knowledge Service | `X-API-KEY` + `user-id` |
| Agent Service | Knowledge Service | `X-API-KEY` |
| All services | AWS | IAM Role (prod) / LocalStack dummy creds (local) |
