---
title: "Worklog Tuần 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---


### Mục tiêu tuần 5:

* Nắm được cách giám sát tài nguyên và ứng dụng bằng Amazon CloudWatch (Logs, Metrics, Alarms).
* Hiểu cơ chế co giãn tự động (EC2 Auto Scaling) để đảm bảo hiệu năng và tối ưu chi phí.
* Nắm được cách phân phối nội dung toàn cầu bằng CDN (Amazon CloudFront).
* Củng cố kiến thức nền tảng để lựa chọn hướng đi cho project cuối khóa.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học Amazon CloudWatch: phân biệt Logs, Metrics và Alarms <br> - Tìm hiểu cách CloudWatch thu thập metric từ các dịch vụ (EC2, RDS...) <br> - Hiểu khái niệm namespace, dimension và các thống kê (Average, Sum, Max) <br> - Tìm hiểu CloudWatch Dashboard để trực quan hóa dữ liệu | 18/05/2026 | 18/05/2026 | <https://000008.awsstudygroup.com/> |
| 3 | - **Thực hành:** xem log và metric của một EC2 instance trên Console <br> - Tạo một alarm đơn giản (ví dụ cảnh báo khi CPU vượt ngưỡng) <br> - Cấu hình action gửi thông báo qua SNS khi alarm kích hoạt <br> - Kiểm tra trạng thái alarm (OK / ALARM / INSUFFICIENT_DATA) | 19/05/2026 | 19/05/2026 | <https://000008.awsstudygroup.com/> |
| 4 | - Tìm hiểu EC2 Auto Scaling: launch template, Auto Scaling Group (ASG) <br> - Học các loại scaling policy: target tracking, step scaling, scheduled scaling <br> - Hiểu khái niệm desired/min/max capacity và health check <br> - Tìm hiểu cách kết hợp ASG với Load Balancer để phân phối tải | 20/05/2026 | 20/05/2026 | <https://000006.awsstudygroup.com/> |
| 5 | - Thực hiện networking workshop theo tài liệu FCJ: dựng VPC, subnet, route table và kết nối các thành phần <br> - Áp dụng kiến thức mạng đã học ở tuần trước vào một bài lab hoàn chỉnh <br> - Kiểm tra kết nối và khắc phục lỗi cấu hình phát sinh | 21/05/2026 | 21/05/2026 | <https://000092.awsstudygroup.com/1-introduce/> |
| 6 | - Tìm hiểu Amazon CloudFront (CDN): khái niệm edge location và cache <br> - Hiểu vai trò của CDN trong việc giảm độ trễ và tải cho origin (S3/EC2) <br> - Tìm hiểu cách tạo distribution và cấu hình origin, cache behavior <br> - Tổng hợp và viết worklog tuần 5 | 22/05/2026 | 22/05/2026 | <https://000094.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **CloudWatch:** phân biệt Logs, Metrics và Alarms; hiểu cách giám sát tài nguyên, tạo alarm và gửi thông báo qua SNS.
* **Auto Scaling:** hiểu cơ chế co giãn tự động qua launch template, Auto Scaling Group và các scaling policy; biết cách đảm bảo hiệu năng khi tải thay đổi.
* **Load Balancing:** nắm được ý tưởng kết hợp ASG với Load Balancer để phân phối tải và tăng tính sẵn sàng.
* **CloudFront (CDN):** hiểu cách CDN dùng edge location để cache và phân phối nội dung, giảm độ trễ cho người dùng cuối và giảm tải cho origin.
* **Vận dụng tổng hợp:** qua networking workshop, biết cách kết hợp nhiều dịch vụ (VPC, EC2, monitoring) thành một hệ thống hoàn chỉnh.

### Khó khăn trong tuần:

* Khó hình dung cơ chế hoạt động của CloudWatch, Auto Scaling và CloudFront khi thực hành lần đầu.

### Kết quả đạt được tuần 5:

* Biết cách xem log, metric và tạo alarm giám sát bằng CloudWatch.
* Hiểu cơ chế co giãn ứng dụng (Auto Scaling) và phân phối tải trên AWS.
* Nắm được vai trò của CloudFront trong việc phân phối nội dung toàn cầu.
* Hoàn thành networking workshop và có kiến thức nền để chọn hướng project phù hợp.
