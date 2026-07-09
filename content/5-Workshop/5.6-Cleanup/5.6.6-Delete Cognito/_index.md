---
title : "Delete Cognito & IAM Role"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6.6. </b> "
---

#### Delete Cognito User Pool

1. Open **Amazon Cognito Console → User pools**.
2. Select the pool created for this project (e.g. `rag-chatbot-pool`).
3. Click **Delete**, type the pool name to confirm, and click **Delete**.

![delete cognito](/images/5-Workshop/5.6-Cleanup/delete-cognito.png)

#### Delete the Lambda IAM role

1. Open **IAM Console → Roles**.
2. Search for the Lambda execution role (e.g. `rag-lambda-role`).
3. Select it, click **Delete**, and confirm.

![delete iam role](/images/5-Workshop/5.6-Cleanup/delete-iam-role.png)

#### (Optional) Delete CloudWatch Log groups

To avoid tiny ongoing storage charges, also delete the log groups under **CloudWatch → Log groups** matching `/aws/lambda/rag-*`.

![delete log groups](/images/5-Workshop/5.6-Cleanup/delete-log-groups.png)

{{% notice tip %}}
🔍 **Verify on Console**: refresh **Cognito → User pools** (pool gone), **IAM → Roles** (search `rag-`, no results), and **CloudWatch → Log groups** (search `/aws/lambda/rag-`, no results if you did the optional step). Also run `aws sts get-caller-identity` to confirm you are still using a scoped IAM user, not root.
{{% /notice %}}

#### Result

All remaining resources — Cognito User Pool, the Lambda IAM role, and (optionally) CloudWatch log groups — have been removed. Your AWS account is now clean of everything created in this workshop.
