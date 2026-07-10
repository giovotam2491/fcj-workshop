---
title: "Worklog Tuần 10"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10:
* Hoàn thiện giao diện Chatbot: hỗ trợ định dạng Markdown và hiển thị nguồn trích dẫn tài liệu tham chiếu (Citations).
* Áp dụng GSAP để xử lý các hiệu ứng động mượt mà: hiệu ứng gõ chữ (typing effect) và panel thông tin nguồn trích dẫn trượt từ cạnh phải.
* Triển khai hạ tầng Frontend sử dụng AWS CDK: S3 Bucket (Static Web Hosting) kết hợp Amazon CloudFront CDN.
* Tích hợp AWS WAF (Web Application Firewall) bảo vệ hệ thống trước các đòn tấn công mạng và giới hạn IP truy cập.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Cài đặt thư viện `react-markdown` phục vụ hiển thị câu trả lời của LLM. <br> - Xây dựng component hiển thị nguồn trích dẫn: Click vào nguồn sẽ mở panel xem trước tệp tài liệu gốc trên S3. | 22/06/2026 | 22/06/2026 | [React Markdown NPM](https://www.npmjs.com/package/react-markdown) |
| 3 | - Sử dụng GSAP Timeline lập trình hiệu ứng gõ chữ của chatbot khi nhận phản hồi từ API. <br> - Viết hiệu ứng mở panel trích dẫn (citations panel) trượt từ lề phải mượt mà. | 23/06/2026 | 23/06/2026 | Tài liệu cá nhân |
| 4 | - Viết mã CDK định nghĩa S3 Bucket cho static hosting, tạo CloudFront Distribution trỏ về S3 Origin. <br> - Cấu hình CloudFront Origin Access Control (OAC) để bảo mật S3 bucket (chỉ cho phép CloudFront đọc file). | 24/06/2026 | 24/06/2026 | [CDK CloudFront Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_cloudfront-readme.html) |
| 5 | - Định nghĩa AWS WAF WebACL trong CDK. <br> - Kích hoạt các nhóm rule bảo mật mặc định của AWS (Managed Rule Groups: CommonRuleSet và SQLiRuleSet) để chống XSS, SQL Injection. <br> - Liên kết WAF WebACL với CloudFront Distribution. | 25/06/2026 | 25/06/2026 | [AWS WAF Developer Guide](https://docs.aws.amazon.com/waf/) |
| 6 | - Chạy build Frontend (`npm run build`), upload các file tĩnh lên S3. <br> - Khắc phục sự cố định tuyến SPA trên CloudFront. <br> - Tổng hợp và viết worklog tuần 10. | 26/06/2026 | 26/06/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **Tích hợp CloudFront + S3 OAC:** Hiểu cơ chế bảo mật S3 bucket không public ra ngoài internet mà chỉ chấp nhận các request hợp lệ đi qua CloudFront CDN bằng OAC (Origin Access Control).
* **AWS WAF (Web Application Firewall):** Cách áp dụng các bộ rule bảo mật tiêu chuẩn chống lại các lỗi bảo mật phổ biến OWASP Top 10 tại tầng phân phối mạng.
* **SPA Routing trên CDN:** Hiểu cơ chế định tuyến client-side của React Router và sự khác biệt với định tuyến server-side.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Khi người dùng truy cập trang chính CloudFront `https://domain.cloudfront.net` thì hoạt động bình thường, nhưng nếu họ bấm reload (F5) hoặc truy cập trực tiếp link con như `https://domain.cloudfront.net/login`, CloudFront trả về lỗi `Access Denied (403)` do S3 không có tệp vật lý nào tên là `login`.
* **Cách giải quyết:** Lỗi này do cơ chế định tuyến Single Page Application (SPA). Để khắc phục, cần cấu hình Custom Error Response trên CloudFront Distribution thông qua CDK: Thiết lập nếu gặp mã lỗi `403 Forbidden` hoặc `404 Not Found` từ S3 origin, CloudFront sẽ tự động chuyển hướng và trả về file `/index.html` với HTTP Status là `200 OK`. React Router ở client sẽ đọc URL và hiển thị đúng view mong muốn.

### Kết quả đạt được tuần 10:
* Chatbot UI hoàn tất với tính năng render Markdown và trích xuất citation trực quan.
* Deploy thành công Frontend lên S3, phân phối qua CloudFront CDN toàn cầu.
* Thiết lập thành công AWS WAF chặn đứng các lỗ hổng bảo mật phổ biến.
