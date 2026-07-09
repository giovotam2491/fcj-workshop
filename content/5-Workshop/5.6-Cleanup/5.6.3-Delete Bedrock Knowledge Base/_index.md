---
title : "Delete Bedrock Knowledge Base"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.6.3. </b> "
---

#### Delete Bedrock Knowledge Base

In this step, delete the Knowledge Base and its underlying S3 Vectors index — the most expensive resource if left running.

#### Steps

1. Open **Amazon Bedrock Console → Knowledge Bases**.
2. Select your Knowledge Base (e.g. `rag-kb`).
3. Click **Delete**.
4. Type the confirmation text and click **Delete**.
5. Go to **S3 → Vector buckets** and delete the vector bucket/index created for this Knowledge Base, if it was not removed automatically.

![delete kb](/images/5-Workshop/5.6-Cleanup/delete-kb.png)

{{% notice warning %}}
Deleting the Knowledge Base does **not** delete the IAM service role it created, nor the S3 data-source bucket. Those are removed in separate steps.
{{% /notice %}}

{{% notice tip %}}
🔍 **Verify on Console**: refresh **Bedrock → Knowledge Bases** — your KB must no longer appear; and refresh **S3 → Vector buckets** to confirm the vector bucket/index is gone.
{{% /notice %}}

#### Result

The Bedrock Knowledge Base and its S3 Vectors store have been removed.
