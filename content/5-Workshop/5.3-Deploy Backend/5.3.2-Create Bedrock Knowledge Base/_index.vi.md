---
title : "Tạo Bedrock Knowledge Base"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

Đây là **bước thủ công duy nhất** trong toàn bộ quá trình triển khai. Bắt buộc phải tạo đúng region với các tài nguyên khác.

{{% notice warning %}}
Knowledge Base **bắt buộc** phải tạo ở **us-east-1 (N. Virginia)** — cùng region với mọi tài nguyên khác. Tạo nhầm region sẽ gây lỗi `Knowledge Base ... does not exist` khi chat.
{{% /notice %}}

#### Bước 1 — Mở Bedrock console và chọn "Unstructured Vector Store KB"

Vào **Amazon Bedrock → Knowledge Bases (KB)**. AWS hiện mặc định nút cam **Create Managed KB** dẫn thẳng tới luồng "fully-managed" — luồng này **ẩn** phần chọn embeddings model và vector store, **không phù hợp** với dự án này. Bấm mũi tên **▾** nhỏ cạnh nút đó, ở mục **Self-managed KB**, chọn **Unstructured Vector Store KB** (toàn quyền kiểm soát indexing — đúng với cấu hình S3 Vectors + Titan Text Embeddings V2 thủ công của dự án).

![create kb](/images/5-Workshop/5.3-Deploy%20Backend/kb-create-start.png)

#### Bước 2 — Provide Knowledge Base details

Ở trang "Provide Knowledge Base details":
- **KB name**: vd `rag-kb`.
- **IAM permissions**: giữ **Create and use a new service role**.
- **Choose data source type**: **Amazon S3** (đã chọn sẵn mặc định).

Bỏ trống Tags và Log deliveries, rồi nhấn **Next**.

![kb details](/images/5-Workshop/5.3-Deploy%20Backend/kb-details.png)

#### Bước 3 — Configure data source

- **Data source name**: bất kỳ.
- **S3 URI**: nhấn **Browse S3**, chọn bucket đã tạo là `DOCS_BUCKET` (bước trước).

{{% notice warning %}}
Sau khi chọn bucket, **bắt buộc gõ thêm hậu tố `documents/`** vào cuối S3 URI, vd `s3://rag-docs-cua-ban/documents/`. Điều này đảm bảo Knowledge Base chỉ nạp file nằm đúng thư mục mà Lambda upload tạo ra.
{{% /notice %}}

- **Parsing strategy**: giữ **Amazon Bedrock default parser**.
- **Chunking strategy**: giữ **Default chunking**.

Nhấn **Next**.

![data source](/images/5-Workshop/5.3-Deploy%20Backend/kb-data-source.png)

#### Bước 4 — Configure data storage and processing

- **Embeddings model**: nhấn **Select model → Amazon → Titan Text Embeddings V2 → Apply**.
- Bấm icon **bút chì (edit)** cạnh model vừa chọn để mở rộng **Additional configurations**:
  - **Embeddings type**: giữ **Floating-point vector embeddings**.
  - **Vector dimensions**: đổi từ mặc định `1024` thành **`512`**.
- **Vector store creation method**: giữ **Quick create a new vector store - Recommended**.
- **Vector store type**: chọn **Amazon S3 Vectors - new**.

{{% notice tip %}}
Dùng 512 chiều thay vì mặc định 1024 giúp giảm khoảng một nửa chi phí lưu trữ/truy vấn vector, chất lượng gần như không đổi.
{{% /notice %}}

![vector config](/images/5-Workshop/5.3-Deploy%20Backend/kb-vector-config.png)

#### Bước 5 — Review and create

Xem lại thông tin và nhấn **Create Knowledge Base**. Chờ trạng thái chuyển sang màu xanh.

![kb created](/images/5-Workshop/5.3-Deploy%20Backend/kb-created.png)

#### Bước 6 — Sync data source

Nhấn **Sync** một lần ở góc phải để kích hoạt data source (bucket trống vẫn sync nhanh).

![kb sync](/images/5-Workshop/5.3-Deploy%20Backend/kb-sync.png)

#### Bước 7 — Copy ID

Trên trang tổng quan Knowledge Base, copy **Knowledge Base ID**; ở tab data source, copy **Data source ID**. Bạn sẽ cần cả hai ở bước tiếp theo.

![kb ids](/images/5-Workshop/5.3-Deploy%20Backend/kb-ids.png)
