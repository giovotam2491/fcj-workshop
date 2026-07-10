---
title: "Chia sẻ, đóng góp ý kiến"
date: 2026-07-10
weight: 7
chapter: false
pre: " <b> 7. </b> "
---

Tại đây, em xin tự do chia sẻ ý kiến cá nhân về trải nghiệm của mình khi tham gia chương trình **Workforce Bootcamp – First Cloud AI Journey (FCAJ)** tại Amazon Web Services Việt Nam, nhằm giúp team FCAJ cải thiện dựa trên các hạng mục sau:

### Đánh giá chung

**1. Môi trường làm việc**

Môi trường làm việc rất thân thiện và cởi mở. Các thành viên FCAJ luôn sẵn sàng hỗ trợ khi em gặp khó khăn, kể cả ngoài giờ làm việc — có những lúc em debug lỗi CORS khi Lambda xử lý chat của Bedrock đến tận tối muộn, gửi câu hỏi lên channel thì vẫn có người phản hồi và gợi ý hướng đi. Các hoạt động cộng đồng FCAJ cũng tạo cơ hội để em kết nối với các bạn thực tập sinh và mentor khác ngoài công việc hằng ngày, khiến 12 tuần không cảm thấy đơn độc.

**2. Sự hỗ trợ của mentor / team admin**

Mentor hướng dẫn rất chi tiết và có lộ trình rõ ràng mỗi tuần — từ kiến thức nền tảng AWS và thiết kế kiến trúc ở những tuần đầu, đến việc review RAG pipeline, Cognito authentication flow và WAF configuration ở giai đoạn sau. Điều em trân trọng nhất là phương pháp của mentor: khi em gặp vướng mắc kỹ thuật (như lỗi phân quyền IAM, JWT sai loại token hay Lambda cold start quá lâu), mentor luôn khuyến khích em tự tìm hiểu và thử xử lý trước, chỉ gợi ý định hướng khi em thực sự bí. Nhờ vậy em rèn được khả năng tự giải quyết vấn đề thực sự, thay vì chỉ nhận đáp án có sẵn. Team admin cũng hỗ trợ rất nhanh — mọi thủ tục, lịch sự kiện, tài khoản AWS sandbox đều được lo chu đáo để em có thể toàn tâm cho việc học và làm dự án.

**3. Sự phù hợp giữa công việc và chuyên ngành học**

Là sinh viên ngành Công nghệ thông tin, chương trình phù hợp tốt với nền tảng lý thuyết học ở trường, đồng thời lấp đầy những khoảng trống thực tế mà chương trình đại học thường chưa đi sâu — đặc biệt là kiến trúc cloud serverless thực tế, Infrastructure as Code với AWS CDK, và ứng dụng AI tạo sinh (Generative AI) với Amazon Bedrock. Thay vì các bài tập rời rạc theo từng chủ đề, em được thực hành trên một dự án hoàn chỉnh end-to-end, từ thiết kế kiến trúc đến triển khai và vận hành — điều mà môi trường học thuật rất khó mô phỏng được.

**4. Cơ hội học hỏi & phát triển kỹ năng**

Trong 12 tuần, em đã đi từ việc chưa bao giờ dùng AWS CDK hay Amazon Bedrock, đến việc tự tay viết toàn bộ Infrastructure as Code, tích hợp RAG pipeline hoàn chỉnh, deploy frontend lên CloudFront với WAF bảo vệ và cấu hình Cognito authentication. Ngoài kỹ năng kỹ thuật, em cũng cải thiện được kỹ năng mềm như viết báo cáo tiến độ hằng tuần rõ ràng, quản lý thời gian trên một dự án nhiều module song song, và giải thích quyết định kỹ thuật trong các buổi check-in.

**5. Văn hóa & tinh thần đồng đội**

Văn hóa tại FCAJ rất tích cực và có tính hợp tác cao — mọi người nghiêm túc trong công việc nhưng bầu không khí vẫn thân thiện và khích lệ. Khi deadline tuần đến gần, các bạn trong nhóm tự nhiên hỗ trợ nhau, ai giải quyết được vấn đề gì thì chia sẻ lại luôn trên channel chung. Dù chỉ là thực tập sinh, em vẫn cảm thấy mình thực sự là một phần của tập thể chứ không phải người ngoài cuộc.

**6. Chính sách / phúc lợi cho thực tập sinh**

Chương trình có cơ cấu tuần rõ ràng và nhất quán, cung cấp tài liệu học tập kịp thời và cơ hội tham gia các sự kiện, buổi đào tạo nội bộ bổ ích. Tài khoản AWS sandbox được cấp với ngân sách hợp lý (em chỉ tiêu $4.2 trong cả 12 tuần nhờ thiết lập AWS Budgets ngay từ đầu), giúp em thoải mái thực hành mà không lo phát sinh chi phí ngoài ý muốn.

---

### Vài dòng chia sẻ thêm

Nhìn lại 12 tuần vừa qua, khoảnh khắc đáng nhớ nhất với em chắc chắn là lần đầu tiên chatbot của em trả lời đúng một câu hỏi dựa vào tài liệu nội bộ công ty — câu hỏi về quy trình nghỉ phép, và chatbot trích dẫn chính xác đoạn văn trong file PDF đã upload lên S3. Sau nhiều tuần cấu hình Bedrock Knowledge Base, debug lỗi IAM, vật lộn với Cognito JWT và sửa CORS lần này đến lần khác — cái khoảnh khắc thấy frontend, API Gateway, Lambda, Bedrock RAG và DynamoDB tất cả ráp lại hoạt động ăn khớp với nhau là một cảm giác mà em nghĩ sẽ nhớ mãi.

Nếu phải chọn một kỷ niệm "vừa vui vừa ám ảnh" thì chắc là buổi tối cặm cụi debug lỗi CORS mà mãi không tìm ra nguyên nhân — hóa ra là khi Lambda ném lỗi 500, nó trả về response không có CORS headers, còn API Gateway proxy lại nguyên xi response đó về browser. Sau khi tìm ra và fix bằng một helper module cho mọi Lambda response, em hiểu sâu hơn nhiều về cơ chế Lambda Proxy Integration so với đọc tài liệu thuần túy.

Có lẽ nếu không có sự hỗ trợ đó, em đã khó lòng đi trọn vẹn chặng đường 12 tuần và cho ra một sản phẩm hoàn chỉnh như vậy. Đây thực sự là một trong những trải nghiệm đáng nhớ nhất trong quãng thời gian sinh viên của em — không chỉ giúp em vững hơn về kỹ thuật AWS và AI, mà còn cho em thấy giá trị của một môi trường làm việc biết quan tâm và hỗ trợ lẫn nhau. Nếu có cơ hội, em rất mong được tiếp tục gắn bó với cộng đồng FCAJ trong tương lai.