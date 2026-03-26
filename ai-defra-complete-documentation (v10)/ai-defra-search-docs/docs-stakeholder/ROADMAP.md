# Considerations for Adopting Teams

[← Back to Stakeholder Docs](README.md)

> This codebase is a reference implementation — a proven, production-quality starting point. Teams adopting it will need to design and build the capabilities below to meet their specific needs. These are not planned enhancements to this project; they are known gaps that each team should assess and prioritise for their context.

---

## Data and Access

| Consideration | Why it matters |
|---|---|
| **Shared knowledge groups** | This implementation supports personal document groups only. Most team use cases will require shared document pools that multiple users can access and contribute to. Teams will need to define access control rules and build the sharing model that suits their users |
| **Connection to existing document stores** | Documents must currently be uploaded manually. Teams with large existing document libraries in SharePoint, shared drives, or other systems will need to build automated ingestion pipelines and decide how to keep content in sync |
| **Metadata and filtering** | The current system searches by meaning only — there is no ability to filter by date, author, topic, or document type. Teams with large document sets will likely need this to keep results relevant and manageable |

---

## Query Capabilities

| Consideration | Why it matters |
|---|---|
| **Hybrid search (keyword + semantic)** | The system uses meaning-based search only. For queries involving specific codes, references, or exact terminology, keyword matching is more reliable. Most mature implementations combine both approaches |
| **Whole-document context** | The system works with passages, not full documents. Teams whose users need to summarise reports, compare whole documents, or synthesise content across many pages will need to implement a different retrieval strategy |
| **Additional file types** | Only PDF, Word, PowerPoint, and JSONL are supported. Teams working with Excel, CSV, images, scanned documents, or legacy formats will need to build extractors for those types |

---

## User Experience

| Consideration | Why it matters |
|---|---|
| **Streaming responses** | Answers currently appear in full once complete. For longer responses, users may experience a noticeable wait. Streaming (showing the answer as it is generated) significantly improves the feel of the tool and is worth considering early |
| **Accessibility audit** | Some automated accessibility testing is in place, but a formal WCAG 2.1 AA audit has not been conducted. Teams deploying to public-facing or staff-wide audiences should commission this before wider rollout |

---

## Technical Maturity

| Consideration | Why it matters |
|---|---|
| **Performance baseline** | The performance testing framework (JMeter) is included, but no formal benchmarks have been established. Teams should define and test expected response times and system capacity before production deployment at scale |
| **Data governance** | Teams should confirm with their information governance teams what categories of content can be uploaded, how long conversations and documents should be retained, and what audit logging is required |
| **Security review** | A security assessment appropriate to the sensitivity of the data being handled should be conducted before production deployment |

---

> Each team's priorities will differ based on their users, data, and deployment context. This list is a starting point for planning — not every item will apply to every team.
