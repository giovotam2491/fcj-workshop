---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu tuần 10:

* Rà soát toàn bộ project và kiểm tra các chức năng chính hoạt động đúng.
* Bổ sung monitoring cho ứng dụng serverless bằng CloudWatch.
* Rà soát bảo mật (IAM, S3) theo best practice và kiểm soát chi phí.
* Chuẩn bị và viết phần Clean-up để tránh phát sinh chi phí sau khi kết thúc project.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà soát toàn bộ project end-to-end, kiểm tra từng chức năng chính (Lambda, DynamoDB, API Gateway) <br> - Test lại các luồng CRUD và xác thực để đảm bảo hoạt động ổn định <br> - Ghi nhận và sửa các lỗi còn tồn đọng | 22/06/2026 | 22/06/2026 |  |
| 3 | - Tìm hiểu Monitoring Serverless Applications: xem log và metric của Lambda trên CloudWatch <br> - Học cách đọc log group, log stream và các metric quan trọng (Invocations, Errors, Duration, Throttles) <br> - Thực hành debug lỗi Lambda dựa trên log | 23/06/2026 | 23/06/2026 | <https://000085.awsstudygroup.com/> |
| 4 | - Rà soát IAM Role/Policy của từng service theo nguyên tắc least privilege <br> - Kiểm tra và thu hẹp các quyền cấp thừa, loại bỏ quyền không cần thiết <br> - Đảm bảo mỗi Lambda/service chỉ có đúng quyền tối thiểu để hoạt động | 24/06/2026 | 24/06/2026 |  |
| 5 | - Xem lại chi phí sử dụng qua AWS Budgets và Cost Explorer, xác định dịch vụ tốn chi phí nhất <br> - Tìm hiểu S3 security best practices: Block Public Access, mã hóa (SSE), bucket policy tối thiểu <br> - Rà soát cấu hình bảo mật của bucket trong project | 25/06/2026 | 25/06/2026 |  |
| 6 | - Viết phần Clean-up cho báo cáo: liệt kê và hướng dẫn xóa/tắt các resource không còn dùng <br> - Xác định thứ tự xóa hợp lý để tránh lỗi phụ thuộc giữa các tài nguyên <br> - Tổng hợp và viết worklog tuần 10 | 26/06/2026 | 26/06/2026 |  |

### Kiến thức thu được trong tuần:

* **Kiểm thử hệ thống:** biết cách rà soát end-to-end và test lại các chức năng chính để đảm bảo tính ổn định trước khi hoàn thiện.
* **Monitoring serverless:** hiểu cách đọc log group/log stream và các metric quan trọng của Lambda (Invocations, Errors, Duration, Throttles) để debug.
* **Bảo mật IAM:** nắm được cách rà soát và thu hẹp quyền theo nguyên tắc least privilege, giảm rủi ro bảo mật.
* **Bảo mật S3:** hiểu các best practice như Block Public Access, mã hóa dữ liệu và bucket policy tối thiểu.
* **Quản lý chi phí & Clean-up:** biết cách theo dõi chi phí qua Budgets/Cost Explorer và quy trình dọn dẹp tài nguyên đúng thứ tự để tránh phát sinh chi phí.

### Khó khăn trong tuần:

* Chưa quen đọc log Lambda trên CloudWatch để tìm nguyên nhân lỗi.

### Kết quả đạt được tuần 10:

* Rà soát và xác nhận các chức năng chính của project (Lambda, DynamoDB, API) hoạt động đúng.
* Project có phần monitoring cơ bản qua CloudWatch.
* Hoàn thành rà soát bảo mật (IAM least privilege, S3 best practices) và chi phí.
* Viết được hướng dẫn Clean-up để tránh phát sinh chi phí sau khi kết thúc project.
