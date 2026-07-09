---
title : "Clean up"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Overview

Congratulations on completing the workshop! You provisioned a serverless RAG chatbot end-to-end: DynamoDB, S3, Cognito, IAM, Lambda, API Gateway, and a Bedrock Knowledge Base with S3 Vectors.

To avoid ongoing charges, delete every resource created during this workshop. There is **no automated cleanup script** — all deletions below are done manually in the AWS Console, in the recommended order.

{{% notice warning %}}
Delete resources in this order to avoid dependency errors: **Lambda → API Gateway → DynamoDB → S3 → Bedrock Knowledge Base → Cognito → IAM role → (optional) CloudWatch Log groups**.
{{% /notice %}}

#### Content

- [Delete AWS Lambda](5.6.1-Delete%20Lambda/)
- [Delete API Gateway](5.6.2-Delete%20API%20Gateway/)
- [Delete Bedrock Knowledge Base](5.6.3-Delete%20Bedrock%20Knowledge%20Base/)
- [Delete DynamoDB tables](5.6.4-Delete%20DynamoDB/)
- [Delete S3 bucket](5.6.5-Delete%20S3%20Bucket/)
- [Delete Cognito User Pool](5.6.6-Delete%20Cognito/)
