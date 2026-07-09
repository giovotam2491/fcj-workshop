---
title : "Provision hạ tầng"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.3.1 </b> "
---

#### Bước 1 — Clone project

```bash
git clone https://github.com/vanphucpham2k4/rag-chatbot-aws-serverless.git
cd rag-chatbot-aws-serverless
```

#### Bước 2 — Cấu hình biến môi trường backend

```bash
cd backend
npm install
cp .env.example .env
```

Mở `backend/.env` và điền:

```bash
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=<access key của bạn>
AWS_SECRET_ACCESS_KEY=<secret key của bạn>

# Tên bucket S3 phải DUY NHẤT TOÀN CẦU
DOCS_BUCKET=rag-docs-<tên-riêng-duy-nhất>

# Tài khoản admin sẽ được tạo trong Cognito + seed vào DynamoDB
SEED_ADMIN_EMAIL=admin@example.com
TEST_PASSWORD=RagTest#2026

# ĐỂ TRỐNG — provision sẽ tự sinh; KB_ID điền ở bước sau
MODEL_ARN=
KB_ID=
DATA_SOURCE_ID=
```

{{% notice warning %}}
Không bao giờ commit `.env` lên Git (đã có trong `.gitignore`). `.env.example` chỉ chứa placeholder.
{{% /notice %}}

![env file](/images/5-Workshop/5.3-Deploy%20Backend/env-backend.png)

#### Bước 3 — Chạy script provision

```bash
npm run provision
```

`provision.ts` sẽ tạo (idempotent — chạy lại vẫn an toàn):
- **DynamoDB**: 4 bảng (Users, Roles, Documents, ChatHistory) + GSI + TTL.
- **S3**: bucket data source + cấu hình CORS (cho phép PUT presigned).
- **Cognito**: User Pool + App Client (SPA) + user admin (đặt mật khẩu vĩnh viễn).
- **IAM**: role thực thi cho Lambda (DynamoDB, S3, Bedrock, CloudWatch Logs).
- **Lambda**: 6 hàm (build code, tạo hàm, set biến môi trường, timeout 30s/256MB).
- **API Gateway** HTTP API + **JWT authorizer** + 5 route + tích hợp.
- **S3 → Lambda trigger** (ingestion) + **seed** roles/admin.

![provision output](/images/5-Workshop/5.3-Deploy%20Backend/provision-output.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: đối chiếu lại những gì script vừa tạo, không cần chạy thêm lệnh nào:
- **DynamoDB → Tables**: 4 bảng `Users`, `Roles`, `Documents`, `ChatHistory` trạng thái **Active**.
- **S3 → Buckets**: bucket `DOCS_BUCKET` của bạn đã tồn tại.
- **Lambda → Functions**: 6 hàm có tiền tố `rag-` (vd `rag-chat`, `rag-ingest`).
- **API Gateway → APIs**: `rag-chatbot-api` (HTTP API) với 5 route.
- **Cognito → User pools**: `rag-chatbot-pool`, có 1 user trùng với `SEED_ADMIN_EMAIL`.
{{% /notice %}}

![console check provision](/images/5-Workshop/5.3-Deploy%20Backend/console-check-provision.png)
