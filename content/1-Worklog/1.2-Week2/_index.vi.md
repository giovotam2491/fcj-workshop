---
title: "Worklog Tuần 2"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Mục tiêu tuần 2:
* Thiết kế ma trận phân quyền IAM Roles và Access Policies chi tiết cho toàn bộ hệ thống (Least Privilege).
* Nghiên cứu cơ chế xác thực người dùng thông qua Amazon Cognito (User Pool và Client App).
* Xây dựng giao diện Chatbot thô (Chat Layout) bằng React + Tailwind CSS.
* Tích hợp các hiệu ứng hoạt ảnh GSAP Timeline ban đầu cho Sidebar và khung chat.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Lập sơ đồ phân quyền IAM Roles: Lambda Execution Roles cho từng hàm nghiệp vụ, quyền tiếp cận S3 và DynamoDB. <br> - Phân biệt cơ chế trust policy và permission policy. | 27/04/2026 | 27/04/2026 | [IAM Policies and Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| 3 | - Nghiên cứu cơ chế hoạt động của Amazon Cognito User Pool. <br> - Tìm hiểu cách cấu hình ID token, Access token và Refresh token để xác thực người dùng ở API Gateway. | 28/04/2026 | 28/04/2026 | [Amazon Cognito Developer Guide](https://docs.aws.amazon.com/cognito/) |
| 4 | - Thiết kế giao diện Frontend cơ bản: thanh bên Sidebar (danh sách hội thoại), khung Chat chính, và ô nhập văn bản (input). <br> - Sử dụng CSS Tailwind để tối ưu giao diện đáp ứng (responsive). | 29/04/2026 | 29/04/2026 | [Tailwind CSS Documentation](https://tailwindcss.com/docs) |
| 5 | - Cài đặt thư viện `@gsap/react`. <br> - Thực hành viết mã nguồn GSAP tạo hiệu ứng trượt mở rộng (sliding sidebar) và hiệu ứng mờ dần (fade-in) cho tin nhắn mới gửi. | 30/04/2026 | 30/04/2026 | [GSAP React Integration](https://gsap.com/resources/react/) |
| 6 | - Kiểm tra hiệu năng và khắc phục lỗi hoạt ảnh GSAP khi kết hợp với React state re-render. <br> - Tổng hợp và viết worklog tuần 2. | 01/05/2026 | 01/05/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Ma trận phân quyền IAM:** Hiểu rõ cơ chế AssumeRole và cấu trúc một IAM Policy hoàn chỉnh. Biết cách viết policy hạn chế tài nguyên cụ thể (Resource-level permissions).
* **Xác thực Cognito:** Phân biệt được Cognito User Pool (quản lý đăng ký/đăng nhập) và Identity Pool (cấp quyền AWS temporary credentials).
* **GSAP với React:** Cách quản lý vòng đời hoạt ảnh trong React bằng hook `useGSAP` giúp tránh rò rỉ bộ nhớ (memory leaks) và xung đột hoạt ảnh.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Hiệu ứng hoạt ảnh GSAP của Sidebar bị giật và tự động chạy lại từ đầu mỗi khi nhập text vào ô chat input (do React component re-render khi cập nhật State `inputText`).
* **Cách giải quyết:** Sử dụng hook `useGSAP` kết hợp với tham chiếu `useRef` để đóng gói animation context. Định nghĩa một timeline tĩnh chỉ trigger khi người dùng chủ động click nút "Toggle Sidebar", loại bỏ các biến state thay đổi liên tục khỏi dependency array của hoạt ảnh.

### Kết quả đạt được tuần 2:
* Hoàn thành ma trận phân quyền IAM chuẩn Least Privilege cho backend lambda.
* Thiết lập thành công khung giao diện Frontend Chatbot với tính năng responsive.
* Tích hợp thành công hiệu ứng hoạt ảnh đóng/mở sidebar và chuyển động tin nhắn mượt mà bằng GSAP.
