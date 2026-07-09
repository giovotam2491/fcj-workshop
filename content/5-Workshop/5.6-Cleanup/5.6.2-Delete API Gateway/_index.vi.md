---
title : "Xóa API Gateway"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.6.2. </b> "
---

#### Xóa API Gateway

Trong bước này, chúng ta sẽ xóa HTTP API cung cấp backend cho chatbot.

#### Các bước thực hiện

1. Mở **API Gateway Console → APIs**.
2. Tích vào **nút radio** ở đầu dòng ứng với `rag-chatbot-api` (hoặc tên bạn đặt lúc provision) — giờ đây là danh sách phẳng, không còn menu **Actions** dropdown nữa.
3. Bấm nút **Delete** ở góc trên bên phải (cạnh nút **Create API**).
4. Xác nhận xóa trong hộp thoại hiện ra.

![api gateway](/images/5-Workshop/5.6-Cleanup/delete-api-gateway.png)

{{% notice note %}}
Xóa API cũng sẽ xóa luôn các route, cấu hình JWT authorizer, và các integration tới 6 Lambda function — nhưng **không** xóa bản thân các Lambda function (cần xóa riêng, xem [5.6.1](../5.6.1-Delete%20Lambda/)).
{{% /notice %}}

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh **API Gateway → APIs** — `rag-chatbot-api` không còn xuất hiện trong danh sách.
{{% /notice %}}

#### Kết quả

API Gateway HTTP API đã được xóa khỏi tài khoản AWS.
