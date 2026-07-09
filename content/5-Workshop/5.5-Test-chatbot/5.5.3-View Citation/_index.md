---
title : "View Citation / Sources"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---

This step covers steps **8–10**: retrieval from S3 Vectors and answer generation by Nova Lite — the core of RAG.

#### Step 1 — Read the response in the UI

The chat answer should display **source citations** (document name / chunk) alongside the generated text.

![citation ui](/images/5-Workshop/5.5-Test-chatbot/citation-ui.png)

#### Step 2 — Confirm in the Lambda log

In **CloudWatch → `/aws/lambda/rag-chat`**, open the newest log stream and find the `REPORT RequestId: ...` line for your chat request. Look at **Duration**: a plain DynamoDB read/write finishes in well under 100 ms, but this request should show **several seconds** (e.g. `Duration: 4397.08 ms`) — that gap is the Lambda waiting on Bedrock's Knowledge Base retrieval + Nova Lite generation over the network, real proof an external AI call happened, not an instant canned response.

![retrieve and generate log](/images/5-Workshop/5.5-Test-chatbot/retrieve-generate-log.png)

The direct, unambiguous proof that `RetrieveAndGenerate` ran — and what it returned — is the response body in Step 3 below.

#### Step 3 — Inspect the raw JSON response

Open DevTools → Network → the `/chat` response body. It should contain both `answer` and a `sources` array with the retrieved document references.

![response json](/images/5-Workshop/5.5-Test-chatbot/response-json.png)