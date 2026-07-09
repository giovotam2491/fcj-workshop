---
title : "Ask a Question"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

This step covers steps **4–7** of the query flow: calling the API, invoking the Lambda orchestrator, and writing to DynamoDB.

#### Step 1 — Send a chat message

Type a question about content in an uploaded document and click send.

![send question](/images/5-Workshop/5.5-Test-chatbot/send-question.png)

#### Step 2 — Inspect the API call

In DevTools → Network, find `POST .../chat` with header `Authorization: Bearer eyJ...`.

![chat request](/images/5-Workshop/5.5-Test-chatbot/chat-network.png)

{{% notice tip %}}
**Optional security test**: copy the request as `curl`, tamper with one character in the token, and resend it. You should get **401 Unauthorized** — proof that API Gateway's Cognito JWT authorizer validates the token *before* the Lambda ever runs.
{{% /notice %}}

#### Step 3 — Watch the Lambda get invoked

Right after sending, switch to the **CloudWatch → `/aws/lambda/rag-chat`** tab and refresh — a new log stream appears immediately.

![cloudwatch log stream](/images/5-Workshop/5.5-Test-chatbot/cloudwatch-log.png)

#### Step 4 — Confirm the write to DynamoDB

Once the answer returns, go to **DynamoDB → `ChatHistory` → Explore items** and find the newest item, with `question`, `answer`, `sources`, and `latencyMs`.

![dynamodb chathistory](/images/5-Workshop/5.5-Test-chatbot/dynamodb-chathistory.png)

{{% notice note %}}
Prefer the command line? `npx tsx scripts/ask.ts "your question"` runs the same 4→10 chain without the UI and prints the answer + sources directly.
{{% /notice %}}
