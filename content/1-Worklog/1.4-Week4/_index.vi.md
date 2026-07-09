---
title: "Worklog Tuần 4"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---


### Mục tiêu tuần 4:

* Hiểu cơ sở dữ liệu quan hệ (Amazon RDS) và cơ sở dữ liệu NoSQL (Amazon DynamoDB) trên AWS.
* Thực hành tạo database mẫu, kết nối và test các thao tác đọc/ghi dữ liệu.
* Phân biệt được khi nào nên dùng relational database và khi nào nên dùng NoSQL.
* Nắm được vai trò của lớp cache (ElastiCache) trong việc tăng hiệu năng hệ thống.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học Amazon RDS: các database engine được hỗ trợ (MySQL, PostgreSQL, MariaDB, SQL Server, Oracle) <br> - Tìm hiểu khái niệm DB instance, instance class và storage <br> - Học cơ chế backup tự động, snapshot thủ công và khôi phục dữ liệu <br> - Tìm hiểu Multi-AZ (tính sẵn sàng cao) và Read Replica (mở rộng đọc) | 11/05/2026 | 11/05/2026 | <https://000005.awsstudygroup.com/> |
| 3 | - **Thực hành:** tạo một DB instance RDS mẫu (chọn engine, instance class, cấu hình Security Group) <br> - Kết nối tới database bằng client (ví dụ MySQL Workbench/CLI) <br> - Tạo bảng, chèn dữ liệu và test truy vấn đọc/ghi cơ bản <br> - Thực hành tạo snapshot và xem lại quá trình khôi phục | 12/05/2026 | 12/05/2026 | <https://000005.awsstudygroup.com/> |
| 4 | - Học DynamoDB: mô hình bảng NoSQL, item và attribute <br> - Hiểu partition key và sort key, cách chúng quyết định phân phối và truy vấn dữ liệu <br> - Phân biệt hai chế độ dung lượng: on-demand và provisioned (RCU/WCU) <br> - Tìm hiểu khái niệm index (LSI, GSI) ở mức cơ bản | 13/05/2026 | 13/05/2026 | <https://000060.awsstudygroup.com/> |
| 5 | - **Thực hành:** tạo một bảng DynamoDB (định nghĩa partition key, sort key) <br> - Thực hiện các thao tác put/get/update/delete item trên Console <br> - Thử truy vấn dữ liệu bằng query và scan, so sánh sự khác biệt | 14/05/2026 | 14/05/2026 | <https://000060.awsstudygroup.com/> |
| 6 | - Tìm hiểu ElastiCache ở mức khái niệm: vai trò của cache trong việc giảm tải cho database <br> - Phân biệt hai engine Redis và Memcached <br> - Hiểu mô hình cache-aside và khi nào nên dùng cache <br> - Tổng hợp và viết worklog tuần 4 | 15/05/2026 | 15/05/2026 | <https://000061.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **Amazon RDS:** hiểu mô hình database quan hệ được quản lý (managed), các engine hỗ trợ, cơ chế backup/snapshot, Multi-AZ và Read Replica.
* **Amazon DynamoDB:** nắm được mô hình NoSQL key-value, vai trò của partition key/sort key và hai chế độ dung lượng on-demand/provisioned.
* **So sánh RDS vs DynamoDB:** biết RDS phù hợp dữ liệu có cấu trúc, quan hệ phức tạp và truy vấn SQL; DynamoDB phù hợp dữ liệu lớn, truy cập theo key với độ trễ thấp và khả năng mở rộng cao.
* **Query vs Scan:** hiểu sự khác biệt về hiệu năng và chi phí giữa query (theo key) và scan (quét toàn bảng) trong DynamoDB.
* **ElastiCache:** hiểu vai trò của lớp cache in-memory (Redis/Memcached) trong việc tăng tốc truy cập và giảm tải cho database.

### Khó khăn trong tuần:

* Gặp khó khăn khi phân biệt cách lưu trữ và sử dụng giữa RDS và DynamoDB.

### Kết quả đạt được tuần 4:

* Phân biệt được RDS (quan hệ) và DynamoDB (NoSQL) về mô hình lưu trữ và cách sử dụng.
* Tạo được database RDS và bảng DynamoDB cơ bản, thực hành đọc/ghi dữ liệu.
* Hiểu khi nào nên chọn relational database và khi nào nên chọn NoSQL.
* Nắm được vai trò của ElastiCache trong việc tối ưu hiệu năng hệ thống.
