---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---



### Mục tiêu tuần 7:

* Xây dựng phần core serverless backend cho project (Lambda + DynamoDB).
* Nắm được mô hình CRUD serverless và cách xử lý dữ liệu qua Lambda function.
* Hiểu cách API Gateway kết nối frontend với backend qua REST API.
* Thiết kế cấu trúc bảng cho cơ sở dữ liệu của hệ thống.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học mô hình CRUD serverless (Create, Read, Update, Delete) với Lambda + DynamoDB <br> - Tìm hiểu cách mỗi thao tác CRUD được ánh xạ tới một hàm xử lý và một truy vấn DynamoDB <br> - Hiểu luồng xử lý: request → Lambda → DynamoDB → response | 01/06/2026 | 01/06/2026 | <https://000133.awsstudygroup.com/> |
| 3 | - **Thực hành:** tạo một Lambda function mới, cấu hình runtime và IAM execution role <br> - Viết handler cơ bản để nhận event và trả về response <br> - Thực hành test function bằng test event trên Console và đọc log trên CloudWatch | 02/06/2026 | 02/06/2026 | <https://000133.awsstudygroup.com/2-create-lambda-functions/> |
| 4 | - **Thực hành:** kết nối Lambda với DynamoDB bằng AWS SDK <br> - Viết code thực hiện put item (lưu) và get item (đọc) dữ liệu <br> - Cấp quyền cho Lambda truy cập DynamoDB qua IAM policy <br> - Kiểm tra dữ liệu được ghi/đọc đúng trên bảng DynamoDB | 03/06/2026 | 03/06/2026 | <https://000133.awsstudygroup.com/> |
| 5 | - Tìm hiểu Amazon API Gateway: khái niệm REST API, resource, method (GET/POST/PUT/DELETE) <br> - Hiểu cách tích hợp API Gateway với Lambda (Lambda proxy integration) <br> - Tìm hiểu về stage, deployment và cách API Gateway kết nối frontend với backend | 04/06/2026 | 04/06/2026 | <https://000135.awsstudygroup.com/> |
| 6 | - Thiết kế các bảng cho cơ sở dữ liệu: xác định entity, thuộc tính, partition key/sort key <br> - Rà soát mối quan hệ giữa các bảng và cách truy vấn dữ liệu <br> - Chuẩn bị nội dung ban đầu cho phần step-by-step trong báo cáo <br> - Tổng hợp và viết worklog tuần 7 | 05/06/2026 | 05/06/2026 | |

### Kiến thức thu được trong tuần:

* **Mô hình CRUD serverless:** hiểu cách xây dựng các thao tác Create/Read/Update/Delete bằng Lambda và DynamoDB không cần máy chủ.
* **Lambda thực chiến:** biết cách tạo function, viết handler, cấu hình execution role và test/debug bằng CloudWatch Logs.
* **Kết nối Lambda – DynamoDB:** nắm được cách dùng AWS SDK để đọc/ghi dữ liệu và cấp quyền tối thiểu qua IAM policy.
* **API Gateway:** hiểu cách phơi bày backend Lambda ra thành REST API cho frontend gọi, qua resource, method và Lambda proxy integration.
* **Thiết kế database:** biết cách mô hình hóa dữ liệu, chọn partition key/sort key và tổ chức bảng phù hợp với truy vấn.

### Kết quả đạt được tuần 7:

* Backend serverless xử lý được dữ liệu cơ bản (CRUD).
* Test thành công luồng: request → xử lý (Lambda) → lưu/đọc dữ liệu (DynamoDB) → response.
* Nắm được cách API Gateway kết nối frontend với backend.
* Hoàn thành thiết kế bảng cho cơ sở dữ liệu.
* Chuẩn bị được nội dung ban đầu cho phần step-by-step trong báo cáo.
