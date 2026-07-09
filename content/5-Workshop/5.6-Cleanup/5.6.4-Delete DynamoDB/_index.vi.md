---
title : "Xóa bảng DynamoDB"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.6.4. </b> "
---

#### Xóa bảng DynamoDB

Trong bước này, chúng ta sẽ xóa 4 bảng đã tạo cho dự án: `Users`, `Roles`, `Documents`, `ChatHistory`.

#### Các bước thực hiện

1. Mở **DynamoDB Console → Tables**.
2. Chọn cả 4 bảng (`Users`, `Roles`, `Documents`, `ChatHistory`).
3. Nhấn **Delete**.
4. Bỏ chọn "Create a backup" nếu không cần, nhập **confirm**, và nhấn **Delete**.

![delete dynamodb](/images/5-Workshop/5.6-Cleanup/delete-dynamodb.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh **DynamoDB → Tables** — `Users`, `Roles`, `Documents`, `ChatHistory` không còn trong danh sách.
{{% /notice %}}

#### Kết quả

Cả 4 bảng DynamoDB đã được xóa khỏi tài khoản AWS.
