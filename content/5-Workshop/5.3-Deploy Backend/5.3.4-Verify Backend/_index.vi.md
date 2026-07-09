---
title : "Kiểm tra Backend"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.3.4 </b> "
---

Trước khi chuyển sang frontend, hãy xác nhận backend đã hoạt động hoàn chỉnh.

#### Bước 1 — Kiểm tra sức khoẻ hạ tầng

```bash
npm run check
```

Kỳ vọng cả 4 mục đều PASS: **bảng DynamoDB**, **dữ liệu seed**, **S3 bucket**, **Knowledge Base**.

![check pass](/images/5-Workshop/5.3-Deploy%20Backend/check-pass.png)

#### Bước 2 — Kiểm tra nhanh API

```bash
npx tsx scripts/token-test.ts
```

Script đăng nhập bằng tài khoản admin đã seed, sau đó gọi `GET /documents` (kỳ vọng `200`), `POST /upload-url` (kỳ vọng `201`), và `POST /chat` (kỳ vọng `200`).

![token test](/images/5-Workshop/5.3-Deploy%20Backend/token-test.png)

#### Bước 3 — Kiểm tra RAG đầu-cuối

```bash
npx tsx scripts/e2e-rag.ts
```

Script tự động upload 1 tài liệu mẫu, đợi Bedrock nạp (ingest) xong, đặt câu hỏi liên quan, và kiểm tra câu trả lời có **grounded** (trích dẫn đúng tài liệu vừa upload).

![e2e rag](/images/5-Workshop/5.3-Deploy%20Backend/e2e-rag.png)

Khi cả 3 bước đều PASS, backend đã sẵn sàng — tiếp tục sang **[5.4 Triển khai Frontend](../../5.4-Deploy-frontend/)**.
