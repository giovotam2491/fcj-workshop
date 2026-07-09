---
title : "Dọn dẹp tài nguyên"
date : 2024-01-01
weight : 6
chapter : false
pre : " <b> 5.6. </b> "
---

#### Tổng quan

Chúc mừng bạn đã hoàn thành workshop! Bạn đã dựng xong một RAG chatbot serverless từ đầu đến cuối: DynamoDB, S3, Cognito, IAM, Lambda, API Gateway, và Bedrock Knowledge Base với S3 Vectors.

Để tránh phát sinh chi phí, hãy xoá toàn bộ tài nguyên đã tạo trong workshop này. **Không có script dọn dẹp tự động** — tất cả các bước xoá dưới đây thực hiện thủ công trên AWS Console, theo đúng thứ tự khuyến nghị.

{{% notice warning %}}
Xoá tài nguyên theo đúng thứ tự sau để tránh lỗi phụ thuộc: **Lambda → API Gateway → DynamoDB → S3 → Bedrock Knowledge Base → Cognito → IAM role → (tuỳ chọn) CloudWatch Log groups**.
{{% /notice %}}

#### Nội dung

- [Xóa AWS Lambda](5.6.1-Delete%20Lambda/)
- [Xóa API Gateway](5.6.2-Delete%20API%20Gateway/)
- [Xóa Bedrock Knowledge Base](5.6.3-Delete%20Bedrock%20Knowledge%20Base/)
- [Xóa bảng DynamoDB](5.6.4-Delete%20DynamoDB/)
- [Xóa S3 bucket](5.6.5-Delete%20S3%20Bucket/)
- [Xóa Cognito User Pool](5.6.6-Delete%20Cognito/)
