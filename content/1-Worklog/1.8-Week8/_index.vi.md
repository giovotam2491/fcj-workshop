---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---



### Mục tiêu tuần 8:

* Thêm chức năng xác thực người dùng cho project bằng Amazon Cognito.
* Nắm được cơ chế đăng nhập cho ứng dụng Single Page Application (SPA).
* Kết nối frontend với serverless API và test luồng đăng nhập.
* Tìm hiểu AWS Amplify hỗ trợ storage/auth cho frontend.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu Amazon Cognito: khái niệm User Pool và Identity Pool <br> - Học luồng đăng ký (sign-up), xác nhận email và đăng nhập (sign-in) <br> - Hiểu cơ chế cấp token (ID token, Access token, Refresh token) sau khi đăng nhập thành công | 08/06/2026 | 08/06/2026 | <https://000081.awsstudygroup.com/> |
| 3 | - Tìm hiểu cơ chế đăng nhập cho Single Page Application (SPA) <br> - Học cách frontend lưu và gửi token trong header khi gọi API <br> - Hiểu khái niệm JWT và cách backend/API Gateway xác thực token | 09/06/2026 | 09/06/2026 | <https://000055.awsstudygroup.com/> |
| 4 | - Tìm hiểu AWS Amplify ở mức khái niệm: hỗ trợ auth, storage và hosting cho frontend <br> - Hiểu cách Amplify tích hợp sẵn với Cognito và S3 <br> - So sánh việc dùng Amplify so với tự cấu hình các dịch vụ riêng lẻ | 10/06/2026 | 10/06/2026 | <https://000134.awsstudygroup.com/> |
| 5 | - **Thực hành:** kết nối frontend với serverless API (API Gateway + Lambda) <br> - Cấu hình Cognito authorizer cho API Gateway để bảo vệ endpoint <br> - Test luồng đăng nhập: đăng nhập → nhận token → gọi API có xác thực <br> - Chụp screenshot các bước để làm tư liệu báo cáo | 11/06/2026 | 11/06/2026 | <https://000079.awsstudygroup.com/> |
| 6 | - Nghiên cứu sâu hơn cách frontend kết nối và xác thực với serverless backend <br> - Rà soát luồng end-to-end: người dùng → Cognito → frontend → API Gateway → Lambda → DynamoDB <br> - Xử lý các lỗi CORS và authentication phát sinh <br> - Tổng hợp và viết worklog tuần 8 | 12/06/2026 | 12/06/2026 | <https://000079.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **Amazon Cognito:** hiểu vai trò của User Pool/Identity Pool, luồng sign-up/sign-in và cơ chế cấp ID/Access/Refresh token.
* **Xác thực SPA:** nắm được cách frontend lưu và gửi JWT token khi gọi API, và cách backend xác thực token.
* **Cognito Authorizer:** biết cách bảo vệ endpoint API Gateway bằng Cognito authorizer để chỉ cho phép request đã xác thực.
* **AWS Amplify:** hiểu vai trò của Amplify trong việc tích hợp sẵn auth/storage/hosting cho frontend.
* **Luồng end-to-end:** hình dung được toàn bộ luồng từ người dùng qua xác thực đến xử lý dữ liệu, bao gồm cả xử lý lỗi CORS.

### Kết quả đạt được tuần 8:

* Project đã có phần frontend kết nối với backend qua serverless API.
* Tích hợp được xác thực người dùng bằng Amazon Cognito.
* Test thành công luồng đăng nhập và gọi API có bảo vệ bằng token.
* Hiểu cách xác thực người dùng ở mức cơ bản (Cognito + JWT).
* Có thêm screenshot và ghi chú làm tư liệu cho phần workshop trong báo cáo.
