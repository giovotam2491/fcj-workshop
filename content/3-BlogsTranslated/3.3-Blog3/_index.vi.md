---
title: "Blog 3"
date: 2026-06-29
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---
# Tự động số hóa hồ sơ bệnh án với Amazon Bedrock Data Automation và AWS HealthLake

Các cơ sở y tế đang quản lý hàng triệu hồ sơ bệnh án giấy vốn tách rời khỏi các hệ thống lâm sàng hiện đại. Bác sĩ đưa ra quyết định mà không có đầy đủ tiền sử bệnh nhân, các tổ chức tiêu tốn hàng triệu đô cho việc nhập liệu thủ công, và những thông tin quan trọng vẫn bị "mắc kẹt" trong các định dạng mà ứng dụng hiện đại không đọc được. Thách thức kỹ thuật ở đây rất rõ ràng: làm sao chuyển đổi các tài liệu scan phi cấu trúc thành dữ liệu y tế chuẩn hóa, có khả năng tương tác (interoperable) ở quy mô lớn — mà không phải xây dựng model machine learning (ML) tùy chỉnh hay tự viết parser cho từng loại biểu mẫu.

Trong bài viết này, bạn sẽ học cách xây dựng một pipeline serverless tự động, chuyển đổi hồ sơ bệnh án dạng PDF scan thành dữ liệu tuân thủ chuẩn **FHIR R4** bằng **Amazon Bedrock Data Automation** và **AWS HealthLake**. Chúng ta sẽ đi qua kiến trúc, giải thích cách từng dịch vụ AWS kết nối với dịch vụ tiếp theo, cho bạn thấy pipeline hoạt động như thế nào khi chạy, và giúp bạn triển khai xong trong chưa đầy 20 phút. Về cấu hình nâng cao, xử lý sự cố và các tùy chọn tùy biến, hãy xem GitHub repository của giải pháp.

---

## Thách thức với hồ sơ bệnh án giấy

Các tổ chức y tế đối mặt với một vấn đề chồng chất. Hồ sơ giấy không chỉ tạo ra khó khăn về lưu trữ, mà còn tạo ra khoảng trống trong chăm sóc (care gap). Khi một bệnh nhân đến một cơ sở mới, bác sĩ thường phải tiến hành với thông tin không đầy đủ vì việc truy xuất và diễn giải hồ sơ cũ mất quá nhiều thời gian. Số hóa thủ công thì tốn kém, dễ sai sót và không mở rộng được.

Giải pháp đòi hỏi nhiều hơn là chỉ scan tài liệu. Nó cần trích xuất dữ liệu có cấu trúc, có ý nghĩa lâm sàng, và lưu trữ ở định dạng tích hợp được với các hệ thống hiện có. Đó là lúc **Fast Healthcare Interoperability Resources (FHIR)** phát huy vai trò. FHIR là chuẩn của ngành y tế để trao đổi thông tin sức khỏe điện tử.

---

## Tổng quan giải pháp

Giải pháp này sử dụng kiến trúc serverless, hướng sự kiện (event-driven) để tự động hóa toàn bộ hành trình từ lúc upload PDF đến khi có dữ liệu FHIR truy vấn được. Không cần model machine learning tùy chỉnh hay cấu hình template thủ công.

**Các dịch vụ AWS được sử dụng:**

1. **Amazon Bedrock Data Automation (BDA):** Trích xuất hơn 50 trường dữ liệu lâm sàng có cấu trúc từ PDF scan bằng năng lực AI tiên tiến, bao gồm thông tin nhân khẩu học bệnh nhân, chẩn đoán kèm mã ICD-10, thuốc, dấu hiệu sinh tồn và kết quả xét nghiệm.
2. **AWS Lambda:** Hai function serverless điều phối pipeline: một *BDA Trigger* function kích hoạt khi có PDF được upload, và một *FHIR Processor* function chuyển JSON đã trích xuất sang định dạng FHIR R4.
3. **Amazon Simple Storage Service (Amazon S3):** Các bucket đầu vào và đầu ra với event notification tự động vận hành pipeline, không cần polling hay job theo lịch.
4. **AWS HealthLake:** Kho dữ liệu tuân thủ FHIR R4, đủ điều kiện HIPAA, có nhiệm vụ xác thực, đánh chỉ mục và cung cấp dữ liệu qua các endpoint API FHIR chuẩn.
5. **AWS CloudFormation:** Cấp phát toàn bộ hạ tầng dưới dạng code trong một lần triển khai tự động (khoảng 15–20 phút).
6. **Amazon CloudWatch và AWS CloudTrail:** Cung cấp giám sát, ghi log và dấu vết audit đầu-cuối trên mọi thành phần của pipeline.
7. **AWS Key Management Service (AWS KMS):** Mã hóa dữ liệu AWS HealthLake ở trạng thái nghỉ (at rest) bằng customer managed key.

{{% notice warning %}}
**Quan trọng:** Giải pháp này là một mẫu minh họa (demonstration sample), được thiết kế để dùng với **dữ liệu tổng hợp (synthetic data)** mà thôi. Nó **chưa sẵn sàng cho môi trường production** với Thông tin Sức khỏe Được bảo vệ (PHI) thật nếu chưa bổ sung các biện pháp kiểm soát bảo mật HIPAA. Hãy xem phần **Cân nhắc về bảo mật** trước khi triển khai trong bất kỳ môi trường nào có dữ liệu bệnh nhân thật.
{{% /notice %}}

---

## Kiến trúc

![Hình 1](/images/3-BlogsTranslated/3.3-Blog3/ARCHBLOG-1364-1.png)

> *Hình 1. Kiến trúc đầu-cuối thể hiện pipeline hướng sự kiện, từ lúc upload PDF đến khi lưu trữ dữ liệu tuân thủ FHIR.*

Pipeline chạy qua ba pha, mỗi pha xây dựng trên pha trước.

### Pha 1: Triển khai hạ tầng

AWS CloudFormation cấp phát mọi tài nguyên cần thiết trong một stack duy nhất: bucket đầu vào và đầu ra của Amazon S3, hai Lambda function, các IAM role với quyền least-privilege (đặc quyền tối thiểu), khóa AWS KMS, các CloudWatch log group, và một kho dữ liệu FHIR R4 của AWS HealthLake. Toàn bộ môi trường — bao gồm mọi quyền giữa các dịch vụ — được quản lý phiên bản và có thể tái lập.

### Pha 2: Xử lý dữ liệu hướng sự kiện

Pipeline xử lý hoàn toàn hướng sự kiện. Không cần scheduler hay dịch vụ điều phối. Mỗi bước tự động kích hoạt bước tiếp theo:

1. Upload PDF → S3 Input Bucket
2. S3 Event → Kích hoạt BDA Lambda function
3. BDA Processing → Trích xuất hơn 50 trường lâm sàng kèm điểm tin cậy (confidence score)
4. JSON Storage → S3 Output Bucket
5. S3 Event → Kích hoạt FHIR Processor Lambda function
6. FHIR Conversion → Tạo FHIR R4 Bundle (JSON + NDJSON)
7. HealthLake Import → Tự động nạp và xác thực NDJSON
8. FHIR API Access → Truy vấn qua các endpoint HealthLake

### Pha 3: Truy vấn và phân tích

Sau khi dữ liệu đã nằm trong AWS HealthLake, nó có thể được truy vấn ngay lập tức qua các endpoint API FHIR R4 chuẩn. Các script Python xác thực bằng AWS Signature Version 4 (SigV4) và hỗ trợ tìm kiếm theo bệnh nhân, tình trạng bệnh, thuốc, hoặc loại kết quả xét nghiệm.

---

## Các dịch vụ kết nối với nhau như thế nào

Hiểu được cách các dịch vụ liên kết với nhau là chìa khóa để tùy biến hoặc mở rộng giải pháp này.

### Amazon S3 làm xương sống của pipeline

Amazon S3 đóng vai trò kép: vừa là điểm vào cho các PDF thô, vừa là lớp bàn giao (handoff) giữa các giai đoạn xử lý. Amazon S3 event notification loại bỏ nhu cầu polling. Khi một PDF rơi vào input bucket, BDA Lambda kích hoạt ngay lập tức. Khi BDA ghi kết quả JSON của nó vào output bucket, FHIR Processor Lambda tự động kích hoạt. Thiết kế tách rời (decoupled) này giúp mỗi giai đoạn có thể mở rộng độc lập.

### Amazon Bedrock Data Automation làm lớp trí tuệ

BDA đóng vai trò lớp trí tuệ (intelligence layer). Khi Lambda kích hoạt job trích xuất, BDA lấy PDF từ Amazon S3 và áp dụng một *blueprint* y tế tùy chỉnh — một schema định nghĩa hơn 50 trường lâm sàng cần trích xuất. Dịch vụ này hiểu được cấu trúc tài liệu mà không cần template hay dữ liệu huấn luyện. Mỗi trường được trích xuất trả về kèm một điểm tin cậy (0.0–1.0), mà FHIR Processor Lambda dùng để áp các ngưỡng xác thực (validation threshold) trước khi chuyển đổi.

### AWS Lambda làm lớp chuyển đổi

Hai Lambda function được thiết kế có phạm vi hẹp một cách chủ đích:

- ***BDA Trigger Lambda*** nhận sự kiện Amazon S3, dựng lời gọi API BDA, và submit job xử lý.
- ***FHIR Processor Lambda*** đọc kết quả JSON của BDA, ánh xạ từng trường đã trích xuất sang loại tài nguyên FHIR R4 phù hợp, lắp ráp một FHIR Bundle, xuất ra NDJSON, và kích hoạt một job import của AWS HealthLake.

Việc tách bạch trách nhiệm (separation of concerns) này giúp mỗi function có thể kiểm thử và thay thế độc lập.

### AWS HealthLake làm kho dữ liệu FHIR

AWS HealthLake nhận bản import NDJSON, xác thực từng tài nguyên theo đặc tả FHIR R4, tạo quan hệ giữa các tài nguyên (ví dụ liên kết tài nguyên `Condition` với `Patient` tương ứng), đánh chỉ mục dữ liệu để truy vấn hiệu quả, và sinh ID tài nguyên FHIR duy nhất. Kết quả là một kho dữ liệu FHIR truy vấn được đầy đủ, truy cập qua các lời gọi API đã xác thực.

### IAM role làm "vải bảo mật"

Mỗi dịch vụ giao tiếp với dịch vụ tiếp theo bằng IAM role với quyền least-privilege. Không có credential nhúng cứng (hardcoded) và không có policy quá rộng. Các Lambda function assume các role chỉ cấp đúng những hành động chúng cần (ví dụ `bedrock-data-automation:InvokeDataAutomationAsync` và `s3:GetObject` cho BDA Trigger Lambda).

---

## Hướng dẫn thực hành

Phần hướng dẫn này đưa bạn từ điều kiện tiên quyết qua triển khai đến kiểm chứng.

### Điều kiện tiên quyết

Trước khi triển khai, hãy xác nhận bạn có những thứ sau:

**Phần mềm cần thiết:**

- Python 3.10 trở lên.
- Poetry (công cụ quản lý phụ thuộc Python).
- AWS Command Line Interface (AWS CLI) đã cấu hình với credential phù hợp.

**Kiểm tra bản cài Poetry:**

```bash
poetry --version
```

**Nếu bạn cần cài Poetry:**

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

**Quyền AWS cần thiết:**

Bạn cần quyền IAM cho các dịch vụ sau:

- Amazon Bedrock Data Automation.
- AWS CloudFormation (tạo, cập nhật và xóa stack).
- Amazon S3 (tạo bucket, upload và download object).
- AWS Lambda (tạo và cập nhật function).
- AWS Identity and Access Management (IAM) (tạo role và policy).
- AWS HealthLake (tạo data store).

**Các AWS Region được hỗ trợ:**

Giải pháp này hiện chỉ hỗ trợ `us-east-1` (US East N. Virginia) và `us-west-2` (US West Oregon). Đây là các Region mà Amazon Bedrock Data Automation khả dụng.

---

### Triển khai pipeline

Việc triển khai mất khoảng 15–20 phút. Chạy bốn lệnh sau để đi từ con số 0 đến một pipeline đã triển khai hoàn chỉnh:

```bash
# 1. Clone repository và cài phụ thuộc
git clone <repository-url>
cd Medical-Record-Digitization-and-FHIR-Integration-Pipeline
poetry install

# 2. Cấu hình môi trường của bạn
poetry run python src/utils/setup_env.py

# 3. Triển khai CloudFormation stack (khoảng 15 phút)
poetry run python src/automation/deploy.py

# 4. Kiểm chứng việc triển khai
aws cloudformation describe-stacks \
  --stack-name bda-medical-records-stack \
  --query 'Stacks[0].StackStatus'
# Kết quả mong đợi: "CREATE_COMPLETE"
```

Quá trình triển khai tạo ra các tài nguyên sau:

- Blueprint và project của Amazon Bedrock Data Automation (schema hồ sơ bệnh án tùy chỉnh với hơn 50 trường).
- Bucket đầu vào và đầu ra của Amazon S3 với event notification tự động.
- Hai AWS Lambda function (BDA Trigger và FHIR Processor).
- Kho dữ liệu FHIR R4 của AWS HealthLake.
- Các IAM role và policy với quyền least-privilege.
- Các Amazon CloudWatch log group cho mọi lần thực thi Lambda.

> Về cấu hình môi trường thủ công, các tùy chọn triển khai nâng cao và xử lý sự cố, hãy xem GitHub repository.

---

### Xem pipeline hoạt động

Sau khi triển khai xong, hãy upload một hồ sơ bệnh án mẫu để kích hoạt toàn bộ pipeline. Bạn có thể dùng file mẫu có sẵn trong GitHub repository.

```bash
# Lấy tên input bucket từ output của CloudFormation stack
INPUT_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name bda-medical-records-stack \
  --query 'Stacks[0].Outputs[?OutputKey==`InputBucketName`].OutputValue' \
  --output text)

# Upload một PDF mẫu (dùng hồ sơ tổng hợp có trong repository)
aws s3 cp samples/medical-record-sample.pdf s3://$INPUT_BUCKET/

# Theo dõi các job xử lý của BDA
poetry run python src/utils/track_bda_jobs.py
```

Trong vòng 2–3 phút, Amazon Bedrock Data Automation xử lý PDF và FHIR Processor Lambda import kết quả vào HealthLake. Xem dữ liệu đã trích xuất:

```bash
poetry run python src/utils/view_results.py
```

![Hình 2](/images/3-BlogsTranslated/3.3-Blog3/vidu-2.png)

> *Hình 2. Kết quả trích xuất mẫu — thông tin bệnh nhân, tình trạng bệnh kèm mã ICD-10, thuốc và kết quả xét nghiệm kèm điểm tin cậy.*

**Ví dụ kết quả đầu ra:**

```plaintext
Found 8 result files in output bucket
Processing: medical-record-sample_results.json

Patient Information:
---------------------
Name: Wilkins, Samantha
Patient ID: A1B2C3D4
Date of Birth: 10/28/1953

Conditions (5 found):
- Hypothyroidism (ICD-10: E03.9) - Confidence: 0.98
- Vitamin D Deficiency (ICD-10: E55.9) - Confidence: 0.95
- Hypertension (ICD-10: I10) - Confidence: 0.97
- Osteoarthritis (ICD-10: M19.90) - Confidence: 0.92
- Gastroesophageal Reflux Disease (ICD-10: K21.9) - Confidence: 0.96

Medications (4 found):
- Levothyroxine 100 mcg daily
- Vitamin D3 2000 IU daily
- Lisinopril 10 mg daily
- Omeprazole 20 mg daily
Lab Results (16 tests):
TSH: 2.3 mIU/L (Normal range: 0.4-4.0) ✓
Vitamin D: 28 ng/mL (Normal range: 30-100) ⚠️
Blood Pressure: 128/82 mmHg (Stage 1 Hypertension) ⚠️

[✅] FHIR conversion complete
[✅] Imported to HealthLake datastore: ds-abc123xyz456
```

---

### Truy vấn dữ liệu FHIR từ AWS HealthLake

Sau khi nạp dữ liệu, hãy truy vấn bằng giao diện truy vấn FHIR tương tác:

```bash
poetry run python src/utils/query_medical_data.py
```

**Các mẫu truy vấn FHIR được hỗ trợ:**

```plaintext
# Tìm theo tên bệnh nhân
Patient?name=Wilkins

# Lấy các tình trạng bệnh của một bệnh nhân cụ thể
Condition?patient=Patient/47ef817a-9826-4498-b693-2af5eb2b5250

# Chỉ lấy kết quả xét nghiệm
Observation?category=laboratory

# Chỉ lấy dấu hiệu sinh tồn
Observation?category=vital-signs

# Lấy tất cả thuốc
MedicationRequest
```

**Ví dụ Python — lời gọi API FHIR đã xác thực:**

```python
import boto3, requests, os
from botocore.auth import SigV4Auth
from botocore.awsrequest import AWSRequest

session = boto3.Session()
credentials = session.get_credentials()
region = os.environ.get('AWS_REGION', 'us-west-2')
datastore_id = os.environ.get('DATASTORE_ID')

url = f'https://healthlake.{region}.amazonaws.com/datastore/{datastore_id}/r4/Patient?name=Wilkins'
request = AWSRequest(method='GET', url=url, headers={'Accept': 'application/fhir+json'})
SigV4Auth(credentials, 'healthlake', region).add_auth(request)

response = requests.get(url, headers=dict(request.headers))
print(response.json())
```

---

## Cân nhắc về bảo mật

Đây là một mẫu minh họa chỉ dành cho dữ liệu tổng hợp. **Không** dùng với Thông tin Sức khỏe Được bảo vệ (PHI) thật nếu chưa triển khai các biện pháp kiểm soát liệt kê ở các phần dưới đây.

**Các biện pháp bảo mật đã bao gồm trong mẫu này:**

- IAM role với quyền least-privilege.
- Kiểm soát truy cập bucket Amazon S3 (mặc định private).
- Mã hóa AWS KMS cho dữ liệu AWS HealthLake ở trạng thái nghỉ.
- Phân quyền giữa các dịch vụ AWS bằng IAM role.
- Ghi log Amazon CloudWatch cho dấu vết audit.

**Các biện pháp bổ sung cần thiết cho workload PHI production:**

AWS HealthLake là một dịch vụ đủ điều kiện HIPAA (HIPAA Eligible Service). Khách hàng phải xem lại **Mô hình Trách nhiệm Chung của AWS (AWS Shared Responsibility Model)** để hiểu nghĩa vụ về bảo mật và tuân thủ của mình. Trước khi xử lý dữ liệu bệnh nhân thật, hãy triển khai:

1. **AWS Business Associate Addendum (BAA):** Bắt buộc theo HIPAA trước khi xử lý PHI trên AWS.
2. **Cô lập Amazon Virtual Private Cloud (Amazon VPC):** Đặt Lambda function và AWS HealthLake trong các private subnet với AWS PrivateLink.
3. **Ghi log toàn diện:** AWS CloudTrail, AWS Config, Amazon S3 access log, và Amazon VPC flow log.
4. **Mã hóa khi truyền (in transit):** TLS 1.2 trở lên. Dùng Amazon VPC endpoint để tránh phơi bày ra internet công cộng.
5. **Kiểm soát truy cập:** Xác thực đa yếu tố (MFA), kiểm soát truy cập theo vai trò (RBAC), credential tạm thời, và rà soát truy cập định kỳ.
6. **Giám sát tuân thủ:** AWS Security Hub với các kiểm tra tuân thủ HIPAA.
7. **Quản lý vòng đời dữ liệu:** Chính sách lưu giữ (retention), xóa an toàn, và các biện pháp chống thất thoát dữ liệu (DLP).

Để có hướng dẫn đầy đủ, xem trang **AWS HIPAA Compliance**.

---

## Chi phí

Các ước tính sau áp dụng cho việc thử nghiệm với khoảng 100 hồ sơ bệnh án mỗi tháng tại Region US West (Oregon):

| Dịch vụ                        | Mức sử dụng                                  | Chi phí ước tính hàng tháng |
| ------------------------------ | --------------------------------------------- | --------------------------- |
| Amazon Bedrock Data Automation | 100 trang (khoảng $0.20–$0.30/trang)          | $20–$30                     |
| AWS HealthLake                 | 5 GB lưu trữ + 100 truy vấn                    | $15–$20                     |
| AWS Lambda                     | 200 lần gọi (512 MB, trung bình khoảng 30 giây) | $5–$10                      |
| Amazon S3                      | 1 GB lưu trữ + 200 request                     | $1–$2                       |
| AWS KMS                        | 1 customer managed key                         | $1                          |
| **Tổng cộng**                  |                                               | **khoảng $50–$100/tháng**   |

Với workload production xử lý 10.000 hồ sơ mỗi tháng, hãy dự trù chi phí trong khoảng $2.000–$3.000/tháng. Các yếu tố chi phí chính là BDA (tính theo trang), HealthLake (tính theo request tìm kiếm), và VPC endpoint (phí PrivateLink theo giờ trong triển khai production).

**Mẹo tối ưu chi phí:**

- Xóa CloudFormation stack khi không tích cực thử nghiệm: `aws cloudformation delete-stack --stack-name bda-medical-records-stack`.
- Thiết lập cảnh báo AWS Budgets để phát hiện sớm chi phí bất thường.
- Giám sát thời lượng Lambda trong CloudWatch để tối ưu thời gian thực thi function.

---

## Dọn dẹp tài nguyên

Để tránh phát sinh chi phí liên tục, hãy xóa CloudFormation stack khi bạn đã xong:

```bash
aws cloudformation delete-stack --stack-name bda-medical-records-stack
aws cloudformation wait stack-delete-complete --stack-name bda-medical-records-stack
```

Để dọn dẹp các project Amazon Bedrock Data Automation tạo thủ công và nội dung trong S3 bucket, hãy xem GitHub repository.

---

## Bước tiếp theo

Sau khi triển khai, bạn có thể mở rộng nền tảng này để:

- Tích hợp với các hệ thống hồ sơ sức khỏe điện tử (EHR) hiện có qua các API FHIR.
- Xây dựng dashboard phân tích bằng Amazon QuickSight.
- Thêm tìm kiếm bằng ngôn ngữ tự nhiên với Amazon Kendra.
- Thêm Amazon Simple Queue Service (Amazon SQS) làm bộ đệm giữa sự kiện Amazon S3 và BDA Trigger Lambda để xử lý các đợt upload đột biến và quản lý giới hạn concurrency của BDA ở quy mô lớn.
- Điều phối bằng AWS Step Functions để xử lý lỗi, logic retry, và định tuyến các bản trích xuất có độ tin cậy thấp sang cho con người rà soát.
- Triển khai xử lý real-time, khối lượng lớn với Amazon Kinesis Data Streams để nạp liên tục từ nhiều nguồn.

---

## Kết luận

Trong bài viết này, bạn đã thấy cách Amazon Bedrock Data Automation, AWS Lambda, Amazon S3 và AWS HealthLake phối hợp với nhau để tự động hóa việc chuyển đổi hồ sơ bệnh án scan thành dữ liệu tuân thủ FHIR R4. Kiến trúc hướng sự kiện loại bỏ việc nhập liệu thủ công, mở rộng mà không cần model machine learning tùy chỉnh, và giúp các hồ sơ cũ trở nên khả dụng cho các hệ thống chăm sóc hiện đại.

**Những điểm cốt lõi:**

- **Amazon Bedrock Data Automation** trích xuất hơn 50 trường dữ liệu lâm sàng có cấu trúc từ PDF mà không cần cấu hình template.
- **AWS Lambda** điều phối pipeline với hai function tập trung, hướng sự kiện.
- **Amazon S3 event notification** tách rời từng giai đoạn, nên mỗi giai đoạn có thể mở rộng độc lập.
- **AWS HealthLake** xác thực, đánh chỉ mục và cung cấp dữ liệu FHIR R4 qua các API chuẩn.
- **Các biện pháp bảo mật** là trách nhiệm của khách hàng theo Mô hình Trách nhiệm Chung của AWS.

Để khám phá mã nguồn đầy đủ, các tùy chọn cấu hình nâng cao và hướng dẫn tùy biến, hãy truy cập GitHub repository của giải pháp.

> *Giải pháp này nhằm mục đích giáo dục, sử dụng dữ liệu tổng hợp. Hãy xem lại phần cân nhắc bảo mật và tham khảo ý kiến đội ngũ tuân thủ của bạn trước khi triển khai trong bất kỳ môi trường nào có dữ liệu bệnh nhân thật.*

---

## Tài nguyên bổ sung

- [Tài liệu Amazon Bedrock Data Automation](https://docs.aws.amazon.com/bedrock/)
- [Tài liệu AWS HealthLake](https://docs.aws.amazon.com/healthlake/)
- [Đặc tả FHIR R4](https://hl7.org/fhir/R4/)
- [AWS HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)
- [AWS Architecture Center: Healthcare solutions](https://aws.amazon.com/architecture/healthcare/)
- [GitHub repository của giải pháp](https://github.com/aws-samples/sample-bedrock-data-automation-fhir-pipeline)
