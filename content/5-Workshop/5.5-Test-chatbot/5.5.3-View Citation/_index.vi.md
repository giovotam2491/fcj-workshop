---
title : "Xem trích dẫn nguồn"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.5.3 </b> "
---

Bước này minh hoạ bước **8–10**: truy hồi từ S3 Vectors và sinh câu trả lời bằng Nova Lite — phần cốt lõi của RAG.

#### Bước 1 — Đọc câu trả lời trên UI

Câu trả lời chat nên hiển thị **trích dẫn nguồn** (tên tài liệu / đoạn trích) kèm theo nội dung sinh ra.

![citation ui](/images/5-Workshop/5.5-Test-chatbot/citation-ui.png)

#### Bước 2 — Xác nhận trong log Lambda

Trong **CloudWatch → `/aws/lambda/rag-chat`**, mở log stream mới nhất, tìm dòng `REPORT RequestId: ...` ứng với request chat vừa gửi. Nhìn vào **Duration**: 1 lần đọc/ghi DynamoDB thuần thường xong dưới 100 ms, nhưng request này phải mất **vài giây** (vd `Duration: 4397.08 ms`) — khoảng thời gian đó chính là lúc Lambda đang chờ Bedrock truy hồi Knowledge Base + Nova Lite sinh câu trả lời qua mạng, bằng chứng thật có 1 lệnh gọi AI bên ngoài, không phải trả lời dựng sẵn tức thì.

![retrieve and generate log](/images/5-Workshop/5.5-Test-chatbot/retrieve-generate-log.png)

Bằng chứng trực tiếp, rõ ràng nhất rằng `RetrieveAndGenerate` đã chạy — và nó trả về gì — nằm ở nội dung response tại Bước 3 bên dưới.

#### Bước 3 — Xem JSON response gốc

Mở DevTools → Network → body response của `/chat`. Response phải có cả `answer` và mảng `sources` chứa các tài liệu được truy hồi.

![response json](/images/5-Workshop/5.5-Test-chatbot/response-json.png)