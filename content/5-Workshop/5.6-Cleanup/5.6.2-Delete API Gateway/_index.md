---
title : "Delete API Gateway"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.6.2. </b> "
---

#### Delete API Gateway

In this step, delete the HTTP API that exposes the chatbot backend.

#### Steps

1. Open the **API Gateway Console → APIs**.
2. Tick the **radio button** on the left of the row for `rag-chatbot-api` (or the name you gave it during provisioning) — this is a flat list now, there's no "Actions" dropdown menu anymore.
3. Click the **Delete** button in the top-right toolbar (next to **Create API**).
4. Confirm the deletion in the dialog that appears.

![api gateway](/images/5-Workshop/5.6-Cleanup/delete-api-gateway.png)

{{% notice note %}}
Deleting the API also removes its routes, the JWT authorizer attachment, and the integrations to the 6 Lambda functions — but not the Lambda functions themselves (delete those separately, see [5.6.1](../5.6.1-Delete%20Lambda/)).
{{% /notice %}}

{{% notice tip %}}
🔍 **Verify on Console**: refresh **API Gateway → APIs** — `rag-chatbot-api` must no longer appear in the list.
{{% /notice %}}

#### Result

The API Gateway HTTP API has been removed from your AWS account.
