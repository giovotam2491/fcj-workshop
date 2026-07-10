---
title: "Nhật ký công việc"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Trang này chứa nhật ký công việc (worklog) chi tiết trong suốt **12 tuần thực tập** của tôi dưới vai trò là Thực tập sinh Nghiên cứu Giải pháp AWS Cloud / RAG. 

Dự án nghiên cứu và triển khai thực tế có tên là: **"Hệ thống tra cứu tài liệu nội bộ công ty (RAG Chatbot)"** chạy hoàn toàn trên kiến trúc **AWS Serverless**. 

### Tổng quan Lộ trình Dự án 12 Tuần:

* **[Tuần 1](1.1-week1/):** Khảo sát & Thiết kế kiến trúc. Thiết lập AWS IAM bảo mật ban đầu, cấu hình AWS Budgets để kiểm soát chi phí của tài khoản thực tập sinh. Khởi tạo dự án Frontend React + Vite + TypeScript và tích hợp thư viện hoạt ảnh GSAP.
* **[Tuần 2](1.2-week2/):** Thiết kế ma trận quyền truy cập IAM nâng cao. Xây dựng giao diện chat tĩnh thô và nghiên cứu tích hợp hiệu ứng hoạt ảnh GSAP cho các tương tác người dùng.
* **[Tuần 3](1.3-week3/):** Bắt đầu viết Infrastructure as Code (IaC) bằng AWS CDK với TypeScript để tạo S3 Bucket làm kho chứa tài liệu. Tìm hiểu cơ chế RAG của Amazon Bedrock Knowledge Base & mô hình Titan Embeddings V2.
* **[Tuần 4](1.4-week4/):** Triển khai hoàn chỉnh Amazon Bedrock Knowledge Base, kết nối S3 Data Source và Vector Store (Amazon OpenSearch Serverless). Tiến hành chạy và thử nghiệm đồng bộ hóa (Sync) dữ liệu.
* **[Tuần 5](1.5-week5/):** Thiết kế cơ sở dữ liệu DynamoDB để lưu trữ lịch sử trò chuyện. Khởi tạo Amazon Cognito User Pool để xác thực người dùng. Viết các API Lambda đầu tiên (quản lý Session).
* **[Tuần 6](1.6-week6/):** Thiết kế API Gateway REST API. Tích hợp Cognito User Pool Authorizer để bảo vệ API và triển khai các Lambda xử lý CRUD cho tin nhắn và Session.
* **[Tuần 7](1.7-week7/):** Kết nối Backend với RAG Pipeline. Viết Lambda function trung tâm gọi API `RetrieveAndGenerate` của Amazon Bedrock Agent Runtime sử dụng mô hình LLM Amazon Nova Lite.
* **[Tuần 8](1.8-week8/):** Tinh chỉnh chất lượng phản hồi RAG qua Custom Prompt Template nhằm tránh hiện tượng hallucination (trả lời bừa). Quản lý lịch sử hội thoại ngắn hạn (Conversation State).
* **[Tuần 9](1.9-week9/):** Tích hợp Frontend React với Amazon Cognito SDK để xử lý Đăng ký / Đăng nhập / OTP. Cấu hình Axios Interceptor để truyền JWT token trong API Header.
* **[Tuần 10](1.10-week10/):** Hoàn thiện UI/UX Chatbot (Markdown render, citation sources) kết hợp hiệu ứng mượt mà từ GSAP. Deploy Frontend lên S3 Static Web Hosting + CloudFront CDN + bảo mật qua AWS WAF.
* **[Tuần 11](1.11-week11/):** Thiết lập giám sát thông qua CloudWatch Logs & Metrics. Thực hiện đánh giá chất lượng RAG (RAG Evaluation) và rà soát lại các IAM Roles theo nguyên tắc Least Privilege (quyền hạn tối thiểu).
* **[Tuần 12](1.12-week12/):** Phân tích chi phí tài khoản thực tập thông qua AWS Budgets. Tối ưu thời gian phản hồi (giảm Cold Start của Lambda còn < 600ms, bật cache CloudFront). Đóng gói và viết tài liệu bàn giao dự án.