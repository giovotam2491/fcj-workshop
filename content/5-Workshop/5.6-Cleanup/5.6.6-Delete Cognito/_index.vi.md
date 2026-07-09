---
title : "Xóa Cognito & IAM Role"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6.6. </b> "
---

#### Xóa Cognito User Pool

1. Mở **Amazon Cognito Console → User pools**.
2. Chọn pool đã tạo cho dự án (vd `rag-chatbot-pool`).
3. Nhấn **Delete**, nhập tên pool để xác nhận, và nhấn **Delete**.

![delete cognito](/images/5-Workshop/5.6-Cleanup/delete-cognito.png)

#### Xóa IAM role của Lambda

1. Mở **IAM Console → Roles**.
2. Tìm role thực thi Lambda (vd `rag-lambda-role`).
3. Chọn role, nhấn **Delete**, và xác nhận.

![delete iam role](/images/5-Workshop/5.6-Cleanup/delete-iam-role.png)

#### (Tuỳ chọn) Xóa CloudWatch Log groups

Để tránh phát sinh phí lưu trữ nhỏ lẻ, hãy xóa luôn các log group dưới **CloudWatch → Log groups** có tên `/aws/lambda/rag-*`.

![delete log groups](/images/5-Workshop/5.6-Cleanup/delete-log-groups.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh **Cognito → User pools** (pool đã biến mất), **IAM → Roles** (tìm `rag-`, không còn kết quả), và **CloudWatch → Log groups** (tìm `/aws/lambda/rag-`, không còn kết quả nếu đã làm bước tuỳ chọn). Có thể chạy thêm `aws sts get-caller-identity` để xác nhận vẫn đang dùng IAM user đã scoped, không phải root.
{{% /notice %}}

#### Kết quả

Toàn bộ tài nguyên còn lại — Cognito User Pool, IAM role của Lambda, và (tuỳ chọn) CloudWatch log group — đã được xóa. Tài khoản AWS của bạn giờ sạch mọi tài nguyên đã tạo trong workshop này.
