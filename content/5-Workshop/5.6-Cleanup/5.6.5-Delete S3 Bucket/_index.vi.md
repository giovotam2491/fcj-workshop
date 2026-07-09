---
title : "Xóa S3 Bucket"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.6.5. </b> "
---

#### Xóa S3 bucket

Trong bước này, chúng ta sẽ xóa bucket chứa tài liệu nguồn (`DOCS_BUCKET`) và, nếu bạn đã triển khai frontend ở [5.4.4](../../5.4-Deploy-frontend/5.4.4-Connect%20Backend/), cả bucket hosting web tĩnh.

#### Các bước thực hiện

1. Mở **S3 Console → Buckets**.
2. Chọn bucket chứa tài liệu nguồn.
3. Nhấn **Empty**, nhập **permanently delete** và xác nhận — S3 bucket phải rỗng trước khi xóa được.
4. Sau khi rỗng, nhấn **Delete**, nhập tên bucket để xác nhận, và nhấn **Delete bucket**.
5. Lặp lại với bucket hosting web frontend, nếu có tạo.

![delete s3](/images/5-Workshop/5.6-Cleanup/delete-s3.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: refresh **S3 → Buckets** — bucket `DOCS_BUCKET` (và bucket web, nếu có tạo) không còn trong danh sách.
{{% /notice %}}

#### Tuỳ chọn — xoá CloudFront distribution và WAF Web ACL

Nếu bạn đã làm mục [5.4.4](../../5.4-Deploy-frontend/5.4.4-Connect%20Backend/), dọn luôn CloudFront (và WAF, nếu có bật — WAF vẫn tính phí theo Web ACL/rule cho đến khi bị xoá):

1. **CloudFront Console → Distributions** → chọn distribution của bạn → **Disable** (phải disable trước khi xoá được; có thể mất vài phút để áp dụng).
2. Khi **Status** hiện **Disabled**, chọn lại distribution đó → **Delete**.
3. Nếu có bật WAF: **WAF & Shield Console → Web ACLs** (region **Global (CloudFront)**) → chọn Web ACL của bạn → **Delete**. AWS sẽ chặn xoá nếu Web ACL vẫn đang gắn với 1 distribution, nên chỉ làm bước này sau khi đã xoá (hoặc gỡ liên kết) distribution.

{{% notice warning %}}
Nếu distribution kẹt ở trạng thái **Deploying** sau khi bấm Disable, chỉ cần đợi và refresh chứ đừng bấm lại nhiều lần. Xoá Web ACL mới là bước dừng khoản phí ~$5/tháng của WAF — chỉ xoá CloudFront distribution **không** tự động xoá Web ACL.
{{% /notice %}}

#### Kết quả

(Các) S3 bucket đã được xóa khỏi tài khoản AWS.
