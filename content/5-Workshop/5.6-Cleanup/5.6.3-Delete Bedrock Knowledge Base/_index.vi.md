---
title : "Xóa Bedrock Knowledge Base"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.6.3. </b> "
---

#### Xóa Bedrock Knowledge Base

Trong bước này, chúng ta sẽ xóa Knowledge Base và chỉ mục S3 Vectors phía sau nó — tài nguyên tốn chi phí nhất nếu để chạy lâu.

#### Các bước thực hiện

1. Mở **Amazon Bedrock Console → Knowledge Bases**.
2. Chọn Knowledge Base của bạn (vd `rag-kb`).
3. Nhấn **Delete**.
4. Nhập đúng văn bản xác nhận và nhấn **Delete**.
5. Vào **S3 → Vector buckets**, xóa vector bucket/index được tạo cho Knowledge Base này nếu chưa tự động bị xóa.

![delete kb](/images/5-Workshop/5.6-Cleanup/delete-kb.png)

{{% notice warning %}}
Xóa Knowledge Base **không** tự xóa IAM service role mà nó tạo ra, cũng không xóa S3 bucket chứa tài liệu nguồn. Những phần này được xóa ở các bước riêng.
{{% /notice %}}

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh **Bedrock → Knowledge Bases** — KB của bạn không còn xuất hiện; refresh thêm **S3 → Vector buckets** để xác nhận vector bucket/index đã biến mất.
{{% /notice %}}

#### Kết quả

Bedrock Knowledge Base và kho S3 Vectors đã được xóa.
