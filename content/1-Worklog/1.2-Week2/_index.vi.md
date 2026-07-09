---
title: "Worklog Tuần 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---


### Mục tiêu tuần 2:

* Hiểu sâu hơn cách AWS quản lý quyền truy cập thông qua IAM Role và Policy.
* Nắm được các thành phần cơ bản của EC2 và tạo, kết nối thành công một EC2 instance.
* Biết cách gắn IAM Role vào EC2 (instance profile) để truy cập dịch vụ AWS an toàn thay vì hard-code access key.
* Làm quen với môi trường phát triển trên cloud (AWS Cloud9).

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Ôn lại IAM: thực hành tạo Role và Policy, gán Policy vào Role <br> - Phân biệt rõ User và Role: User dùng cho con người/ứng dụng cố định, Role dùng để cấp quyền tạm thời cho dịch vụ hoặc người dùng khác <br> - Tìm hiểu khái niệm trust policy và cơ chế assume role | 27/04/2026 | 27/04/2026 | <https://youtu.be/mCLYcsJ0GXQ?si=Y_CHiyxREA6GTUkZ> |
| 3 | - Học các thành phần cơ bản của EC2: instance type (họ máy, vCPU, RAM), AMI (Amazon Machine Image), EBS (ổ đĩa gắn kèm) và key pair (khóa SSH) <br> - Tìm hiểu cách chọn instance type phù hợp với nhu cầu và chi phí <br> - Hiểu vai trò của Security Group trong việc kiểm soát truy cập vào instance | 28/04/2026 | 28/04/2026 | <https://youtu.be/Dc0t4LDOySY?si=TTeWvYwVEjmFrz7M> |
| 4 | - **Thực hành:** tạo một EC2 instance (chọn AMI, instance type, key pair, Security Group) <br> - Kết nối tới instance qua SSH bằng key pair đã tạo <br> - Tạo và gắn (attach) thêm một EBS volume vào instance, thực hành mount ổ đĩa <br> - Kiểm tra trạng thái và thông tin instance trên Console | 29/04/2026 | 29/04/2026 | <https://000004.awsstudygroup.com/> |
| 5 | - Tìm hiểu IAM Role gắn với EC2 (instance profile) và cách EC2 tự động lấy credential tạm thời <br> - Thực hành tạo Role có quyền truy cập một dịch vụ (ví dụ S3) và gắn vào EC2 <br> - Hiểu vì sao nên dùng Role thay cho việc hard-code access key trong mã nguồn (an toàn, tự động xoay vòng credential) | 30/04/2026 | 30/04/2026 | <https://000048.awsstudygroup.com/> |
| 6 | - Làm quen với môi trường phát triển AWS Cloud9: tạo môi trường, dùng terminal và editor tích hợp <br> - Tìm hiểu cách Cloud9 kết nối với EC2 và sử dụng IAM credential <br> - Tổng hợp và viết worklog tuần 2 | 01/05/2026 | 01/05/2026 | <https://000049.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **IAM nâng cao:** phân biệt rõ User và Role, hiểu trust policy và cơ chế assume role để cấp quyền tạm thời một cách an toàn.
* **EC2:** nắm được các thành phần cốt lõi (instance type, AMI, EBS, key pair, Security Group) và cách chúng phối hợp để tạo nên một máy chủ ảo.
* **Kết nối & lưu trữ:** biết cách kết nối SSH bằng key pair và gắn thêm EBS volume để mở rộng dung lượng lưu trữ.
* **Instance Profile:** hiểu cơ chế gắn IAM Role vào EC2 để ứng dụng truy cập dịch vụ AWS mà không cần lưu access key trong code.
* **Cloud9:** làm quen với IDE trên cloud, cách nó tích hợp terminal, editor và dùng credential của IAM để thao tác với AWS.

### Khó khăn trong tuần:

* Cần thêm thời gian để làm quen Cloud9 và kiểm tra quyền truy cập của EC2 thông qua IAM Role.

### Kết quả đạt được tuần 2:

* Hiểu sâu hơn cách AWS quản lý quyền truy cập bằng IAM Role và Policy.
* Nắm được các thành phần cơ bản của EC2, tạo và kết nối thành công một EC2 instance.
* Thực hành gắn thêm EBS volume và kết nối SSH tới instance.
* Biết vì sao nên dùng IAM Role (instance profile) thay vì hard-code access key.
* Làm quen bước đầu với môi trường phát triển AWS Cloud9.
