---
title : "Configure Environment & Finalize"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3.3 </b> "
---

Now that the Knowledge Base exists, wire its IDs back into the backend and push the final Lambda code.

#### Step 1 — Fill in the Knowledge Base IDs

Open `backend/.env` again and paste the two values copied in the previous step:

```bash
KB_ID=<Knowledge Base ID>
DATA_SOURCE_ID=<Data source ID>
```

![env kb](/images/5-Workshop/5.3-Deploy%20Backend/env-kb-id.png)

#### Step 2 — Finalize the deployment

```bash
npm run finalize
```

This command:
- rebuilds the Lambda TypeScript code,
- runs `deploy-sdk.ts` to push the built code to all 6 Lambda functions,
- runs `set-lambda-env.ts` to set environment variables (`KB_ID`, `DATA_SOURCE_ID`, `MODEL_ARN`, `DOCS_BUCKET`, ...) on every function.

![finalize output](/images/5-Workshop/5.3-Deploy%20Backend/finalize-output.png)

{{% notice tip %}}
🔍 **Verify on Console**: open **Lambda → Functions → `rag-chat`**:
- **Code** tab: check "Last modified" matches the time you just ran `finalize`.
- **Configuration → Environment variables**: confirm `KB_ID`, `DATA_SOURCE_ID`, and `MODEL_ARN` are present and non-empty.
{{% /notice %}}

![lambda env vars](/images/5-Workshop/5.3-Deploy%20Backend/lambda-env-console.png)

#### Reference — script list (`backend/scripts/`)

| Command | Purpose |
|---|---|
| `npm run provision` | Create all AWS infrastructure + seed + write `.env` |
| `npm run finalize` | Build + push code to 6 Lambdas + set env vars |
| `npm run check` | Validate infrastructure (tables, seed, bucket, KB) |
| `npm run seed` | Re-seed Roles + admin user |
| `tsx scripts/inspect-fn.ts <function-name>` | Inspect env vars + latest logs of one Lambda |
| `tsx scripts/scan-kb.ts` | Find which region a Knowledge Base lives in |
| `tsx scripts/fix-iam.ts` | Patch the Lambda role's Bedrock IAM permissions |

{{% notice tip %}}
If you see the Lambda still returning `"Hello from Lambda!"`, it still has the placeholder sample code — simply re-run `npm run finalize`.
{{% /notice %}}
