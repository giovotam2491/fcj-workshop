---
title : "Cấu hình biến môi trường & Finalize"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3.3 </b> "
---

Sau khi đã có Knowledge Base, nối ID của nó vào backend rồi nạp code Lambda cuối cùng.

#### Bước 1 — Điền ID Knowledge Base

Mở lại `backend/.env` và dán 2 giá trị đã copy ở bước trước:

```bash
KB_ID=<Knowledge Base ID>
DATA_SOURCE_ID=<Data source ID>
```

![env kb](/images/5-Workshop/5.3-Deploy%20Backend/env-kb-id.png)

#### Bước 2 — Hoàn tất triển khai

```bash
npm run finalize
```

Lệnh này sẽ:
- build lại code TypeScript của Lambda,
- chạy `deploy-sdk.ts` để nạp code đã build vào cả 6 Lambda function,
- chạy `set-lambda-env.ts` để set biến môi trường (`KB_ID`, `DATA_SOURCE_ID`, `MODEL_ARN`, `DOCS_BUCKET`, ...) cho từng function.

![finalize output](/images/5-Workshop/5.3-Deploy%20Backend/finalize-output.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: mở **Lambda → Functions → `rag-chat`**:
- Tab **Code**: xem "Last modified" khớp với thời điểm bạn vừa chạy `finalize`.
- **Configuration → Environment variables**: xác nhận `KB_ID`, `DATA_SOURCE_ID`, `MODEL_ARN` đã có giá trị, không rỗng.
{{% /notice %}}

![lambda env vars](/images/5-Workshop/5.3-Deploy%20Backend/lambda-env-console.png)

#### Tham chiếu — danh sách script (`backend/scripts/`)

| Lệnh | Mục đích |
|---|---|
| `npm run provision` | Tạo toàn bộ hạ tầng AWS + seed + ghi `.env` |
| `npm run finalize` | Build + nạp code 6 Lambda + set biến môi trường |
| `npm run check` | Kiểm tra hạ tầng (bảng, seed, bucket, KB) |
| `npm run seed` | Seed lại Roles + user admin |
| `tsx scripts/inspect-fn.ts <tên-hàm>` | Xem biến môi trường + log mới nhất của 1 Lambda |
| `tsx scripts/scan-kb.ts` | Dò Knowledge Base đang ở region nào |
| `tsx scripts/fix-iam.ts` | Vá lại quyền IAM Bedrock cho Lambda role |

{{% notice tip %}}
Nếu Lambda vẫn trả về `"Hello from Lambda!"` nghĩa là còn code mẫu — chỉ cần chạy lại `npm run finalize`.
{{% /notice %}}
