---
title: "Worklog Tuần 5"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu tuần 5:
* Thiết kế cơ sở dữ liệu Amazon DynamoDB để lưu trữ thông tin session và lịch sử trò chuyện của người dùng.
* Cấu hình Amazon Cognito User Pool làm hệ thống quản lý tài khoản và xác thực người dùng.
* Viết và deploy các Lambda function TypeScript đầu tiên: xử lý tạo mới session (`createSession`) và lấy danh sách session (`getSessions`).
* Thiết lập quy trình đóng gói (bundling) tự động cho Lambda TypeScript bằng `esbuild` trong AWS CDK.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Thiết kế cấu trúc bảng DynamoDB lưu lịch sử chat. <br> - Xác định Partition Key là `SessionId` (định dạng UUID) và Sort Key là `Timestamp` để đảm bảo truy vấn tin nhắn sắp xếp theo trình tự thời gian tối ưu. | 18/05/2026 | 18/05/2026 | [DynamoDB Design Patterns](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/) |
| 3 | - Viết CDK code tạo Amazon Cognito User Pool. <br> - Cấu hình đăng nhập bằng Email, chính sách mật khẩu an toàn, và cơ chế gửi mã xác thực OTP qua Email (Self-service sign-up verification). | 19/05/2026 | 19/05/2026 | [CDK Cognito Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_cognito-readme.html) |
| 4 | - Khởi tạo thư mục dự án `backend/src` chứa mã nguồn Lambda. <br> - Viết mã nguồn TypeScript cho hai hàm: `createSession` (ghi record mới vào DynamoDB) và `getSessions` (truy vấn danh sách session dựa theo `UserId`). | 20/05/2026 | 20/05/2026 | [AWS SDK for JavaScript v3](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/) |
| 5 | - Cấu hình thuộc tính `NodejsFunction` trong AWS CDK để sử dụng `esbuild` tự động biên dịch code TypeScript thành file JS và tối ưu hóa dung lượng (bundle & minify) trước khi nén zip. | 21/05/2026 | 21/05/2026 | [AWS CDK Nodejs Library](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_lambda_nodejs-readme.html) |
| 6 | - Triển khai (`cdk deploy`) các tài nguyên lên tài khoản AWS. <br> - Kiểm thử nhanh Lambda function bằng tính năng Test Event trên Console. <br> - Tổng hợp và viết worklog tuần 5. | 22/05/2026 | 22/05/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Thiết kế DynamoDB:** Hiểu cách lựa chọn Partition Key và Sort Key để tối ưu chi phí đọc/ghi và tránh lỗi phân vùng nóng (hot partition).
* **Quản lý Auth bằng Cognito:** Cách thiết lập luồng đăng ký, đăng nhập và xác thực token JWT được tạo bởi Cognito User Pool.
* **Đóng gói Lambda TypeScript:** Nắm vững cách tích hợp `esbuild` vào CDK để giải quyết vấn đề quản lý dependencies trong Lambda (như đóng gói riêng biệt, giảm cold start).

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Quá trình deploy CDK bị lỗi `esbuild is not installed` do môi trường CDK CLI chạy trên docker hoặc local không tìm thấy thư viện `esbuild` để biên dịch các file TypeScript của Lambda.
* **Cách giải quyết:** Có 2 cách xử lý: cài đặt `esbuild` toàn cục trên máy (`npm install -g esbuild`) hoặc cài đặt nó làm devDependency trong thư mục root (`npm install -D esbuild`). Đã chọn cách thứ hai (devDependency) để đảm bảo mọi thành viên trong team sau này khi kéo code về chỉ cần chạy `npm install` là có thể deploy được ngay mà không cần cài global tools.

### Kết quả đạt được tuần 5:
* Thiết kế và tạo thành công bảng DynamoDB với cấu trúc PK/SK chuẩn.
* Cấu hình thành công Cognito User Pool cho việc đăng ký/đăng nhập.
* Viết và deploy thành công hai API Lambda quản lý session người dùng viết bằng TypeScript với quy trình build tự động bằng `esbuild`.
