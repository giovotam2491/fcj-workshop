---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Internal Document Q&A Chatbot — Retrieval-Augmented Generation (RAG) on AWS Serverless

| Item | Details |
| --- | --- |
| Project title | Internal Document Q&A Chatbot Using RAG on AWS Serverless |
| Architecture type | Serverless (no server management), pay-as-you-go |
| Platform | Amazon Web Services (AWS) — region us-east-1 (N. Virginia) |
| AI core | Amazon Bedrock Knowledge Base · Titan Embeddings V2 · Nova Lite · S3 Vectors |
| Target users | Internal staff & administrators of the organization (role-based access) |
| Target cost | Near $0 when idle; ~$5–10/month at internal scale with light traffic |

### 1. Executive Summary

This project builds an **internal document Q&A chatbot** that lets employees ask questions in natural language and receive accurate answers **grounded in the organization's own documents, always accompanied by source citations** so users can verify them. The system applies **RAG (Retrieval-Augmented Generation)**: rather than letting a language model "make things up," it first retrieves the relevant passages from the document store and only then generates an answer based on that retrieved content.

The entire system is deployed as a **Serverless architecture on AWS**: no servers to manage, automatic scaling with load, and billing strictly by actual usage. The interface is a React web app (Vite + TypeScript) delivered through Amazon CloudFront; users sign in via Amazon Cognito; business logic runs on AWS Lambda behind API Gateway; and the RAG core is handled by Amazon Bedrock Knowledge Base with Titan Embeddings V2, an S3 Vectors vector store, and Amazon Nova Lite generating the final answer.

The architecture is organized into **three clear layers**: (1) an Interface/Edge layer that delivers the web app and authenticates users; (2) an API & Processing layer that orchestrates the RAG flow, enforces authorization, and stores conversation history; and (3) an AI layer (Amazon Bedrock) that retrieves relevant knowledge and generates cited answers. The project follows the **First Cloud Journey** spirit: a real-world Serverless use case built directly on AWS-managed services rather than self-hosted servers, addressing a genuine business need.

### 2. Problem Statement

*The Problem*

In many organizations, internal knowledge is scattered across formats and locations (PDF, Word, wikis, email, shared drives), which leads to:

- **Time lost searching:** employees have to open multiple files and read through them manually just to find one piece of information.
- **Limited keyword search:** traditional search tools only match keywords and don't understand the intent behind a question.
- **Fragmented, outdated knowledge:** it's hard to know which version is current, so people often end up answering from stale documents.
- **Risk of using a "bare" LLM:** public tools like ChatGPT don't know an organization's internal data, are prone to hallucination, and carry a risk of data leakage.
- **Dependence on a few experienced people:** repeated questions pile up on a handful of staff, creating a bottleneck.

*The Solution*

The system addresses these problems with the following core capabilities:

- **Natural-language Q&A with citations:** answers stay on-topic and are based only on ingested documents, with sources attached for verification; the system flags questions that fall outside the scope of the documents.
- **Multi-turn conversation:** the system remembers the context of prior questions (via the ChatHistory table) to keep answers coherent across a conversation.
- **Document management console:** admins upload documents, and the system automatically parses, chunks, embeds, and updates the vector store — no manual steps required.
- **Role-based access control (RBAC):** governs who can use the chatbot and who can manage documents and users.
- **Infrastructure provisioned via scripts:** a set of provision/finalize scripts can rebuild the entire system on a fresh AWS account (idempotent), which also powers the accompanying Workshop.

*AWS Infrastructure*

- **AWS WAF + Amazon CloudFront + S3 (private + OAC)** deliver the React web app over HTTPS globally, with WAF filtering malicious requests in front of CloudFront.
- **Amazon Cognito** handles sign-in, issues JWTs, and integrates with the JWT authorizer.
- **Amazon API Gateway (HTTP API) + AWS Lambda** handle all serverless business logic (12 functions, 10 routes).
- **Amazon DynamoDB** stores system data (4 tables, On-Demand + TTL).
- **Amazon Bedrock Knowledge Base** automatically parses, chunks, embeds, and retrieves knowledge.
- **Amazon Titan Text Embeddings V2 (512 dimensions)** generates embeddings cost-efficiently.
- **Amazon Nova Lite** generates answers with citations.
- **Amazon S3 Vectors** is the low-cost vector store managed by Bedrock KB.
- **Amazon CloudWatch + AWS Budgets** monitor the system and alert on cost.
- **AWS IAM** enforces least-privilege, with a dedicated role per Lambda function.

*Benefits and Return on Investment*

- Employees find internal knowledge in seconds instead of manually reading through stacks of documents.
- Answers are grounded in official documents and cited — eliminating the hallucination and data-leakage risk of public LLMs.
- No server management, with automatic scaling to demand thanks to the Serverless architecture.
- Near-$0 cost when idle, ~$5–10/month at internal scale; easy to extend with new documents over time.

### 3. Solution Architecture

The platform is built on a **3-layer AWS Serverless architecture**, with AWS Lambda orchestrating business logic and Amazon Bedrock Knowledge Base handling the RAG core. All resources reside in the us-east-1 region.

![3-Layer AWS Serverless Architecture](/images/2-Proposal/architecture.png)

*AWS Services Used*

| Service | Purpose |
| --- | --- |
| AWS WAF | Web application firewall in front of CloudFront: filters malicious requests, blocks common attacks (SQLi, XSS), rate-limits traffic |
| Amazon CloudFront | CDN delivering the React web app over HTTPS, paired with OAC to protect the bucket |
| Amazon S3 (Static) | Stores the built React + Vite files (private bucket, accessible only via CloudFront) |
| Amazon S3 (Data Source) | Stores source documents (PDF/DOCX/TXT) as the Knowledge Base's data source |
| Amazon API Gateway | HTTP API, 10 routes, Cognito JWT authorizer (cheaper than REST API) |
| Amazon Cognito | Sign-up, sign-in, session management, JWT issuance |
| AWS Lambda | 12 functions (Node.js 20, TypeScript) handling all backend logic |
| Amazon DynamoDB | 4 NoSQL tables (On-Demand + TTL) storing users, roles, documents, chat history |
| Amazon Bedrock Knowledge Base | RAG core: automatic parsing + chunking + embedding + retrieval with citations |
| Amazon Titan Embeddings V2 | Generates 512-dimension embeddings — roughly 50% cheaper vector storage |
| Amazon Nova Lite | LLM that generates answers from retrieved context |
| Amazon S3 Vectors | Low-cost vector store (roughly 90% cheaper than OpenSearch Serverless) |
| Amazon CloudWatch | Logs, metrics, alarms for errors/latency/throttling |
| AWS Budgets | Email alerts when cost exceeds a threshold (e.g. $10/month) |
| AWS IAM | Least-privilege: a dedicated role per Lambda, scoped to only the permissions needed |

### 4. Feature Details

*Target Users*

- **Internal staff (user):** search, ask questions, and receive cited answers through the chat interface.
- **Administrators (admin):** upload and manage documents, and manage users and roles.
- The system is for internal use only, gated by Cognito sign-in and RBAC.

*Query Flow (a user asks a question)*

1. The user opens the app: the request passes through AWS WAF → CloudFront; CloudFront serves the React app from S3 (a private bucket accessed through OAC).
2. The user signs in directly inside the web app (already loaded via CloudFront), never calling Cognito directly; the app authenticates against Cognito and receives a JWT (containing the user's identity and role).
3. The app calls API Gateway (HTTP API) with the JWT attached; the Cognito JWT authorizer verifies the token.
4. The Lambda orchestrator checks the CHAT_USE permission and reads recent history from ChatHistory to preserve multi-turn context.
5. The Lambda calls Bedrock KB's RetrieveAndGenerate: the KB retrieves relevant passages from S3 Vectors, and Nova Lite generates an answer with citations.
6. The Lambda writes the turn (question, answer, sources, latency, isAnswered) to ChatHistory and returns the result to the user.

*Ingestion Flow (an admin uploads a document)*

1. The admin requests an upload → the Lambda issues a presigned URL, and the file is written directly to the S3 data-source bucket.
2. An S3 ObjectCreated event triggers the ingest Lambda.
3. The Lambda writes metadata to the Documents table (including a kbIngestionJobId) and calls Bedrock's StartIngestionJob.
4. The Knowledge Base handles everything from there: parse → chunk → generate embeddings (Titan V2) → write to S3 Vectors — no need for Textract, Step Functions, or a separate chunking Lambda.
5. The Lambda updates the Documents status once the job completes (processing → active).

*Administration & Access Control (RBAC)*

- Roles are stored in DynamoDB with a denormalized permission list: DOC_UPLOAD, DOC_DELETE, DOC_VIEW, CHAT_USE, USER_MANAGE.
- Admin console: document management (upload, update, delete, versioning) and user management (create, edit, disable).
- Document versioning: the old version is marked inactive → removed from S3 → StartIngestionJob is re-run, preventing answers based on stale content.

### 5. Technical Implementation

*Technology Used*

| Component | Technology |
| --- | --- |
| Frontend | React + Vite (TypeScript), GSAP — 3D dark-glass UI |
| Web delivery | AWS WAF + Amazon CloudFront + S3 (static hosting, private bucket + OAC) |
| Backend (compute) | AWS Lambda (Node.js 20, TypeScript, AWS SDK v3) — 12 functions |
| API | Amazon API Gateway (HTTP API) + Cognito JWT authorizer — 10 routes |
| Auth & access control | Amazon Cognito (sign-in, JWT) + RBAC stored in DynamoDB |
| RAG core | Amazon Bedrock Knowledge Base (automatic parse + chunk + embed) |
| Embedding | Amazon Titan Text Embeddings V2 (512 dimensions — cost-efficient) |
| Answer-generation LLM | Amazon Nova Lite |
| Vector store | Amazon S3 Vectors (managed by Bedrock KB) |
| Database | Amazon DynamoDB (NoSQL, On-Demand + TTL) — 4 tables |
| Monitoring / alerting | Amazon CloudWatch + AWS Budgets |
| Infrastructure as scripts | TypeScript provision/finalize script set (idempotent) |

*Lambda Functions — 12 functions*

| Function | Responsibility |
| --- | --- |
| rag-chat | RAG orchestrator: checks permissions, reads history, calls RetrieveAndGenerate, writes ChatHistory |
| rag-chatHistory | Returns a user's conversation history |
| rag-me | Returns the current user's profile, role, and permissions |
| rag-uploadUrl | Issues a presigned URL so admins can upload documents directly to S3 |
| rag-listDocuments | Lists documents and their ingestion status |
| rag-updateDocument | Updates document metadata/status |
| rag-deleteDocument | Removes a document from S3 + Documents, re-syncs the KB |
| rag-ingest | S3 event handler → writes metadata, calls StartIngestionJob, polls status (300s timeout) |
| rag-contextualize | Asynchronous chunking/contextualization processing (600s timeout) |
| rag-listUsers | Lists users (admin console) |
| rag-createUser | Creates a new user (Cognito + Users table) |
| rag-updateUser | Updates a user's role/status |

*DynamoDB — 4 tables*

Designed with proper NoSQL thinking: UUID/string keys, GSIs for lookups, denormalized permissions (no join tables), and TTL to auto-clean old logs. Split into two groups: **Access control** (Users, Roles) and **Business data** (Documents, ChatHistory).

| Table | Partition Key | Sort Key | Index | Used For |
| --- | --- | --- | --- | --- |
| Users | cognitoSub | — | email-index | User profile (mirrors Cognito) + role |
| Roles | roleName | — | — | Role + permission list (denormalized) |
| Documents | docId | — | status-index | Document metadata + ingestion status |
| ChatHistory | userId | timestamp | — | Q&A history (TTL enabled to auto-purge old logs) |

*API Gateway — 10 routes*

| Route | Method | Used For |
| --- | --- | --- |
| /chat | POST | Submit a question, receive a cited answer (RAG) |
| /chat-history | GET | Fetch conversation history |
| /me | GET | Fetch the current user's profile + permissions |
| /documents | GET | List documents |
| /documents/upload-url | POST | Issue a presigned URL for document upload |
| /documents/{id} | PUT | Update a document |
| /documents/{id} | DELETE | Delete a document |
| /users | GET | List users (admin) |
| /users | POST | Create a user (admin) |
| /users/{sub} | PATCH | Update a user (admin) |

*Key Design & Security Decisions*

- **Eliminated redundant pipeline stages:** Bedrock KB already handles parsing + chunking + embedding, so Textract, Step Functions, and a separate chunking Lambda were dropped entirely — avoiding paying twice for the same work.
- **S3 Vectors instead of OpenSearch:** avoids OpenSearch Serverless's minimum cost floor (~$700/month), roughly 90% cheaper.
- **No VPC/NAT:** Lambda reaches DynamoDB & Bedrock via IAM, avoiding NAT Gateway cost (~$32/month).
- **AWS WAF + CloudFront + OAC:** WAF blocks attacks in front of CloudFront; the web bucket is private and reachable only via CloudFront; the static web bucket and the data-source bucket are kept separate.
- **IAM least-privilege:** a dedicated role per Lambda, scoped to only the permissions it needs.

### 6. Timeline & Milestones

The plan is broken into stages (sprints) within the internship workshop. **Total project duration: 7 weeks (May 25 – July 10, 2026), per the Week 6–12 worklog.**

| Stage | Main Activities | Deliverable | Dates |
| --- | --- | --- | --- |
| 1. Research & Design | Research RAG, select AWS services, draw the 3-layer architecture, design the DynamoDB schema | Architecture diagram + DB design | May 25 – May 29, 2026 |
| 2. Infrastructure Setup | Write provision scripts: DynamoDB, S3, Cognito, IAM, Lambda, API Gateway | Automated infrastructure + seed data | Jun 1 – Jun 5, 2026 |
| 3. RAG Core (Bedrock) | Create the Knowledge Base, Titan V2 at 512 dimensions, Nova Lite; ingestion + query flows | Working KB, e2e-rag passes | Jun 8 – Jun 12, 2026 |
| 4. Backend & API | 12 Lambda functions, 10 routes, JWT authorizer, RBAC | API live, token tests pass | Jun 15 – Jun 19, 2026 |
| 5. Frontend | React + Vite: sign-in, multi-turn chat, upload/admin pages | Usable web app | Jun 22 – Jun 26, 2026 |
| 6. Testing & Optimization | E2E testing, IAM/timeout tuning, AWS Budgets setup, cost optimization | Stable system | Jun 29 – Jul 3, 2026 |
| 7. Deployment & Workshop | Deploy the web app (CloudFront + OAC), write the workshop with step-by-step screenshots | Live deployment + workshop docs | Jul 6 – Jul 10, 2026 |

### 7. Budget Estimation

Because the architecture is Serverless and makes use of the free tier, cost is close to **$0 when idle** and scales only with actual usage.

*Estimated Monthly Cost*

| Service | Estimate | Notes |
| --- | --- | --- |
| AWS Lambda | ~$0.00 | Free tier: 1M requests/month |
| Amazon DynamoDB | ~$0.00 | On-Demand, 25 GB free tier |
| Amazon S3 (static + data source) | ~$0.05 | Web build + source documents |
| Amazon API Gateway (HTTP API) | ~$0.01 | Light internal traffic |
| Amazon CloudFront | $0.00 | Free tier: 1 TB/month |
| Amazon Cognito | $0.00 | Free tier: 50,000 MAU |
| Amazon S3 Vectors | ~$0.10 | Vector store at project scale |
| Bedrock — Titan Embeddings V2 | ~$0.10 | Embedding cost during document ingestion |
| Bedrock — Nova Lite | ~$1–5 | Answer-generation tokens, depending on traffic |
| Amazon CloudWatch + AWS Budgets | ~$0.00 | Free tier |
| **Total estimate** | **~$5–10/month** | Driven mainly by Bedrock tokens; near $0 when idle |

*Cost Summary*

- **Startup cost:** $0 (no custom domain; uses the default CloudFront domain).
- **Monthly operating cost:** ~$5–10 at internal scale with light traffic; near $0 when idle.
- **Guardrail:** AWS Budgets alerts early when cost approaches the $10/month threshold.

### 8. Risk Assessment

*Risk Matrix*

| Risk | Impact | Probability | Mitigation |
| --- | --- | --- | --- |
| New-account Bedrock quota limits (ThrottlingException) | High | Medium | Request a quota increase in Service Quotas, or use an account with quota already granted |
| Knowledge Base created in the wrong region → "KB does not exist" error | High | Low | Enforce us-east-1 for all resources; scan-kb.ts script checks the region |
| Access key leak / internal data exposure | High | Low | Never commit .env files; rotate keys; IAM least-privilege; private bucket + OAC |
| Default 3s Lambda timeout when Bedrock responds slowly | Medium | Medium | Set 30s timeout + 256MB memory (set-lambda-timeout.ts script) |
| Unexpected cost overrun | Medium | Medium | AWS Budgets threshold alerts; optimize S3 Vectors / Nova Lite / On-Demand usage |
| Chatbot hallucinates on out-of-scope questions | Medium | Low | Grounded RAG; flags isAnswered=false when outside document scope |
| Globally duplicate S3 bucket name (BucketAlreadyExists) | Low | Low | Use a unique, globally distinct bucket-name suffix |
| Updated document causes answers based on a stale version | Low | Medium | Versioning: mark old version inactive → remove from S3 → re-run StartIngestionJob |

### 9. Expected Outcomes

*Success Criteria*

- Sign-in, access control, chat Q&A, and document administration all work fully according to role.
- Answers are grounded in the documents with source citations; the E2E test (e2e-rag.ts) confirms that asking about content from a just-ingested file returns the correct answer.
- Multi-turn conversations preserve context coherently; latencyMs is logged per turn and processing stays within the Lambda 30s limit.
- Document ingestion is fully automatic: upload → processing → active once KB sync completes, with no manual steps.
- Cost stays near/within the free tier, below the AWS Budgets alert threshold ($10/month).
- The system is stable, with no critical CloudWatch alarms; IAM least-privilege is enforced for every Lambda.

*Long-Term Value*

- The internal knowledge base grows incrementally and is easy to extend with new documents and categories.
- The idempotent provision script set can rebuild the entire system on a new AWS account — reusable for future projects.
- The Serverless architecture scales easily: streaming can be added (Lambda Function URL / WebSocket), along with MFA or additional data sources.
- The team gains hands-on experience with AWS Serverless, GenAI/RAG on Bedrock, and full-stack TypeScript.
