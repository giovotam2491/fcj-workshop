---
title: "Worklog Tuần 6"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu tuần 6:
* Thiết kế và cấu hình Amazon API Gateway (REST API) làm cổng kết nối cho các dịch vụ Backend.
* Tích hợp Cognito User Pool Authorizer vào API Gateway để bảo vệ toàn bộ các API endpoint.
* Cấu hình CORS (Cross-Origin Resource Sharing) trên API Gateway để cho phép Frontend ở Local gọi API an toàn.
* Triển khai các API method đầu tiên cho resource `/sessions` (GET/POST) kết nối với Lambda.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Viết CDK stack định nghĩa API Gateway REST API. <br> - Cấu hình tài nguyên `/sessions` và liên kết với các Lambda function thông qua Lambda Proxy Integration. | 25/05/2026 | 25/05/2026 | [CDK API Gateway Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_apigateway-readme.html) |
| 3 | - Tạo Cognito User Pool Authorizer trong cấu hình API Gateway của CDK. <br> - Gán Authorizer này vào các API GET và POST `/sessions`, cấu hình header nhận token mặc định là `Authorization`. | 26/05/2026 | 26/05/2026 | [API Gateway Cognito Authorizer Docs](https://docs.aws.amazon.com/apigateway/) |
| 4 | - Viết Lambda function `getChatHistory` xử lý truy vấn bảng DynamoDB lấy toàn bộ tin nhắn chat của một `SessionId` (GET `/sessions/{sessionId}/messages`). | 27/05/2026 | 27/05/2026 | Tài liệu cá nhân |
| 5 | - Cấu hình CORS cho API Gateway bằng cách định nghĩa các OPTIONS method và gán headers cho phép truy cập. <br> - Sử dụng Postman để test thử cuộc gọi API: thực hiện login bằng Cognito CLI để lấy ID Token, đính kèm vào Header `Authorization: Bearer <ID_TOKEN>`. | 28/05/2026 | 28/05/2026 | Postman Docs |
| 6 | - Kiểm tra, sửa lỗi CORS phát sinh từ Lambda response và hoàn thiện deploy API Gateway. <br> - Tổng hợp và viết worklog tuần 6. | 29/05/2026 | 29/05/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **API Gateway Lambda Proxy Integration:** Hiểu cơ chế chuyển giao nguyên bản HTTP request từ API Gateway sang Lambda dưới dạng một JSON object (`event`), giúp Lambda kiểm soát hoàn toàn HTTP response trả về.
* **Xác thực JWT:** Cách API Gateway tự động xác thực chữ ký số của token JWT do Cognito phát hành thông qua cơ chế Authorizer mà không cần Lambda phải chạy code giải mã token thủ công.
* **Cơ chế CORS nâng cao:** Phân biệt CORS ở tầng API Gateway (dành cho preflight request - OPTIONS) và CORS ở tầng ứng dụng/Lambda (dành cho request thực tế).

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Gặp lỗi CORS khi Frontend gọi API, trình duyệt báo lỗi `No 'Access-Control-Allow-Origin' header is present on the requested resource` mặc dù đã bật cấu hình CORS ở API Gateway CDK.
* **Cách giải quyết:** Nguyên nhân là do khi Lambda xảy ra lỗi nghiệp vụ (ví dụ: trả về mã lỗi 400 hoặc 500 do query sai tham số), Lambda handler trả về response JSON mà không đính kèm CORS headers. Khi sử dụng Lambda Proxy Integration, API Gateway sẽ chuyển tiếp nguyên văn response từ Lambda. Đã sửa lỗi bằng cách cập nhật module helper chung ở Backend để mọi response trả về từ Lambda (kể cả response thành công 200 hay lỗi 400, 500) luôn chứa đầy đủ các headers:
  ```json
  "headers": {
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token",
    "Access-Control-Allow-Methods": "DELETE,GET,HEAD,OPTIONS,PATCH,POST,PUT"
  }
  ```

### Kết quả đạt được tuần 6:
* Deploy thành công API Gateway REST API và tích hợp Cognito Authorizer bảo mật tuyệt đối.
* Triển khai xong các endpoint quản lý Session và lấy lịch sử tin nhắn.
* Giải quyết triệt để lỗi CORS ở cả tầng Gateway và tầng Lambda Function.
