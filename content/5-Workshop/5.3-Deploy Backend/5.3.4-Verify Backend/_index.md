---
title : "Verify Backend"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.3.4 </b> "
---

Before moving to the frontend, confirm the backend is fully healthy.

#### Step 1 — Infrastructure health check

```bash
npm run check
```

Expect all 4 checks to pass: **DynamoDB tables**, **seed data**, **S3 bucket**, **Knowledge Base**.

![check pass](/images/5-Workshop/5.3-Deploy%20Backend/check-pass.png)

#### Step 2 — API smoke test

```bash
npx tsx scripts/token-test.ts
```

This authenticates as the seeded admin, then calls `GET /documents` (expect `200`), `POST /upload-url` (expect `201`), and `POST /chat` (expect `200`).

![token test](/images/5-Workshop/5.3-Deploy%20Backend/token-test.png)

#### Step 3 — End-to-end RAG test

```bash
npx tsx scripts/e2e-rag.ts
```

The script uploads a sample document, waits for Bedrock ingestion to finish, asks a question about it, and verifies the answer is **grounded** (cites the uploaded document).

![e2e rag](/images/5-Workshop/5.3-Deploy%20Backend/e2e-rag.png)

Once all three checks pass, the backend is ready — continue to **[5.4 Deploy Frontend](../../5.4-Deploy-frontend/)**.
