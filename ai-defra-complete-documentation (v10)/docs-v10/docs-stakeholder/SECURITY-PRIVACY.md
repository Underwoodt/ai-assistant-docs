# Security & Privacy

[← Back to Stakeholder Docs](README.md)

---

## Authentication

All users must sign in with their DEFRA Microsoft account (Microsoft Entra ID) before accessing the assistant. There is no separate registration or password — access is controlled through existing DEFRA identity management.

---

## Data Ownership and Isolation

Documents and conversations are **personal to the signed-in user**. Each user's knowledge groups and uploaded files are isolated — other users cannot see or query your documents.

Conversations are stored linked to your user identity. You can view your own conversation history; other users cannot.

---

## Data Storage

| Data | Where it is stored | Retention |
|---|---|---|
| Uploaded files | Amazon S3 (DEFRA's AWS environment) | Until deleted by user |
| Document text and embeddings | PostgreSQL database (CDP-managed) | Until document deleted |
| Conversation history | MongoDB database (CDP-managed) | Per retention policy |
| Session data | Redis (short-lived cache) | Cleared on session expiry |

All storage is within DEFRA's approved Amazon Web Services environment.

---

## Data in Transit

All communication between your browser and the service is encrypted (HTTPS). All inter-service communication within the platform uses encrypted connections and API key authentication.

---

## AI Model Data

When you submit a question or upload a document, the content is sent to **Amazon Bedrock** (AWS's AI model service) to generate responses and create document embeddings. This occurs within DEFRA's AWS account under DEFRA's data processing agreement with Amazon.

No conversation content or document content is shared with third parties outside of AWS under DEFRA's agreement.

---

## No Training on Your Data

Content you submit (questions, documents) is not used to train the underlying AI model. Amazon Bedrock processes your content for the purpose of generating responses only.

---

## Access Controls

Service-to-service communication is protected by API keys managed through DEFRA's CDP secrets management system. AWS services are accessed via IAM roles — no static credentials are embedded in code or containers.

Uploaded files are stored in S3 with access restricted to the Knowledge Service; they are not publicly accessible.

---

## Security Scanning

All software components are continuously scanned for known vulnerabilities using Trivy (container and dependency scanning) and SonarCloud (code quality and security analysis). Findings are reviewed and addressed as part of normal development.

---

## Sensitive Data Considerations

This system is a **reference implementation** with a generalised design. Teams adopting it for production use cases involving sensitive or personal data should conduct their own Data Protection Impact Assessment (DPIA) and ensure the system configuration meets their data classification requirements.
