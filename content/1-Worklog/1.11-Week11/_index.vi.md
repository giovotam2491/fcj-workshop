---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11:
* Thiết lập hệ thống giám sát hoạt động bằng Amazon CloudWatch (Logs, Metrics và Dashboards).
* Đánh giá chất lượng RAG (RAG Evaluation) dựa trên độ trung thực thông tin (groundedness) và giảm thiểu lỗi ảo giác.
* Thực hiện rà soát bảo mật bảo đảm áp dụng nguyên tắc phân quyền tối thiểu (Least Privilege) cho tất cả IAM Roles của Lambda.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo một CloudWatch Dashboard trung tâm. <br> - Thêm các widget theo dõi metrics quan trọng: Số lượng Request đến API Gateway, lỗi HTTP 4XX/5XX, thời gian chạy của Lambda và lỗi Lambda. | 29/06/2026 | 29/06/2026 | [Amazon CloudWatch Guide](https://docs.aws.amazon.com/AmazonCloudWatch/) |
| 3 | - Sử dụng tính năng CloudWatch Logs Insights để viết các câu lệnh truy vấn log lỗi của các hàm Lambda và thống kê thời gian chạy trung bình (P99 Duration). | 30/06/2026 | 30/06/2026 | [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/) |
| 4 | - Xây dựng chương trình đánh giá chất lượng câu trả lời RAG dựa trên 50 câu hỏi mẫu thực tế. <br> - Đánh giá thủ công kết hợp sử dụng LLM chấm điểm độ liên quan (Answer Relevance) và mức độ dựa trên tài liệu (Groundedness Score). | 01/07/2026 | 01/07/2026 | RAG Triangulation Methods |
| 5 | - Rà soát lại tất cả các IAM policies được tạo bởi CDK. <br> - Loại bỏ các ký tự wildcard `*` trong phần `Resource` của các Lambda Role, thay thế bằng ARN chính xác của DynamoDB table và S3 bucket. | 02/07/2026 | 02/07/2026 | [IAM Least Privilege Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| 6 | - Kiểm tra và tối ưu hóa các Lambda Function execution permissions, loại bỏ các quyền đọc/ghi thừa. <br> - Tổng hợp và viết worklog tuần 11. | 03/07/2026 | 03/07/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Giám sát Serverless:** Cách cấu hình CloudWatch để chủ động phát hiện lỗi hệ thống thay vì đợi người dùng báo cáo.
* **Đánh giá RAG (RAG Evaluation):** Nắm vững hai chỉ số cốt lõi: Faithfulness (câu trả lời có hoàn toàn dựa vào ngữ cảnh tài liệu hay không) và Answer Relevance (câu trả lời có đi đúng vào trọng tâm câu hỏi của người dùng hay không).
* **IAM Least Privilege:** Ý nghĩa bảo mật của việc giới hạn quyền hạn chi tiết đến từng ARN của tài nguyên thay vì cho phép toàn quyền truy cập.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Khi chạy script đánh giá chất lượng tự động gửi 50 câu hỏi đồng thời đến API để test hiệu năng hệ thống và độ chính xác, Amazon Bedrock liên tục ném lỗi `ThrottlingException: Rate exceeded for Nova Lite model` khiến script bị dừng đột ngột.
* **Cách giải quyết:** Lỗi do vượt quá hạn mức Transactions Per Second (TPS) mặc định của model Bedrock Nova Lite ở tài khoản Intern Sandbox. Đã xử lý bằng cách:
  1. Thêm cấu hình giới hạn số lượng request chạy song song tối đa là 2 (concurrency limit = 2) trong script kiểm thử.
  2. Bổ sung cơ chế retry-backoff tự động sử dụng thuật toán exponential backoff trong mã nguồn test để khi gặp lỗi 429 (Too Many Requests), script tự động đợi một khoảng thời gian tăng dần trước khi gửi lại request. Kết quả là script test 50 câu hỏi đã chạy hoàn thành mà không gặp lỗi.

### Kết quả đạt được tuần 11:
* Thiết lập thành công CloudWatch Dashboard giám sát hệ thống thời gian thực.
* Đánh giá chất lượng RAG đạt kết quả tốt với điểm số Groundedness trung bình đạt ~92%.
* Hệ thống được thắt chặt bảo mật bằng cách rà soát và cấu hình Least Privilege cho toàn bộ IAM Roles.
