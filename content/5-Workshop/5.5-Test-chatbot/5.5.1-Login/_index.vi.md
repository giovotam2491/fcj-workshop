---
title : "Đăng nhập"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

Bước này minh hoạ **luồng xác thực** (bước 1–3 trong sơ đồ hỏi-đáp): CloudFront/S3 phục vụ app, Cognito cấp JWT, và frontend gọi API kèm token đó.

#### Bước 1 — Mở app và đăng nhập

Mở URL của app (local `http://localhost:5173` hoặc domain CloudFront) và đăng nhập bằng tài khoản admin đã được `npm run provision` in ra:

```
Email: <SEED_ADMIN_EMAIL>
Password: <TEST_PASSWORD>
```

![login form](/images/5-Workshop/5.5-Test-chatbot/login-form.png)

#### Bước 2 — Xem request tới Cognito

Mở DevTools (F12) → tab **Network** **trước khi** đăng nhập. Sau khi submit, tìm request tới `cognito-idp.us-east-1.amazonaws.com` (`InitiateAuth`). Response chứa `IdToken` (JWT).

![cognito request](/images/5-Workshop/5.5-Test-chatbot/cognito-network.png)

{{% notice tip %}}
Copy giá trị `IdToken` và dán vào [jwt.io](https://jwt.io) để xem các claim đã decode (`sub`, `email`, thời hạn) — bằng chứng cho thấy Cognito đã thực sự cấp token.
{{% /notice %}}

![jwt decoded](/images/5-Workshop/5.5-Test-chatbot/jwt-io.png)

#### Bước 3 — Xác nhận vào được màn hình chính

Sau khi đăng nhập thành công, bạn sẽ được chuyển đến trang dashboard chat/tài liệu chính.

![chat home](/images/5-Workshop/5.5-Test-chatbot/chat-home.png)
