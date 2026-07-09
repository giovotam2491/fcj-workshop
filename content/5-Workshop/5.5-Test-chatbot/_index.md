---
title : "Test the Chatbot"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5. </b> "
---

#### Overview

With both backend and frontend running, this section walks through the **full query flow** (steps 1–10) and the **document ingestion flow** (steps A–H) of the architecture diagram, so you can see each step actually happen — not just trust the diagram.

![query flow](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

Open 4 AWS Console tabs in **us-east-1** before you start:
1. **CloudWatch → Log groups** (`/aws/lambda/rag-chat`, `/aws/lambda/rag-ingest`, `/aws/lambda/rag-upload`)
2. **DynamoDB → Tables → Explore items** (`ChatHistory`, `Documents`)
3. **S3 → your data-source bucket** (`DOCS_BUCKET` value)
4. **Bedrock → Knowledge Bases → your KB → Data source → Sync history**

#### Content

- [Login](5.5.1-Login/)
- [Ask a question](5.5.2-Ask%20Question/)
- [View citation / sources](5.5.3-View%20Citation/)
- [Chat history](5.5.4-Chat%20History/)
