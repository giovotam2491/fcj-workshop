---
title : "Chat History & Document Ingestion"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.5.4 </b> "
---

#### Part 1 — Chat history

1. Reload the chat page and confirm the previous question/answer pair persists (loaded from `ChatHistory` via DynamoDB).

![chat history ui](/images/5-Workshop/5.5-Test-chatbot/chat-history-ui.png)

2. Ask a short follow-up question ("what about ...?") without repeating context — the assistant should still answer correctly, proving multi-turn context is read from `ChatHistory` before calling Bedrock.

![multi turn](/images/5-Workshop/5.5-Test-chatbot/multi-turn.png)

#### Part 2 — Document ingestion (steps A–H)

Log in as admin, go to the document management page, and upload a small PDF/TXT file.

| Step | Where to look | What you should see |
|---|---|---|
| A–B | DevTools → Network | `POST /upload-url` with Bearer token → response contains a presigned `uploadUrl` |
| C | DynamoDB → `Documents` | New item with `status = "processing"` |
| D | DevTools Network + S3 Console | `PUT https://<bucket>.s3...` request (presigned URL); file appears at `documents/<documentId>/<filename>` in S3 |
| E | CloudWatch → `/aws/lambda/rag-ingest` | New log stream right after the S3 upload (S3 event trigger) |
| F | Bedrock Console → KB → Data source → Sync history | New ingestion job, STARTING → IN_PROGRESS |
| G | Wait for job to COMPLETE | Job reports documents indexed — vectors written to S3 Vectors |
| H | DynamoDB → `Documents` (refresh) | `status` changes from `processing` to **`active`** |

![upload flow](/images/5-Workshop/5.5-Test-chatbot/upload-flow.png)
![ingestion job](/images/5-Workshop/5.5-Test-chatbot/ingestion-job.png)
![document active](/images/5-Workshop/5.5-Test-chatbot/document-active.png)

Once `status` is `active`, go back to chat and ask a question **only answerable from that document** — the citation should point to the file you just uploaded, closing the loop: **A–H ingests data in, 1–10 retrieves it out.**

{{% notice tip %}}
For a demo recording: split the screen — chat UI on the left, CloudWatch Live Tail (`rag-chat`) on the right. Send a question and the log fires instantly; then open DynamoDB to show the new record. 30 seconds is enough to prove the whole architecture diagram is real, not just a picture.
{{% /notice %}}

{{% notice note %}}
Prefer a single automated pass? `cd backend && npx tsx scripts/e2e-rag.ts` runs the entire loop (login → upload → wait for ingestion → ask → verify citation) in one command.
{{% /notice %}}
