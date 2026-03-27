# Agent Service

[Back to Developer Docs](../README.md)

**Repo:** [ai-defra-search-agent](https://github.com/DEFRA/ai-defra-search-agent)
**Language:** Python 3.13.7+
**Framework:** FastAPI
**Package manager:** uv

---

## Purpose

The Agent Service is the AI brain of the platform. It:

- Exposes the chat API consumed by the Frontend
- Manages multi-turn conversation state in MongoDB
- Routes user prompts to AWS Bedrock (Claude models) for LLM inference
- Orchestrates RAG lookup by calling the Knowledge Service when a knowledge group is selected
- Processes messages asynchronously via an AWS SQS worker

---

## Directory Structure

```
ai-defra-search-agent/
├── app/              # Main application code (FastAPI app, routes, services)
├── compose/          # Docker Compose configurations (local, debug, test)
├── docs/ai/          # AI development guidelines
├── scripts/          # Utility scripts
├── tests/            # pytest test suite
├── .github/          # GitHub Actions workflows
├── Dockerfile        # Container image
├── pyproject.toml    # Dependencies and project metadata
└── .env.example      # Environment variable template
```

---

## Key Responsibilities

| Responsibility | Detail |
|---|---|
| Chat API | `POST /chat` — accepts question, conversation_id, model_id, knowledge_group_ids |
| Async queue | Enqueues messages to SQS; worker polls SQS and processes independently |
| LLM inference | Calls AWS Bedrock `converse()` API with conversation history + optional RAG context |
| RAG orchestration | Calls Knowledge Service `POST /rag/search` when knowledge_group_ids are provided |
| Conversation persistence | Saves all messages (user + assistant) to MongoDB with status tracking |
| CloudWatch metrics | Publishes custom metrics via AWS Embedded Metrics |

---

## Configuration

Key environment variables (see `.env.example`):

| Variable | Description |
|---|---|
| `MONGO_URI` | MongoDB connection string |
| `AWS_REGION` | AWS region for Bedrock and SQS |
| `SQS_QUEUE_URL` | SQS queue URL |
| `KNOWLEDGE_SERVICE_URL` | Base URL of the Knowledge Service |
| `BEDROCK_ENDPOINT` | Optional override for Bedrock (point to local stub) |

---

## Running Locally

```bash
cd ai-defra-search-agent
uv sync
cp .env.example .env  # Edit as needed
docker compose up
```

FastAPI auto-generated docs available at `http://localhost:<port>/docs`.

---

## Testing

```bash
uv run pytest
uv run ruff check .
```
