---
title : "Xóa AWS Lambda"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.6.1. </b> "
---

#### Xóa AWS Lambda

Trong phần này, chúng ta sẽ xóa hàm AWS Lambda đã triển khai cho Backend.

#### Các bước thực hiện

1. Mở **AWS Lambda Console**.
2. Chọn hàm Lambda của dự án.
3. Chọn **Actions** → **Delete**.
4. Nhập **delete** để xác nhận.
5. Chọn **Delete**.

![lambda](/images/5-Workshop/5.6-Cleanup/delete-lambda.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh danh sách **Lambda → Functions** — hàm vừa xóa không còn xuất hiện. Lặp lại bước 2–5 cho 5 hàm `rag-*` còn lại, rồi refresh lần nữa để xác nhận cả 6 hàm đã biến mất.
{{% /notice %}}

#### Kết quả

Hàm AWS Lambda đã được xóa khỏi tài khoản AWS.