# Security & Privacy

> 📊 **Stakeholder Docs** — [Home](Home) › [Stakeholder Docs](Home#stakeholder-docs)

---

## Authentication

All users sign in with their DEFRA Microsoft account (Microsoft Entra ID). No separate registration or password — access is controlled through existing DEFRA identity management.

---

## Data Ownership and Isolation

Documents and conversations are **personal to the signed-in user**. Knowledge groups and uploaded files are isolated — other users cannot see or query your documents. Conversation history is linked to your user identity only.

---

## Data Storage

| Data | Where stored | Retention |
|---|---|---|
| Uploaded files | Amazon S3 (DEFRA's AWS) | Until deleted by user |
| Document embeddings | PostgreSQL (CDP-managed) | Until document deleted |
| Conversation history | MongoDB (CDP-managed) | Per retention policy |
| Session data | Redis (short-lived cache) | Cleared on session expiry |

All storage is within DEFRA's approved AWS environment.

---

## Data in Transit

All browser-to-service communication is encrypted (HTTPS). All inter-service communication uses encrypted connections and API key authentication.

---

## AI Model Data

When you submit a question or upload a document, the content is sent to **Amazon Bedrock** (AWS's AI service) to generate responses and create document embeddings. This occurs within DEFRA's AWS account under DEFRA's data processing agreement with Amazon.

No content is shared with third parties outside of AWS under DEFRA's agreement.

---

## No Training on Your Data

Content you submit is not used to train the underlying AI model. Amazon Bedrock processes content for response generation only.

---

## Access Controls

Service-to-service calls are protected by API keys managed through CDP's secrets store. AWS services use IAM roles — no static credentials in code or containers. Uploaded files in S3 are not publicly accessible.

---

## Security Scanning

All components are continuously scanned for vulnerabilities via Trivy and SonarCloud as part of every deployment.

---

## Sensitive Data Considerations

This is a **reference implementation** with a generalised design. Teams adopting it for use cases involving sensitive or personal data should conduct their own DPIA and ensure the configuration meets their data classification requirements.
