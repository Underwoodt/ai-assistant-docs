# Agent Service

[← Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent)

---

## Overview

Python/FastAPI service that handles all LLM inference for the assistant. It receives chat requests from the Frontend, queues them via AWS SQS, and processes them asynchronously — calling AWS Bedrock for generation and the Knowledge Service for RAG retrieval when needed. All conversation state is persisted to MongoDB.

## Stack

| Technology | Role |
|---|---|
| Python 3.13+ | Runtime |
| FastAPI | REST API framework |
| Uvicorn | ASGI server |
| MongoDB (pymongo) | Conversation & message persistence |
| AWS Bedrock (boto3) | LLM inference (Claude) and embedding |
| AWS SQS (boto3) | Async message queue |
| AWS CloudWatch | Embedded metrics |
| uv | Package manager |
| pytest | Test framework |
| Ruff | Linting & formatting |

## Ports

| Port | Service |
|---|---|
| `8086` | FastAPI HTTP |
| `5679` | Remote debugger (debug compose only) |

## Key Endpoints

| Endpoint | Description |
|---|---|
| `GET /health` | Liveness probe |
| `POST /chat` | Submit a chat message (returns 202 + conversation ID) |
| `GET /conversations/{id}` | Poll for conversation state and completed response |
| `POST /feedback` | Submit user feedback on a response |
| `GET /docs` | Swagger UI |

## SQS Worker

A background worker polls SQS continuously. For each message:

1. Deserialise `{ message_id, conversation_id, question }`
2. Load full conversation history from MongoDB
3. If `knowledge_group_ids` present → call `POST /rag/search` on Knowledge Service
4. Call Bedrock `converse(history + RAG chunks, system_prompt, model)`
5. Save completed `AssistantMessage` to MongoDB
6. Delete message from SQS

## RAG Decision Logic

The Agent conditionally performs RAG based on whether `knowledge_group_ids` were provided in the original request. If absent, the prompt is sent directly to Bedrock without any document retrieval.

## Environment Variables

| Variable | Description |
|---|---|
| `MONGODB_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region |
| `BEDROCK_MODEL_ID` | LLM model ID (Claude) |
| `KNOWLEDGE_SERVICE_URL` | Base URL of Knowledge Service |
| `KNOWLEDGE_API_KEY` | API key for Knowledge Service |
| `SQS_QUEUE_URL` | SQS queue for chat messages |
| `CLOUDWATCH_ENABLED` | Enable CloudWatch metrics |

## Running Locally

```bash
pipx install uv && uv sync
cp .env.example .env
docker compose --profile service up
```

## Testing

```bash
uv run task test    # pytest
uv run task lint    # Ruff
```
