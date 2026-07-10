---
title: "Worklog Tuần 12"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12:
* Tối ưu hóa thời gian phản hồi tổng thể của hệ thống (E2E Latency) và giảm thời gian khởi động lạnh (Cold Start) của Lambda.
* Phân tích, tổng kết chi phí thực tế phát sinh thông qua AWS Cost Explorer so với AWS Budgets đã lập.
* Hoàn thiện bộ tài liệu bàn giao dự án bao gồm tài liệu kiến trúc, hướng dẫn triển khai IaC và tài liệu API.
* Biên soạn slide demo sản phẩm và rà soát toàn bộ báo cáo worklog từ Tuần 1 đến Tuần 12 để chuẩn bị nộp bài.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tối ưu hóa dung lượng đóng gói của các Lambda function bằng cách cấu hình loại trừ (exclude) thư viện AWS SDK v3 trong esbuild (do runtime Lambda đã có sẵn). <br> - Tăng cấu hình bộ nhớ RAM của Lambda xử lý chat để AWS cấp thêm tài nguyên CPU tương ứng. | 06/07/2026 | 06/07/2026 | [AWS Lambda Performance Tuning](https://docs.aws.amazon.com/lambda/) |
| 3 | - Bật cấu hình nén Gzip/Brotli trên CloudFront Distribution. <br> - Thiết lập cache-control headers cho các file tĩnh ở S3 để tối ưu tốc độ tải trang Frontend. | 07/07/2026 | 07/07/2026 | [CloudFront Caching Guide](https://docs.aws.amazon.com/AmazonCloudFront/) |
| 4 | - Sử dụng AWS Cost Explorer để truy vấn chi tiết chi phí tích lũy sau 12 tuần chạy thử nghiệm. <br> - Kiểm tra tình trạng ngân sách trên AWS Budgets để xác nhận không phát sinh chi phí vượt tầm kiểm soát. | 08/07/2026 | 08/07/2026 | AWS Cost Management Console |
| 5 | - Soạn thảo tài liệu kỹ thuật hướng dẫn chạy dự án (README.md cho cả Frontend và Backend). <br> - Vẽ sơ đồ kiến trúc hoàn thiện và chụp màn hình kết quả kiểm thử. | 09/07/2026 | 09/07/2026 | Tài liệu dự án |
| 6 | - Rà soát lần cuối toàn bộ định dạng, liên kết và hình ảnh của trang báo cáo Nhật ký công việc. <br> - Hoàn tất slide thuyết trình và nộp báo cáo dự án thực tập. | 10/07/2026 | 10/07/2026 | Hệ thống nộp bài FCAJ |

### Kiến thức thu được trong tuần:
* **Tối ưu Cold Start cho Lambda:** Hiểu mối tương quan giữa dung lượng file zip code của Lambda, cấu hình RAM được phân bổ, và tốc độ khởi tạo ban đầu của môi trường thực thi (execution environment).
* **Quản lý chi phí Cloud:** Nắm vững cách phân tích chi tiết chi phí dịch vụ qua Cost Explorer. Thấy rõ sự tối ưu chi phí của mô hình Serverless: chi phí chỉ phát sinh khi có request thực tế.
* **Tư duy đóng gói sản phẩm:** Học cách viết tài liệu kỹ thuật chi tiết giúp người tiếp quản sau có thể tự deploy và vận hành hệ thống một cách dễ dàng chỉ bằng vài lệnh cơ bản.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Thời gian khởi động lạnh (Cold Start) của Lambda xử lý chat (`chatWithKB`) ban đầu lên tới gần 3.5 giây, khiến người dùng cảm thấy ứng dụng bị đơ trong lượt chat đầu tiên.
* **Cách giải quyết:** Đã thực hiện hai bước tối ưu hóa quan trọng:
  1. Điều chỉnh cấu hình đóng gói `esbuild` trong CDK để đưa `@aws-sdk/client-bedrock-agent-runtime` và các SDK khác vào danh sách `external` (loại bỏ khỏi bundle code zip gửi lên vì AWS runtime đã có sẵn). Việc này giúp giảm dung lượng code zip từ 10MB xuống còn 780KB.
  2. Tăng cấu hình RAM của hàm Lambda xử lý chat từ 128MB lên 512MB. AWS cấp CPU tỷ lệ thuận với dung lượng RAM, giúp tăng tốc độ giải nén code và khởi tạo container.
  Kết quả: Thời gian Cold Start giảm mạnh xuống còn dưới 600ms, mang lại trải nghiệm cực kỳ mượt mà.

### Kết quả đạt được tuần 12:
* Tối ưu hóa hiệu năng toàn diện (Cold Start giảm từ 3.5s xuống < 600ms, cache CloudFront hoạt động tốt).
* Tổng chi phí thực tế cho toàn bộ dự án thử nghiệm 12 tuần chỉ tốn $4.2 USD (nằm an toàn trong ngân sách $10 đề ra).
* Hoàn thành slide demo sản phẩm và bộ tài liệu hướng dẫn vận hành kỹ thuật.
* Hoàn tất và nộp toàn bộ báo cáo kết quả thực tập (worklog, proposal, self-evaluation).
