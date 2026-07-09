---
title : "Lịch sử Chat & Nạp tài liệu"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.5.4 </b> "
---

#### Phần 1 — Lịch sử chat

1. Tải lại trang chat và xác nhận cặp câu hỏi/trả lời trước đó vẫn còn (được nạp từ `ChatHistory` trong DynamoDB).

![chat history ui](/images/5-Workshop/5.5-Test-chatbot/chat-history-ui.png)

2. Đặt 1 câu hỏi tiếp nối ngắn ("còn về ... thì sao?") mà không lặp lại ngữ cảnh — trợ lý vẫn trả lời đúng, chứng minh ngữ cảnh đa lượt được đọc từ `ChatHistory` trước khi gọi Bedrock.

![multi turn](/images/5-Workshop/5.5-Test-chatbot/multi-turn.png)

#### Phần 2 — Nạp tài liệu (bước A–H)

Đăng nhập bằng tài khoản admin, vào trang quản lý tài liệu, upload 1 file PDF/TXT nhỏ.

| Bước | Xem ở đâu | Thấy gì |
|---|---|---|
| A–B | DevTools → Network | `POST /upload-url` kèm Bearer token → response chứa `uploadUrl` (presigned) |
| C | DynamoDB → `Documents` | Bản ghi mới, `status = "processing"` |
| D | DevTools Network + S3 Console | Request `PUT https://<bucket>.s3...` (presigned URL); file xuất hiện tại `documents/<documentId>/<tên-file>` trên S3 |
| E | CloudWatch → `/aws/lambda/rag-ingest` | Log stream mới ngay sau khi upload lên S3 (S3 event trigger) |
| F | Bedrock Console → KB → Data source → Sync history | 1 ingestion job mới, STARTING → IN_PROGRESS |
| G | Đợi job COMPLETE | Job báo số document đã index — vector đã ghi vào S3 Vectors |
| H | DynamoDB → `Documents` (refresh) | `status` đổi từ `processing` sang **`active`** |

![upload flow](/images/5-Workshop/5.5-Test-chatbot/upload-flow.png)
![ingestion job](/images/5-Workshop/5.5-Test-chatbot/ingestion-job.png)
![document active](/images/5-Workshop/5.5-Test-chatbot/document-active.png)

Khi `status` chuyển sang `active`, quay lại chat và hỏi 1 câu **chỉ có thể trả lời được từ tài liệu vừa upload** — trích dẫn phải trỏ đúng file đó, khép kín vòng lặp: **A–H nạp dữ liệu vào, 1–10 lấy dữ liệu ra.**

{{% notice tip %}}
Quay video demo: chia đôi màn hình — bên trái UI chat, bên phải CloudWatch Live Tail (`rag-chat`). Gửi 1 câu hỏi, log chạy ngay lập tức; sau đó mở DynamoDB để chỉ bản ghi mới. 30 giây là đủ chứng minh toàn bộ sơ đồ kiến trúc là thật, không chỉ là hình vẽ.
{{% /notice %}}

{{% notice note %}}
Muốn chạy tự động 1 lần cho cả luồng? `cd backend && npx tsx scripts/e2e-rag.ts` sẽ chạy trọn vòng (đăng nhập → upload → đợi ingestion → hỏi → kiểm tra trích dẫn) chỉ bằng 1 lệnh.
{{% /notice %}}
