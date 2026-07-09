---
title : "Delete DynamoDB Tables"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.6.4. </b> "
---

#### Delete DynamoDB tables

In this step, delete the 4 tables created for the project: `Users`, `Roles`, `Documents`, `ChatHistory`.

#### Steps

1. Open the **DynamoDB Console → Tables**.
2. Select all 4 tables (`Users`, `Roles`, `Documents`, `ChatHistory`).
3. Click **Delete**.
4. Uncheck "Create a backup" if not needed, type **confirm**, and click **Delete**.

![delete dynamodb](/images/5-Workshop/5.6-Cleanup/delete-dynamodb.png)

{{% notice tip %}}
🔍 **Verify on Console**: refresh **DynamoDB → Tables** — `Users`, `Roles`, `Documents`, `ChatHistory` must no longer be listed.
{{% /notice %}}

#### Result

All 4 DynamoDB tables have been removed from your AWS account.
