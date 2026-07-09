---
title: "Worklog Tuần 1"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---


### Mục tiêu tuần 1:

* Tham dự lễ khai mạc FCAJ và nắm được yêu cầu của chương trình thực tập cũng như project cuối khóa.
* Tìm hiểu tổng quan về AWS và các nhóm dịch vụ chính (Compute, Storage, Networking, Database).
* Tạo, cấu hình và bảo mật tài khoản AWS đầu tiên theo đúng best practice (MFA, IAM, least privilege).
* Thiết lập cơ chế theo dõi và kiểm soát chi phí ngay từ đầu để tránh phát sinh ngoài ý muốn.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tham dự lễ khai mạc FCAJ tại trường, nghe giới thiệu về mục tiêu, lộ trình 12 tuần và tiêu chí đánh giá của chương trình <br> - Đọc và ghi chú nội quy chương trình, quy định về worklog, yêu cầu đầu ra của project cuối khóa <br> - Làm quen với môi trường làm việc, cách nộp báo cáo và công cụ quản lý (Notion) | 20/04/2026 | 20/04/2026 | <https://app.notion.com/p/Group-description-TP-HCM-347df829a730809a8f63d39505644917> |
| 3 | - Tìm hiểu tổng quan về điện toán đám mây và mô hình dịch vụ (IaaS, PaaS, SaaS) <br> - Nghiên cứu 4 nhóm dịch vụ chính của AWS: Compute (EC2, Lambda), Storage (S3, EBS), Networking (VPC, Route 53), Database (RDS, DynamoDB) <br> - Tìm hiểu khái niệm Region và Availability Zone, cách chọn Region phù hợp | 21/04/2026 | 21/04/2026 | <https://cloudjourney.awsstudygroup.com/1-explore/> |
| 4 | - Đăng ký và tạo tài khoản AWS mới, xác thực email và thông tin thanh toán <br> - Bật MFA (Multi-Factor Authentication) cho tài khoản root để tăng bảo mật <br> - Tạo IAM user riêng có quyền quản trị (admin) để dùng hàng ngày thay cho root <br> - Thiết lập alias cho tài khoản và đăng nhập thử bằng IAM user | 22/04/2026 | 22/04/2026 | <https://000001.awsstudygroup.com/> |
| 5 | - Học các khái niệm IAM cơ bản: User, Group, Role và Policy; phân biệt vai trò của từng thành phần <br> - Thực hành tạo Group, gán Policy và thêm User vào Group <br> - Tìm hiểu cấu trúc của một IAM Policy (Effect, Action, Resource) <br> - Áp dụng nguyên tắc least privilege (quyền tối thiểu) khi cấp quyền | 23/04/2026 | 23/04/2026 | <https://000002.awsstudygroup.com/> |
| 6 | - Thiết lập AWS Budgets để đặt ngưỡng ngân sách và nhận cảnh báo qua email khi chi phí vượt mức <br> - Cấu hình Cost Explorer để theo dõi chi phí theo dịch vụ <br> - Tìm hiểu các gói AWS Support và Free Tier, cách kiểm tra mức sử dụng miễn phí <br> - Tổng hợp và viết worklog tuần 1 | 24/04/2026 | 24/04/2026 | <https://000007.awsstudygroup.com/> <https://000009.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **Tổng quan AWS:** hiểu mô hình điện toán đám mây, phân biệt IaaS/PaaS/SaaS và nắm được vai trò của 4 nhóm dịch vụ cốt lõi (Compute, Storage, Networking, Database).
* **Region & Availability Zone:** hiểu cách AWS tổ chức hạ tầng vật lý và tiêu chí chọn Region (độ trễ, chi phí, tuân thủ pháp lý).
* **Bảo mật tài khoản:** biết vì sao không nên dùng tài khoản root hàng ngày, cách bật MFA và tạo IAM user quản trị thay thế.
* **IAM:** phân biệt được User, Group, Role, Policy; đọc hiểu cấu trúc Policy (Effect – Action – Resource) và áp dụng nguyên tắc least privilege.
* **Quản lý chi phí:** biết cách dùng AWS Budgets, Cost Explorer và Free Tier để giám sát, kiểm soát chi phí và tránh phát sinh ngoài dự kiến.

### Kết quả đạt được tuần 1:

* Hiểu rõ mục tiêu thực tập, lộ trình chương trình và yêu cầu của project cuối khóa.
* Nắm được tổng quan AWS và các nhóm dịch vụ chính.
* Tạo và bảo mật thành công tài khoản AWS đầu tiên (root MFA + IAM user quản trị).
* Nắm vững IAM cơ bản (User / Group / Role / Policy) và nguyên tắc least privilege.
* Cấu hình cảnh báo ngân sách để chủ động kiểm soát và tránh phát sinh chi phí ngoài ý muốn.
