---
title : "Kiểm thử Chatbot"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5 </b> "
---

#### Tổng quan

Khi cả backend và frontend đã chạy, phần này sẽ đi qua **toàn bộ luồng hỏi-đáp** (bước 1–10) và **luồng nạp tài liệu** (bước A–H) trong sơ đồ kiến trúc, để bạn tận mắt thấy từng bước xảy ra thật — không chỉ tin vào sơ đồ.

![query flow](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

Mở sẵn 4 tab AWS Console (region **us-east-1**) trước khi bắt đầu:
1. **CloudWatch → Log groups** (`/aws/lambda/rag-chat`, `/aws/lambda/rag-ingest`, `/aws/lambda/rag-upload`)
2. **DynamoDB → Tables → Explore items** (bảng `ChatHistory`, `Documents`)
3. **S3 → bucket chứa tài liệu của bạn** (giá trị `DOCS_BUCKET`)
4. **Bedrock → Knowledge Bases → KB của bạn → Data source → Sync history**

#### Nội dung

- [Đăng nhập](5.5.1-Login/)
- [Đặt câu hỏi](5.5.2-Ask%20Question/)
- [Xem trích dẫn nguồn](5.5.3-View%20Citation/)
- [Lịch sử chat](5.5.4-Chat%20History/)
