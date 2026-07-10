---
title: "Worklog Tuần 7"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7:
* Phát triển Lambda function cốt lõi (`chatWithKB`) xử lý hội thoại RAG.
* Sử dụng AWS SDK v3 (`@aws-sdk/client-bedrock-agent-runtime`) kết nối trực tiếp với Bedrock Knowledge Base.
* Định nghĩa System Prompt Template cho mô hình LLM Amazon Nova Lite trong mã nguồn API để kiểm soát chất lượng câu trả lời.
* Triển khai ghi lịch sử tin nhắn hỏi-đáp của cả User và Chatbot vào cơ sở dữ liệu DynamoDB.

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Cài đặt gói SDK `@aws-sdk/client-bedrock-agent-runtime` cho dự án Backend. <br> - Tìm hiểu các phương thức của client và cấu trúc request/response của lệnh `RetrieveAndGenerateCommand`. | 01/06/2026 | 01/06/2026 | [AWS SDK Bedrock Agent Runtime](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/bedrock-agent-runtime/) |
| 3 | - Viết mã nguồn Lambda handler `chatWithKB` (POST `/sessions/{sessionId}/chat`). <br> - Cấu hình tham số kết nối: Knowledge Base ID, Model ARN của Amazon Nova Lite và cấu hình kiểu sinh văn bản `KNOWLEDGE_BASE`. | 02/06/2026 | 02/06/2026 | Giao diện AWS Console |
| 4 | - Thiết lập Prompt Template tùy chỉnh (Custom Prompt Template) trong tham số `generationConfiguration` của API. <br> - Viết prompt hướng dẫn cụ thể: yêu cầu LLM trích dẫn chính xác nguồn, không tự ý bịa đặt thông tin nếu tài liệu không nhắc tới. | 03/06/2026 | 03/06/2026 | [Bedrock Custom Prompt Guide](https://docs.aws.amazon.com/bedrock/) |
| 5 | - Bổ sung logic lưu trữ: Sau khi nhận câu trả lời từ Bedrock, trích xuất văn bản trả lời cùng danh sách metadata nguồn tham khảo (citation sources). <br> - Ghi đồng thời 2 items (User Message và Bot Message kèm Citation) vào bảng DynamoDB. | 04/06/2026 | 04/06/2026 | Tài liệu cá nhân |
| 6 | - Phân quyền IAM policy cho phép Lambda thực thi hành động `bedrock:RetrieveAndGenerate`. <br> - Deploy CDK stack và test gọi API qua Postman để nhận câu trả lời thực tế. <br> - Tổng hợp và viết worklog tuần 7. | 05/06/2026 | 05/06/2026 | Báo cáo cá nhân |

### Kiến thức thu được trong tuần:
* **AWS SDK v3 for Bedrock:** Nắm vững cách khởi tạo và cấu hình Bedrock Agent Runtime client bằng JavaScript/TypeScript.
* **Cơ chế hoạt động của API `RetrieveAndGenerate`:** Hiểu cách AWS tự động thực hiện hai bước: Tìm kiếm ngữ cảnh phù hợp từ Vector Store (Retrieve) và đẩy ngữ cảnh đó cùng câu hỏi của người dùng vào LLM để sinh câu trả lời (Generate) trong một lần gọi API duy nhất.
* **Citation & Metadata:** Cách phân tích mảng `citations` trả về từ Bedrock để biết câu trả lời được rút ra từ file PDF nào và đoạn văn bản nào trên S3.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Lambda bị crash khi gọi API Bedrock với lỗi `AccessDeniedException: User: arn:aws:sts::...:assumed-role/LambdaRole is not authorized to perform: bedrock:RetrieveAndGenerate on resource: ...`.
* **Cách giải quyết:** Nguyên nhân do Lambda Execution Role mặc định chưa có quyền tương tác với Bedrock. Đã sửa lỗi bằng cách cập nhật CDK Stack, thêm một chính sách bảo mật IAM Policy mới cấp quyền `bedrock:RetrieveAndGenerate` và gán nó trực tiếp vào Lambda Execution Role:
  ```typescript
  lambdaFunction.addToRolePolicy(new iam.PolicyStatement({
    actions: ['bedrock:RetrieveAndGenerate'],
    resources: ['*'], // Có thể giới hạn ARN của Knowledge Base cụ thể
  }));
  ```

### Kết quả đạt được tuần 7:
* Tích hợp thành công RAG Pipeline của Bedrock Knowledge Base vào API Gateway và Lambda.
* API Chat `/chat` hoạt động hoàn chỉnh: nhận câu hỏi, tự động truy xuất tài liệu từ S3, gọi LLM Nova Lite sinh câu trả lời, trả về nguồn trích dẫn và lưu trữ lịch sử vào DynamoDB.
