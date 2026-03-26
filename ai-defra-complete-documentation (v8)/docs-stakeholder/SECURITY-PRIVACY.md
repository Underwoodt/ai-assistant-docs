# Security & Privacy

---

## Data Handling

**Document storage:** Uploaded documents are stored within DEFRA's infrastructure on AWS S3. They do not leave DEFRA's environment or get sent to third-party services.

**Document passages:** The text content of documents is processed and stored as mathematical representations (vectors) in a secure database. Only the passages relevant to a given question are sent to the AI model.

**Conversation history:** Chat conversations are stored in a database within DEFRA's infrastructure. They are not used to train any AI model.

**No cross-user data access:** Each user's uploaded documents are personal. Other users cannot access your document groups or see your conversations.

---

## Access Control

**API authentication:** All communication between the browser interface and the backend services is authenticated using API keys. Requests without a valid key are rejected.

**AWS credentials:** Access to AWS services (for AI model invocation and document storage) is controlled via IAM roles and credentials managed by DEFRA's platform team. These are never stored in code.

**Secrets management:** All sensitive configuration — API keys, database passwords, AWS credentials — is managed via environment variables injected at deployment time. No secrets are stored in the codebase.

---

## AI Model Usage

The tool uses AWS Bedrock to access AI language models (primarily Claude models from Anthropic). When the tool sends a question to the AI model, it sends:
- The user's question
- A set of relevant document passages retrieved from the user's knowledge group

The AI model does not receive the full document library, user identity, or any information beyond what is needed to answer the specific question.

> ⚠ Verify with your information governance team whether the content of documents you upload is subject to any handling restrictions before uploading to this system.

---

## Code Quality and Vulnerability Management

- All services are scanned for known security vulnerabilities (Trivy) on every code change and daily
- Code quality is analysed via SonarCloud
- Dependencies are locked to specific versions to prevent supply-chain risks
- All services are licenced under the Open Government Licence v3
