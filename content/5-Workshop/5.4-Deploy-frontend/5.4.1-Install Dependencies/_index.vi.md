---
title : "Cài đặt Dependencies"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

Frontend nằm trong thư mục `frontend/` của cùng repo bạn đã clone ở [5.3.1](../../5.3-Deploy%20Backend/5.3.1-deploy-lambda/).

#### Bước 1 — Vào thư mục frontend

```bash
cd rag-chatbot-aws-serverless/frontend
```

#### Bước 2 — Cài dependencies

```bash
npm install
```

![npm install](/images/5-Workshop/5.4-Deploy-frontend/npm-install.png)

{{% notice note %}}
Không cần cấu hình Cognito/API thủ công ở đây — `frontend/.env` đã được `npm run provision` (ở bước backend) tự động sinh và điền sẵn (`VITE_API_BASE_URL`, `VITE_COGNITO_USER_POOL_ID`, `VITE_COGNITO_CLIENT_ID`). Bạn sẽ kiểm tra lại các giá trị này ở bước tiếp theo.
{{% /notice %}}

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: mở **Cognito → User pools → `rag-chatbot-pool` → Applications → App clients**, xác nhận **Client ID** hiển thị ở đó khớp với `VITE_COGNITO_CLIENT_ID` trong `frontend/.env`.
{{% /notice %}}

![cognito app client](/images/5-Workshop/5.4-Deploy-frontend/cognito-app-client.png)
