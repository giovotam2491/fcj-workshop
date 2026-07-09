---
title: "Worklog Tuần 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---


### Mục tiêu tuần 3:

* Hiểu kiến trúc mạng cơ bản trên AWS (VPC, subnet, route table, internet gateway).
* Nắm được cách kiểm soát truy cập mạng bằng Security Group và cấu hình network cho EC2.
* Nắm được các khái niệm cốt lõi của S3 và host thành công một website tĩnh trên Amazon S3.
* Hiểu vai trò của DNS và Route 53 trong việc định tuyến truy cập cho hệ thống cloud.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học kiến trúc VPC (Virtual Private Cloud): dải CIDR, cách chia subnet public/private <br> - Tìm hiểu route table và cách định tuyến traffic giữa các subnet <br> - Hiểu vai trò của Internet Gateway trong việc cho phép subnet public ra Internet | 04/05/2026 | 04/05/2026 | <https://www.youtube.com/watch?v=P8g7Z4NYk3Q&t=3s> |
| 3 | - Học Security Group: khái niệm stateful firewall, inbound/outbound rule <br> - Phân biệt Security Group và Network ACL <br> - **Thực hành:** cấu hình network cơ bản cho EC2 (gắn subnet, mở port SSH/HTTP qua Security Group) và kiểm tra kết nối | 05/05/2026 | 05/05/2026 | <https://youtu.be/TtlKFgfN3PU?si=xMyuJQQ7oQDA6043> <https://000003.awsstudygroup.com/1-introduce/> |
| 4 | - Học các khái niệm cơ bản của S3: bucket, object, key, region <br> - Tìm hiểu cơ chế phân quyền: bucket policy, ACL và Block Public Access <br> - Hiểu mô hình lưu trữ object storage và các storage class của S3 | 06/05/2026 | 06/05/2026 | <https://000057.awsstudygroup.com/> |
| 5 | - **Thực hành:** tạo một S3 bucket, upload file HTML/CSS của website <br> - Bật tính năng Static Website Hosting, cấu hình index/error document <br> - Chỉnh bucket policy để cho phép truy cập công khai và kiểm tra website qua endpoint | 07/05/2026 | 07/05/2026 | <https://000057.awsstudygroup.com/> |
| 6 | - Tìm hiểu Route 53: khái niệm DNS, hosted zone, các loại record (A, CNAME, Alias) <br> - Học các chính sách định tuyến (routing policy) cơ bản <br> - Tìm hiểu cách trỏ tên miền tới website S3 <br> - Tổng hợp và viết worklog tuần 3 | 08/05/2026 | 08/05/2026 | <https://www.youtube.com/watch?v=6BoTfTtNsGU> <https://000010.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **VPC:** hiểu cách thiết kế mạng riêng ảo trên AWS với dải CIDR, subnet public/private, route table và Internet Gateway.
* **Bảo mật mạng:** phân biệt Security Group (stateful) và Network ACL (stateless), biết cách mở đúng port cần thiết theo nguyên tắc tối thiểu.
* **S3:** nắm được mô hình object storage (bucket/object/key), các cơ chế phân quyền (bucket policy, ACL, Block Public Access) và các storage class.
* **Static Website Hosting:** biết cách host một website tĩnh hoàn chỉnh trên S3, từ upload file đến cấu hình quyền truy cập công khai.
* **DNS & Route 53:** hiểu vai trò của DNS, các loại record và cách trỏ tên miền tới tài nguyên AWS.

### Kết quả đạt được tuần 3:

* Hiểu cấu trúc mạng cơ bản trên AWS (VPC, subnet, route table, internet gateway).
* Cấu hình được network và Security Group cơ bản cho EC2.
* Host thành công một website tĩnh bằng Amazon S3.
* Nắm được vai trò của DNS và Route 53 trong hệ thống cloud.
