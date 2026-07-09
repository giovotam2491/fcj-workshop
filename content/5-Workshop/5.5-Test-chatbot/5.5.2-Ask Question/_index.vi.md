---
title : "Đặt câu hỏi"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.5.2 </b> "
---

Bước này minh hoạ bước **4–7** của luồng hỏi-đáp: gọi API, kích hoạt Lambda orchestrator, và ghi vào DynamoDB.

#### Bước 1 — Gửi 1 câu hỏi

Gõ 1 câu hỏi liên quan đến nội dung tài liệu đã upload rồi nhấn gửi.

![send question](/images/5-Workshop/5.5-Test-chatbot/send-question.png)

#### Bước 2 — Xem request API

Trong DevTools → Network, tìm request `POST .../chat` có header `Authorization: Bearer eyJ...`.

![chat request](/images/5-Workshop/5.5-Test-chatbot/chat-network.png)

{{% notice tip %}}
**Kiểm thử bảo mật (tuỳ chọn)**: copy request dưới dạng `curl`, sửa 1 ký tự trong token, rồi gửi lại. Bạn sẽ nhận **401 Unauthorized** — chứng minh JWT authorizer của API Gateway đã xác thực token *trước khi* Lambda kịp chạy.
{{% /notice %}}

#### Bước 3 — Theo dõi Lambda được kích hoạt

Ngay sau khi gửi, chuyển sang tab **CloudWatch → `/aws/lambda/rag-chat`** và refresh — một log stream mới xuất hiện ngay lập tức.

![cloudwatch log stream](/images/5-Workshop/5.5-Test-chatbot/cloudwatch-log.png)

#### Bước 4 — Xác nhận bản ghi trong DynamoDB

Sau khi nhận được câu trả lời, vào **DynamoDB → `ChatHistory` → Explore items** để tìm bản ghi mới nhất, gồm `question`, `answer`, `sources`, `latencyMs`.

![dynamodb chathistory](/images/5-Workshop/5.5-Test-chatbot/dynamodb-chathistory.png)

{{% notice note %}}
Thích dùng dòng lệnh hơn? `npx tsx scripts/ask.ts "câu hỏi của bạn"` chạy đúng chuỗi 4→10 mà không cần UI, in thẳng câu trả lời + nguồn trích dẫn.
{{% /notice %}}
