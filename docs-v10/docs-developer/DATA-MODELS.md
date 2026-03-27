# Data Models

[← Back to Developer Docs](README.md)

> **Source:** ADR-004 — AI Assistant Architecture

---

## Agent Service Data Model

![Agent Data Model](images/agent-data-model.png)

The Agent service owns all conversation and message state, stored in MongoDB.

### Conversation

| Field | Type | Description |
|---|---|---|
| `id` | UUID | Unique conversation identifier |
| `messages` | list\<Message\> | Ordered list of messages in this conversation |

### Message (base)

| Field | Type | Description |
|---|---|---|
| `role` | str | `"user"` or `"assistant"` |
| `content` | str | Message text |
| `model_id` | str | AWS Bedrock model identifier used |
| `model_name` | str | Human-readable model name |
| `message_id` | UUID | Unique message identifier |
| `timestamp` | datetime | ISO 8601 creation time |

### UserMessage (extends Message)

| Field | Type | Description |
|---|---|---|
| `role` | str | Always `"user"` |
| `status` | MessageStatus | Processing state of the message |
| `error_message` | str? | Error detail when status indicates failure |

### AssistantMessage (extends Message)

| Field | Type | Description |
|---|---|---|
| `role` | str | Always `"assistant"` |
| `usage` | TokenUsage | Token consumption for this response |
| `sources` | list\<Source\> | Document chunks used in RAG lookup (empty if no RAG) |
| `rag_error` | str? | Error detail if the RAG lookup failed |

### KnowledgeDoc

Attached to AssistantMessage when a RAG lookup was performed. Represents the document-level reference.

| Field | Type | Description |
|---|---|---|
| `content` | str | Retrieved content |
| `file_name` | str | Source document filename |
| `s3_key` | str | S3 object key for the source file |
| `score` | float | Similarity score from pgvector search |

### Source

Each entry is a document chunk that contributed to the assistant response.

| Field | Type | Description |
|---|---|---|
| `name` | str | Document name |
| `location` | str | Location reference within the document |
| `snippet` | str | The text passage that was retrieved |
| `score` | float | Similarity score from pgvector search |

---

## Knowledge Service Data Model

![Knowledge Data Model](images/knowledge-data-model.png)

The Knowledge service owns all document metadata, ingestion state, and vector embeddings. Document metadata is in MongoDB; vector embeddings are in PostgreSQL (pgvector).

### KnowledgeGroup

| Field | Type | Description |
|---|---|---|
| `id` | str | Unique identifier |
| `name` | str | User-facing group name |
| `description` | str? | Optional description |
| `information_asset_owner` | str? | Owner of the information asset |
| `created_by` | str? | User who created the group |

### Document

| Field | Type | Description |
|---|---|---|
| `id` | str | Unique document identifier |
| `file_name` | str | Original uploaded filename |
| `status` | str | `not_started` · `in_progress` · `ready` · `failed` |
| `knowledge_group_id` | str | Parent knowledge group |
| `cdp_upload_id` | str | Reference from the CDP Uploader |
| `s3_key` | str | S3 object key for the source file |
| `created_at` | datetime? | Ingestion start timestamp |
| `chunk_count` | int? | Number of chunks stored after ingestion |

### KnowledgeVector

Stored in PostgreSQL with the pgvector extension. One row per document chunk.

| Field | Type | Description |
|---|---|---|
| `id` | int | Auto-generated primary key |
| `content` | str | Raw chunk text |
| `embedding` | list\<float\> | Titan Embed v2 vector (1024 dimensions) |
| `snapshot_id` | str | Snapshot reference for the ingestion batch |
| `source_id` | str | Foreign key → Document |
| `metadata` | dict? | Additional chunk metadata |
| `created_at` | datetime | Ingestion timestamp |

### ChunkData (internal processing)

Used during the ingestion pipeline before vectors are stored. Text is embedded via Bedrock Titan Embed v2 (`amazon.titan-embed-text-v2:0`).

| Field | Type | Description |
|---|---|---|
| `source` | str | Source document reference |
| `text` | str | Raw chunk text (800 chars, 100 char overlap; JSONL lines treated as pre-formed chunks) |
