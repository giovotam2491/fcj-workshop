---
title : "Delete AWS Lambda"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.6.1. </b> "
---

#### Delete AWS Lambda

In this step, delete the 6 Lambda functions deployed for the backend.

#### Steps

1. Open the **AWS Lambda Console**.
2. Select a function of the project (prefixed `rag-`, e.g. `rag-chat`, `rag-ingest`, `rag-upload`, `rag-documents`, `rag-users`, `rag-me`).
3. Choose **Actions** → **Delete**.
4. Type **delete** to confirm.
5. Click **Delete**.
6. Repeat for the remaining 5 functions.

![lambda](/images/5-Workshop/5.6-Cleanup/delete-lambda.png)

{{% notice tip %}}
🔍 **Verify on Console**: refresh the **Lambda → Functions** list — the function you just deleted must no longer appear. Repeat steps 2–5 for the remaining 5 `rag-*` functions, then refresh once more to confirm all 6 are gone.
{{% /notice %}}

#### Result

All 6 AWS Lambda functions have been removed from your AWS account.
