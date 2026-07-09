---
title : "Deploy Backend"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Overview

In this section, you will provision the **entire backend infrastructure** of the RAG chatbot in your own AWS account — DynamoDB tables, S3 data-source bucket, Cognito User Pool, IAM roles, 6 Lambda functions, and an API Gateway HTTP API — using a single automated script. You will then create the **Amazon Bedrock Knowledge Base** (the one step that must be done manually in the Console) and finish wiring it into the Lambda functions.

![architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

#### Content

- [Provision infrastructure & clone project](5.3.1-deploy-lambda/)
- [Create the Bedrock Knowledge Base](5.3.2-%20Configure%20API%20Gateway/)
- [Configure environment variables & finalize](5.3.3-Configure%20Environment/)
- [Verify the backend](5.3.4-Verify%20Backend/)
