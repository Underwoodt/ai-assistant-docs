# Knowledge API Documentation

**Document ingestion and semantic search API**

Port: 8085

---

## Endpoints

### POST /documents

Register documents for ingestion

[Source: ai-defra-search-knowledge/app/](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/app/)

**Supported Formats:**
- PDF (PyMuPDF)
- DOCX (python-docx)
- PPTX (python-pptx)
- JSONL (JSON Lines)

### POST /rag/search

Semantic search

**Request:**
```json
{
  "query": "your search query",
  "knowledge_group_id": "optional",
  "limit": 10
}
```

**Response:**
```json
{
  "results": [
    {
      "document": "doc content",
      "source": "source file",
      "score": 0.95
    }
  ]
}
```

⚠️ **UNVERIFIED:** Exact schema not confirmed in code

### GET /knowledge-groups

List knowledge groups

### GET /health

Health check

---

## Document Format Support

[Source: ai-defra-search-knowledge/pyproject.toml](https://github.com/DEFRA/ai-defra-search-knowledge/blob/main/pyproject.toml)

- PDF: PyMuPDF==1.23.8
- DOCX: python-docx==0.8.11
- PPTX: python-pptx==0.6.21

---

## See Also

- [EXAMPLES.md](./EXAMPLES.md) - Working examples
- [../troubleshooting/DEBUGGING.md](../troubleshooting/DEBUGGING.md) - Troubleshooting
