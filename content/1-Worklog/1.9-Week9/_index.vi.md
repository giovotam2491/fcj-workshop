---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9:
* Tích hợp thư viện xác thực Amazon Cognito (`amazon-cognito-identity-js`) vào dự án React Frontend.
* Thiết kế và xây dựng giao diện Đăng ký, Đăng nhập, Đăng xuất, và trang xác thực mã OTP gửi qua Email.
* Cấu hình Axios Interceptor trên Frontend để tự động đính kèm ID Token vào Header Authorization gửi lên API Gateway.
* Triển khai cơ chế tự động làm mới token (Silent Token Refresh) bằng Refresh Token khi Access/ID Token hết hạn.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Cài đặt thư viện `amazon-cognito-identity-js` vào Frontend React. <br> - Cấu hình các biến môi trường cho ứng dụng (Cognito User Pool ID và Client App ID) lưu tại file `.env`. | 15/06/2026 | 15/06/2026 | [Cognito Auth SDK NPM](https://www.npmjs.com/package/amazon-cognito-identity-js) |
| 3 | - Tạo `AuthContext` và `AuthProvider` trong React sử dụng React Context API để quản lý trạng thái đăng nhập của người dùng toàn cục. | 16/06/2026 | 16/06/2026 | [React Context Docs](https://react.dev/reference/react/useContext) |
| 4 | - Xây dựng giao diện các trang form: Login, Register, và Verification (nhập OTP). <br> - Áp dụng các hiệu ứng chuyển trang cơ bản bằng GSAP. | 17/06/2026 | 17/06/2026 | Tài liệu cá nhân |
| 5 | - Cấu hình Axios client cho dự án. <br> - Viết Axios Request Interceptor tự động chèn `Authorization: Bearer <ID_TOKEN>` vào mọi request. <br> - Viết Axios Response Interceptor xử lý lỗi `401 Unauthorized` bằng cách tự động gọi hàm refresh token và gửi lại request lỗi. | 18/06/2026 | 18/06/2026 | [Axios Interceptors Docs](https://axios-http.com/docs/interceptors) |
| 6 | - Kiểm thử toàn diện luồng đăng ký -> gửi OTP -> xác nhận -> đăng nhập -> lấy token gọi API lấy Session. <br> - Tổng hợp và viết worklog tuần 9. | 19/06/2026 | 19/06/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Amazon Cognito SDK:** Cách quản lý user session, đăng ký và xác thực người dùng trực tiếp từ mã nguồn Frontend React mà không cần trung gian.
* **Axios Interceptors:** Hiểu cách chặn và chỉnh sửa Request/Response trên HTTP client, hỗ trợ triển khai cơ chế refresh token ngầm giúp tăng trải nghiệm người dùng.
* **Quy trình xác thực JWT trên Cloud:** Cách trao đổi mã xác thực lấy Token JWT và lưu trữ chúng an toàn trên LocalStorage/SessionStorage của trình duyệt.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Frontend liên tục bị API Gateway trả về mã lỗi `401 Unauthorized` khi gọi API `/sessions` mặc dù đã login thành công và lấy được token JWT đính kèm vào Header.
* **Cách giải quyết:** Tiến hành debug bằng cách copy Token JWT kiểm tra trên trang `jwt.io`. Phát hiện ra 2 lỗi:
  1. Frontend đang gửi nhầm `Access Token` của Cognito thay vì `ID Token` (API Gateway Cognito Authorizer yêu cầu ID Token để xác thực thông tin user).
  2. Định dạng Header gửi đi bị sai: API Gateway cấu hình Authorizer kiểm tra token thô (không có tiền tố `Bearer `). Đã cấu hình lại Axios Interceptor để truyền token dạng `"Authorization": idToken` (không có chữ Bearer) và điều chỉnh config Gateway để đồng bộ.

### Kết quả đạt được tuần 9:
* Tích hợp thành công Cognito User Pool Authentication vào React Frontend.
* Thiết lập xong giao diện Login/Register/OTP mượt mà.
* Hoàn thành cấu hình Axios Interceptors quản lý JWT token và tự động refresh token thành công.
