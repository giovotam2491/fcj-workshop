---
title : "Triển khai lên S3 + CloudFront (tuỳ chọn)"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

Chạy bằng `npm run dev` đã đủ để kiểm thử toàn bộ workshop. Bước này hướng dẫn cách host ứng dụng thật, đúng theo mô hình các trang tĩnh production thường được triển khai trên AWS — chính là cách bản demo thật của dự án này đang chạy.

#### Bước 1 — Build bản production

```bash
cd frontend
npm run build
```

Lệnh này xuất file tĩnh vào `frontend/dist/` (`index.html` + các file JS/CSS đã hash tên).

![vite build](/images/5-Workshop/5.4-Deploy-frontend/vite-build.png)

#### Bước 2 — Tạo S3 bucket riêng tư để hosting web

1. **S3 Console → Create bucket**. Tên bucket phải **duy nhất toàn cầu**, vd `rag-web-<tên-của-bạn>` (phải bắt đầu bằng `rag-web-` để khớp statement `S3ProjectBuckets` trong IAM policy ở mục 5.2).
2. **AWS Region**: **US East (N. Virginia) us-east-1**.
3. **Block Public Access settings for this bucket**: giữ nguyên **cả 4 ô đều BẬT** (mặc định) — bucket luôn ở chế độ private hoàn toàn; CloudFront sẽ truy cập qua OAC, không bao giờ qua public access trực tiếp.
4. Giữ mặc định các mục còn lại → **Create bucket**.
5. Upload nội dung build:

```bash
aws s3 sync dist/ s3://<tên-web-bucket-của-bạn>
```

![s3 web bucket](/images/5-Workshop/5.4-Deploy-frontend/s3-web-bucket.png)

#### Bước 3 — Tạo CloudFront distribution

AWS đã đổi hẳn **Create distribution** thành wizard 6 trang. Làm theo thứ tự sau:

1. **Choose a plan**: chọn **Pay as you go** (lựa chọn cuối). Các gói tính phí cố định Free/Pro/Business/Premium đi kèm nhiều tính năng workshop không cần đến; Pay-as-you-go không có cam kết trả phí hàng tháng và vẫn giữ **$0** với traffic thấp nhờ free tier sẵn có của CloudFront (1 TB data transfer + 10 triệu request/tháng, áp dụng toàn tài khoản, tách biệt với các gói đặt tên ở trên).
2. **Get started**:
   - **Distribution name**: vd `rag-chatbot-frontend`.
   - **Distribution type**: giữ **Single website or app**.
   - **Domain – optional**: để trống — dự án này không dùng domain Route 53 riêng, URL mặc định `*.cloudfront.net` là đủ.
3. **Specify origin**:
   - **Origin type**: **Amazon S3**.
   - **S3 origin**: bấm **Browse S3**, chọn bucket web hosting của bạn.
   - **Settings**: giữ tick **"Allow private S3 bucket access to CloudFront – Recommended"**. Chỉ 1 checkbox này thay thế hoàn toàn quy trình thủ công "tạo OAC rồi copy-paste bucket policy" trước đây — CloudFront giờ tự tạo Origin Access Control **và** tự viết đúng bucket policy cho bạn.
   - Giữ **Origin settings** và **Cache settings** ở mặc định khuyến nghị.

![cloudfront oac](/images/5-Workshop/5.4-Deploy-frontend/cloudfront-oac.png)

4. **Enable security**: ở mục **Web Application Firewall (WAF)**, chọn **Enable security protections** — khớp với sơ đồ kiến trúc đã vẽ WAF đứng trước CloudFront. Bước này tự động gộp sẵn các bảo vệ chống lỗ hổng phổ biến/kẻ tấn công dò lỗi/threat intelligence hiển thị trên màn hình — không còn bước "vào WAF console thêm managed rule group" riêng như trước nữa. Giữ **Use monitor mode** tắt (để thực sự chặn, không chỉ ghi log) và giữ **Protection against Layer 7 DDoS attacks** tắt (tốn thêm phí, không cần cho demo).

![waf managed rules](/images/5-Workshop/5.4-Deploy-frontend/waf-managed-rules.png)

{{% notice warning %}}
Bước này hiện **ước tính chi phí** trực tiếp (vd "~$14 cho mỗi 10 triệu request/tháng") — bật WAF *không* hoàn toàn miễn phí như khi bỏ qua, nhưng chi phí tỷ lệ theo lượng request: vài chục request test cho workshop chỉ tốn một phần rất nhỏ của 1 cent. Nếu muốn chắc chắn $0 tuyệt đối, chọn **Do not enable security protections** thay vào đó và ghi chú trong báo cáo rằng kiến trúc có hỗ trợ WAF nhưng tắt để kiểm soát chi phí.
{{% /notice %}}

5. **Get TLS certificate**: không cần cấu hình gì vì không nhập domain riêng — CloudFront dùng chứng chỉ mặc định cho `*.cloudfront.net`. Nhấn **Next**.
6. **Review and create**: kiểm tra **Origin → Grant CloudFront access to origin: Yes** (bằng chứng bucket policy đã tự áp dụng) và **Security → Security protections: Enabled**, rồi nhấn **Create distribution**.

![s3 bucket policy oac](/images/5-Workshop/5.4-Deploy-frontend/s3-bucket-policy-oac.png)

Chờ **Status** của distribution chuyển từ **Deploying** sang **Enabled** (thường mất 3–10 phút).

{{% notice tip %}}
Nếu app dùng client-side routing và việc reload 1 trang con (vd `/documents`) hiện trang lỗi 403/404 mặc định của CloudFront thay vì app, hãy thêm **Custom error response** cho distribution (tab **Error pages → Create custom error response**): HTTP error code `403` (và `404`) → Response page path `/index.html` → HTTP Response code `200`. Cách này để router của SPA tự xử lý URL thay vì CloudFront trả lỗi.
{{% /notice %}}

#### Bước 4 — Cập nhật CORS

Domain CloudFront là **origin mới** mà trình duyệt gọi tới — cả 2 backend endpoint mà frontend gọi đều phải cho phép origin này:

1. **API Gateway → `rag-chatbot-api` → CORS** (thanh bên trái của API) → thêm URL CloudFront của bạn (vd `https://dxxxxxxxxxxxxx.cloudfront.net`) vào **Access-Control-Allow-Origin** → **Save**.
2. **S3 → bucket tài liệu (`DOCS_BUCKET`) → Permissions → Cross-origin resource sharing (CORS) → Edit** → thêm cùng URL CloudFront vào mảng `AllowedOrigins` (cần để presigned PUT upload hoạt động từ trình duyệt) → **Save changes**.

![cors update](/images/5-Workshop/5.4-Deploy-frontend/cors-update.png)

#### Bước 5 — Kiểm tra lại

Mở URL CloudFront hiển thị ở ô **Domain name** của distribution (vd `https://dxxxxxxxxxxxxx.cloudfront.net`) và xác nhận:

- Trang đăng nhập hiển thị đúng (chứng minh `index.html` + các file asset đã hash được phục vụ đúng).
- DevTools → Network → response của document có header `via: 1.1 ....cloudfront.net (CloudFront)` — chứng minh app được phục vụ hoàn toàn qua CloudFront, không bao giờ trực tiếp từ S3.
- Mở trực tiếp URL object S3 (hoặc endpoint của bucket) — phải trả về `AccessDenied`, xác nhận bucket thực sự private và OAC đang hoạt động đúng.
- Nếu có bật WAF: vào **WAF & Shield Console → Web ACL của bạn → Sampled requests**, xác nhận traffic của chính bạn hiện lên với **Action: ALLOW** — chứng minh Web ACL đang thực sự kiểm tra request thật, không phải chỉ tạo cho có.

![cloudfront live](/images/5-Workshop/5.4-Deploy-frontend/cloudfront-live.png)
