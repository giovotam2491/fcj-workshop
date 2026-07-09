---
title: "Blog 2"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# RAG đa tenant an toàn với Amazon Bedrock và Verified Permissions

Các tổ chức lớn khi xây dựng ứng dụng generative AI nội bộ thường gặp một thách thức lặp đi lặp lại: kiểm soát nhóm hoặc phòng ban nào được truy cập tài liệu nào, mà không phải nhân bản hạ tầng cho từng nhóm. Trong phạm vi một tenant, nhân viên của một phòng ban cụ thể chỉ nên truy cập tài liệu được gán cho phòng ban đó. Tuy nhiên, các lãnh đạo — với phạm vi kiểm soát rộng hơn — lại cần truy cập tài liệu trải rộng trên nhiều phòng ban. **Retrieval Augmented Generation (RAG)** là một trong nhiều kỹ thuật bổ trợ (cùng với fine-tuning và continued pre-training) để tùy biến câu trả lời của ứng dụng generative AI bằng dữ liệu của bạn.

Trong bối cảnh doanh nghiệp — nơi dữ liệu thay đổi nhanh và có nhiều người dùng — RAG mang lại điểm cân bằng giữa chi phí và hiệu năng. Bài viết này chỉ cho bạn cách dùng một **Knowledge Base (KB)** dùng chung duy nhất để giảm chi phí và độ phức tạp so với việc tách nhiều instance riêng. Bạn có thể cập nhật quy tắc truy cập trong vài phút mà không cần deploy lại code, đồng thời duy trì một dấu vết audit chi tiết cho mọi quyết định phân quyền. Bạn chạy một ứng dụng RAG duy nhất phục vụ nhiều phòng ban, với quyền truy cập tài liệu được đánh giá ngay tại thời điểm truy xuất (retrieval time).

![Hình 1](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-1.png)
> *Hình 1. Truy cập theo cấp độ vai trò (role) tới dữ liệu và tài nguyên dùng chung của tổ chức.*

Một bài viết trước đó, *Multi-tenancy in RAG applications in a single Amazon Bedrock knowledge base with metadata filtering*, đã minh họa cách dùng cấu trúc thư mục Amazon Simple Storage Service (Amazon S3) và metadata filtering để phân tách dữ liệu giữa các tenant trong cùng một knowledge base. Mô hình đó hoạt động tốt cho các ranh giới rộng ở cấp tenant, nơi giá trị bộ lọc được biết trước tại thời điểm thiết kế và nhúng cứng trong code ứng dụng. Tuy nhiên, trong phạm vi một tenant, các phòng ban hoặc vai trò khác nhau thường cần độ hiển thị tài liệu khác nhau, và lãnh đạo có thể cần quyền truy cập xuyên suốt nhiều ranh giới. Bài viết này mở rộng nền tảng đó bằng cách **đưa logic chọn bộ lọc ra bên ngoài (externalize)** thành các chính sách **Cedar** do **Amazon Verified Permissions** quản lý, cho phép ra quyết định phân quyền động, đánh giá tại runtime.

Mô hình này cho phép một ứng dụng RAG duy nhất phục vụ nhiều phòng ban trong khi vẫn giữ tài liệu của từng phòng ban được cô lập, mà không phải dựng riêng một knowledge base cho mỗi team. Nó xây dựng trên nền metadata filtering trong **Amazon Bedrock Knowledge Bases** — một năng lực RAG được quản lý hoàn toàn, xử lý ingestion, retrieval và prompt augmentation qua một API duy nhất. Metadata filtering là nền tảng vững chắc, nhưng nó tạo ra một khoảng trống: logic chọn bộ lọc không được quản trị từ bên ngoài, và thay đổi quy tắc đồng nghĩa với việc phải deploy lại code.

Khi logic phân quyền nằm trong code, các quy tắc có thể trở nên thiếu nhất quán theo thời gian và cần cả một chu kỳ deployment để thay đổi. **Amazon Verified Permissions** giải quyết điều này bằng cách cung cấp cơ chế phân quyền chi tiết (fine-grained) và quản lý quyền có khả năng mở rộng cho các ứng dụng tùy chỉnh. Các chính sách Cedar được externalize trong Verified Permissions có thể audit được, quản lý phiên bản được, và cập nhật được ngay tại runtime.

Bài viết này hướng dẫn bạn xây dựng một mô hình phân quyền hai lớp theo chiến lược **defense-in-depth** (phòng thủ theo chiều sâu) để kiểm soát truy cập chi tiết trong nội bộ một tenant cho ứng dụng RAG. Defense in depth là chiến lược bảo mật dùng nhiều lớp bảo vệ độc lập. Mỗi lớp hoạt động độc lập. Nếu một lớp bị cấu hình sai, lớp còn lại vẫn thực thi kiểm soát truy cập. Mô hình chạy trên **Amazon Bedrock** — dịch vụ được quản lý hoàn toàn, cung cấp nhiều foundation model (FM) hiệu năng cao từ Amazon và các công ty AI qua một API duy nhất, cùng bộ năng lực rộng để xây dựng ứng dụng generative AI với bảo mật, quyền riêng tư và AI có trách nhiệm.

Trong bài này, bạn sẽ học cách:

1. Thực thi kiểm soát truy cập chi tiết ở cấp tài liệu (document-level) ngay tại thời điểm truy xuất, chỉ dùng một instance Amazon Bedrock Knowledge Bases duy nhất.
2. Đánh giá các chính sách Cedar tại runtime để dựng động metadata filter được truyền vào API *RetrieveAndGenerate*.
3. Cập nhật quy tắc phân quyền mà không cần đổi code ứng dụng hay kích hoạt một lần deployment.
4. Thiết kế hệ thống phân quyền **deny-by-default** (mặc định từ chối), được thiết kế để chặn truy cập khi dịch vụ phân quyền không khả dụng.

---

## Mô hình cô lập và phạm vi áp dụng

Mô hình này cung cấp **cô lập ở cấp bộ lọc (logic)**, chứ không phải **cô lập được thực thi bằng IAM (hạ tầng)**. Metadata filter kiểm soát tài liệu nào được trả về tại thời điểm truy xuất, nhưng knowledge base bên dưới vẫn là tài nguyên dùng chung. Nếu logic middleware dựng bộ lọc bị lỗi theo hướng "fail open" (mở toang khi lỗi), tài liệu của nhóm khác có thể bị lộ.

Mô hình này được thiết kế để kiểm soát truy cập chi tiết **trong phạm vi một tenant** — ví dụ kiểm soát phòng ban, team hoặc vai trò nào trong một tổ chức được truy cập tài liệu nào. Nó **không** thay thế cho việc cô lập tenant cứng (hard isolation) trong một sản phẩm SaaS đa tenant. Đối với cô lập xuyên tenant, nơi cần một ranh giới tuân thủ (compliance boundary) giữa các khách hàng hoặc tổ chức riêng biệt, hãy cấp một knowledge base riêng cho mỗi tenant với ranh giới tài nguyên được thực thi bằng IAM. Bên trong knowledge base của từng tenant, bạn có thể phủ thêm mô hình dựa trên bộ lọc này để kiểm soát truy cập ở mức chi tiết hơn.

**Dùng mô hình này khi:**

- Bạn cần kiểm soát truy cập tài liệu giữa các phòng ban, team hoặc vai trò trong cùng một tổ chức.
- Quy tắc truy cập thay đổi thường xuyên và bạn muốn cập nhật mà không cần deploy lại code.
- Bạn muốn dùng một instance knowledge base duy nhất để giảm chi phí và chi phí vận hành cho việc phân tách tài liệu trong nội bộ tenant.

**Không dùng mô hình này khi:**

- Bạn cần cô lập cứng giữa các khách hàng hoặc tổ chức riêng biệt (hãy dùng một knowledge base cho mỗi tenant với ranh giới IAM).
- Yêu cầu tuân thủ hoặc audit của bạn bắt buộc phải phân tách ở cấp hạ tầng giữa các tập dữ liệu.
- Việc lỗi cơ chế bộ lọc sẽ cấu thành vi phạm quy định (cô lập cấp bộ lọc là ranh giới logic, không phải ranh giới vật lý).

{{% notice info %}}
**Rủi ro tồn dư — race condition khi ingestion:** Tồn tại một khoảng thời gian ngắn giữa lúc upload tài liệu và lúc tạo file sidecar. Cơ chế bảo vệ ingestion (Bước 1) giảm rủi ro này bằng cách loại trừ tài liệu chưa có sidecar. Tuy nhiên, nếu bạn chỉnh lịch ingestion để chạy liên tục hoặc với chu kỳ ngắn, hãy kiểm tra rằng cửa sổ gom lô (batching window) trong Amazon Simple Queue Service (Amazon SQS) — mặc định 30 giây — đủ thời gian cho Lambda gắn thẻ hoàn tất trước chu kỳ ingestion kế tiếp.
{{% /notice %}}

---

## Điều kiện tiên quyết

Trước khi triển khai mô hình này, bạn cần:

1. Quen thuộc với Python, AWS Lambda và các khái niệm infrastructure as code.
2. Một AWS account có quyền AWS Identity and Access Management (AWS IAM) để tạo AWS Lambda function, một Amazon API Gateway REST API, Amazon Cognito user pool, Verified Permissions policy store và Amazon Bedrock Knowledge Bases.
3. Amazon Cognito đã cấu hình với các user group theo phòng ban (như `dept-a`, `dept-b`, `dept-c`) và `cognito:groups` được đưa vào JSON Web Token (JWT) cấp cho client.
4. Quyền truy cập model Amazon Bedrock cho các FM bạn dự định dùng (như Anthropic Claude 3 Haiku và Amazon Nova Lite 2).
5. Một Verified Permissions policy store đã được tạo và schema Cedar đã được định nghĩa (principals, resources, actions) trước khi deploy.
6. AWS Cloud Development Kit (AWS CDK) hoặc AWS CloudFormation để triển khai hạ tầng mô tả trong bài.
7. Tài liệu mẫu được chuẩn bị sẵn với prefix theo phòng ban (như `docs/dept-a/report.pdf`) để upload lên Amazon S3.

{{% notice warning %}}
**Quan trọng:** Triển khai mô hình này sẽ tạo ra các tài nguyên AWS tính phí, bao gồm Amazon Bedrock Knowledge Bases, AWS Lambda, Amazon API Gateway, Amazon S3, Amazon Cognito, Amazon Verified Permissions, Amazon EventBridge, Amazon SQS, Amazon DynamoDB, AWS WAF và Amazon CloudFront. Chi phí thay đổi tùy theo lưu lượng sử dụng và AWS Region. Hãy xem AWS Pricing cho từng dịch vụ trước khi deploy, và tham khảo phần **Dọn dẹp tài nguyên** ở cuối bài để xóa tài nguyên sau khi thử nghiệm xong.
{{% /notice %}}

---

## Tổng quan giải pháp

Phục vụ nhiều phòng ban từ một ứng dụng không có nghĩa là cho mọi phòng ban truy cập mọi tài liệu. Giải pháp này giữ tài liệu của từng phòng ban được cô lập bên trong một instance Amazon Bedrock Knowledge Bases duy nhất, nhờ đó bạn tránh được chi phí và gánh nặng vận hành của việc dựng một knowledge base cho mỗi team trong tenant đó. Các thẻ metadata phân tách tài liệu về mặt logic, và Verified Permissions đóng vai trò điểm thực thi chính sách được externalize (externalized policy enforcement point), quyết định tài liệu nào mà nhóm hoặc vai trò của người dùng được phép nhìn thấy trong mỗi request.

Một mục tiêu then chốt là phục vụ nhiều phòng ban trong cùng một tenant chỉ từ một instance Knowledge Base. Nếu phải cấp riêng một instance cho mỗi phòng ban, hạ tầng sẽ nhân lên nhiều lần: nguồn dữ liệu riêng, pipeline ingestion riêng và chi phí quản lý riêng. Một knowledge base duy nhất với truy cập được lọc theo metadata tránh được sự trùng lặp này, đồng thời cung cấp cô lập tài liệu về mặt logic trong ranh giới tenant. Tài liệu được phân tách logic theo thẻ metadata, và Verified Permissions cung cấp một điểm thực thi chính sách externalize đáng tin cậy để xác định thẻ nào mà một người dùng — trong một nhóm hoặc vai trò cụ thể — được phép truy cập.

Pipeline ingestion gắn thẻ metadata phòng ban cho tài liệu. Verified Permissions đánh giá chính sách Cedar tại thời điểm truy vấn để xác định bạn được phép nhìn thấy những thẻ phòng ban nào. Một dịch vụ middleware (triển khai bằng AWS Lambda) chuyển quyết định chính sách đó thành một metadata filter và dùng nó trong API *RetrieveAndGenerate* của Amazon Bedrock. API này kết hợp bước truy xuất và bước sinh câu trả lời, trả về một câu trả lời có căn cứ (grounded) dựa trên tập tài liệu đã lọc. FM chỉ xử lý những tài liệu đã vượt qua bộ lọc.

**Hai lớp độc lập thực thi phân quyền:**

- **Lớp 1 (API access):** Một Lambda Authorizer trên Amazon API Gateway gọi Verified Permissions để quyết định bạn có được phép gọi API hay không.
- **Lớp 2 (document access):** Một middleware Lambda, dùng để điều phối lời gọi tới Knowledge Base, cũng gọi Verified Permissions để xác định những tài nguyên Knowledge Base nào phòng ban của bạn được phép truy vấn, rồi dựng metadata filter tương ứng.

Không lớp nào phụ thuộc vào lớp kia để đảm bảo tính đúng đắn. Nếu Lớp 1 bị vượt qua, Lớp 2 vẫn được thiết kế để thực thi cô lập ở cấp tài liệu tại metadata filter của KB.

![Hình 2](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-2.png)
> *Hình 2. Pipeline ingestion: tài liệu upload lên Amazon S3 kích hoạt Amazon EventBridge, định tuyến qua Amazon SQS đến một AWS Lambda function ghi metadata. Một Lambda theo lịch sau đó kích hoạt job ingestion của Amazon Bedrock Knowledge Bases.*

![Hình 3](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-3.png)
> *Hình 3. Luồng truy vấn: request của người dùng đi qua Amazon CloudFront và AWS WAF đến Amazon API Gateway, nơi Lambda Authorizer đánh giá phân quyền Lớp 1 (cấp API) với Verified Permissions. Nếu được phép, middleware Lambda đánh giá phân quyền Lớp 2 (cấp tài liệu), dựng metadata filter và gọi RetrieveAndGenerate.*

### Luồng quyết định phân quyền

| **Bước** | **Thành phần**                 | **Quyết định**                            | **Khi bị từ chối**              |
| -------- | ------------------------------ | ----------------------------------------- | ------------------------------- |
| 1        | AWS WAF                        | Giới hạn tốc độ và kiểm tra rule           | Request bị chặn                 |
| 2        | Lambda Authorizer (Lớp 1)      | Bạn có được gọi API không?                 | Trả về 403                      |
| 3        | Middleware Lambda (Lớp 2)      | Bạn được truy cập những phòng ban nào?     | Tập kết quả rỗng                |
| 4        | Amazon Bedrock Knowledge Bases | Áp metadata filter khi truy xuất           | Loại trừ tài liệu không có quyền |
| 5        | Guardrails for Amazon Bedrock  | Câu trả lời có bám sát ngữ cảnh không?     | Chặn hoặc chỉnh sửa câu trả lời  |

---

## Các dịch vụ AWS chính

| **Lớp**                                    | **Dịch vụ**                    | **Vai trò**                                                                                              |
| ------------------------------------------ | ------------------------------ | ------------------------------------------------------------------------------------------------------- |
| Lớp danh tính                              | Amazon Cognito                 | Cấp JWT kèm claim nhóm phòng ban (`cognito:groups`) từ user pool                                          |
| Lớp bảo mật API                            | AWS WAF                        | Áp giới hạn tốc độ, lọc IP và đánh giá managed rule tại biên (edge)                                       |
| Phân quyền Lớp 1                           | Amazon Verified Permissions    | Đánh giá chính sách Cedar cho quyết định truy cập cấp API trong Lambda Authorizer                         |
| Phân quyền Lớp 2                           | Amazon Verified Permissions    | Đánh giá chính sách Cedar cho truy cập cấp tài liệu và điều khiển việc dựng metadata filter               |
| Lớp truy xuất RAG và gọi FM                | Amazon Bedrock Knowledge Bases | Năng lực RAG được quản lý hoàn toàn, xử lý truy xuất kèm metadata filtering và sinh FM trong một lời gọi   |
| Lớp ingestion                              | Amazon EventBridge, Amazon SQS | Pipeline hướng sự kiện để gắn thẻ sidecar; Amazon SQS đệm khi upload đột biến và định tuyến lỗi về DLQ     |

---

## Triển khai kỹ thuật

Phần hướng dẫn trình bày pipeline ingestion trước để gắn thẻ và index tài liệu. Sau đó bạn chuyển sang Luồng truy vấn (Query Flow) — nơi chứa mô hình phân quyền cốt lõi. Mỗi phần dưới đây tương ứng với một thành phần riêng biệt trong kiến trúc.

### Bước 1: Thiết lập pipeline ingestion hướng sự kiện kèm gắn thẻ metadata

Để metadata filter hoạt động tại thời điểm truy vấn, tài liệu cần được gắn thẻ phòng ban trước khi được index. Pipeline ingestion xử lý việc này qua hai pha.

**Pha 1 (hướng sự kiện):** Khi một tài liệu được upload lên Amazon S3 dưới một prefix phòng ban (như `docs/dept-a/report.pdf`), Amazon EventBridge phát ra một sự kiện *ObjectCreated*. Sự kiện định tuyến qua Amazon SQS đến một AWS Lambda function, function này ghi một file sidecar *.metadata.json* bên cạnh tài liệu. Hàng đợi Amazon SQS đệm các đợt upload lớn và định tuyến các lần gắn thẻ thất bại về một dead-letter queue để thử lại.

**Pha 2 (theo lịch):** Một lịch Amazon EventBridge kích hoạt một Lambda ingestion mỗi 5 phút; Lambda này gọi API *StartIngestionJob* trên data source của Amazon Bedrock Knowledge Bases. Amazon Bedrock đọc tài liệu và sidecar của chúng từ Amazon S3, chia nhỏ (chunk), sinh embedding, và index các vector kèm thuộc tính phòng ban.

```python
# Xác thực sidecar tồn tại trước khi ingestion
objects = s3.list_objects_v2(Bucket=BUCKET, Prefix=f"docs/{dept}/")
docs = [o["Key"] for o in objects.get("Contents", []) if not o["Key"].endswith(".metadata.json")]
for doc_key in docs:
    sidecar_key = f"{doc_key}.metadata.json"
    try:
        s3.head_object(Bucket=BUCKET, Key=sidecar_key)
    except s3.exceptions.ClientError:
        logger.warning(f"Skipping {doc_key}: no metadata sidecar found")
        docs.remove(doc_key)
```

**Phát hiện giả mạo sidecar metadata:** Bật S3 Versioning trên bucket tài liệu và cấu hình AWS CloudTrail S3 data events để ghi lại các lời gọi *PutObject* và *DeleteObject*. Tạo một metric filter của Amazon CloudWatch Alarms để cảnh báo khi có *PutObject* tới một key *.metadata.json* xuất phát từ principal khác với role của Lambda gắn thẻ. Với các workload yêu cầu tuân thủ nghiêm ngặt, cân nhắc dùng S3 Object Lock ở chế độ compliance để sidecar trở nên bất biến sau khi tạo — lưu ý rằng khi đó việc phân loại lại tài liệu sẽ cần một quy trình có chủ đích để tạo phiên bản mới.

**Thực thi prefix khi upload:** Lambda gắn thẻ metadata suy ra nhãn phòng ban từ prefix của S3 key (ví dụ `docs/dept-a/` → `department: dept-a`). Để giúp ngăn một người dùng hoặc tiến trình upload tài liệu dưới prefix của phòng ban khác, hãy giới hạn quyền upload bằng một điều kiện trong IAM policy:

```json
{
    "Effect": "Allow",
    "Action": "s3:PutObject",
    "Resource": "arn:aws:s3:::your-doc-bucket/docs/dept-a/*",
    "Condition": {
        "StringEquals": {
            "aws:PrincipalTag/department": "dept-a"
        }
    }
}
```

Mỗi principal upload (dù là user role, CI/CD pipeline hay ứng dụng) nên mang một thẻ phòng ban khớp với prefix được phép của nó. Điều này giúp ngăn Lambda gắn thẻ bị "lừa" gán nhầm nhãn cho tài liệu khi có ai upload sai đường dẫn.

**Cơ chế bảo vệ ingestion:** Trước khi kích hoạt job ingestion, Lambda lên lịch sẽ liệt kê các object dưới từng prefix phòng ban và kiểm tra rằng mọi tài liệu đều có sidecar *.metadata.json* tương ứng. Tài liệu không có sidecar bị loại khỏi phạm vi ingestion và ghi log lên CloudWatch dưới dạng "chưa gắn thẻ" (untagged). Điều này giúp ngăn một tài liệu chưa gắn thẻ bị index mà không có thuộc tính phòng ban, vốn có thể khiến nó lọt qua metadata filter tại thời điểm truy vấn. Nếu workload của bạn cần đảm bảo chặt chẽ hơn, hãy chuyển tài liệu chưa gắn thẻ sang một prefix cách ly (quarantine) và cảnh báo qua Amazon Simple Notification Service (Amazon SNS).

**Giới hạn ghi vào S3:** Giới hạn quyền *s3:PutObject* trên bucket tài liệu chỉ cho IAM execution role của Lambda gắn thẻ metadata. Mọi principal khác — kể cả application role, CI/CD pipeline và người vận hành — chỉ nên có tối đa *s3:GetObject* và *s3:ListBucket*. Điều này giúp ngăn việc chỉnh sửa vô tình hoặc cố ý các file sidecar *.metadata.json*, vốn có thể gắn thẻ lại tài liệu sang phòng ban khác và làm lộ nó cho người dùng trái phép ở chu kỳ ingestion kế tiếp. Dùng một bucket policy với deny tường minh cho *s3:PutObject*, chỉ miễn trừ role ARN của Lambda gắn thẻ:

```json
{
    "Effect": "Deny",
    "Principal": "*",
    "Action": ["s3:PutObject", "s3:DeleteObject"],
    "Resource": "arn:aws:s3:::your-doc-bucket/*",
    "Condition": {
        "StringNotEquals": {
            "aws:PrincipalArn": "arn:aws:iam::123456789012:role/MetadataTaggingLambdaRole"
        }
    }
}
```

Cửa sổ gom lô 30 giây trên event source của Amazon SQS nghĩa là các đợt upload lớn được xử lý gộp lại thay vì một tài liệu cho mỗi lần gọi Lambda. Việc tách thành hai pha giúp đảm bảo sidecar metadata phòng ban được ghi trước khi job ingestion chạy, qua đó giảm rủi ro race condition nơi một tài liệu có thể bị index mà không có thẻ phòng ban.

```python
# metadata_lambda/handler.py
import boto3, json

def handler(event, context):
    for record in event["Records"]:
        body = json.loads(record["body"])
        s3_key = body["detail"]["object"]["key"]
        # Trích phòng ban từ prefix: docs/dept-a/report.pdf -> dept-a
        dept = s3_key.split("/")[1]
        meta_key = s3_key + ".metadata.json"
        s3 = boto3.client("s3")
        s3.put_object(
            Bucket=BUCKET,
            Key=meta_key,
            Body=json.dumps({"metadataAttributes": {"department": dept}})
        )
```

**Ghi chú triển khai:** Triển khai đoạn này làm handler cho AWS Lambda function gắn thẻ metadata (runtime Python 3.12). Đặt biến môi trường `BUCKET`. IAM execution role của function cần quyền `s3:PutObject` trên bucket tài liệu.

### Bước 2: Cấu hình Amazon Bedrock Knowledge Bases

Với Amazon Bedrock Knowledge Bases, bạn cấu hình chia nhỏ tài liệu (chunking), chọn embedding model, index vector và metadata filtering mà không phải quản lý hạ tầng bên dưới. Bạn cấu hình một data source được hậu thuẫn bởi chính bucket Amazon S3 dùng cho pipeline ingestion.

Amazon Bedrock Knowledge Bases chia nhỏ tài liệu ở mức 300 token với 20% chồng lấp (giá trị mặc định, hoạt động tốt cho tài liệu doanh nghiệp có cấu trúc). Một embedding model, chẳng hạn Amazon Titan Text Embeddings V2, sinh ra các embedding. Các thuộc tính metadata định nghĩa trong file sidecar *.metadata.json* được index cùng với vector, khiến chúng khả dụng làm pre-filter trên lời gọi *RetrieveAndGenerate*.

### Bước 3: Định nghĩa schema và chính sách Cedar trong Amazon Verified Permissions

Verified Permissions cung cấp phân quyền chi tiết thông qua **Cedar** — một ngôn ngữ chính sách chuyên dụng. Schema Cedar định nghĩa ba loại entity cho giải pháp này:

1. **Principal:** `GenAIApp::UserGroup` (nhóm phòng ban trích từ JWT).
2. **Action:** `query` (truy xuất tài liệu từ knowledge base) và `invokeModel` (gọi một FM).
3. **Resource:** `GenAIApp::KnowledgeBase` và `GenAIApp::Model`.

Namespace `GenAIApp` là một prefix tùy chỉnh bạn định nghĩa khi tạo schema Cedar. Bạn có thể thay bằng namespace ứng dụng của riêng mình (như `MyCompany` hoặc `RAGApp`).

Sáu chính sách sau bao phủ ba phòng ban dùng trong bài. Phòng ban C có một quyền truy cập xuyên phòng ban bao trùm mọi tài nguyên Knowledge Base, phù hợp cho một nhóm lãnh đạo hoặc điều hành:

```
// dept-a: truy vấn KB của mình + dùng Claude 3 Haiku
permit(
    principal in GenAIApp::UserGroup::"dept-a",
    action == GenAIApp::Action::"query",
    resource == GenAIApp::KnowledgeBase::"dept-a"
);

permit(
    principal in GenAIApp::UserGroup::"dept-a",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"anthropic.claude-3-haiku-20240307-v1:0"
);

// dept-b: truy vấn KB của mình + dùng Claude 3 Haiku
permit(
    principal in GenAIApp::UserGroup::"dept-b",
    action == GenAIApp::Action::"query",
    resource == GenAIApp::KnowledgeBase::"dept-b"
);

permit(
    principal in GenAIApp::UserGroup::"dept-b",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"anthropic.claude-3-haiku-20240307-v1:0"
);

// dept-c: truy cập xuyên phòng ban + dùng Amazon Nova Lite 2
permit(
    principal in GenAIApp::UserGroup::"dept-c",
    action == GenAIApp::Action::"query",
    resource
);

permit(
    principal in GenAIApp::UserGroup::"dept-c",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"amazon.nova-2-lite-v1:0"
);
```

Để điều chỉnh các chính sách này cho tổ chức của bạn, hãy thay các định danh phòng ban (`dept-a`, `dept-b`, `dept-c`) bằng tên nhóm của riêng bạn. Mô hình hỗ trợ nhiều kiểu truy cập: theo team, theo project, hoặc phân cấp. Với các quyền truy cập tạm thời, thêm một chính sách Cedar có điều kiện `when` đánh giá một thuộc tính theo thời gian, và gỡ chính sách đó khi quyền cần hết hạn.

Thay đổi chính sách có hiệu lực ở lời gọi API kế tiếp. Bạn không cần deploy lại Lambda hay cập nhật AWS CDK.

**Quản trị chính sách:** Vì thay đổi chính sách Cedar có hiệu lực ngay lập tức, hãy giới hạn ai được sửa chính sách trong môi trường production. Áp điều kiện IAM lên *verifiedpermissions:CreatePolicy*, *UpdatePolicy* và *DeletePolicy* để chỉ một role CI/CD chuyên dụng hoặc một nhóm nhỏ quản trị viên được ủy quyền mới có thể thay đổi policy store. Bật AWS CloudTrail cho các lời gọi API Verified Permissions và tạo CloudWatch Alarm kích hoạt khi có sự kiện thay đổi chính sách nằm ngoài quy trình quản lý thay đổi. Với các bản triển khai production, hãy kiểm thử chính sách Cedar với các kịch bản test trong một policy store phi production trước khi đưa lên. Đối xử với thay đổi chính sách nghiêm túc như với deployment code ứng dụng.

### Bước 4: Cấu hình Lớp 1 — phân quyền cấp API (Lambda Authorizer)

Khi một request đến Amazon API Gateway, Lambda Authorizer chạy trước logic ứng dụng của bạn. Nó xác thực chữ ký JWT với endpoint JSON Web Key Set (JWKS) của Amazon Cognito, rồi gọi Verified Permissions *IsAuthorized* với thông tin thành viên nhóm của bạn. Đây là kiểm tra "cấp truy cập/phân quyền" truyền thống: nó xác minh bạn có được phép gọi API hay không, chứ không phải bạn được truy cập tài liệu nào.

Authorizer từ chối truy cập theo mặc định. Nếu Verified Permissions không khả dụng, function ném ra exception và Amazon API Gateway trả về 403.

```python
import boto3, json

avp = boto3.client("verifiedpermissions")

def handler(event, context):
    token = event["authorizationToken"]
    claims = decode_and_verify_jwt(token)  # xác thực với Amazon Cognito JWKS
    groups = claims.get("cognito:groups", [])

    if not groups:
        raise Exception("Unauthorized")

    # Đánh giá từng nhóm; cho phép nếu bất kỳ nhóm nào có chính sách permit
    allowed = False
    for group in groups:
        response = avp.is_authorized(
            policyStoreId=POLICY_STORE_ID,
            principal={"entityType": "GenAIApp::UserGroup", "entityId": group},
            action={"actionType": "GenAIApp::Action", "actionId": "query"},
            resource={"entityType": "GenAIApp::Application", "entityId": "api"}
        )
        if response["decision"] == "ALLOW":
            allowed = True
            break

    if not allowed:
        raise Exception("Unauthorized")

    return generate_policy("Allow", event["methodArn"], claims)
```

**Ghi chú triển khai:** Triển khai đoạn này làm Lambda Authorizer (loại TOKEN) trên Amazon API Gateway REST API của bạn. Đặt TTL của cache authorizer bằng 0 trong lúc test để thay đổi chính sách có hiệu lực ngay.

**TTL cache trong production:** Ở production, TTL cache của API Gateway authorizer quyết định tốc độ có hiệu lực của việc thu hồi chính sách. TTL bằng 0 nghĩa là mỗi request kích hoạt một lần đánh giá Verified Permissions mới. Thu hồi có hiệu lực ngay nhưng độ trễ tăng. TTL 300 giây (mặc định của API Gateway) cải thiện độ trễ nhưng nghĩa là một chính sách đã thu hồi vẫn có thể cho phép truy cập tới 5 phút. Với các workload cần thu hồi kịp thời (ví dụ khi nhân viên nghỉ việc hoặc ứng phó sự cố), đặt TTL bằng 0 hoặc một giá trị ngắn có chủ đích (ví dụ 30–60 giây) và chấp nhận thêm các lời gọi API Verified Permissions. Khẳng định "thay đổi chính sách có hiệu lực ở lời gọi API kế tiếp" chỉ đúng khi TTL cache của authorizer bằng 0 hoặc mục cache đã hết hạn.

**Thành viên nhiều nhóm:** Một người dùng có thể thuộc nhiều nhóm Cognito — ví dụ một nhân viên vừa thuộc `dept-a` vừa thuộc một nhóm lãnh đạo liên phòng ban. Authorizer đánh giá các nhóm hiện diện trong JWT và cho phép gọi API nếu một nhóm có chính sách permit Cedar khớp. Điều này giúp tránh việc giới hạn truy cập tùy tiện dựa trên thứ tự xuất hiện của nhóm trong token. Truy cập cấp tài liệu sau đó được xác định độc lập ở Lớp 2, nơi middleware đánh giá từng tài nguyên phòng ban theo các nhóm của người dùng để dựng metadata filter phù hợp.

Với xử lý lỗi, hãy triển khai exponential backoff kèm jitter cho lời gọi API Verified Permissions. Ghi log các quyết định phân quyền lên Amazon CloudWatch để giám sát và audit.

### Bước 5: Cấu hình Lớp 2 — phân quyền cấp tài liệu (middleware Lambda)

Sau khi một request vượt qua Lớp 1, middleware Lambda chạy một lần đánh giá Verified Permissions thứ hai, độc lập. Lần này, nó kiểm tra bạn được phép truy vấn những tài nguyên KB nào dựa trên thành viên nhóm của bạn, rồi dịch quyết định đó trực tiếp thành một metadata filter trên lời gọi *RetrieveAndGenerate*.

Amazon Bedrock Knowledge Bases áp metadata filter *trước khi* chạy tìm kiếm tương đồng vector. Nghĩa là FM chỉ xử lý những tài liệu bạn được phép truy cập. Bộ lọc giúp ngăn tài liệu không có quyền xuất hiện trong tập truy xuất.

**Mô hình truy cập theo phòng ban**

| **Nhóm** | **Foundation model** | **Quyền truy cập knowledge base**       |
| -------- | -------------------- | --------------------------------------- |
| dept-a   | Claude 3 Haiku       | Chỉ tài liệu Phòng ban A                 |
| dept-b   | Claude 3 Haiku       | Chỉ tài liệu Phòng ban B                 |
| dept-c   | Amazon Nova Lite 2   | Nhiều phòng ban (A, B và C)              |

```python
def build_filter_and_invoke(user_group, query, session_id):
    permitted_depts = []
    for dept in ["dept-a", "dept-b", "dept-c"]:
        resp = avp.is_authorized(
            policyStoreId=POLICY_STORE_ID,
            principal={"entityType": "GenAIApp::UserGroup",
                       "entityId": user_group},
            action={"actionType": "GenAIApp::Action",
                    "actionId": "query"},
            resource={"entityType": "GenAIApp::KnowledgeBase",
                      "entityId": dept}
        )
        if resp["decision"] == "ALLOW":
            permitted_depts.append(dept)

    if not permitted_depts:
        raise PermissionError("No permitted Knowledge Base resources")

    # Triển khai retry với exponential backoff cho các lời gọi avp.is_authorized

    # Dựng metadata filter theo các phòng ban được phép
    if len(permitted_depts) == 1:
        kb_filter = {"equals": {"key": "department",
                                "value": permitted_depts[0]}}
    else:
        kb_filter = {"orAll": [{"equals": {"key": "department",
                                           "value": d}}
                               for d in permitted_depts]}

    return bedrock_agent.retrieve_and_generate(
        input={"text": query},
        retrieveAndGenerateConfiguration={
            "type": "KNOWLEDGE_BASE",
            "knowledgeBaseConfiguration": {
                "knowledgeBaseId": KB_ID,
                "modelArn": get_permitted_model(user_group),
                "retrievalConfiguration": {
                    "vectorSearchConfiguration": {
                        "filter": kb_filter
                    }
                }
            }
        }
    )
```

**Ghi chú triển khai:** Triển khai đoạn này làm handler cho middleware AWS Lambda function (runtime Python 3.12). Đặt biến môi trường `POLICY_STORE_ID` và `KB_ID`. IAM execution role của function cần quyền *verifiedpermissions:IsAuthorized* và *bedrock:RetrieveAndGenerate*.

Việc chọn FM dùng chung policy store của Verified Permissions. Các chính sách Cedar cấp quyền *invokeModel* quyết định model ID nào mà middleware truyền cho Amazon Bedrock, nên kiểm soát truy cập model được điều khiển bởi cùng các chính sách externalize như truy cập tài liệu.

**Ghi chú bảo mật:** Metadata filter loại trừ tài liệu không có quyền khỏi tập truy xuất trước khi FM xử lý chúng. Nếu một người dùng truy vấn dữ liệu của phòng ban khác, request trả về không có kết quả liên quan. Để giám sát hành vi truy xuất bất thường, dùng ghi log Amazon CloudWatch trên middleware AWS Lambda function.

#### Lợi ích của hai lớp phân quyền độc lập

|                          | **Lớp 1 (API Gateway)**       | **Lớp 2 (Middleware Lambda)**                    |
| ------------------------ | ----------------------------- | ------------------------------------------------ |
| **Câu hỏi được trả lời** | Bạn có được gọi API không?    | Phòng ban của bạn truy cập được tài liệu nào?    |
| **Điểm thực thi**        | Trước khi logic ứng dụng chạy | Tại metadata filter của Amazon Bedrock KB        |
| **Kiểu lỗi**             | Trả về 403 cho bạn            | Tập kết quả rỗng hoặc đã lọc                     |

**Đánh đổi về tính khả dụng:** Cả hai lớp đều phụ thuộc vào Amazon Verified Permissions. Nếu dịch vụ bị throttle hoặc không khả dụng, thiết kế deny-by-default nghĩa là người dùng bị từ chối truy cập. Đây là hành vi bảo mật đúng và có chủ đích. Với hầu hết workload, một khoảng từ chối ngắn vẫn tốt hơn là "fail open". Nếu ứng dụng của bạn có yêu cầu khả dụng nghiêm ngặt, cân nhắc triển khai exponential backoff kèm jitter trên mọi lời gọi *IsAuthorized* (ở cả Lambda Authorizer và middleware Lambda) để xử lý throttling tạm thời một cách mượt mà. Một circuit-breaker rơi về các quyết định phân quyền "last-known-good" trong cache có thể cải thiện khả dụng, nhưng tạo ra một cửa sổ nơi quyền đã thu hồi vẫn có thể được chấp nhận. Hãy ghi rõ đánh đổi này nếu bạn áp dụng nó, và đảm bảo các quyết định cache hết hạn theo một TTL ngắn.

### Bước 6: Thêm Guardrails for Amazon Bedrock làm lớp an toàn đầu ra

**Guardrails for Amazon Bedrock** áp các kiểm tra độ trung thực với nguồn theo ngữ cảnh (contextual source fidelity) và lọc nội dung như một lớp an toàn bổ trợ. Trong khi Verified Permissions kiểm soát FM truy cập tài liệu nào, thì Guardrails đánh giá câu trả lời của FM trước khi trả về cho bạn.

Kiểm tra độ trung thực với nguồn theo ngữ cảnh giúp đảm bảo câu trả lời bám sát tài liệu đã truy xuất thay vì lấy từ dữ liệu pre-training của FM. Kết hợp điều này với metadata filter từ Lớp 2 để có một phương pháp defense in depth trọn vẹn: phân quyền giới hạn tập truy xuất, còn Guardrails xác thực đầu ra được sinh ra.

Cấu hình Guardrail trong lời gọi *RetrieveAndGenerate* áp hai kiểm tra:

1. **Contextual grounding (bám ngữ cảnh):** Giúp hạn chế các câu trả lời suy diễn vượt ra ngoài ngữ cảnh đã truy xuất, hỗ trợ độ chính xác thực tế gắn với tài liệu của bạn.
2. **Content filtering (lọc nội dung):** Chặn các câu trả lời chứa nội dung độc hại hoặc không phù hợp dựa trên ngưỡng bạn cấu hình.

Bạn áp Guardrail trong lời gọi *RetrieveAndGenerate* bằng cách truyền tham số *guardrailConfiguration* kèm Guardrail ID và version. Contextual grounding giúp giảm thiểu prompt injection bằng cách giới hạn câu trả lời trong ngữ cảnh đã truy xuất, nhưng không loại bỏ mọi vector injection. Để phòng thủ thêm, hãy xác thực độ dài đầu vào và làm sạch (sanitize) truy vấn trước khi truyền cho *RetrieveAndGenerate*.

### Bước 7: Kiểm thử luồng phân quyền đầu-cuối

Với giải pháp đã triển khai, đây là những gì diễn ra khi một người dùng `dept-a` gửi truy vấn:

1. Người dùng gửi truy vấn qua ứng dụng web với `Authorization: Bearer`, đi qua Amazon CloudFront tới AWS WAF.
2. AWS WAF áp giới hạn tốc độ và managed rule, sau đó chuyển lưu lượng sạch tới Amazon API Gateway.
3. Lambda Authorizer xác thực JWT và gọi Verified Permissions. Nhóm `dept-a` có chính sách permit cho `query`, nên lời gọi được cho phép.
4. Middleware Lambda gọi Verified Permissions cho từng tài nguyên Knowledge Base. Chỉ `dept-a` được phép, nên bộ lọc `{"equals": {"key": "department", "value": "dept-a"}}` được dựng.
5. Middleware gọi *RetrieveAndGenerate* với metadata filter đã áp. Amazon Bedrock Knowledge Bases lọc tập tài liệu trước khi chạy tìm kiếm tương đồng vector.
6. Tài liệu Phòng ban B và C bị loại khỏi không gian tìm kiếm. FM sinh ra câu trả lời chỉ bám vào tài liệu Phòng ban A.
7. Câu trả lời được Guardrails for Amazon Bedrock kiểm tra trước khi trả về.

Để kiểm thử, dùng lệnh `curl` sau với một JWT hợp lệ từ Amazon Cognito:

```bash
curl -X POST https://<api-id>.execute-api.<region>.amazonaws.com/prod/query \
  -H "Authorization: Bearer <id_token>" \
  -d '{"query": "Summarize the latest department report"}'
```

Một request `dept-a` thành công trả về câu trả lời chỉ bám vào tài liệu Phòng ban A. Nếu phân quyền thất bại ở Lớp 1, bạn nhận về 403. Nếu Lớp 2 không tìm thấy tài nguyên nào được phép, function trả về `PermissionError`.

Giám sát các quyết định phân quyền trong Amazon CloudWatch Logs cho cả Lambda Authorizer và middleware function. Thiết lập metric filter và alarm CloudWatch cho các chỉ số sau:

- **Tỷ lệ từ chối phân quyền** (Lớp 1 và Lớp 2) — tăng đột biến có thể chỉ dấu việc dò credential, client cấu hình sai, hoặc lỗi chính sách.
- **Độ trễ Verified Permissions** — tăng kéo dài có thể báo hiệu throttling.
- **Số message trong dead-letter queue của SQS** — message trong DLQ cho thấy các sự kiện gắn thẻ metadata thất bại cần xử lý.
- **Tỷ lệ thất bại job ingestion** — cảnh báo về các tài liệu không được index.

AWS CloudTrail tự động ghi lại các lời gọi *IsAuthorized* của Verified Permissions, cung cấp dấu vết audit cho mọi quyết định phân quyền mà không cần cấu hình thêm.

Để quan sát hành vi cập nhật chính sách trực tiếp, hãy cấp cho `dept-a` một chính sách Cedar cho phép truy cập tài nguyên `dept-b`, rồi gửi lại truy vấn ngay lập tức. Lời gọi API kế tiếp phản ánh thay đổi. Thu hồi chính sách và ràng buộc được khôi phục ở lời gọi sau.

### Một knowledge base duy nhất với cô lập bằng metadata

Thêm một phòng ban chỉ đòi hỏi thêm một chính sách Cedar và gắn thẻ tài liệu mới. Bạn không cần cấp hạ tầng bổ sung, deploy stack mới, hay quản lý pipeline ingestion riêng. FM chỉ được cấp tập con tài liệu được phép dựa trên metadata pre-filter đã áp.

Với quản lý phiên (session), dùng một bảng Amazon DynamoDB với session TTL để duy trì ngữ cảnh hội thoại xuyên nhiều request. API *RetrieveAndGenerate* nhận tham số *sessionId* để quản lý ngữ cảnh nhiều lượt tự động. Sinh session ID bằng giá trị ngẫu nhiên về mặt mật mã (ví dụ *uuid4*) và gắn mỗi phiên với danh tính cùng nhóm của người dùng đã xác thực tại thời điểm tạo.

Ở mỗi request tiếp theo, hãy xác thực rằng claim subject và group của bearer token khớp với chủ phiên trước khi tiếp tục hội thoại. Vô hiệu hóa phiên khi thành viên nhóm của người dùng thay đổi hoặc token bị thu hồi, và đặt TTL phù hợp với use case của bạn (ví dụ 30 phút không hoạt động).

---

## Dọn dẹp tài nguyên

Nếu bạn triển khai tài nguyên riêng lẻ, hãy xóa theo thứ tự sau để tránh lỗi phụ thuộc:

1. **Amazon CloudFront distribution** và **AWS WAF web ACL.**
2. **Amazon API Gateway REST API** (đồng thời gỡ liên kết Lambda Authorizer).
3. **Các AWS Lambda function** (gắn thẻ metadata, authorizer, middleware).
4. **Amazon Bedrock Knowledge Base** và data source liên quan.
5. **Amazon S3 bucket** — làm rỗng bucket trước. Nếu bật versioning, xóa mọi phiên bản object và delete marker trước khi xóa bucket.
6. **Amazon EventBridge rule** và **Amazon SQS queue.**
7. **Bảng Amazon DynamoDB.**
8. **Policy store Amazon Verified Permissions.**
9. **Amazon Cognito user pool** (nếu tạo riêng cho mô hình này).
10. **Các IAM role và policy** đã tạo cho Lambda function và API Gateway.
11. **Các Amazon CloudWatch log group** của từng Lambda function.

{{% notice warning %}}
⚠️ Việc xóa các tài nguyên này **không thể hoàn tác**. Hãy sao lưu mọi tài liệu trong S3, dữ liệu DynamoDB, hoặc chính sách Verified Permissions mà bạn có thể cần trước khi tiến hành.
{{% /notice %}}

---

## Kết luận

Bạn giờ đã có một mô hình phân quyền defense-in-depth hoạt động được để kiểm soát truy cập tài liệu chi tiết trong nội bộ tenant cho ứng dụng RAG xây trên Amazon Bedrock. Với cách tiếp cận này, bạn có thể: thay đổi chính sách truy cập tại runtime mà không cần deploy lại code; duy trì cô lập logic ở cấp tài liệu vốn vẫn hiệu quả ngay cả khi lớp API bị cấu hình sai; và audit mọi quyết định phân quyền từ một policy store Verified Permissions duy nhất.

### Những điểm cốt lõi

- **Cập nhật không cần deploy lại.** Chính sách Cedar trong Verified Permissions dễ đọc với con người, được quản lý phiên bản bên ngoài code Lambda, và có hiệu lực ở lời gọi API kế tiếp. Bạn có thể thu hồi quyền của một phòng ban hoặc cấp quyền xuyên phòng ban cho nhóm lãnh đạo chỉ bằng cách cập nhật một chính sách trong console Verified Permissions.
- **Cô lập tài liệu tiết kiệm chi phí, không nhân bản hạ tầng.** Một instance Amazon Bedrock Knowledge Bases duy nhất với metadata pre-filtering mang lại cô lập logic giữa các phòng ban trong một tenant, với chi phí và gánh nặng vận hành chỉ bằng một phần nhỏ so với nhiều instance riêng. Lưu ý đây là cô lập cấp bộ lọc, không phải cấp hạ tầng — với ranh giới tenant cứng, hãy dùng một knowledge base riêng cho mỗi tenant.
- **Các lớp thực thi độc lập giúp giảm rủi ro về điểm lỗi đơn.** Lớp 1 (Lambda Authorizer) và Lớp 2 (middleware Lambda) thực thi các kiểm tra chính sách độc lập. Cả hai đều gọi Verified Permissions riêng, và cả hai đều "fail closed" (mặc định từ chối).

---

## Bước tiếp theo

Để mở rộng mô hình này thêm nữa:

1. **Bước đầu tiên nên làm: Kiểm thử với tài liệu của chính bạn.** Thay các tài liệu phòng ban mẫu bằng nội dung của bạn, upload dưới prefix phù hợp, và xác minh rằng metadata filter cô lập chúng đúng cách.
2. **Thêm một phòng ban thứ tư.** Tạo một chính sách Cedar mới, thêm một user group trong Amazon Cognito, và upload tài liệu đã gắn thẻ để xác thực rằng mô hình mở rộng được mà không cần đổi code.
3. **Mở rộng sang phân quyền tool của agent với Amazon Bedrock AgentCore.** Tính năng Policy dùng chính ngôn ngữ Cedar để thực thi phân quyền chi tiết trên các lời gọi tool và gateway của agent.
4. **Thêm attribute-based access control (ABAC).** Mở rộng chính sách Cedar để đánh giá các thuộc tính người dùng ngoài thành viên nhóm, như phân công project, cấp độ clearance, hoặc vị trí địa lý.
5. **Tích hợp với identity provider của bạn.** Thay Amazon Cognito bằng identity provider doanh nghiệp của bạn (như Okta hoặc Microsoft Entra ID) bằng cách cấu hình một identity source cho Verified Permissions.
6. **Tự động hóa kiểm thử chính sách.** Xây dựng một pipeline CI/CD xác thực chính sách Cedar với các kịch bản test trước khi deploy chúng lên policy store.

---

## Tài nguyên liên quan

- [Multi-tenancy in RAG applications in a single Amazon Bedrock knowledge base with metadata filtering](https://aws.amazon.com/blogs/machine-learning/multi-tenancy-in-rag-applications-in-a-single-amazon-bedrock-knowledge-base-with-metadata-filtering/) (AWS Machine Learning Blog, 4/2025)
- [Tài liệu Amazon Verified Permissions](https://docs.aws.amazon.com/verifiedpermissions/)
- [Tài liệu Amazon Bedrock Knowledge Bases](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base.html)
- [Amazon Bedrock Knowledge Bases with metadata filtering](https://aws.amazon.com/blogs/machine-learning/metadata-filtering-for-tabular-data-with-knowledge-bases-for-amazon-bedrock/) (AWS Machine Learning Blog, 7/2024)
- [Design secure generative AI application workflows with Amazon Verified Permissions and Amazon Bedrock Agents](https://aws.amazon.com/blogs/machine-learning/design-secure-generative-ai-application-workflows-with-amazon-verified-permissions-and-amazon-bedrock-agents/) (AWS Machine Learning Blog, 10/2024)
- [Authorizing access to data with RAG implementations](https://aws.amazon.com/blogs/security/authorizing-access-to-data-with-rag-implementations/) (AWS Security Blog, 9/2025)
- [Tài liệu ngôn ngữ chính sách Cedar](https://docs.cedarpolicy.com/)
