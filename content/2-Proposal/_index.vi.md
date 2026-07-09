---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Hệ thống Chatbot Hỏi – Đáp Tài liệu Nội bộ ứng dụng RAG trên AWS Serverless
## Internal Document Q&A Chatbot — Retrieval-Augmented Generation (RAG)

| Hạng mục | Thông tin |
| --- | --- |
| Tên đề tài | Hệ thống Chatbot hỏi đáp tài liệu nội bộ ứng dụng RAG trên AWS Serverless |
| Loại kiến trúc | Serverless (không quản lý máy chủ), trả tiền theo mức dùng |
| Nền tảng | Amazon Web Services (AWS) — region us-east-1 (N. Virginia) |
| Lõi AI | Amazon Bedrock Knowledge Base · Titan Embeddings V2 · Nova Lite · S3 Vectors |
| Đối tượng dùng | Nhân viên & quản trị viên nội bộ của tổ chức (phân quyền theo vai trò) |
| Chi phí định hướng | Gần $0 khi nhàn rỗi; ~$5–10/tháng ở quy mô nội bộ, lưu lượng nhẹ |

### 1. Tóm tắt điều hành

Dự án xây dựng một **chatbot hỏi – đáp tài liệu nội bộ** cho phép nhân viên đặt câu hỏi bằng ngôn ngữ tự nhiên và nhận về câu trả lời chính xác, **bám sát nội dung tài liệu chính thống của tổ chức và luôn kèm trích dẫn nguồn** để người dùng kiểm chứng. Hệ thống áp dụng kỹ thuật **RAG (Retrieval-Augmented Generation)**: thay vì để mô hình ngôn ngữ tự "bịa", hệ thống truy hồi đúng đoạn văn liên quan trong kho tài liệu rồi mới sinh câu trả lời dựa trên đoạn đó.

Toàn bộ hệ thống triển khai theo mô hình **Serverless trên AWS**: không quản lý máy chủ, tự động co giãn theo tải và chỉ trả tiền theo mức sử dụng thực tế. Giao diện là ứng dụng web React (Vite + TypeScript) phân phối qua Amazon CloudFront; người dùng đăng nhập bằng Amazon Cognito; phần xử lý chạy trên AWS Lambda sau API Gateway; lõi RAG do Amazon Bedrock Knowledge Base đảm nhiệm với Titan Embeddings V2, kho vector S3 Vectors và mô hình Amazon Nova Lite sinh câu trả lời.

Kiến trúc gồm **ba tầng rõ ràng**: (1) tầng Giao diện/Edge phân phối ứng dụng web và xác thực người dùng; (2) tầng API & Xử lý điều phối luồng RAG, kiểm quyền và lưu lịch sử hội thoại; (3) tầng AI (Amazon Bedrock) truy hồi tri thức và sinh câu trả lời có trích dẫn. Đề tài bám sát định hướng **First Cloud Journey**: một use-case Serverless thực tế, dùng trực tiếp các dịch vụ quản trị của AWS thay vì tự dựng máy chủ, gắn với một nhu cầu doanh nghiệp có thật.

### 2. Tuyên bố vấn đề

*Vấn đề hiện tại*

Tại nhiều tổ chức, tri thức nội bộ nằm rải rác ở nhiều định dạng và nhiều nơi (PDF, Word, wiki, email, thư mục chia sẻ), dẫn đến:

- **Tìm kiếm mất thời gian:** nhân viên phải mở nhiều file và đọc thủ công chỉ để lấy một thông tin nhỏ.
- **Search từ khóa hạn chế:** công cụ tìm truyền thống chỉ khớp từ khóa, không hiểu ngữ nghĩa câu hỏi.
- **Tri thức phân mảnh & lỗi thời:** khó biết đâu là bản mới nhất, dễ trả lời theo tài liệu cũ.
- **Rủi ro khi dùng LLM "trần":** ChatGPT/LLM công cộng không biết dữ liệu nội bộ, dễ bịa (hallucination) và tiềm ẩn rò rỉ dữ liệu.
- **Phụ thuộc người có kinh nghiệm:** câu hỏi lặp lại dồn về một số ít nhân sự, tạo nút thắt cổ chai.

*Giải pháp*

Hệ thống giải quyết các vấn đề trên với các tính năng cốt lõi:

- **Hỏi đáp ngôn ngữ tự nhiên có trích dẫn:** trả lời đúng trọng tâm, chỉ dựa trên tài liệu đã nạp, kèm nguồn để kiểm chứng; đánh dấu khi câu hỏi nằm ngoài phạm vi tài liệu.
- **Hội thoại đa lượt:** ghi nhớ ngữ cảnh các câu hỏi trước (bảng ChatHistory) để trả lời mạch lạc theo mạch trao đổi.
- **Trang quản trị tài liệu:** admin tải tài liệu lên, hệ thống tự parse, cắt chunk, tạo embedding và cập nhật kho vector — không thao tác thủ công.
- **Phân quyền theo vai trò (RBAC):** kiểm soát ai được dùng chatbot, ai được quản trị tài liệu và người dùng.
- **Hạ tầng "dựng bằng script":** bộ script provision/finalize tạo lại toàn bộ hệ thống trên một tài khoản AWS mới (idempotent), phục vụ phần Workshop.

*Hạ tầng AWS*

- **AWS WAF + Amazon CloudFront + S3 (private + OAC)** phân phối web app React qua HTTPS toàn cầu, WAF lọc request độc hại trước CloudFront.
- **Amazon Cognito** đăng nhập, cấp JWT, tích hợp JWT authorizer.
- **Amazon API Gateway (HTTP API) + AWS Lambda** xử lý toàn bộ nghiệp vụ serverless (12 functions, 10 routes).
- **Amazon DynamoDB** lưu trữ dữ liệu hệ thống (4 bảng, On-Demand + TTL).
- **Amazon Bedrock Knowledge Base** tự parse, chunk, embed và truy hồi tri thức.
- **Amazon Titan Text Embeddings V2 (512 chiều)** tạo embedding tiết kiệm chi phí.
- **Amazon Nova Lite** sinh câu trả lời kèm trích dẫn.
- **Amazon S3 Vectors** kho vector chi phí thấp do Bedrock KB quản lý.
- **Amazon CloudWatch + AWS Budgets** giám sát hệ thống và cảnh báo chi phí.
- **AWS IAM** least-privilege, mỗi Lambda một role riêng.

*Lợi ích và hoàn vốn đầu tư (ROI)*

- Nhân viên tra cứu tri thức nội bộ trong vài giây thay vì đọc thủ công hàng loạt tài liệu.
- Câu trả lời grounded theo tài liệu chính thống, có trích dẫn — loại bỏ rủi ro bịa và rò rỉ dữ liệu của LLM công cộng.
- Không cần quản lý server, tự động mở rộng theo nhu cầu nhờ kiến trúc Serverless.
- Chi phí gần $0 khi nhàn rỗi, ~$5–10/tháng ở quy mô nội bộ; dễ bổ sung tài liệu mới trong tương lai.

### 3. Kiến trúc giải pháp

Nền tảng áp dụng kiến trúc **AWS Serverless 3 tầng** với AWS Lambda điều phối nghiệp vụ và Amazon Bedrock Knowledge Base đảm nhiệm lõi RAG. Toàn bộ tài nguyên đặt tại region us-east-1.

![Kiến trúc AWS Serverless 3 tầng](/images/2-Proposal/architecture.png)

*Dịch vụ AWS sử dụng*

| Dịch vụ | Mục đích |
| --- | --- |
| AWS WAF | Tường lửa ứng dụng web đặt trước CloudFront: lọc request độc hại, chặn tấn công phổ biến (SQLi, XSS), giới hạn tần suất |
| Amazon CloudFront | CDN phân phối web app React qua HTTPS, kết hợp OAC bảo vệ bucket |
| Amazon S3 (Static) | Lưu trữ file build React + Vite (bucket private, chỉ CloudFront truy cập) |
| Amazon S3 (Data Source) | Lưu tài liệu gốc (PDF/DOCX/TXT) làm data source cho Knowledge Base |
| Amazon API Gateway | HTTP API, 10 routes, Cognito JWT authorizer (rẻ hơn REST API) |
| Amazon Cognito | Đăng ký, đăng nhập, quản lý phiên, cấp JWT |
| AWS Lambda | 12 functions (Node.js 20, TypeScript) xử lý toàn bộ nghiệp vụ backend |
| Amazon DynamoDB | 4 bảng NoSQL (On-Demand + TTL) lưu users, roles, documents, chat history |
| Amazon Bedrock Knowledge Base | Lõi RAG: tự parse + chunk + embed + truy hồi kèm trích dẫn |
| Amazon Titan Embeddings V2 | Tạo embedding 512 chiều — tiết kiệm ~50% chi phí lưu vector |
| Amazon Nova Lite | LLM sinh câu trả lời từ ngữ cảnh truy hồi |
| Amazon S3 Vectors | Kho vector chi phí thấp (rẻ hơn ~90% so với OpenSearch Serverless) |
| Amazon CloudWatch | Giám sát logs, metrics, alarm lỗi/latency/throttling |
| AWS Budgets | Cảnh báo email khi chi phí vượt ngưỡng (vd $10/tháng) |
| AWS IAM | Least-privilege: mỗi Lambda một role riêng, chỉ cấp đúng quyền cần |

### 4. Tính năng chi tiết

*Đối tượng người dùng*

- **Nhân viên nội bộ (user):** tra cứu, đặt câu hỏi và nhận trả lời kèm trích dẫn qua giao diện chat.
- **Quản trị viên (admin):** tải và quản lý tài liệu, quản lý người dùng và vai trò.
- Hệ thống phục vụ nội bộ, giới hạn truy cập qua đăng nhập Cognito và phân quyền RBAC.

*Luồng truy vấn (Query — người dùng hỏi đáp)*

1. Người dùng mở app: request đi qua AWS WAF → CloudFront; CloudFront phục vụ React từ S3 (bucket private, truy cập qua OAC).
2. Người dùng đăng nhập ngay trên web app (đã tải qua CloudFront), không gọi thẳng Cognito; app xác thực với Cognito và nhận JWT (chứa định danh người dùng, kèm vai trò).
3. App gọi API Gateway (HTTP API) kèm JWT; Cognito JWT authorizer xác minh token.
4. Lambda orchestrator kiểm quyền CHAT_USE và đọc lịch sử gần nhất từ ChatHistory để giữ ngữ cảnh đa lượt.
5. Lambda gọi Bedrock KB RetrieveAndGenerate: KB truy hồi đoạn liên quan từ S3 Vectors, Nova Lite sinh câu trả lời kèm trích dẫn.
6. Lambda ghi lượt hỏi – đáp (câu hỏi, trả lời, nguồn, độ trễ, isAnswered) vào ChatHistory và trả kết quả về người dùng.

*Luồng nạp tài liệu (Ingestion — quản trị viên tải lên)*

1. Admin yêu cầu upload → Lambda cấp presigned URL, file được ghi thẳng vào S3 data source bucket.
2. Sự kiện S3 ObjectCreated kích hoạt Lambda ingest.
3. Lambda ghi metadata vào bảng Documents (kèm kbIngestionJobId) và gọi Bedrock StartIngestionJob.
4. Knowledge Base tự lo toàn bộ: parse → cắt chunk → tạo embedding (Titan V2) → ghi vào S3 Vectors — không cần Textract, Step Functions hay Lambda chunk riêng.
5. Lambda cập nhật trạng thái Documents khi job hoàn tất (processing → active).

*Quản trị & phân quyền (RBAC)*

- Vai trò lưu trong DynamoDB với danh sách quyền denormalize: DOC_UPLOAD, DOC_DELETE, DOC_VIEW, CHAT_USE, USER_MANAGE.
- Trang quản trị: quản lý tài liệu (upload, cập nhật, xoá, versioning) và quản lý người dùng (tạo, sửa, khoá).
- Versioning tài liệu: đặt bản cũ inactive → xoá khỏi S3 → re-sync StartIngestionJob, tránh trả lời theo bản cũ.

### 5. Triển khai kỹ thuật

*Công nghệ sử dụng*

| Thành phần | Công nghệ |
| --- | --- |
| Frontend | React + Vite (TypeScript), GSAP — giao diện 3D dark glass |
| Phân phối web | AWS WAF + Amazon CloudFront + S3 (static hosting, bucket private + OAC) |
| Backend (compute) | AWS Lambda (Node.js 20, TypeScript, AWS SDK v3) — 12 functions |
| API | Amazon API Gateway (HTTP API) + Cognito JWT authorizer — 10 routes |
| Xác thực & phân quyền | Amazon Cognito (đăng nhập, JWT) + RBAC lưu trong DynamoDB |
| Lõi RAG | Amazon Bedrock Knowledge Base (tự parse + chunk + embed) |
| Embedding | Amazon Titan Text Embeddings V2 (512 chiều — tiết kiệm) |
| LLM sinh trả lời | Amazon Nova Lite |
| Kho vector | Amazon S3 Vectors (do Bedrock KB quản lý) |
| Cơ sở dữ liệu | Amazon DynamoDB (NoSQL, On-Demand + TTL) — 4 bảng |
| Giám sát / cảnh báo | Amazon CloudWatch + AWS Budgets |
| Hạ tầng dựng bằng script | Bộ script provision/finalize TypeScript (idempotent) |

*Lambda Functions — 12 functions*

| Function | Nhiệm vụ |
| --- | --- |
| rag-chat | Orchestrator RAG: kiểm quyền, đọc lịch sử, gọi RetrieveAndGenerate, ghi ChatHistory |
| rag-chatHistory | Trả lịch sử hội thoại của người dùng |
| rag-me | Trả hồ sơ + vai trò + quyền của người dùng hiện tại |
| rag-uploadUrl | Cấp presigned URL cho admin upload tài liệu thẳng lên S3 |
| rag-listDocuments | Liệt kê tài liệu + trạng thái ingestion |
| rag-updateDocument | Cập nhật metadata/trạng thái tài liệu |
| rag-deleteDocument | Xoá tài liệu khỏi S3 + Documents, re-sync KB |
| rag-ingest | S3 event → ghi metadata, StartIngestionJob, poll trạng thái (timeout 300s) |
| rag-contextualize | Xử lý chunk/ngữ cảnh hóa bất đồng bộ (timeout 600s) |
| rag-listUsers | Liệt kê người dùng (trang quản trị) |
| rag-createUser | Tạo người dùng mới (Cognito + bảng Users) |
| rag-updateUser | Cập nhật vai trò/trạng thái người dùng |

*DynamoDB — 4 bảng*

Thiết kế đúng tư duy NoSQL: khóa UUID/chuỗi, GSI để tra cứu, denormalize quyền (không bảng nối), TTL tự dọn log cũ. Chia 2 nhóm: **Phân quyền** (Users, Roles) và **Nghiệp vụ** (Documents, ChatHistory).

| Bảng | Partition Key | Sort Key | Index | Dùng cho |
| --- | --- | --- | --- | --- |
| Users | cognitoSub | — | email-index | Hồ sơ user (mirror Cognito) + vai trò |
| Roles | roleName | — | — | Vai trò + danh sách quyền (denormalize) |
| Documents | docId | — | status-index | Metadata tài liệu + trạng thái ingestion |
| ChatHistory | userId | timestamp | — | Lịch sử hỏi – đáp (bật TTL tự xoá log cũ) |

*API Gateway — 10 routes*

| Route | Method | Dùng cho |
| --- | --- | --- |
| /chat | POST | Gửi câu hỏi, nhận trả lời kèm trích dẫn (RAG) |
| /chat-history | GET | Lấy lịch sử hội thoại |
| /me | GET | Lấy hồ sơ + quyền người dùng hiện tại |
| /documents | GET | Liệt kê tài liệu |
| /documents/upload-url | POST | Cấp presigned URL upload tài liệu |
| /documents/{id} | PUT | Cập nhật tài liệu |
| /documents/{id} | DELETE | Xoá tài liệu |
| /users | GET | Liệt kê người dùng (admin) |
| /users | POST | Tạo người dùng (admin) |
| /users/{sub} | PATCH | Cập nhật người dùng (admin) |

*Quyết định thiết kế & bảo mật nổi bật*

- **Loại bỏ pipeline thừa:** Bedrock KB đã tự parse + chunk + embed nên bỏ hẳn Textract, Step Functions và Lambda chunk riêng — tránh trả tiền hai lần.
- **S3 Vectors thay OpenSearch:** tránh phí tối thiểu của OpenSearch Serverless (~$700/tháng), rẻ hơn tới ~90%.
- **Không dùng VPC/NAT:** Lambda gọi DynamoDB & Bedrock qua IAM, tránh chi phí NAT Gateway (~$32/tháng).
- **AWS WAF + CloudFront + OAC:** WAF chặn tấn công trước CloudFront; bucket web để private, chỉ CloudFront truy cập; tách riêng bucket web tĩnh và bucket data source.
- **IAM least-privilege:** mỗi Lambda một role riêng, chỉ cấp đúng quyền cần thiết.

### 6. Lộ trình & Mốc triển khai

Kế hoạch chia thành các giai đoạn (sprint) trong khuôn khổ workshop thực tập. **Tổng thời gian thực hiện project: 7 tuần (25/05 – 10/07/2026), theo worklog Tuần 6–12.**

| Giai đoạn | Nội dung chính | Kết quả bàn giao | Thời gian cụ thể |
| --- | --- | --- | --- |
| 1. Nghiên cứu & thiết kế | Nghiên cứu RAG, chọn dịch vụ AWS, vẽ kiến trúc 3 tầng, thiết kế DB DynamoDB | Sơ đồ kiến trúc + thiết kế DB | 25/05 – 29/05/2026 |
| 2. Dựng hạ tầng | Viết script provision: DynamoDB, S3, Cognito, IAM, Lambda, API Gateway | Hạ tầng tạo tự động + seed | 01/06 – 05/06/2026 |
| 3. Lõi RAG (Bedrock) | Tạo Knowledge Base, Titan V2 512 chiều, Nova Lite; luồng ingestion + query | KB hoạt động, e2e-rag pass | 08/06 – 12/06/2026 |
| 4. Backend & API | 12 Lambda functions, 10 routes, JWT authorizer, RBAC | API chạy, token-test pass | 15/06 – 19/06/2026 |
| 5. Frontend | React + Vite: đăng nhập, chat đa lượt, trang upload/quản trị | Web app dùng được | 22/06 – 26/06/2026 |
| 6. Kiểm thử & tối ưu | Test E2E, chỉnh IAM/timeout, đặt AWS Budgets, tối ưu chi phí | Hệ thống ổn định | 29/06 – 03/07/2026 |
| 7. Triển khai & Workshop | Deploy web (CloudFront + OAC), viết workshop kèm ảnh chụp từng bước | Bản deploy + tài liệu workshop | 06/07 – 10/07/2026 |

### 7. Ước tính ngân sách

Vì là kiến trúc Serverless và tận dụng free tier, chi phí gần như **bằng 0 khi nhàn rỗi** và chỉ phát sinh theo mức sử dụng.

*Ước tính chi phí hàng tháng*

| Dịch vụ | Ước tính | Ghi chú |
| --- | --- | --- |
| AWS Lambda | ~$0.00 | Free tier 1M requests/tháng |
| Amazon DynamoDB | ~$0.00 | On-Demand, free tier 25 GB |
| Amazon S3 (static + data source) | ~$0.05 | Web build + tài liệu gốc |
| Amazon API Gateway (HTTP API) | ~$0.01 | Lưu lượng nội bộ nhẹ |
| Amazon CloudFront | $0.00 | Free tier 1 TB/tháng |
| Amazon Cognito | $0.00 | Free tier 50,000 MAU |
| Amazon S3 Vectors | ~$0.10 | Kho vector quy mô đồ án |
| Bedrock — Titan Embeddings V2 | ~$0.10 | Embedding khi nạp tài liệu |
| Bedrock — Nova Lite | ~$1–5 | Token sinh trả lời, tùy lưu lượng |
| Amazon CloudWatch + AWS Budgets | ~$0.00 | Free tier |
| **Tổng ước tính** | **~$5–10/tháng** | Chủ yếu từ token Bedrock; gần $0 khi nhàn rỗi |

*Tổng kết chi phí*

- **Chi phí khởi động:** $0 (không domain riêng, dùng domain CloudFront mặc định).
- **Chi phí vận hành hàng tháng:** ~$5–10 ở quy mô nội bộ lưu lượng nhẹ; gần $0 khi nhàn rỗi.
- **Guardrail:** AWS Budgets cảnh báo sớm khi vượt ngưỡng $10/tháng.

### 8. Đánh giá rủi ro

*Ma trận rủi ro*

| Rủi ro | Ảnh hưởng | Xác suất | Giải pháp giảm thiểu |
| --- | --- | --- | --- |
| Quota Bedrock account mới bị giới hạn (ThrottlingException) | Cao | Trung bình | Xin tăng quota ở Service Quotas, hoặc dùng account đã được cấp quota |
| Tạo Knowledge Base sai region → lỗi "KB does not exist" | Cao | Thấp | Bắt buộc tạo mọi tài nguyên ở us-east-1; script scan-kb.ts dò region |
| Rò rỉ Access Key / dữ liệu nội bộ | Cao | Thấp | Không commit .env; rotate key; IAM least-privilege; bucket private + OAC |
| Lambda timeout mặc định 3s khi Bedrock chậm | Trung bình | Trung bình | Đặt timeout 30s + memory 256MB (script set-lambda-timeout.ts) |
| Vượt chi phí ngoài dự kiến | Trung bình | Trung bình | AWS Budgets cảnh báo ngưỡng; tối ưu S3 Vectors / Nova Lite / On-Demand |
| Chatbot bịa khi hỏi ngoài tài liệu | Trung bình | Thấp | RAG grounded; đánh dấu isAnswered=false khi ngoài phạm vi tài liệu |
| Tên bucket S3 trùng toàn cầu (BucketAlreadyExists) | Thấp | Thấp | Đặt tên bucket theo hậu tố riêng, duy nhất toàn cầu |
| Tài liệu cập nhật gây trả lời theo bản cũ | Thấp | Trung bình | Versioning: bản cũ inactive → xoá khỏi S3 → re-sync StartIngestionJob |

### 9. Kết quả kỳ vọng

*Tiêu chí thành công*

- Đăng nhập, phân quyền, chat hỏi – đáp và quản trị tài liệu hoạt động đầy đủ theo vai trò.
- Câu trả lời grounded theo tài liệu, kèm trích dẫn nguồn; test E2E (e2e-rag.ts) hỏi đúng nội dung file vừa nạp phải trả lời đúng.
- Hội thoại đa lượt giữ ngữ cảnh mạch lạc; ghi latencyMs mỗi lượt, xử lý trong ngưỡng Lambda 30s.
- Nạp tài liệu tự động: upload → processing → active sau khi KB sync xong, không thao tác tay.
- Chi phí duy trì gần/trong free tier, dưới ngưỡng cảnh báo AWS Budgets ($10/tháng).
- Hệ thống ổn định, CloudWatch không có alarm nghiêm trọng; IAM least-privilege cho từng Lambda.

*Giá trị dài hạn*

- Kho tri thức nội bộ tích lũy dần, dễ bổ sung tài liệu và chuyên mục mới.
- Bộ script provision idempotent cho phép dựng lại toàn bộ hệ thống trên tài khoản AWS mới — tái sử dụng cho dự án tương lai.
- Kiến trúc Serverless dễ mở rộng: có thể nâng cấp streaming (Lambda Function URL / WebSocket), thêm MFA, thêm nguồn dữ liệu.
- Nhóm tích lũy kinh nghiệm thực tế AWS Serverless, GenAI/RAG trên Bedrock và full-stack TypeScript.
