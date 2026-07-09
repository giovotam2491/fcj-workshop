---
title : "Cấu hình biến môi trường"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

`npm run provision` (mục 5.3.1) đã tự sinh `frontend/.env` sẵn cho bạn. Ở bước này bạn chỉ cần kiểm tra lại xem đã trỏ đúng tài nguyên chưa.

#### Bước 1 — Mở `frontend/.env`

Nội dung sẽ có dạng (giá trị khác nhau tuỳ tài khoản của bạn):

```bash
VITE_API_BASE_URL=https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com
VITE_COGNITO_USER_POOL_ID=us-east-1_xxxxxxxxx
VITE_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
VITE_AWS_REGION=us-east-1
```

![frontend env](/images/5-Workshop/5.4-Deploy-frontend/frontend-env.png)

#### Bước 2 — Đối chiếu với output của provision

So sánh từng giá trị với **API endpoint**, **User Pool ID**, **App Client ID** đã được `npm run provision` in ra ở mục 5.3.1. Chúng phải khớp chính xác.

{{% notice warning %}}
Nếu sau này bạn deploy frontend qua domain riêng (CloudFront/S3), nhớ thêm domain đó vào **CORS allow-list** của API Gateway và của S3 bucket chứa tài liệu — nếu không request từ trình duyệt sẽ bị chặn bởi CORS.
{{% /notice %}}
