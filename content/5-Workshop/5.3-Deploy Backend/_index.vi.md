---
title : "Triển khai Backend"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.3. </b> "
---

#### Tổng quan

Trong phần này, bạn sẽ dựng **toàn bộ hạ tầng backend** của RAG chatbot trên tài khoản AWS của mình — bảng DynamoDB, S3 bucket chứa tài liệu, Cognito User Pool, IAM role, 6 Lambda function và API Gateway HTTP API — chỉ bằng **một script tự động**. Sau đó bạn sẽ tạo **Amazon Bedrock Knowledge Base** (bước duy nhất phải làm thủ công trên Console) và hoàn tất việc kết nối nó với các Lambda function.

![architecture](/images/5-Workshop/5.1-Workshop-overview/architecture.png)

#### Nội dung

- [Provision hạ tầng & clone project](5.3.1-deploy-lambda/)
- [Tạo Bedrock Knowledge Base](5.3.2-%20Configure%20API%20Gateway/)
- [Cấu hình biến môi trường & finalize](5.3.3-Configure%20Environment/)
- [Kiểm tra backend](5.3.4-Verify%20Backend/)
