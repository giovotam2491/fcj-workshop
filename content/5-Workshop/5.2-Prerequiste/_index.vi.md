---
title : "Các bước chuẩn bị"
date : 2024-01-01 
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

Để bắt đầu thực hiện bài lab **Xây dựng Chatbot Q&A Tài liệu Nội bộ bằng RAG**, bạn cần chuẩn bị đầy đủ các công cụ và tài khoản theo các bước hướng dẫn dưới đây.

#### 1. Tài khoản AWS & Quyền hạn IAM
Bạn cần có một tài khoản AWS hoạt động. Trong suốt bài lab này, chúng ta sẽ làm việc tại region **us-east-1 (N. Virginia)** vì các mô hình Bedrock mới nhất (như Nova Lite) và kho vector S3 Vectors hỗ trợ tốt nhất ở đây.

#### Bước 1 — Tạo IAM user least-privilege (ngừng dùng tài khoản root)

Bạn sẽ thực hiện bước 1–5 dưới đây **khi vẫn đang đăng nhập bằng root** — chỉ root mới có quyền tạo IAM user/policy, nên không thể tự làm việc này từ user mới. Chỉ đến bước cuối cùng (bước 6) bạn mới chuyển hẳn khỏi root.

**1. Đăng nhập bằng root và mở IAM**

Đăng nhập [AWS Management Console](https://console.aws.amazon.com/) bằng email/mật khẩu **root** (tài khoản bạn đăng ký ban đầu). Ở ô tìm kiếm góc trên bên phải, gõ **IAM** và mở dịch vụ **IAM**.

![iam console](/images/5-Workshop/5.2-Prerequisite/iam-console-open.png)

**2. Tạo IAM user (chưa có quyền gì)**

Ở thanh bên trái, chọn **Users → Create user**. Nhập **User name** (vd `rag-chatbot-deployer`). Tích chọn **Provide user access to the AWS Management Console** nếu bạn cũng muốn đăng nhập qua trình duyệt, và đặt mật khẩu tuỳ chỉnh.

![create iam user](/images/5-Workshop/5.2-Prerequisite/create-iam-user-1.png)

Ở bước cấp quyền, chọn **Attach policies directly** rồi nhấn **Next** mà chưa chọn gì — vì policy tuỳ chỉnh chưa được tạo cho đến bước 4 bên dưới, nên chưa có gì để gắn ở đây.

![create iam user permissions](/images/5-Workshop/5.2-Prerequisite/create-iam-user-2.png)

Xem lại thông tin và nhấn **Create user**. User này giờ đã tồn tại nhưng **chưa có quyền gì cả**.

![iam user created](/images/5-Workshop/5.2-Prerequisite/create-iam-user-3.png)

**3. Tạo policy least-privilege (vẫn bằng root)**

Mở thêm: **IAM Console → Policies → Create policy**. Chuyển sang tab **JSON**, xoá nội dung mặc định, dán policy bên dưới vào — policy này chỉ cấp đúng các action mà `backend/scripts/*.ts` (provision, finalize, cleanup) và luồng tạo Bedrock Knowledge Base trên Console gọi tới, giới hạn theo tên/prefix resource ở những nơi AWS cho phép (bảng DynamoDB `Users/Roles/Documents/ChatHistory`, S3 bucket `rag-docs-*`/`rag-web-*`, Lambda `rag-*`, IAM role `rag-lambda-role` + role do Bedrock tự sinh cho KB). Một số API của AWS (Cognito, API Gateway, gọi model Bedrock) không hỗ trợ giới hạn theo ARN resource, nên các action đó dùng `Resource: "*"`.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DynamoDBProjectTables",
      "Effect": "Allow",
      "Action": [
        "dynamodb:CreateTable",
        "dynamodb:DeleteTable",
        "dynamodb:DescribeTable",
        "dynamodb:UpdateTable",
        "dynamodb:UpdateTimeToLive",
        "dynamodb:DescribeTimeToLive",
        "dynamodb:TagResource",
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query",
        "dynamodb:Scan",
        "dynamodb:BatchGetItem",
        "dynamodb:BatchWriteItem"
      ],
      "Resource": [
        "arn:aws:dynamodb:us-east-1:*:table/Users",
        "arn:aws:dynamodb:us-east-1:*:table/Roles",
        "arn:aws:dynamodb:us-east-1:*:table/Documents",
        "arn:aws:dynamodb:us-east-1:*:table/ChatHistory",
        "arn:aws:dynamodb:us-east-1:*:table/Users/index/*",
        "arn:aws:dynamodb:us-east-1:*:table/Roles/index/*",
        "arn:aws:dynamodb:us-east-1:*:table/Documents/index/*",
        "arn:aws:dynamodb:us-east-1:*:table/ChatHistory/index/*"
      ]
    },
    {
      "Sid": "DynamoDBListTablesGlobal",
      "Effect": "Allow",
      "Action": "dynamodb:ListTables",
      "Resource": "*"
    },
    {
      "Sid": "S3ProjectBuckets",
      "Effect": "Allow",
      "Action": [
        "s3:CreateBucket",
        "s3:DeleteBucket",
        "s3:ListBucket",
        "s3:GetBucketLocation",
        "s3:GetBucketCors",
        "s3:PutBucketCors",
        "s3:PutBucketPolicy",
        "s3:GetBucketPolicy",
        "s3:PutBucketNotification",
        "s3:GetBucketNotification",
        "s3:PutBucketPublicAccessBlock",
        "s3:GetBucketPublicAccessBlock",
        "s3:PutBucketOwnershipControls",
        "s3:GetBucketOwnershipControls",
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:AbortMultipartUpload",
        "s3:ListBucketMultipartUploads",
        "s3:ListMultipartUploadParts"
      ],
      "Resource": [
        "arn:aws:s3:::rag-docs-*",
        "arn:aws:s3:::rag-docs-*/*",
        "arn:aws:s3:::rag-web-*",
        "arn:aws:s3:::rag-web-*/*"
      ]
    },
    {
      "Sid": "S3ListAllBucketsAndVectors",
      "Effect": "Allow",
      "Action": ["s3:ListAllMyBuckets", "s3vectors:*"],
      "Resource": "*"
    },
    {
      "Sid": "CognitoUserPool",
      "Effect": "Allow",
      "Action": [
        "cognito-idp:CreateUserPool",
        "cognito-idp:DeleteUserPool",
        "cognito-idp:DescribeUserPool",
        "cognito-idp:ListUserPools",
        "cognito-idp:CreateUserPoolClient",
        "cognito-idp:DeleteUserPoolClient",
        "cognito-idp:ListUserPoolClients",
        "cognito-idp:AdminCreateUser",
        "cognito-idp:AdminDeleteUser",
        "cognito-idp:AdminSetUserPassword",
        "cognito-idp:AdminGetUser",
        "cognito-idp:AdminEnableUser",
        "cognito-idp:AdminDisableUser",
        "cognito-idp:ListUsers",
        "cognito-idp:TagResource"
      ],
      "Resource": "*"
    },
    {
      "Sid": "IAMForLambdaRoleAndBedrockKBRole",
      "Effect": "Allow",
      "Action": [
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:GetRole",
        "iam:ListRolePolicies",
        "iam:PutRolePolicy",
        "iam:GetRolePolicy",
        "iam:DeleteRolePolicy",
        "iam:AttachRolePolicy",
        "iam:DetachRolePolicy",
        "iam:TagRole",
        "iam:ListAttachedRolePolicies",
        "iam:PassRole"
      ],
      "Resource": [
        "arn:aws:iam::*:role/rag-lambda-role",
        "arn:aws:iam::*:role/AmazonBedrockExecutionRoleForKnowledgeBase_*"
      ]
    },
    {
      "Sid": "IAMListRolesGlobal",
      "Effect": "Allow",
      "Action": "iam:ListRoles",
      "Resource": "*"
    },
    {
      "Sid": "LambdaFunctions",
      "Effect": "Allow",
      "Action": [
        "lambda:CreateFunction",
        "lambda:DeleteFunction",
        "lambda:GetFunction",
        "lambda:GetFunctionConfiguration",
        "lambda:UpdateFunctionCode",
        "lambda:UpdateFunctionConfiguration",
        "lambda:AddPermission",
        "lambda:RemovePermission",
        "lambda:TagResource",
        "lambda:InvokeFunction"
      ],
      "Resource": "arn:aws:lambda:us-east-1:*:function:rag-*"
    },
    {
      "Sid": "LambdaListFunctionsGlobal",
      "Effect": "Allow",
      "Action": "lambda:ListFunctions",
      "Resource": "*"
    },
    {
      "Sid": "ApiGatewayHttpApi",
      "Effect": "Allow",
      "Action": ["apigateway:GET", "apigateway:POST", "apigateway:PUT", "apigateway:DELETE", "apigateway:PATCH"],
      "Resource": "arn:aws:apigateway:us-east-1::/apis*"
    },
    {
      "Sid": "ApiGatewayConsoleReadOnly",
      "Effect": "Allow",
      "Action": "apigateway:GET",
      "Resource": "*"
    },
    {
      "Sid": "BedrockKnowledgeBaseAndModels",
      "Effect": "Allow",
      "Action": "bedrock:*",
      "Resource": "*"
    },
    {
      "Sid": "SageMakerPublicHubReadOnlyForBedrockModelCatalog",
      "Effect": "Allow",
      "Action": ["sagemaker:ListHubContents", "sagemaker:DescribeHubContent"],
      "Resource": "arn:aws:sagemaker:us-east-1:aws:hub/SageMakerPublicHub"
    },
    {
      "Sid": "LogsMetricsAndBudgetGuardrail",
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:DeleteLogGroup",
        "logs:DescribeLogGroups",
        "logs:DescribeLogStreams",
        "logs:GetLogEvents",
        "logs:FilterLogEvents",
        "logs:PutRetentionPolicy",
        "cloudwatch:PutMetricAlarm",
        "cloudwatch:DescribeAlarms",
        "cloudwatch:DeleteAlarms",
        "cloudwatch:GetMetricData",
        "budgets:ViewBudget",
        "budgets:ModifyBudget"
      ],
      "Resource": "*"
    },
    {
      "Sid": "CloudFrontForOptionalFrontendHosting",
      "Effect": "Allow",
      "Action": [
        "cloudfront:CreateDistribution",
        "cloudfront:GetDistribution",
        "cloudfront:ListDistributions",
        "cloudfront:CreateInvalidation",
        "cloudfront:CreateOriginAccessControl",
        "cloudfront:ListOriginAccessControls",
        "cloudfront:UpdateDistribution",
        "cloudfront:DeleteDistribution",
        "cloudfront:TagResource"
      ],
      "Resource": "*"
    },
    {
      "Sid": "WAFForCloudFrontProtection",
      "Effect": "Allow",
      "Action": "wafv2:*",
      "Resource": "*"
    },
    {
      "Sid": "SelfIdentityCheck",
      "Effect": "Allow",
      "Action": ["sts:GetCallerIdentity", "iam:GetUser", "iam:ListAttachedUserPolicies"],
      "Resource": "*"
    }
  ]
}
```

Nhấn **Next**, đặt tên vd `rag-chatbot-deploy-policy`, rồi nhấn **Create policy**.

![iam policy](/images/5-Workshop/5.2-Prerequisite/iam-custom-policy.png)

**4. Gắn policy vào IAM user (vẫn bằng root)**

Vào **IAM → Users → `rag-chatbot-deployer`** (user đã tạo ở bước 2) **→ Add permissions → Attach policies directly**, tìm `rag-chatbot-deploy-policy`, chọn, rồi nhấn **Add permissions**.

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: ở tab **Permissions** của user, xác nhận `rag-chatbot-deploy-policy` đã xuất hiện trong danh sách policy đã gắn (không phải `AdministratorAccess`). Chụp lại màn hình này — đây là bằng chứng mạnh cho tiêu chí "IAM least-privilege" khi chấm điểm.
{{% /notice %}}

![iam attached](/images/5-Workshop/5.2-Prerequisite/iam-policy-attached.png)

**5. Tạo access key cho IAM user (vẫn bằng root)**

Vẫn ở trang user `rag-chatbot-deployer`, mở tab **Security credentials** → **Access keys → Create access key**. Chọn use case **Command Line Interface (CLI)**, xác nhận cảnh báo khuyến nghị, rồi nhấn **Create access key**.

![create access key](/images/5-Workshop/5.2-Prerequisite/create-access-key.png)

**Tải file .csv** về (hoặc copy ngay Access Key ID + Secret Access Key — secret chỉ hiển thị 1 lần duy nhất). Bạn sẽ dán 2 giá trị này vào `backend/.env` ở mục 5.3.1.

![access key created](/images/5-Workshop/5.2-Prerequisite/access-key-created.png)

**6. Bây giờ — và chỉ bây giờ — mới đăng xuất root và chuyển sang IAM user**

Đến đây `rag-chatbot-deployer` đã có cả policy least-privilege lẫn access key, nên đã sẵn sàng để dùng. **Đăng xuất tài khoản root** và đăng nhập lại bằng **IAM user sign-in** với account ID/alias + tên user + mật khẩu đã tạo ở bước 2. Từ đây trở đi, dùng IAM user này cho toàn bộ các bước còn lại của workshop — không đăng nhập lại bằng root nữa.

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: sau khi đăng nhập lại, xem menu tài khoản ở góc trên bên phải — phải hiển thị `rag-chatbot-deployer @ <account-id-của-bạn>` thay vì email root. Ảnh chụp này là bằng chứng rõ ràng nhất cho thấy bạn không thao tác bằng root.
{{% /notice %}}

![signed in as iam user](/images/5-Workshop/5.2-Prerequisite/signed-in-as-iam-user.png)

#### 2. Môi trường phát triển cục bộ (Local Development Environment)
Máy tính cá nhân của bạn cần được cài đặt sẵn:
- **Node.js** (Phiên bản Node v20.x hoặc mới hơn) & **npm** (mặc định đi cùng Node.js).
- **Git** để clone mã nguồn của dự án.
- Code Editor: Khuyên dùng **Visual Studio Code**.

Kiểm tra sự tồn tại của Node.js bằng cách chạy lệnh sau trên terminal/terminal của VS Code:
```bash
node -v
npm -v
```

#### 3. Cài đặt và cấu hình AWS CLI
Tải xuống và cài đặt bộ điều khiển AWS CLI phù hợp với hệ điều hành của bạn:
- [Tải và cài đặt AWS CLI từ trang chủ AWS](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Sau khi cài đặt xong, mở terminal/cmd và chạy lệnh sau để thiết lập thông tin đăng nhập:
```bash
aws configure
```
Nhập các thông tin sau khi được yêu cầu:
- **AWS Access Key ID**: Access Key của tài khoản IAM của bạn.
- **AWS Secret Access Key**: Secret Key tương ứng.
- **Default region name**: `us-east-1`
- **Default output format**: `json`

#### 4. Bedrock Model Access (giờ đã tự động — kiểm tra qua Model Catalog)

**Bước 1 — Mở Bedrock, đúng region, vào thẳng Model Catalog**

Ở ô tìm kiếm trên cùng, gõ **Bedrock** và mở **Amazon Bedrock**, kiểm tra region ở góc trên bên phải là **US East (N. Virginia) us-east-1**. Ở thanh điều hướng bên trái, mục **Discover**, chọn **Model catalog** (không phải "Model access" — mục đó giờ bỏ trống).

![mở model catalog](/images/5-Workshop/5.2-Prerequisite/open-model-catalog.png)

**Bước 2 — Xác minh Nova Lite hoạt động (mở trong Playground)**

Trong **Model catalog**, gõ tìm **Nova Lite**, mở model, nhấn **Open in Playground**. Gửi thử 1 prompt ngắn (vd "Say hello in one sentence") và xác nhận nhận được câu trả lời thật — đây là bằng chứng model đã tự động kích hoạt và IAM user gọi được.

![test nova lite trong playground](/images/5-Workshop/5.2-Prerequisite/nova-lite-playground-test.png)

**Bước 3 — Xác minh Titan Text Embeddings V2 khả dụng**

Vẫn ở **Model catalog**, tìm **Titan Text Embeddings V2**, mở model và xác nhận trạng thái **Available** (model embedding không có Playground dạng chat để test trực tiếp — bằng chứng end-to-end thật sự sẽ có ở mục 5.3.4, khi Knowledge Base embed thành công 1 tài liệu thật).

![titan embeddings available](/images/5-Workshop/5.2-Prerequisite/titan-embeddings-available.png)

{{% notice tip %}}
🔍 **Kiểm tra trên Console**: 3 ảnh trên (`open-model-catalog.png`, `nova-lite-playground-test.png`, `titan-embeddings-available.png`) chính là bằng chứng cho mục này. Không cần chụp trang "Model access" nữa vì nó không còn chức năng gì (chỉ hiện dòng thông báo đã retire) — không phải bằng chứng có giá trị.
{{% /notice %}}