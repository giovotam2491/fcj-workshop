---
title: "Worklog Tuần 4"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu tuần 4:
* Tạo và cấu hình Amazon OpenSearch Serverless (AOSS) làm Vector Database cho hệ thống RAG.
* Thiết lập Amazon Bedrock Knowledge Base liên kết với mô hình Titan Embeddings V2.
* Cấu hình Data Source trỏ về S3 Bucket tài liệu và thực hiện đồng bộ (Sync) dữ liệu.
* Thực hiện kiểm thử tính năng truy vấn tài liệu với LLM Amazon Nova Lite trực tiếp trên AWS Console.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo một OpenSearch Serverless collection dạng `Vector search`. <br> - Cấu hình các policy đi kèm: Security Policy (Encryption & Network) và Data Access Policy. | 11/05/2026 | 11/05/2026 | [Amazon OpenSearch Serverless Guide](https://docs.aws.amazon.com/opensearch-service/) |
| 3 | - Tạo Amazon Bedrock Knowledge Base trên Console. <br> - Chọn mô hình embedding `Titan Multimodal Embeddings G1` hoặc `Titan Text Embeddings V2`. <br> - Kết nối KB với OpenSearch Serverless làm kho lưu trữ vector (Vector Index). | 12/05/2026 | 12/05/2026 | [Amazon Bedrock Knowledge Base Docs](https://docs.aws.amazon.com/bedrock/) |
| 4 | - Liên kết S3 Bucket tài liệu làm Data Source cho Knowledge Base. <br> - Cấu hình chiến lược phân mảnh dữ liệu mặc định (Default Chunking: 300 tokens, 20% overlap). | 13/05/2026 | 13/05/2026 | Tài liệu cá nhân |
| 5 | - Upload 5 file tài liệu nội bộ PDF đã soạn thảo ở tuần trước lên S3. <br> - Kích hoạt tác vụ đồng bộ (Ingestion Job) từ giao diện điều khiển (Console) để bắt đầu convert tài liệu thành vector và lưu vào OpenSearch. | 14/05/2026 | 14/05/2026 | Giao diện AWS Console |
| 6 | - Sử dụng tính năng "Test Knowledge Base" trên Console. <br> - Chọn mô hình LLM Amazon Nova Lite, đặt các câu hỏi liên quan đến tài liệu nội bộ để kiểm tra câu trả lời của RAG. <br> - Tổng hợp và viết worklog tuần 4. | 15/05/2026 | 15/05/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Amazon OpenSearch Serverless (AOSS):** Hiểu mô hình cơ sở dữ liệu vector serverless, cơ chế phân tách Network Policy (truy cập public/private endpoint) và Data Access Policy (quyền truy cập đọc/ghi dữ liệu index).
* **Quy trình hoạt động của Ingestion Job:** Hiểu rõ cách dịch vụ tự động tải file từ S3, chia nhỏ (chunking), gọi API Embedding để tạo vector, và lưu trữ vector vào AOSS.
* **Mô hình LLM Amazon Nova Lite:** Nắm được cách mô hình này xử lý thông tin truy xuất từ tài liệu để trả về câu trả lời tự nhiên nhất cho người dùng.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Quá trình đồng bộ dữ liệu (Sync Data Source) bị thất bại với lỗi `AccessDenied: Bedrock does not have permissions to access S3 or OpenSearch Serverless`.
* **Cách giải quyết:** Nguyên nhân do IAM Service Role tự động tạo cho Bedrock thiếu quyền truy cập. Đã khắc phục bằng cách:
  1. Cập nhật IAM Role của Bedrock KB để cấp quyền đọc (`s3:GetObject`, `s3:ListBucket`) đối với S3 bucket cụ thể.
  2. Truy cập vào OpenSearch Serverless Console, điều chỉnh Data Access Policy để thêm IAM Role của Bedrock KB vào danh sách Principal có quyền đọc/ghi dữ liệu (`aoss:WriteDocument`, `aoss:ReadDocument`, `aoss:CreateIndex`). Sau đó, chạy lại Ingestion Job thành công.

### Kết quả đạt được tuần 4:
* Tạo thành công cơ sở dữ liệu vector OpenSearch Serverless trên AWS.
* Thiết lập hoàn chỉnh RAG Data Pipeline từ S3 đến Bedrock Knowledge Base.
* Ingestion Job chạy thành công, đồng bộ hóa 5 tài liệu nội bộ mẫu thành vector.
* Chạy thử nghiệm thành công RAG trên Console, chatbot trả lời chính xác dựa theo tài liệu nội bộ.
