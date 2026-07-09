---
title : "Prerequisites"
date : 2024-01-01 
weight : 2 
chapter : false
pre : " <b> 5.2. </b> "
---

To begin the **Internal Document Q&A Chatbot using RAG** workshop, you must prepare the development environment and configure AWS credentials by following the steps below.

#### 1. AWS Account & IAM Privileges
You need an active AWS Account. Throughout this lab, we will operate in the **us-east-1 (N. Virginia)** region, as it offers the best support for Bedrock's latest models (e.g., Nova Lite) and the S3 Vectors storage format.

#### Step 1 — Create a least-privilege IAM user (stop using the root account)

You will do steps 1–5 below **while still signed in as root** — root is the only account allowed to create IAM users/policies, so you cannot do this from the new user itself. Only at the very last step (step 6) do you switch away from root for good.

**1. Sign in as root and open IAM**

Sign in to the [AWS Management Console](https://console.aws.amazon.com/) using your **root** email/password (the account you signed up with). In the top-right search bar, type **IAM** and open the **IAM** service.

![iam console](/images/5-Workshop/5.2-Prerequisite/iam-console-open.png)

**2. Create the IAM user (without permissions yet)**

In the left sidebar, click **Users → Create user**. Enter a **User name** (e.g. `rag-chatbot-deployer`). Check **Provide user access to the AWS Management Console** if you also want to log in via browser, and set a custom password.

![create iam user](/images/5-Workshop/5.2-Prerequisite/create-iam-user-1.png)

On the permissions step, choose **Attach policies directly** and click **Next** without selecting anything yet — you don't have the custom policy created until step 4 below, so there is nothing to attach here.

![create iam user permissions](/images/5-Workshop/5.2-Prerequisite/create-iam-user-2.png)

Review and click **Create user**. The user now exists but has **zero permissions**.

![iam user created](/images/5-Workshop/5.2-Prerequisite/create-iam-user-3.png)

**3. Create the least-privilege policy (still as root)**

Open a new tab/step: **IAM Console → Policies → Create policy**. Switch to the **JSON** tab, delete the placeholder content, and paste the policy below — it only grants the specific actions used by `backend/scripts/*.ts` (provisioning, finalize, cleanup) and the Bedrock Knowledge Base console flow, scoped by resource name/prefix wherever AWS supports it (DynamoDB tables `Users/Roles/Documents/ChatHistory`, S3 buckets `rag-docs-*`/`rag-web-*`, Lambda functions `rag-*`, IAM role `rag-lambda-role` + the Bedrock-generated KB role). Some AWS APIs (Cognito, API Gateway, Bedrock model invocation) do not support resource-level ARN restriction, so those actions are scoped to the action list only, with `Resource: "*"`.

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

Click **Next**, name the policy e.g. `rag-chatbot-deploy-policy`, and click **Create policy**.

![iam policy](/images/5-Workshop/5.2-Prerequisite/iam-custom-policy.png)

**4. Attach the policy to the IAM user (still as root)**

Go to **IAM → Users → `rag-chatbot-deployer`** (the user created in step 2) **→ Add permissions → Attach policies directly**, search for `rag-chatbot-deploy-policy`, select it, and click **Add permissions**.

{{% notice tip %}}
🔍 **Verify on Console**: on the user's **Permissions** tab, confirm `rag-chatbot-deploy-policy` now appears in the attached policies list (not `AdministratorAccess`). Take a screenshot of this screen — it is strong evidence for the "least-privilege IAM" grading criterion.
{{% /notice %}}

![iam attached](/images/5-Workshop/5.2-Prerequisite/iam-policy-attached.png)

**5. Create an access key for the IAM user (still as root)**

Still on the `rag-chatbot-deployer` user page, open the **Security credentials** tab → **Access keys → Create access key**. Choose use case **Command Line Interface (CLI)**, acknowledge the recommendation notice, and click **Create access key**.

![create access key](/images/5-Workshop/5.2-Prerequisite/create-access-key.png)

**Download the .csv file** (or copy the Access Key ID + Secret Access Key immediately — the secret is shown only once). You will paste these into `backend/.env` in section 5.3.1.

![access key created](/images/5-Workshop/5.2-Prerequisite/access-key-created.png)

**6. Now — and only now — sign out of root and switch to the IAM user**

At this point `rag-chatbot-deployer` has both the least-privilege policy attached **and** an access key, so it is fully ready to use. **Sign out of the root account** and sign back in using **IAM user sign-in** with the account ID/alias + the IAM user name and password created in step 2. From here on, use this IAM user for every remaining step of the workshop — never sign back in as root.

{{% notice tip %}}
🔍 **Verify on Console**: after signing back in, check the top-right account menu — it must show `rag-chatbot-deployer @ <your-account-id>` instead of the root email. This screenshot is the clearest evidence you are not operating as root.
{{% /notice %}}

![signed in as iam user](/images/5-Workshop/5.2-Prerequisite/signed-in-as-iam-user.png)

#### 2. Local Development Environment
Ensure the following tools are installed on your host machine:
- **Node.js** (v20.x or newer) & **npm** (comes bundled with Node.js).
- **Git** for cloning the workshop repository.
- A Code Editor (recommend using **Visual Studio Code**).

Verify that Node.js and npm are installed correctly:
```bash
node -v
npm -v
```

#### 3. Installing and Configuring the AWS CLI
Download and install the AWS Command Line Interface (CLI):
- [Install instructions for AWS CLI on the official AWS User Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

After installation, run the account configuration command in your terminal/command prompt:
```bash
aws configure
```
Provide the requested credentials:
- **AWS Access Key ID**: Your account access key.
- **AWS Secret Access Key**: Your account secret access key.
- **Default region name**: `us-east-1`
- **Default output format**: `json`

#### 4. Bedrock Model Access (now automatic — verify via Model Catalog)

**Step 1 — Open Bedrock, correct region, go straight to Model Catalog**

In the top search bar, type **Bedrock** and open **Amazon Bedrock**, confirm the region selector (top-right) reads **US East (N. Virginia) us-east-1**. In the left navigation, under **Discover**, click **Model catalog** (not "Model access" — that page is now empty).

![open model catalog](/images/5-Workshop/5.2-Prerequisite/open-model-catalog.png)

**Step 2 — Verify Nova Lite works (open it in Playground)**

In **Model catalog**, search for **Nova Lite**, open it, and click **Open in Playground**. Send a short test prompt (e.g. "Say hello in one sentence") and confirm you get a real response back — this proves the model auto-activated and your IAM user can call it.

![nova lite playground test](/images/5-Workshop/5.2-Prerequisite/nova-lite-playground-test.png)

**Step 3 — Verify Titan Text Embeddings V2 is available**

Still in **Model catalog**, search for **Titan Text Embeddings V2**, open it, and confirm status **Available** (embedding models don't have a chat Playground to test directly — the real end-to-end proof comes later in section 5.3.4, when the Knowledge Base successfully embeds a real document).

![titan embeddings available](/images/5-Workshop/5.2-Prerequisite/titan-embeddings-available.png)

{{% notice tip %}}
🔍 **Verify on Console**: the 3 screenshots above (`open-model-catalog.png`, `nova-lite-playground-test.png`, `titan-embeddings-available.png`) are your evidence for this section. Don't bother screenshotting the "Model access" page — it has no functionality left, so it isn't useful evidence.
{{% /notice %}}