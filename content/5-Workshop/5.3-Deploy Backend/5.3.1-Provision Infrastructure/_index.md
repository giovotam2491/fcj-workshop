---
title : "Provision Infrastructure"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

#### Step 1 — Clone the project

```bash
git clone https://github.com/vanphucpham2k4/rag-chatbot-aws-serverless.git
cd rag-chatbot-aws-serverless
```

#### Step 2 — Configure backend environment variables

```bash
cd backend
npm install
cp .env.example .env
```

Open `backend/.env` and fill in:

```bash
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=<your access key>
AWS_SECRET_ACCESS_KEY=<your secret key>

# Must be GLOBALLY UNIQUE across all of S3
DOCS_BUCKET=rag-docs-<your-unique-name>

# Admin account created in Cognito + seeded into DynamoDB
SEED_ADMIN_EMAIL=admin@example.com
TEST_PASSWORD=RagTest#2026

# LEAVE EMPTY — provision will auto-fill; KB_ID is filled in a later step
MODEL_ARN=
KB_ID=
DATA_SOURCE_ID=
```

{{% notice warning %}}
Never commit `.env` to Git (it is already in `.gitignore`). `.env.example` only contains placeholders.
{{% /notice %}}

![env file](/images/5-Workshop/5.3-Deploy%20Backend/env-backend.png)

#### Step 3 — Run the provisioning script

```bash
npm run provision
```

`provision.ts` creates (idempotent — safe to re-run):
- **DynamoDB**: 4 tables (Users, Roles, Documents, ChatHistory) + GSI + TTL.
- **S3**: data-source bucket + CORS configuration (for presigned PUT uploads).
- **Cognito**: User Pool + App Client (SPA) + admin user (permanent password set).
- **IAM**: Lambda execution role (DynamoDB, S3, Bedrock, CloudWatch Logs).
- **Lambda**: 6 functions (build code, create, set env vars, timeout 30s / 256MB).
- **API Gateway** HTTP API + **JWT authorizer** + 5 routes + integrations.
- **S3 → Lambda trigger** (ingestion) + **seed** roles/admin.

![provision output](/images/5-Workshop/5.3-Deploy%20Backend/provision-output.png)

{{% notice tip %}}
🔍 **Verify on Console**: cross-check what the script just created, without running anything else:
- **DynamoDB → Tables**: 4 tables `Users`, `Roles`, `Documents`, `ChatHistory` with status **Active**.
- **S3 → Buckets**: your `DOCS_BUCKET` bucket exists.
- **Lambda → Functions**: 6 functions prefixed `rag-` (e.g. `rag-chat`, `rag-ingest`).
- **API Gateway → APIs**: `rag-chatbot-api` (HTTP API) with 5 routes.
- **Cognito → User pools**: `rag-chatbot-pool`, with 1 user matching `SEED_ADMIN_EMAIL`.
{{% /notice %}}

![console check provision](/images/5-Workshop/5.3-Deploy%20Backend/console-check-provision.png)
