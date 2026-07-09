---
title: "Blog 1"
date: 2026-06-29
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Phân tích log với facets, tương quan, làm giàu và tự động hóa trong Amazon CloudWatch Log Analytics

Các nhóm làm việc với ứng dụng phân tán thường tích lũy log trải rộng trên nhiều log group, bao gồm application log, access log và audit trail. Khi cần điều tra một vấn đề, kỹ sư mở console lên và bắt đầu viết truy vấn từ đầu. Cùng một truy vấn nhưng mỗi người lại viết theo một cách khác nhau. Kết quả trả về thiếu ngữ cảnh vì bản thân log event không chứa thông tin ai sở hữu dịch vụ hay nó thuộc môi trường nào. Kiểm tra hai log group đồng nghĩa với chạy hai truy vấn riêng biệt rồi so sánh kết quả thủ công. Và khi điều tra xong, truy vấn đó biến mất khỏi lịch sử console. Đây là những điểm gây khó chịu thường gặp khi làm việc với log ở quy mô lớn. **Amazon CloudWatch Log Analytics** hiện đã có các tính năng giải quyết trực tiếp những vấn đề này.

1. **Facets** — khám phá dữ liệu log một cách trực quan mà không cần viết truy vấn.
2. **Lookup tables** — làm giàu kết quả truy vấn bằng metadata bên ngoài.
3. **Parameterized queries** — lưu các mẫu truy vấn tái sử dụng với biến điền sẵn.
4. **JOIN và sub-queries** — tương quan dữ liệu giữa nhiều log group trong một truy vấn duy nhất.
5. **Scheduled queries** — chạy truy vấn tự động và gửi kết quả đến Amazon Simple Storage Service (Amazon S3) hoặc Amazon EventBridge.

Bài hướng dẫn này trình bày từng tính năng kèm ví dụ thực tế. Nó chỉ ra vấn đề của khách hàng mà mỗi tính năng giải quyết, cách thiết lập và cách lưu lại để chia sẻ cho cả team. Ở cuối bài, các tính năng được kết hợp trong một ví dụ ngắn để minh họa chúng phối hợp với nhau ra sao, bao gồm cả cách các mẫu truy vấn tái sử dụng này tích hợp vào các quy trình vận hành có AI hỗ trợ (**AIOps**).

---

## Vị trí của các tính năng này trong chiến lược observability của bạn

**AWS Observability Maturity Model** định nghĩa bốn giai đoạn trưởng thành về observability. Các tính năng trong bài này hỗ trợ **Giai đoạn 3 (Advanced Observability)**, nơi các nhóm tương quan tín hiệu giữa nhiều nguồn, phát hiện bất thường và hiểu vấn đề theo ngữ cảnh. Chúng bổ trợ cho các công cụ bạn dùng ở những giai đoạn sớm hơn.

| Giai đoạn                     | Trọng tâm                                                      | Công cụ AWS                                                                                                   |
| ----------------------------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| **Giai đoạn 1: Nền tảng**     | Thu thập telemetry (metrics, logs, traces)                     | Amazon CloudWatch Metrics, Amazon CloudWatch Logs, AWS X-Ray                                                  |
| **Giai đoạn 2: Trung cấp**    | Phân tích, cảnh báo, trực quan hóa                             | Amazon CloudWatch Alarms, Amazon CloudWatch Dashboards, Amazon GuardDuty                                      |
| **Giai đoạn 3: Nâng cao**     | Tương quan đa tín hiệu, làm giàu ngữ cảnh, phát hiện bất thường | Amazon CloudWatch Log Analytics (JOIN, sub-queries, lookup, parameterized queries, scheduled queries)         |
| **Giai đoạn 4: Chủ động**     | Tìm nguyên nhân gốc bằng AI/ML, tự động khắc phục             | Amazon CloudWatch Anomaly Detection                                                                           |

Ở Giai đoạn 3, các nhóm vượt qua việc chỉ nhìn từng metric hay trace riêng lẻ để tương quan dữ liệu giữa nhiều nguồn bằng logic tùy chỉnh. Các tính năng trong bài này giúp thực hiện việc tương quan đó ngay trong dữ liệu log của bạn.

Để truy vết request xuyên dịch vụ, hãy dùng AWS X-Ray hoặc OpenTelemetry. Để phát hiện mối đe dọa tự động, hãy dùng Amazon GuardDuty. Các tính năng Log Analytics được đề cập ở đây dành cho việc phân tích log tùy chỉnh trải rộng trên nhiều log group. Chúng áp dụng các quy tắc nghiệp vụ của tổ chức bạn và tạo ra các mẫu tái sử dụng mà cả team có thể chia sẻ.

---

## Điều kiện tiên quyết

Để làm theo bài hướng dẫn này, bạn cần:

- Một **AWS account** có structured log chảy về ít nhất hai **Amazon CloudWatch** log group (bài này dùng `/demo/security/api-activity` và `/demo/security/access-logs`).
- Quyền **AWS Identity and Access Management (IAM)** cho các hành động sau:
  - `logs:PutQueryDefinition`
  - `logs:StartQuery`
  - `logs:DescribeQueryDefinitions`
  - `logs:GetQueryResults`
  - `logs:PutIndexPolicy`
  - `logs:DeleteQueryDefinition`
  - `logs:DeleteIndexPolicy`
- **AWS CLI** phiên bản 2.34.56 trở lên đã cài đặt và cấu hình.
- **Node.js** 18 trở lên và **AWS Cloud Development Kit (AWS CDK)** phiên bản 2.100 trở lên đã cài đặt.
- Hiểu biết cơ bản về cú pháp truy vấn **CloudWatch Logs Insights** (fields, filter, stats, sort).

---

## Triển khai môi trường ví dụ

Để triển khai môi trường ví dụ kèm dữ liệu log mẫu, hãy dùng CDK stack đi kèm trong repository. Stack này tạo cả hai log group (`/demo/security/api-activity` và `/demo/security/access-logs`) và đẩy vào các event mẫu khớp với schema dùng trong bài.

Clone repository và triển khai stack:

```bash
git clone https://github.com/aws-samples/sample-cloudwatch-logs-advanced-analysis-environment.git
cd sample-cloudwatch-logs-advanced-analysis-environment
npm install
npx cdk bootstrap
npx cdk deploy
```

Lệnh `cdk bootstrap` chỉ cần chạy một lần cho mỗi account và Region. Sau khi stack triển khai thành công (khoảng 5 phút), các log group bắt đầu nhận event mẫu. Hãy đợi 2–3 phút để dữ liệu log tích lũy trước khi chạy các truy vấn bên dưới.

Đặt Region cho phiên terminal này để có thể bỏ qua `--region` trong các lệnh CLI phía sau:

```bash
export AWS_REGION=<your-region>
```

Thay `<your-region>` bằng Region nơi bạn đã triển khai CDK stack. Biến này chỉ tồn tại trong phiên terminal hiện tại.

> **Cách khác:** Nếu bạn đã có structured log chảy về CloudWatch, bạn có thể bỏ qua bước triển khai và dùng chính các log group của mình trong toàn bộ bài hướng dẫn.

---

## Môi trường ví dụ

Bài hướng dẫn sử dụng hai log group:

- **`/demo/security/api-activity`** — các event hoạt động API với các trường như `eventSource`, `eventName`, `sourceIPAddress`, `errorCode`, và `userIdentity_principalId`.
- **`/demo/security/access-logs`** — application access log với các trường như `userId`, `resource`, `action`, và `sourceIP`.
- Một **lookup table** (được tạo trong phần [Lookup tables](#lookup-tables-lam-giau-ngu-canh)) ánh xạ account ID sang metadata của tổ chức.

![Hình 1](/images/3-BlogsTranslated/3.1-Blog1/figure-01-architecture-cloudops-2335-1.png)

> *Hình 1. Môi trường ví dụ thể hiện các nguồn log, các trường quan trọng và lookup table.*

---

## Facets để khám phá trực quan

> **Vấn đề của khách hàng:** "Tôi muốn thấy có những mẫu (pattern) nào tồn tại trong log trước khi viết truy vấn. Tôi chưa biết nên lọc theo cái gì."

Với facets, đầu tiên bạn chạy một truy vấn để nạp log event, và CloudWatch Log Analytics sẽ hiển thị các facet tương tác ở panel bên phải dựa trên các trường đã được index. Mỗi facet cho thấy phân bố giá trị của trường đó. Bạn có thể chọn một giá trị facet để thêm nó làm bộ lọc, rồi chạy lại truy vấn để chỉ xem các kết quả khớp.

### Khám phá log bằng facets

1. Mở **CloudWatch console**, trong mục **Logs**, chọn **Log Analytics**.
2. Trong Log Analytics, chạy truy vấn sau — có sử dụng facet `errorCode` được lọc theo `AccessDenied`. Thay `123456789012` bằng số AWS account của bạn:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
fields @timestamp, eventSource, eventName, errorCode, sourceIPAddress
| sort @timestamp desc
| limit 50
| filterIndex errorCode in ["AccessDenied"]
```

3. Chạy truy vấn. Kết quả giờ chỉ hiển thị các event có `errorCode` là `AccessDenied`, kèm theo các giá trị `sourceIPAddress` tương ứng cho từng request bị từ chối.

![Hình 2](/images/3-BlogsTranslated/3.1-Blog1/figure-02-facets-errorcode-distribution-cloudops-2335-1.png)

> *Hình 2. Facets hiển thị phân bố `errorCode` với `AccessDenied` được chọn làm bộ lọc.*

### Facets mặc định

Mỗi log group đều có sẵn bốn facet mặc định:

- `@aws.region` — Region AWS đã tạo ra log event
- `@data_format` — định dạng dữ liệu log
- `@data_source_name` — tên log group
- `@data_source_type` — loại nguồn log (ví dụ Lambda, ECS, EC2)

Những facet này hoạt động ngay lập tức, không cần cấu hình gì thêm.

![Hình 3](/images/3-BlogsTranslated/3.1-Blog1/figure-03-default-facets-cloudops-2335-1.png)

> *Hình 3. Panel facet mặc định hiển thị `@aws.region`, `@data_format`, `@data_source_name`, và `@data_source_type`.*

### Tạo custom facets bằng index policy

Nếu bạn đã triển khai CDK stack đi kèm, các index policy cho `/demo/security/api-activity` và `/demo/security/access-logs` đã có sẵn. Các lệnh bên dưới cho thấy những gì stack đã tạo, để bạn có thể kiểm tra hoặc tái tạo trong môi trường của mình.

Facet mặc định hữu ích nhưng chung chung. Với application log của bạn, bạn sẽ muốn facet trên những trường thực sự quan trọng với team. Index policy được định nghĩa theo từng log group, vì vậy hãy tạo một cái cho mỗi log group với những trường liên quan đến nội dung của nó.

Với log group hoạt động API, index các trường như `eventSource`, `eventName`, `errorCode`, và `sourceIPAddress`:

```bash
aws logs put-index-policy \
  --log-group-identifier "/demo/security/api-activity" \
  --policy-document '{
    "FieldsV2": {
      "eventSource": {"type": "FACET"},
      "eventName": {"type": "FACET"},
      "errorCode": {"type": "FACET"},
      "sourceIPAddress": {"type": "FACET"},
      "userIdentity_accountId": {"type": "FACET"},
      "userIdentity_principalId": {"type": "FIELD_INDEX"}
    }
  }'
```

Với log group access log, index các trường hữu ích để theo dõi ai đã truy cập cái gì:

```bash
aws logs put-index-policy \
  --log-group-identifier "/demo/security/access-logs" \
  --policy-document '{
    "FieldsV2": {
      "userId": {"type": "FACET"},
      "resource": {"type": "FACET"},
      "action": {"type": "FACET"},
      "sourceIP": {"type": "FACET"}
    }
  }'
```

Sau vài phút, các trường này xuất hiện dưới dạng facet có thể chọn trong Log Analytics console. Kỹ sư có thể chạy một truy vấn, sau đó chọn `errorCode` ở panel facet bên phải để xem phân bố giá trị. Chọn một giá trị cụ thể như `AccessDenied` sẽ thêm nó làm bộ lọc. Chạy lại truy vấn chỉ trả về các event khớp, mà không cần viết lại cú pháp truy vấn thủ công.

### Thực hành tốt khi chọn trường làm facet

Index policy hoạt động tốt nhất trên các trường có **cardinality thấp** (ít giá trị khác nhau). Facet có hơn 100 giá trị duy nhất mỗi ngày được xếp vào loại cardinality cao, và giá trị của chúng sẽ không được hiển thị trong console. Bài hướng dẫn này dùng `eventSource`, `errorCode`, và `userIdentity_accountId` vì chúng có số giá trị khác nhau nhỏ và hữu ích cho việc khám phá trực quan.

---

## Lookup tables để làm giàu ngữ cảnh

> **Vấn đề của khách hàng:** "Log event của tôi chứa tên dịch vụ hoặc account ID, nhưng tôi cần biết ai sở hữu nó, nó thuộc môi trường nào, và cần liên hệ ai. Những thông tin đó nằm trong một bảng tính, không phải trong log."

Với lookup tables, bạn có thể làm giàu kết quả truy vấn bằng metadata bên ngoài. Chỉ cần tải lên một file CSV một lần, và bất kỳ truy vấn nào cũng có thể tham chiếu đến nó để thêm các cột không tồn tại trong log event gốc.

### Tải file CSV về

Bài hướng dẫn này ánh xạ account ID sang ngữ cảnh của tổ chức. Hãy tải file `account-metadata.csv` từ repository và lưu về máy tính của bạn.

### Tải lookup table lên

Tải file CSV lên dưới dạng một lookup table tên là `account_metadata`:

1. Mở **CloudWatch console**.
2. Ở navigation pane, chọn **Settings**, sau đó chọn tab **Logs**.
3. Cuộn đến **Lookup tables** và chọn **Manage**.
4. Chọn **Create lookup table**.
5. Nhập `account_metadata` làm tên bảng.
6. Tải lên file `account-metadata.csv` bạn vừa tải về, rồi chọn **Create lookup table**.

Để kiểm tra bảng có thể truy vấn được, chạy truy vấn sau trong Log Analytics. Thay `123456789012` bằng số AWS account của bạn:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-10800s END=0s |
fields @timestamp, userIdentity_principalId, userIdentity_accountId, eventName
| filter ispresent(userIdentity_accountId)
| lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner
| fields @timestamp, userIdentity_principalId, userIdentity_accountId, account_name, environment, owner
| limit 10
```

Nếu `account_name`, `environment`, và `owner` xuất hiện trong kết quả thì bảng đang hoạt động.

![Hình 4](/images/3-BlogsTranslated/3.1-Blog1/figure-04-lookup-query-results-cloudops-2335-1.png)

> *Hình 4. Kết quả truy vấn lookup hiển thị các cột `account_name` và `owner` được làm giàu từ file CSV.*

---

## Parameterized queries để tạo mẫu tái sử dụng

> **Vấn đề của khách hàng:** "Tôi viết cùng một truy vấn mỗi lần chỉ với các giá trị khác nhau. Thành viên mới không biết nên chạy truy vấn nào. Không có thư viện truy vấn hữu ích nào được chia sẻ chung."

Với parameterized queries, bạn lưu một mẫu truy vấn với các chỗ trống dạng `{{parameter_name}}`. Khi ai đó nạp truy vấn đã lưu từ console, các tham số được thay bằng giá trị mặc định mà họ có thể điều chỉnh trước khi chạy. Truy vấn xuất hiện trong panel **Saved and sample queries**, cả team đều có thể tìm thấy.

### Tạo một parameterized query

Nếu bạn đã triển khai CDK stack đi kèm, query definition `Security/PrincipalActivityAudit` đã được tạo sẵn. Lệnh bên dưới cho thấy những gì stack đã triển khai, để bạn kiểm tra hoặc tái tạo trong môi trường của mình.

Truy vấn này hiển thị mọi hoạt động của một principal nhất định, được làm giàu bằng metadata account từ lookup table. Tham số `{{principalId}}` cho phép bất kỳ thành viên nào chạy truy vấn này cho bất kỳ principal nào mà không cần viết lại.

```
SOURCE "/demo/security/api-activity" START=-604800s END=0s |
fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode, userIdentity_accountId
| filter userIdentity_principalId = "{{principalId}}"
| lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner
| sort @timestamp desc
```

**Tham số**

| Tham số       | Mô tả                          | Giá trị mặc định        |
| ------------- | ------------------------------ | ----------------------- |
| `principalId` | Principal ID cần điều tra       | `AIDA1234567890EXAMPLE` |

Để đưa truy vấn này đến với cả team, hãy lưu nó thành một query definition bằng lệnh CLI sau:

```bash
aws logs put-query-definition \
  --name "Security/PrincipalActivityAudit" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode, userIdentity_accountId | filter userIdentity_principalId = "{{principalId}}" | lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner | sort @timestamp desc' \
  --parameters '[{"name":"principalId","defaultValue":"AIDA1234567890EXAMPLE","description":"The principal ID to investigate"}]'
```

Để xem truy vấn đã lưu, mở **Saved and sample queries** trong Log Analytics console, vào thư mục **Security**, và chọn `PrincipalActivityAudit`. Truy vấn được nạp lên với ô nhập tham số `principalId` đã điền sẵn giá trị mặc định `AIDA1234567890EXAMPLE`.

![Hình 5](/images/3-BlogsTranslated/3.1-Blog1/figure-05-parameterized-query-input-cloudops-2335-1.png)

> *Hình 5. Parameterized query đã lưu hiển thị ô nhập `principalId` trong Log Analytics console.*

Giờ đây bất kỳ thành viên nào cũng có thể mở **Saved and sample queries**, tìm thư mục **Security**, chọn `PrincipalActivityAudit`, điều chỉnh principal ID và chạy — không cần viết truy vấn nào.

### Bật dữ liệu bất thường có tương quan

Các phần còn lại (JOIN, sub-queries và scheduled queries) tương quan dữ liệu giữa cả hai log group. Để có kết quả có ý nghĩa, cả ba bộ sinh log cần bật chế độ anomaly để tạo ra các event có tương quan từ IP `198.51.100.42`. Chạy các lệnh sau trong **AWS CloudShell** ở cùng Region nơi bạn đã triển khai CDK stack.

```bash
aws lambda update-function-configuration \
  --function-name demo-anomaly-generator \
  --environment "Variables={API_LOG_GROUP=/demo/security/api-activity,ACCESS_LOG_GROUP=/demo/security/access-logs,ANOMALY_MODE=true}"
```

```bash
aws lambda update-function-configuration \
  --function-name demo-cloudtrail-generator \
  --environment "Variables={LOG_GROUP_NAME=/demo/security/api-activity,ANOMALY_MODE=true}"
```

```bash
aws lambda update-function-configuration \
  --function-name demo-access-log-generator \
  --environment "Variables={LOG_GROUP_NAME=/demo/security/access-logs,ANOMALY_MODE=true}"
```

Đợi 5–10 phút để các event có tương quan tích lũy trước khi chuyển sang phần JOIN.

---

## JOIN để tương quan giữa các log group

> **Vấn đề của khách hàng:** "Tôi cần xem dữ liệu từ hai log group cùng lúc. Hiện tại tôi phải chạy hai truy vấn riêng và so sánh kết quả thủ công."

Với JOIN, bạn có thể tương quan các event giữa nhiều log group trong một truy vấn duy nhất. Truy vấn chính chạy trên một log group. JOIN kéo về các event khớp từ log group thứ hai dựa trên một trường chung, chẳng hạn địa chỉ IP, user ID, hoặc request ID.

### Tạo một truy vấn JOIN

Truy vấn này hiển thị cả những API call nào đã được thực hiện và những application resource nào đã bị truy cập từ một IP nhất định, trong cùng một tập kết quả. Chạy lệnh sau trong Log Analytics. Thay `123456789012` bằng số AWS account của bạn:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode
| join type=inner left=api right=access where api.sourceIPAddress=access.sourceIP (SOURCE "/demo/security/access-logs")
| filter api.sourceIPAddress = "198.51.100.42"
| fields api.@timestamp, api.eventName, api.eventSource, api.errorCode, access.userId, access.resource, access.action
| sort api.@timestamp desc
| limit 10
```

**Tham số**

| Tham số        | Mô tả                                            | Giá trị mặc định |
| -------------- | ------------------------------------------------ | ---------------- |
| `suspiciousIP` | Địa chỉ IP cần điều tra trên nhiều nguồn log      | `198.51.100.42`  |

Lưu truy vấn này thành một definition tái sử dụng bằng lệnh CLI `put-query-definition`:

```bash
aws logs put-query-definition \
  --name "Security/CrossSourceCorrelation" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode | join type=inner left=api right=access where api.sourceIPAddress=access.sourceIP (SOURCE "/demo/security/access-logs") | filter api.sourceIPAddress = "{{suspiciousIP}}" | fields api.@timestamp, api.eventName, api.eventSource, api.errorCode, access.userId, access.resource, access.action | sort api.@timestamp desc | limit 100' \
  --parameters '[{"name":"suspiciousIP","defaultValue":"198.51.100.42","description":"IP address to investigate across log sources"}]'
```

![Hình 6](/images/3-BlogsTranslated/3.1-Blog1/figure-06-join-correlation-cloudops-2335-1.png)

> *Hình 6. Kết quả JOIN hiển thị các API call và việc truy cập resource được tương quan theo source IP.*

---

## Sub-queries để phân tích nhiều bước

> **Vấn đề của khách hàng:** "Tôi muốn tìm các thực thể khớp với một điều kiện trong một log group, rồi tra cứu hoạt động của chúng trong một log group khác. Việc này hiện cần đến hai truy vấn."

Với sub-queries, đầu ra của một truy vấn được đưa vào bộ lọc của truy vấn khác. Truy vấn bên trong xác định một tập giá trị (như principal ID hoặc tên dịch vụ), và truy vấn bên ngoài dùng các giá trị đó để lấy dữ liệu liên quan.

### Tạo một sub-query

Ví dụ này tìm các principal có nhiều lỗi access denied lặp lại, rồi lấy về tất cả các hành động thành công của chúng. Chạy lệnh sau trong Log Analytics. Thay `123456789012` bằng số AWS account của bạn:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
filter userIdentity_principalId in (
    SOURCE "/demo/security/api-activity"
    | filter errorCode = "AccessDenied"
    | stats count(*) as denied_count by userIdentity_principalId
    | filter denied_count > 3
    | fields userIdentity_principalId
)
| filter errorCode != "AccessDenied"
| stats count(*) as successful_actions by userIdentity_principalId, eventSource, eventName
| sort successful_actions desc
```

**Tham số**

| Tham số           | Mô tả                                              | Giá trị mặc định |
| ----------------- | -------------------------------------------------- | ---------------- |
| `deniedThreshold` | Số hành động bị từ chối tối thiểu để đánh dấu 1 principal | `3`         |

Lưu truy vấn này thành một definition tái sử dụng bằng lệnh CLI `put-query-definition`:

```bash
aws logs put-query-definition \
  --name "Security/DeniedThenSucceeded" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'filter userIdentity_principalId in (SOURCE "/demo/security/api-activity" | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId | filter denied_count > {{deniedThreshold}} | fields userIdentity_principalId) | filter errorCode != "AccessDenied" | stats count(*) as successful_actions by userIdentity_principalId, eventSource, eventName | sort successful_actions desc' \
  --parameters '[{"name":"deniedThreshold","defaultValue":"3","description":"Minimum denied actions to flag a principal"}]'
```

![Hình 7](/images/3-BlogsTranslated/3.1-Blog1/figure-07-subquery-results-cloudops-2335-1.png)

> *Hình 7. Kết quả sub-query hiển thị các hành động thành công bởi những principal đã có lỗi access denied lặp lại.*

---

## Scheduled queries để phân tích tự động

> **Vấn đề của khách hàng:** "Tôi muốn truy vấn này tự chạy theo lịch và cảnh báo cho tôi nếu phát hiện điều gì đó. Tôi không muốn phải mở console lên để kiểm tra."

Với scheduled queries, bạn chạy các truy vấn Log Analytics theo chu kỳ lặp lại và gửi kết quả đến Amazon S3, Amazon EventBridge, hoặc cả hai. Scheduled queries hỗ trợ **CloudWatch Logs Insights QL**, **OpenSearch PPL**, và **OpenSearch SQL**.

### Tạo một scheduled query

Ví dụ này lên lịch một truy vấn chạy mỗi 5 phút và đánh dấu các principal có lỗi access denied lặp lại. Khi tìm thấy kết quả, chúng được đưa vào Amazon S3. Sau đó bạn có thể cấu hình một Amazon S3 event notification hoặc dùng Amazon EventBridge để kích hoạt cảnh báo phía sau.

CDK stack không tạo scheduled queries. Trước khi tạo, hãy lấy các output của stack cần cho lệnh CLI:

```bash
aws cloudformation describe-stacks \
  --stack-name SecurityLogsInsightsStack \
  --query "Stacks[0].Outputs" \
  --output table
```

Từ output, ghi lại giá trị của `ScheduledQueryRoleArn` (ARN của execution role) và `ResultsBucketName` (đường dẫn S3). Dùng các giá trị này để thay vào các chỗ trống tương ứng trong lệnh sau:

```bash
aws logs create-scheduled-query \
  --name "ProactiveAccessDeniedDetection" \
  --query-language "CWLI" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId, sourceIPAddress | filter denied_count > 10 | sort denied_count desc' \
  --log-group-identifiers "/demo/security/api-activity" \
  --schedule-expression "cron(0/5 * * * ? *)" \
  --start-time-offset 600 \
  --execution-role-arn "<ScheduledQueryRoleArn>" \
  --destination-configuration '{"s3Configuration":{"destinationIdentifier":"s3://<ResultsBucketName>/scheduled-query-results","roleArn":"<ScheduledQueryRoleArn>"}}'
```

Để kiểm tra, mở CloudWatch console và vào **Logs**, sau đó **Log Analytics**. Chọn biểu tượng **Scheduled Queries**, và trong cửa sổ pop-up bạn sẽ thấy `ProactiveAccessDeniedDetection`. Chọn nó để xem chi tiết lịch chạy.

![Hình 8](/images/3-BlogsTranslated/3.1-Blog1/figure-08-scheduled-query-config-cloudops-2335-1.png)

> *Hình 8. Cấu hình scheduled query trong CloudWatch console.*

Execution role cần các quyền `logs:StartQuery`, `logs:GetQueryResults`, và `s3:PutObject`. Biểu thức lịch dùng cú pháp cron; `cron(0/5 * * * ? *)` chạy mỗi 5 phút.

### Báo cáo tổng hợp hàng tuần vào Amazon S3

Tùy chọn, bạn cũng có thể thiết lập một báo cáo tổng hợp hàng tuần nhóm các event access denied theo principal và account rồi gửi kết quả đến Amazon S3. Scheduled query này không được CDK stack tạo. Chạy lệnh sau, dùng lại các giá trị output của stack cho `<ScheduledQueryRoleArn>` và `<ResultsBucketName>` mà bạn đã lấy trước đó:

```bash
aws logs create-scheduled-query \
  --name "WeeklyAccessDeniedReport" \
  --query-language "CWLI" \
  --query-string 'fields @timestamp, userIdentity_principalId, userIdentity_accountId, eventSource, eventName, sourceIPAddress | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId, userIdentity_accountId, eventSource | sort denied_count desc' \
  --log-group-identifiers "/demo/security/api-activity" \
  --schedule-expression "cron(0 8 ? * MON *)" \
  --start-time-offset 604800 \
  --execution-role-arn "<ScheduledQueryRoleArn>" \
  --destination-configuration '{"s3Configuration":{"destinationIdentifier":"s3://<ResultsBucketName>/weekly-reports","roleArn":"<ScheduledQueryRoleArn>"}}'
```

Truy vấn này chạy vào 08:00 UTC mỗi thứ Hai và quét 7 ngày trước đó (604.800 giây).

---

## Kết hợp mọi thứ lại với nhau

Ví dụ sau cho thấy các tính năng phối hợp ra sao. Đây không phải là một phương pháp bắt buộc — nó chỉ minh họa cách facets, parameterized queries, lookup, JOIN và sub-queries, cùng scheduled queries hoạt động cùng nhau trong một quy trình duy nhất.

![Hình 9](/images/3-BlogsTranslated/3.1-Blog1/figure-09-investigation-flow-cloudops-2335.png)

> *Hình 9. Quy trình ví dụ thể hiện các tính năng được dùng kết hợp.*

**Kịch bản:** Một scheduled query đánh dấu một principal có nhiều lỗi access denied lặp lại từ một IP lạ. Một kỹ sư bắt đầu điều tra.

1. **Scheduled query (phát hiện).** Scheduled query `ProactiveAccessDeniedDetection` chạy chu kỳ 5 phút và đánh dấu principal `AIDA9876543210SUSPECT` với các lỗi access denied từ IP `198.51.100.42`. Kết quả được đưa vào Amazon S3 và một event EventBridge thông báo cho team.
2. **Facets (phân loại nhanh).** Kỹ sư mở CloudWatch Log Analytics và chọn log group `/demo/security/api-activity`. Facet `errorCode` ở panel bên phải cho thấy số event `AccessDenied` tăng cao. Họ chọn giá trị đó để lọc kết quả và xác định những IP và principal nào liên quan.
3. **Parameterized query + lookup (audit).** Kỹ sư mở panel **Saved and sample queries**, vào thư mục **Security**, và chọn `PrincipalActivityAudit`. Truy vấn nạp lên với tham số `principalId` điền sẵn. Họ đổi nó thành principal bị đánh dấu và chạy truy vấn. Kết quả cho thấy mọi API call của principal đó, được làm giàu với tên account và môi trường từ lookup table.
4. **JOIN (tương quan).** Kỹ sư chọn `CrossSourceCorrelation` từ cùng panel **Saved and sample queries**. Truy vấn nạp lên với tham số `suspiciousIP` (địa chỉ IP cần điều tra trên cả hai log group). Họ đặt nó thành IP bị đánh dấu và chạy truy vấn. Kết quả tương quan hoạt động API với application access log từ cùng một IP trong một khung nhìn duy nhất.
5. **Sub-query (khoanh vùng).** Kỹ sư chọn `DeniedThenSucceeded` từ panel **Saved and sample queries**. Truy vấn nạp lên với tham số `deniedThreshold` (số hành động bị từ chối tối thiểu để đánh dấu một principal). Họ đặt nó thành 3 và chạy truy vấn. Kết quả cho thấy principal bị đánh dấu đã thực hiện thành công những gì sau các lần bị từ chối.

Mỗi bước xây dựng trên bước trước. Trong ví dụ này, kỹ sư không viết truy vấn nào từ đầu. Họ dùng các mẫu đã lưu, điều chỉnh tham số, và nhận về kết quả đã được làm giàu và tương quan.

---

## Từ mẫu truy vấn đến vận hành có AI hỗ trợ

Các parameterized query và quy trình trong bài này không chỉ dành cho kỹ sư con người chạy truy vấn thủ công. Chúng là những khối xây dựng có cấu trúc, truy cập được qua API mà các AI agent và công cụ tự động hóa có thể sử dụng trực tiếp.

Mỗi query definition đã lưu có một cái tên, các tham số kèm mô tả, và một log group đích. Một AI agent có thể nạp một truy vấn đã lưu theo tên và điền vào các tham số dựa trên một cảnh báo hoặc yêu cầu của người dùng. Sau đó nó chạy truy vấn qua CloudWatch Logs API và diễn giải kết quả. Agent không cần biết cú pháp truy vấn. Nó chỉ cần biết chạy truy vấn nào và cung cấp tham số gì.

Ví dụ, trong **Kiro** (một môi trường phát triển có AI hỗ trợ), bạn có thể tạo một "skill". Skill này chứa tên các truy vấn đã lưu, mô tả tham số, quy trình điều tra và các quy tắc cú pháp. Khi bạn yêu cầu Kiro "điều tra IP `198.51.100.42`", nó biết cần chạy `Security/CrossSourceCorrelation` với tham số đó, diễn giải kết quả tương quan và gợi ý các bước tiếp theo. Các công cụ AI khác (ChatOps bot, tự động hóa tùy chỉnh, hoặc bất kỳ agent nào có quyền truy cập CloudWatch Logs API) đều có thể dùng chung các query definition đã lưu này.

Đây là lộ trình từ điều tra thủ công đến AIOps:

1. **Thủ công.** Kỹ sư viết truy vấn từ đầu mỗi lần.
2. **Theo mẫu.** Team lưu các parameterized query. Kỹ sư chọn một mẫu và điền giá trị.
3. **Tự động.** Scheduled query chạy theo chu kỳ mà không cần can thiệp của con người.
4. **AI hỗ trợ.** Kỹ sư yêu cầu một công cụ có AI (như Kiro) điều tra một principal hoặc IP. Công cụ biết chạy truy vấn đã lưu nào, điền tham số, thực thi, và trình bày kết quả kèm ngữ cảnh.

Các tính năng trong bài này đưa bạn từ bước 1 đến bước 3. Những query definition, lookup table và quy trình có cấu trúc mà bạn tạo ra sẽ trở thành các "skill" mà một AI agent có thể dùng ở bước 4. Việc đầu tư xây dựng các mẫu tái sử dụng mang lại lợi ích hai lần: một lần cho team của bạn hôm nay, và một lần nữa khi bạn tích hợp các công cụ có AI hỗ trợ.

---

## Cân nhắc về chi phí

CloudWatch Log Analytics tính phí dựa trên lượng dữ liệu mà mỗi truy vấn quét qua. Để biết giá hiện hành, xem **Amazon CloudWatch pricing**.

**Mẹo tối ưu**

- Đặt lệnh `filter` càng sớm càng tốt trong truy vấn để giảm lượng dữ liệu bị quét. Với truy vấn JOIN, đặt filter sau JOIN bằng tên trường đã được alias (ví dụ `filter api.sourceIPAddress = "value"`).
- JOIN quét cả log group chính và log group trong SOURCE. Hãy chọn trường cụ thể trong lệnh `fields` ban đầu để giới hạn những gì được xử lý.
- Dữ liệu lookup table không tính vào phí dữ liệu bị quét.
- Dùng `stats` để tổng hợp trước khi join, thay vì join các event thô.
- Với scheduled query, đặt `start-time-offset` bằng khung thời gian tối thiểu vẫn đảm bảo phát hiện đáng tin cậy.
- Field index có thể tăng tốc truy vấn và giảm chi phí bằng cách bỏ qua các log event không khớp với trường đã index. Chỉ index những trường bạn sẽ dùng làm facet hoặc thường xuyên lọc theo.
- Các mẫu có tham số khuyến khích truy vấn có mục tiêu, đã lọc sẵn thay vì quét rộng để dò tìm, qua đó giảm tổng chi phí quét theo thời gian.

---

## Dọn dẹp tài nguyên

Để tránh phát sinh chi phí liên tục từ các tài nguyên đã tạo trong bài, hãy xóa những thứ sau. CDK stack đi kèm liên tục sinh dữ liệu log, phát sinh phí ingestion và lưu trữ của CloudWatch Logs chừng nào nó còn được triển khai.

- Nếu bạn đã triển khai môi trường ví dụ bằng CDK stack, chạy `npx cdk destroy` để xóa các tài nguyên của stack (log group, Lambda function, index policy, bộ sinh dữ liệu mẫu và query definition `PrincipalActivityAudit`).

{{% notice warning %}}
⚠️ Hành động này **không thể hoàn tác** và sẽ xóa toàn bộ dữ liệu log trong các log group do stack tạo ra.
{{% /notice %}}

Xóa các tài nguyên sau mà bạn đã tạo thủ công trong bài:

- **Lookup tables.** Xóa lookup table `account_metadata` qua CloudWatch console.
- **Scheduled queries.** Xóa `ProactiveAccessDeniedDetection` và `WeeklyAccessDeniedReport`. Để tìm query ID, chạy lệnh sau rồi xóa từng cái bằng scheduled query ID:

```bash
aws logs describe-scheduled-queries
aws logs delete-scheduled-query --scheduled-query-id <query-id>
```

---

## Kết luận

Bài hướng dẫn này đã trình bày từng tính năng mới của CloudWatch Log Analytics kèm ví dụ thực tế, chỉ ra vấn đề của khách hàng mà mỗi tính năng giải quyết:

1. **Facets** giúp kỹ sư khám phá pattern trong log một cách trực quan mà không cần viết truy vấn.
2. **Lookup tables** làm giàu kết quả bằng ngữ cảnh của tổ chức nằm ngoài log.
3. **Parameterized queries** biến các truy vấn tạm thời thành mẫu tái sử dụng, chia sẻ được.
4. **JOIN và sub-queries** tương quan dữ liệu giữa nhiều log group và nối chuỗi phân tích nhiều bước mà không cần copy-paste thủ công giữa các truy vấn.
5. **Scheduled queries** tự động hóa việc phân tích lặp lại và gửi kết quả mà không cần ai mở console.

Các tính năng này hỗ trợ Giai đoạn 3 (Advanced Observability) của **AWS Observability Maturity Model**, nơi các nhóm tương quan tín hiệu giữa nhiều nguồn và phát hiện bất thường kèm làm giàu ngữ cảnh. Chúng bổ trợ cho CloudWatch Metrics và Alarms (cho các ngưỡng đã biết), truy vết phân tán với AWS X-Ray (cho tương quan request xuyên dịch vụ), và Amazon GuardDuty (cho phát hiện mối đe dọa tự động).

Để bắt đầu, hãy tạo một index policy trên một log group của bạn và khám phá các facet xuất hiện. Từ đó, lưu parameterized query đầu tiên và chia sẻ nó với team của bạn.

---

## Tài nguyên bổ sung

- [AWS Observability Maturity Model](https://aws-observability.github.io/observability-best-practices/guides/observability-maturity-model/)
- [Cú pháp truy vấn Amazon CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
- [Lưu và chạy lại truy vấn Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_Insights-Saving-Queries.html)
- [CloudWatch Logs lookup tables](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Lookup.html)
- [Lệnh JOIN của CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Join.html)
- [Sub-queries của CloudWatch Logs Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Subqueries.html)
- [CloudWatch Logs scheduled queries](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/ScheduledQueries.html)
- [CloudWatch Logs facets](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CloudWatchLogs-Facets.html)
- [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)
