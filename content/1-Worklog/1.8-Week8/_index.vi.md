---
title: "Worklog Tuần 8"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8:
* Tối ưu hóa RAG Pipeline bằng cách tinh chỉnh Search Configuration và cấu hình Metadata.
* Triển khai cơ chế lưu giữ ngữ cảnh hội thoại ngắn hạn (Conversation Session State) để chatbot nhớ các câu hỏi trước đó.
* Viết script tự động đánh giá và kiểm thử chất lượng câu trả lời của chatbot (hạn chế tối đa lỗi ảo giác - hallucination).
* Cập nhật định dạng tài liệu nguồn trên S3 để tăng chất lượng phân mảnh (chunking).

### Các công việc triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu cách tinh chỉnh Search Configuration trong CDK/Console: thay đổi tham số `numberOfResults` (tăng từ 3 lên 5) để lấy được nhiều đoạn văn bản liên quan hơn cho LLM. | 08/06/2026 | 08/06/2026 | [Bedrock Search Configuration](https://docs.aws.amazon.com/bedrock/) |
| 3 | - Tìm hiểu và cấu hình tham số `sessionId` trong API `RetrieveAndGenerate`. <br> - Cho phép Bedrock tự động lưu giữ trạng thái hội thoại trên Cloud thay vì Backend phải gửi thủ công toàn bộ lịch sử tin nhắn vào prompt. | 09/06/2026 | 09/06/2026 | [Bedrock Session Management](https://docs.aws.amazon.com/bedrock/) |
| 4 | - Soạn thảo kịch bản kiểm thử chất lượng gồm 20 câu hỏi (trong đó có 5 câu hỏi lắt léo và 5 câu hỏi không có thông tin trong tài liệu). <br> - Viết script Node.js tự động gọi API `/chat` và ghi nhận kết quả ra file JSON. | 10/06/2026 | 10/06/2026 | Tài liệu cá nhân |
| 5 | - Chạy script đánh giá, phân tích tỷ lệ trả lời đúng và phát hiện lỗi ảo giác. <br> - Điều chỉnh System Prompt Template bổ sung các ràng buộc nghiêm ngặt về ngôn ngữ và phạm vi kiến thức. | 11/06/2026 | 11/06/2026 | Báo cáo đánh giá RAG |
| 6 | - Thử nghiệm convert tài liệu từ dạng PDF scan sang định dạng Markdown có cấu trúc rõ ràng (headings, bullet points) để OpenSearch phân mảnh chính xác hơn. <br> - Sync lại dữ liệu và viết báo cáo tuần 8. | 12/06/2026 | 12/06/2026 | Tài liệu công ty |

### Kiến thức thu được trong tuần:
* **Kỹ thuật Prompt Engineering cho RAG:** Cách viết system prompt mang tính ràng buộc cao (như áp dụng vai trò, quy định cách xử lý khi thiếu thông tin) để định hướng hành vi của mô hình ngôn ngữ lớn LLM.
* **Session State Management:** Cách sử dụng `sessionId` của Bedrock Runtime để hệ thống tự duy trì lịch sử hội thoại của user một cách tối ưu.
* **Tầm quan trọng của chất lượng dữ liệu đầu vào:** Nhận thức rõ định dạng dữ liệu (như Markdown với tiêu đề rõ ràng) giúp thuật toán tìm kiếm vector (vector search) trả về kết quả chính xác hơn nhiều so với file PDF dạng ảnh quét.

### Khó khăn gặp phải & Cách giải quyết:
* **Khó khăn:** Mô hình LLM đôi khi bị ảo giác (hallucination), tự bịa ra câu trả lời dựa trên kiến thức chung có sẵn của nó khi người dùng đặt các câu hỏi nằm ngoài phạm vi tài liệu công ty cung cấp.
* **Cách giải quyết:** Tiến hành viết lại cấu trúc Prompt Template tùy chỉnh, bổ sung chỉ thị nghiêm ngặt ép mô hình tuân thủ:
  "Bạn là trợ lý ảo hỗ trợ tra cứu tài liệu của công ty. Nhiệm vụ của bạn là CHỈ sử dụng thông tin từ các tài liệu tham khảo được cung cấp để trả lời câu hỏi. Nếu thông tin không có trong tài liệu, bạn PHẢI trả lời nguyên văn là: 'Tôi xin lỗi, thông tin này không có trong tài liệu nội bộ công ty.'. Không tự ý bịa đặt hoặc đưa ra các giả định nằm ngoài tài liệu. Câu trả lời phải bằng Tiếng Việt."
  Sau khi cập nhật prompt này, tỷ lệ ảo giác giảm về 0% trong các bài test.

### Kết quả đạt được tuần 8:
* Hoàn thành tối ưu hóa chất lượng tìm kiếm (Retrieval) của Bedrock.
* Tích hợp thành công cơ chế quản lý hội thoại thông minh thông qua Bedrock Session.
* Giảm thiểu hoàn toàn lỗi ảo giác (hallucination) nhờ Prompt Template mới.
* Chuyển đổi thành công tài liệu sang định dạng Markdown tăng độ chính xác của RAG.
