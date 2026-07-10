---
title: "Event 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# BÀI THU HOẠCH: FCAJ COMMUNITY DAY

## 1. Tổng Quan Sự Kiện

- **Mục đích:**
  - Tạo cơ hội kết nối với các thành viên trong cộng đồng công nghệ.
  - Học hỏi từ các chuyên gia về Điện toán đám mây (Cloud) và Trí tuệ nhân tạo (GenAI).
  - Chia sẻ các giải pháp thực tiễn: từ tối ưu hóa câu lệnh LLM, kiến trúc mạng, đến triển khai hệ thống Multi-Agent AI đạt chuẩn bảo mật doanh nghiệp.
- **Thời gian:** 09:00 - 12:00, ngày 23/05/2026.
- **Địa điểm:** Tầng 36, tòa nhà Bitexco Financial Tower, Thành phố Hồ Chí Minh, Việt Nam.
- **Vai trò:** Người tham dự.

## 2. Danh Sách Diễn Giả

- **Nguyễn Gia Hưng:** Solution Architect tại AWS Việt Nam & Người sáng lập FCJ.
- **Tịnh Trương:** Platform Engineer tại GoTymeX.
- **Phạm Nguyễn Hải Anh:** Cloud Consultant tại G-AsiaPacific Vietnam.
- **Nguyễn Tuấn Thịnh:** DevOps Engineer tại First Cloud AI Journey.
- **Team UTMorpho:** Nhóm phát triển dự án đạt giải tại LotusHacks 2026.
- **Duc Dao:** Solution Architect tại Cloud Kinetics.
- **Vy Lam:** Senior Business Systems Analyst tại VPBank.

## 3. Nội Dung Tóm Tắt Từ Các Phiên Chia Sẻ

### Xu Hướng Nghề Nghiệp & Kỹ Năng Kỷ Nguyên AI (Diễn giả Nguyễn Gia Hưng)

- **Nghịch lý của AI:** Khi AI giúp việc phát triển phần mềm rẻ và nhanh hơn, nhu cầu tạo ra phần mềm từ thị trường sẽ tăng lên bùng nổ (tương tự như việc đèn LED giá rẻ làm bùng nổ nhu cầu chiếu sáng).
- **Yêu cầu nhân sự:** Bằng đại học vẫn là một "tấm vé" cần thiết trong tuyển dụng, nhưng kiến thức kỹ thuật đơn thuần không còn đủ để cạnh tranh.
- **Tập trung vào Use Cases và Sản Phẩm (Product):** Kỹ sư cần nắm vững bài toán thực tế của từng lĩnh vực (tài chính, ngân hàng…) và phải có năng lực đóng gói thành sản phẩm thực tế để thuyết phục doanh nghiệp, thay vì chỉ dừng lại ở các bản demo.
- **Tốc độ là then chốt:** Sức mạnh của AI tăng lên gấp đôi mỗi 4 tháng, đòi hỏi người làm công nghệ không được phép trì hoãn việc cập nhật kiến thức.

### Tối Ưu Hóa Context Cho AI (Diễn giả Tịnh Trương)

- Nhiều câu trả lời sai của AI không phải do mô hình kém mà do thiếu ngữ cảnh.
- **Khung ngữ cảnh hiệu quả (4 bước):** Mục tiêu (Goal) $\rightarrow$ Thông tin liên quan (Relevant info) $\rightarrow$ Ràng buộc (Constraints) $\rightarrow$ Tiêu chí thành công (Success criteria).

### Ứng Dụng Trợ Lý AI Với Amazon Q (Diễn giả Phạm Nguyễn Hải Anh)

- Tự động hóa các tác vụ văn phòng lặp đi lặp lại bằng AI Agent, hỗ trợ kết nối an toàn với hơn 40 nguồn dữ liệu doanh nghiệp (S3, Databases…).

### Tối Ưu Hạ Tầng Mạng Với Amazon CloudFront (Diễn giả Nguyễn Tuấn Thịnh)

- Sử dụng CloudFront cùng AWS Shield và WAF để giải quyết bài toán chống DDoS, bảo vệ máy chủ gốc (Origin Cloaking) và kiểm soát chi phí hiệu quả bằng các gói cước cố định.

### Xây Dựng Sản Phẩm Thực Tế Dưới Áp Lực Thời Gian (Team UTMorpho)

- Hành trình 36 giờ xây dựng sản phẩm tại hackathon chứng minh rằng những ý tưởng tốt nhất luôn xuất phát từ các vấn đề thực tế trong công việc hàng ngày.
- Trong phát triển phần mềm, sự đồng bộ và thấu hiểu của đội ngũ quan trọng hơn việc có quá nhiều ý tưởng.

### Sự Thật Về Tính Bất Định Của LLM (Diễn giả Duc Dao)

- Ngay cả khi đặt tham số Temperature = 0 (T=0) để mong chờ kết quả tuyệt đối, AI vẫn có thể trả về các đáp án khác nhau với độ sai lệch lên tới 70%.
- Nguyên nhân cốt lõi: Do đặc tính xử lý số thực dấu phẩy động trên kiến trúc GPU và việc gộp các truy vấn (inference batching) của các nhà cung cấp nền tảng.

### Hệ Thống Multi-Agent Chuẩn Doanh Nghiệp (Diễn giả Vy Lam)

- Khi chấm điểm tín dụng cho startup, mô hình AI đơn lẻ (Single Agent) thường thất bại do thiếu hụt thông tin, quá tải ngữ cảnh và không có cơ chế kiểm tra chéo.
- Việc sử dụng kiến trúc Multi-Agent (đóng vai trò như một hội đồng thẩm định ảo với các đặc vụ phân tích tài chính, thị trường, rủi ro) giúp tăng độ tin cậy, lưu vết rõ ràng (Audit trail) và giảm tới 95% thời gian cũng như chi phí xử lý hồ sơ.

## 4. Giá Trị Và Bài Học Nhận Được

### Về Mặt Tư Duy

- **Tư duy Enterprise-grade:** Khi đưa AI vào hệ thống doanh nghiệp, "chạy được" là chưa đủ. Hệ thống phải đảm bảo bảo mật, tuân thủ dữ liệu (Compliance) và vận hành ổn định ngay từ ngày đầu thiết kế.
- **Góc nhìn về giá trị của kỹ sư:** Giá trị cốt lõi không nằm ở việc viết code nhanh, mà ở việc thấu hiểu bài toán nghiệp vụ (Use cases) và biến nó thành sản phẩm hoàn thiện.
- **Chấp nhận tính xác suất của AI:** Không nên thiết kế ứng dụng kỳ vọng LLM sẽ luôn chính xác 100% như code thông thường, mà phải xây dựng các cơ chế dự phòng (ví dụ: dùng định dạng JSON có cấu trúc) để xử lý sự bất định.

### Ứng Dụng Vào Công Việc & Học Tập

- Thay vì để T=0 khi cần câu trả lời logic, việc đặt mức T=0.1 là một điểm cân bằng tốt để ngăn LLM bị kẹt vào các vòng lặp văn bản (looping) vô tận.
- Cách nhìn nhận thực tế hơn khi bắt đầu một dự án mới: tránh lan man vào nhiều tính năng ảo tưởng, mà phải tập trung giải quyết những "nỗi đau" (frustration) có thật giống như bài học từ nhóm UTMorpho.
- Nắm được quy trình triển khai AI từ máy cá nhân lên môi trường sản phẩm thực tế trên nền tảng AWS (thông qua Docker, ECR, Bedrock, và API Gateway).

## 5. Trải Nghiệm Cá Nhân Tại Sự Kiện

- **Phá vỡ định kiến:** Sự kiện giúp tôi nhận ra nhiều ngộ nhận phổ biến khi dùng AI, điển hình như ảo tưởng về sự "tuyệt đối" khi cấu hình LLM hay những rủi ro khi chỉ sử dụng một AI duy nhất (Single Agent) cho các quyết định quan trọng.
- **Cảm hứng thực hành:** Lắng nghe câu chuyện chạy nước rút 36 giờ của nhóm hackathon UTMorpho đã truyền cho tôi sự hào hứng và động lực để thử thách bản thân tham gia vào các sân chơi công nghệ cường độ cao trong tương lai. Lời khẳng định "thị trường thay đổi mỗi 4 tháng" từ anh Gia Hưng càng thôi thúc tôi hành động ngay.
- **Định hướng nghề nghiệp:** Qua case study tín dụng ngân hàng, tôi hiểu rõ hơn cách các tập đoàn lớn đang định hình tiêu chuẩn khắt khe (6 trụ cột doanh nghiệp) ra sao, từ đó biết mình cần trau dồi thêm các kỹ năng về Security (Bảo mật) và Deployment (Triển khai hệ thống) thay vì chỉ biết viết code đơn thuần.