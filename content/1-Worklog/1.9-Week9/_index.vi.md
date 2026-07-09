---
title: "Worklog Tuần 9"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---


### Mục tiêu tuần 9:

* Thao tác và quản lý tài nguyên AWS bằng dòng lệnh (AWS CLI) thay vì thao tác thủ công trên Console.
* Hiểu khái niệm Infrastructure as Code (IaC) và các công cụ triển khai (CloudFormation, CDK, SAM).
* Nắm được quy trình CI/CD cho ứng dụng serverless để tự động hóa việc build và deploy.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học AWS CLI: cài đặt và cấu hình credential (aws configure), access key, region, output format <br> - Thực hành các lệnh cơ bản để quản lý S3, EC2, IAM (ví dụ aws s3 ls, aws ec2 describe-instances) <br> - Hiểu lợi ích của CLI trong việc tự động hóa và viết script | 15/06/2026 | 15/06/2026 | <https://000011.awsstudygroup.com/> |
| 3 | - Tìm hiểu AWS CloudFormation: khái niệm Infrastructure as Code, template (YAML/JSON) và stack <br> - Học cấu trúc một template: Resources, Parameters, Outputs <br> - Hiểu cách CloudFormation tạo/cập nhật/xóa tài nguyên theo template một cách nhất quán | 16/06/2026 | 16/06/2026 | <https://000037.awsstudygroup.com/> |
| 4 | - Tìm hiểu AWS CDK (Cloud Development Kit): định nghĩa hạ tầng bằng ngôn ngữ lập trình (TypeScript/Python) <br> - So sánh CDK với CloudFormation: CDK sinh ra template CloudFormation nhưng viết bằng code <br> - Hiểu khái niệm construct, stack và app trong CDK | 17/06/2026 | 17/06/2026 | <https://000038.awsstudygroup.com/> |
| 5 | - Tìm hiểu AWS SAM (Serverless Application Model): framework chuyên cho ứng dụng serverless <br> - Học cấu trúc template SAM và các lệnh sam build, sam deploy, sam local <br> - Hiểu cách SAM đơn giản hóa việc định nghĩa Lambda, API Gateway và DynamoDB | 18/06/2026 | 18/06/2026 | <https://000080.awsstudygroup.com/> |
| 6 | - Tìm hiểu CI/CD cho ứng dụng serverless: khái niệm pipeline, các stage build → test → deploy <br> - Tìm hiểu các dịch vụ AWS liên quan (CodePipeline, CodeBuild, CodeDeploy) <br> - Hiểu lợi ích của tự động hóa deploy so với thao tác thủ công <br> - Tổng hợp và viết worklog tuần 9 | 19/06/2026 | 19/06/2026 | <https://000084.awsstudygroup.com/> |

### Kiến thức thu được trong tuần:

* **AWS CLI:** biết cách cấu hình credential và dùng dòng lệnh để quản lý tài nguyên, phục vụ tự động hóa và viết script.
* **Infrastructure as Code:** hiểu triết lý IaC — quản lý hạ tầng bằng file khai báo, giúp tái lập nhất quán và kiểm soát phiên bản.
* **CloudFormation:** nắm được cấu trúc template (Resources, Parameters, Outputs) và cơ chế stack tạo/cập nhật/xóa tài nguyên.
* **CDK & SAM:** phân biệt CDK (định nghĩa hạ tầng bằng code) và SAM (framework tối ưu cho serverless), hiểu chúng đều biên dịch xuống CloudFormation.
* **CI/CD:** hiểu quy trình pipeline build → test → deploy và các dịch vụ AWS Code* để tự động hóa triển khai.

### Kết quả đạt được tuần 9:

* Biết thao tác và quản lý AWS bằng CLI.
* Hiểu Infrastructure as Code ở mức cơ bản và phân biệt được CloudFormation, CDK, SAM.
* Nắm được quy trình CI/CD cho ứng dụng serverless.
* Project có hướng triển khai chuyên nghiệp hơn, tự động hóa thay vì chỉ thao tác thủ công trên Console.
