---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Chatbot Hỏi-Đáp Tài Liệu Nội Bộ — RAG trên AWS Serverless

#### Tổng quan

Trong workshop thực hành này, bạn sẽ triển khai một **chatbot RAG (Retrieval-Augmented Generation)** hoàn chỉnh, chạy hoàn toàn trên các dịch vụ **AWS Serverless**. Hệ thống cho phép người dùng nội bộ tải lên tài liệu (PDF/TXT) và đặt câu hỏi bằng ngôn ngữ tự nhiên, nhận về câu trả lời bám sát nội dung tài liệu — kèm trích dẫn nguồn.

Kiến trúc gồm 3 tầng:
+ **Giao diện / Edge** — CloudFront + S3 riêng tư (React SPA) và Amazon Cognito để xác thực người dùng.
+ **API & Xử lý (Serverless)** — API Gateway (HTTP API) với JWT authorizer, AWS Lambda (Node.js/TypeScript), và DynamoDB lưu dữ liệu ứng dụng.
+ **AI — Amazon Bedrock** — Knowledge Base kết hợp **S3 Vectors**, **Titan Text Embeddings V2**, và **Amazon Nova Lite** để truy hồi và sinh câu trả lời.

Kết thúc workshop, bạn sẽ dựng xong toàn bộ hệ thống trên tài khoản AWS của riêng mình, triển khai frontend, kiểm thử luồng chat từ đầu đến cuối, và dọn dẹp toàn bộ tài nguyên để tránh phát sinh chi phí.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Triển khai Backend](5.3-Deploy%20Backend/)
4. [Triển khai Frontend](5.4-Deploy-frontend/)
5. [Kiểm thử Chatbot](5.5-Test-chatbot/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)
