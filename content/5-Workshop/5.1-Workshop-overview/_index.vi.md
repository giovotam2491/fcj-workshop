---
title : "Giới thiệu"
date : 2024-01-01 
weight : 1
chapter : false
pre : " <b> 5.1. </b> "
---

#### Giới thiệu về RAG và AWS Serverless

+ **RAG (Retrieval-Augmented Generation)** là kỹ thuật tối ưu hóa câu hỏi-đáp của Mô hình ngôn ngữ lớn (LLM) bằng cách truy hồi thông tin từ kho dữ liệu đáng tin cậy bên ngoài (như tài liệu nội bộ) trước khi sinh câu trả lời. Điều này giúp loại bỏ hiện tượng ảo giác (hallucination) và bảo vệ dữ liệu nhạy cảm của tổ chức.
+ **AWS Serverless** cung cấp một môi trường điện toán đám mây linh hoạt, không cần quản lý máy chủ vật lý, tự động mở rộng theo tải thực tế và trả tiền theo mức độ sử dụng (pay-as-you-go) giúp tối ưu tối đa chi phí vận hành.

#### Tổng quan về workshop
Trong workshop này, chúng ta sẽ xây dựng một **Hệ thống Chatbot Hỏi – Đáp Tài liệu Nội bộ** chạy hoàn toàn trên kiến trúc Serverless của AWS. 

Giao diện người dùng được phát triển bằng React (Vite) và tích hợp các thư viện hiệu ứng hình ảnh sống động (phong cách 3D Glassmorphism), được bảo mật xác thực bởi Amazon Cognito. Trái tim của hệ thống là Amazon Bedrock Knowledge Base liên kết với S3 Vectors để tự động thực hiện luồng RAG (parse, chunk, embed bằng Titan Text V2 và sinh câu trả lời bằng Nova Lite).

Bài Lab bao gồm các phần chính sau:
1. **Chuẩn bị môi trường:** Thiết lập tài khoản AWS, cài đặt Node.js và cấu hình IAM.
2. **Cơ sở dữ liệu & Xác thực:** Tạo cơ sở dữ liệu Amazon DynamoDB để lưu trữ thông tin hệ thống và cấu hình Amazon Cognito User Pool để quản lý quyền người dùng.
3. **Lõi Tri thức AI (Bedrock KB):** Cấu hình Amazon Bedrock Knowledge Base, kết nối S3 Bucket dữ liệu nguồn và thiết lập cơ sở dữ liệu vector gọn nhẹ trên Amazon S3 Vectors.
4. **Backend API & Xử lý Serverless:** Triển khai các hàm AWS Lambda (Node.js/TypeScript) đứng sau Amazon API Gateway để xử lý logic dịch vụ RAG và kiểm tra quyền truy cập (RBAC).
5. **Giao diện Frontend:** Liên kết giao diện React với Backend API, kiểm thử luồng RAG trực quan (Hỏi-đáp có trích dẫn nguồn, lịch sử hội thoại đa lượt, và bảng điều khiển quản trị tải tài liệu).
6. **Giám sát & Tối ưu hóa:** Cài đặt giám sát hệ thống qua CloudWatch và cấu hình AWS Budgets để thiết lập cảnh báo chống vượt ngân sách.

![overview](/images/2-Proposal/architecture.png)
