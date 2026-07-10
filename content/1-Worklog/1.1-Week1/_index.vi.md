---
title: "Worklog Tuần 1"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Mục tiêu tuần 1:
* Khảo sát & Thiết kế kiến trúc tổng thể hệ thống RAG Chatbot Serverless trên AWS.
* Thiết lập tài khoản AWS và cấu hình bảo mật căn bản (Root MFA, IAM User Admin).
* Thiết lập AWS Budgets cảnh báo chi phí phòng ngừa phát sinh ngoài ý muốn khi chạy dịch vụ AI (Amazon Bedrock).
* Khởi tạo mã nguồn Frontend sử dụng React + Vite + TypeScript và tích hợp thư viện hoạt ảnh GSAP.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tham gia buổi kick-off dự án thực tập, nhận đề tài xây dựng RAG Chatbot Serverless. <br> - Đọc hiểu các nội quy, quy định viết báo cáo tuần và yêu cầu kỹ thuật của project. | 20/04/2026 | 20/04/2026 | Tài liệu hướng dẫn FCAJ |
| 3 | - Nghiên cứu lý thuyết về RAG (Retrieval-Augmented Generation). <br> - Phân tích kiến trúc serverless trên AWS: Cognito (Auth), API Gateway (API Entry), Lambda (Logic), DynamoDB (Chat History), Bedrock KB (RAG). <br> - Vẽ sơ đồ kiến trúc thô của hệ thống. | 21/04/2026 | 21/04/2026 | [RAG Architecture on AWS](https://aws.amazon.com/blogs/machine-learning/) |
| 4 | - Đăng ký tài khoản AWS Free Tier mới. <br> - Bật tính năng MFA (Multi-Factor Authentication) cho Root Account. <br> - Tạo IAM Group `AdminGroup` và IAM User `intern-admin` để thao tác hàng ngày. | 22/04/2026 | 22/04/2026 | [AWS Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| 5 | - Cấu hình AWS Budgets với giới hạn ngân sách cố định là $10/tháng. <br> - Thiết lập ngưỡng cảnh báo 80% ($8) cho chi phí thực tế (Actual) và chi phí dự báo (Forecasted). <br> - Liên kết cảnh báo với Amazon SNS để gửi thông báo trực tiếp qua Email cá nhân. | 23/04/2026 | 23/04/2026 | [AWS Budgets Guide](https://docs.aws.amazon.com/cost-management/latest/userguide/) |
| 6 | - Khởi tạo Frontend React sử dụng Vite và TypeScript. <br> - Cài đặt thư viện Tailwind CSS phục vụ layout và GSAP phục vụ các hiệu ứng động mượt mà. <br> - Khởi động dự án trên Localhost và kiểm tra cấu trúc thư mục. | 24/04/2026 | 24/04/2026 | [Vite Guide](https://vitejs.dev/) & [GSAP Docs](https://gsap.com/docs/) |

### Kiến thức thu được trong tuần:
* **Mô hình RAG (Retrieval-Augmented Generation):** Hiểu cách kết hợp giữa tìm kiếm ngữ cảnh dữ liệu nội bộ (Retrieval) và khả năng sinh văn bản của LLMs (Generation) giúp hạn chế lỗi ảo giác (hallucination).
* **Bảo mật IAM:** Hiểu nguyên tắc bảo mật tối thiểu, phân biệt tài khoản Root và IAM User. Cách áp dụng MFA để ngăn chặn việc lộ credentials.
* **AWS Budgets:** Nắm được cách thiết lập budget giám sát chi phí tự động trên AWS để không bao giờ bị bất ngờ bởi hóa đơn vào cuối tháng.
* **Cấu hình Vite + React + TS:** Cách tổ chức dự án Single Page Application hiện đại với TypeScript tăng độ an toàn cho code.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Không nhận được email cảnh báo thử nghiệm của AWS Budgets dù đã thiết lập ngưỡng cảnh báo.
* **Cách giải quyết:** Phát hiện ra Amazon SNS topic dùng để định tuyến thông báo yêu cầu người nhận phải xác nhận (Confirm Subscription) qua một email tự động được gửi từ AWS trước. Sau khi bấm vào link "Confirm subscription" trong hòm thư, email cảnh báo từ AWS Budgets đã hoạt động bình thường.

### Kết quả đạt được tuần 1:
* Bản vẽ thiết kế kiến trúc hệ thống RAG Chatbot Serverless được phê duyệt.
* Tài khoản AWS được cấu hình bảo mật đúng chuẩn (bật MFA cho root, sử dụng IAM user).
* Hệ thống AWS Budgets đã hoạt động và có khả năng cảnh báo chi phí.
* Khởi tạo thành công repo Frontend React + Vite + TypeScript chạy ổn định trên localhost.
