---
title: "Worklog Tuần 3"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu tuần 3:
* Bắt đầu viết hạ tầng dưới dạng mã nguồn (IaC) sử dụng AWS CDK bằng TypeScript.
* Thiết lập Amazon S3 Bucket chứa tài liệu nguồn nội bộ, cấu hình mã hóa bảo mật nghiêm ngặt.
* Tìm hiểu cơ chế hoạt động của Amazon Bedrock Knowledge Base và mô hình Titan Embeddings V2.
* Chuẩn bị dữ liệu tài liệu mẫu (quy trình, quy chế nội bộ) dạng PDF/Markdown để chuẩn bị cho pipeline RAG.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo thư mục `backend/infra` và khởi tạo ứng dụng CDK mới bằng lệnh `cdk init app --language typescript`. <br> - Tìm hiểu cấu trúc file trong dự án CDK. | 04/05/2026 | 04/05/2026 | [AWS CDK Getting Started](https://docs.aws.amazon.com/cdk/v2/guide/home.html) |
| 3 | - Viết mã nguồn CDK định nghĩa S3 Bucket lưu trữ tài liệu gốc. <br> - Cấu hình bảo mật nâng cao: chặn hoàn toàn truy cập public (`blockPublicAccess: BlockPublicAccess.BLOCK_ALL`), mã hóa bằng khóa quản lý bởi S3 (`encryption: BucketEncryption.S3_MANAGED`). | 05/05/2026 | 05/05/2026 | [AWS CDK S3 Reference](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_s3-readme.html) |
| 4 | - Thực hiện bootstrap môi trường AWS (`cdk bootstrap`). <br> - Chạy lệnh `cdk deploy` để tạo S3 Bucket trên môi trường AWS thực tế, kiểm tra tài nguyên được tạo trên Console. | 06/05/2026 | 06/05/2026 | Tài liệu cá nhân |
| 5 | - Nghiên cứu tài liệu Amazon Bedrock Knowledge Base. <br> - Tìm hiểu các chiến lược chia nhỏ văn bản (Chunking Strategies) như: Default Chunking (300 tokens, 20% overlap), Fixed-size Chunking, Hierarchical Chunking. | 07/05/2026 | 07/05/2026 | [Bedrock Knowledge Base Chunking](https://docs.aws.amazon.com/bedrock/) |
| 6 | - Soạn thảo 5 tài liệu nội bộ công ty giả lập (Ví dụ: Quy chế nghỉ phép, Sổ tay văn hóa doanh nghiệp) dạng PDF. <br> - Tìm hiểu thông số mô hình Titan Embeddings V2 (Vector size mặc định 1024-dimensions). <br> - Viết worklog tuần 3. | 08/05/2026 | 08/05/2026 | Tài liệu công ty |

### Kiến thức thu được trong tuần:
* **AWS CDK (Cloud Development Kit):** Cách sử dụng TypeScript để định nghĩa và quản lý vòng đời tài nguyên đám mây thay vì viết file CloudFormation JSON/YAML thủ công.
* **Bảo mật S3 Bucket:** Hiểu cách cấu hình bảo mật tài nguyên tĩnh thông qua `Block Public Access` và mã hóa phía máy chủ (Server-side Encryption - SSE).
* **Chunking dữ liệu:** Hiểu vì sao phải chia nhỏ tài liệu lớn thành các chunk nhỏ hơn (giúp LLM dễ tìm kiếm và tránh vượt quá giới hạn token ngữ cảnh).
* **Titan Embeddings V2:** Nắm được cơ chế chuyển văn bản thành vector để lưu trữ vào Vector Database.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Khi chạy lệnh `cdk deploy`, quá trình bị lỗi `ExpiredToken: The security token included in the request is expired` do terminal đang lưu token tạm thời từ phiên đăng nhập cũ đã hết hạn.
* **Cách giải quyết:** Thực hiện kiểm tra lại credentials bằng `aws sts get-caller-identity`. Tiến hành đăng nhập lại và cập nhật các biến môi trường `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` bằng tài khoản IAM User cố định `intern-admin` đã cấu hình cho dự án, sau đó deploy thành công.

### Kết quả đạt được tuần 3:
* Khởi tạo và thiết lập thành công dự án IaC AWS CDK.
* Deploy thành công S3 Bucket bảo mật trên AWS.
* Biên soạn xong bộ tài liệu nội bộ mẫu và xác định cấu hình vector embeddings cho mô hình Titan.
