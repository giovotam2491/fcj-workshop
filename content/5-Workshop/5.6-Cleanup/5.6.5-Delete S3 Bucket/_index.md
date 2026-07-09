---
title : "Delete S3 Bucket"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.6.5. </b> "
---

#### Delete S3 buckets

In this step, delete the data-source bucket (`DOCS_BUCKET`) and, if you deployed the frontend in [5.4.4](../../5.4-Deploy-frontend/5.4.4-Connect%20Backend/), the web-hosting bucket as well.

#### Steps

1. Open the **S3 Console → Buckets**.
2. Select your data-source bucket.
3. Click **Empty**, type **permanently delete**, and confirm — S3 buckets must be empty before they can be deleted.
4. Once empty, click **Delete**, type the bucket name to confirm, and click **Delete bucket**.
5. Repeat for the frontend web-hosting bucket, if created.

![delete s3](/images/5-Workshop/5.6-Cleanup/delete-s3.png)

{{% notice tip %}}
🔍 **Verify on Console**: refresh **S3 → Buckets** — your `DOCS_BUCKET` (and web-hosting bucket, if created) must no longer be listed.
{{% /notice %}}

#### Optional — delete the CloudFront distribution and WAF Web ACL

If you completed [5.4.4](../../5.4-Deploy-frontend/5.4.4-Connect%20Backend/), also clean up CloudFront (and WAF, if you enabled it — it keeps billing per Web ACL/rule until deleted):

1. **CloudFront Console → Distributions** → select your distribution → **Disable** (must be disabled before it can be deleted; this can take several minutes to propagate).
2. Once **Status** shows **Disabled**, select it again → **Delete**.
3. If you enabled WAF: **WAF & Shield Console → Web ACLs** (region **Global (CloudFront)**) → select your Web ACL → **Delete**. AWS blocks deletion while it's still associated with a distribution, so do this only after the distribution itself is deleted (or disassociated).

{{% notice warning %}}
A distribution stuck in **Deploying** after clicking Disable just needs more time — wait and refresh rather than retrying the action. Deleting the Web ACL is what stops the ~$5/month WAF charge; simply deleting the CloudFront distribution alone does **not** delete the Web ACL.
{{% /notice %}}

#### Result

The S3 bucket(s) have been removed from your AWS account.
