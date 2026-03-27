# Data Models

[Back to Developer Docs](./README.md)

---

## Agent Service Data Model

The Agent Service owns conversation and message state, stored in MongoDB.

```mermaid
classDiagram
    class Conversation {
        +UUID id
        +List~Message~ messages
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
        +str? error_message
    }

    class AssistantMessage {
        +str role = "assistant"
        +TokenUsage usage
        +List~Source~ sources
        +str? rag_error
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

    %% Relationships
    Conversation "1" --> "*" Message : messages
    Message <|-- UserMessage
    Message <|-- AssistantMessage
    AssistantMessage "1" --> "*" Source : sources
    KnowledgeDoc ..> Source : mapped to by RAG lookup
```

*Source: ADR-004, Agent Service Data Model Mermaid Code*

**MessageStatus values:** `queued` | `processing` | `completed` | `failed`

---

## Knowledge Service Data Model

The Knowledge Service owns document metadata and vector embeddings, stored across MongoDB (document metadata) and PostgreSQL/pgvector (embeddings).

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
        +dict? metadata
        +datetime created_at
    }

    class Document {
        +str id
        +str file_name
        +str status
        +str knowledge_group_id
        +str cdp_upload_id
        +str s3_key
        +datetime? created_at
        +int? chunk_count
    }

    class KnowledgeGroup {
        +str id
        +str name
        +str? description
        +str? information_asset_owner
        +str created_by
    }

    ChunkData --> KnowledgeVector : text embedded via Bedrock
    KnowledgeVector --> Document : source_id (document id)
    ChunkData --> Document : source (document id)
    Document --> KnowledgeGroup : knowledge_group_id
```

*Source: ADR-004, Knowledge Service Data Model*

**Document status values:** `not_started` | `in_progress` | `ready` | `failed`

---

## Storage Mapping

| Entity | Store | Notes |
|---|---|---|
| `Conversation`, `Message` | MongoDB (Agent Service) | Full conversation history with role, content, timestamps |
| `Document` (metadata) | MongoDB (Knowledge Service) | Ingest status, S3 key, chunk count |
| `KnowledgeGroup` | MongoDB (Knowledge Service) | User-owned, personal only |
| `KnowledgeVector` | PostgreSQL + pgvector | 1024-dim float vectors from Titan Embed v2 |
| Uploaded files | AWS S3 | Raw files, fetched by Knowledge Service during ingest |
| Session cache | Redis (Frontend) | Short-lived conversation placeholder during async processing |
