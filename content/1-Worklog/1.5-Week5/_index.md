---
title: "Week 5 Worklog"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:
* Design the Amazon DynamoDB database schemas to store session logs and chat histories.
* Configure Amazon Cognito User Pool as the centralized authentication and user management system.
* Program and deploy the first TypeScript-based Lambda functions: handling session creation (`createSession`) and session fetching (`getSessions`).
* Establish an automated build and bundle pipeline for TypeScript Lambda functions using `esbuild` within AWS CDK.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Design the DynamoDB table structures for chat data. <br> - Define the Partition Key as `SessionId` (UUID) and the Sort Key as `Timestamp` to query chat messages in chronological order. | 18/05/2026 | 18/05/2026 | [DynamoDB Design Patterns](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/) |
| Tue | - Write CDK constructs to provision the Amazon Cognito User Pool. <br> - Configure email-based login, password safety policies, and email-based self-service OTP verification. | 19/05/2026 | 19/05/2026 | [CDK Cognito Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_cognito-readme.html) |
| Wed | - Initialize the backend project folder structure `backend/src` for Lambda handlers. <br> - Code TypeScript handlers for `createSession` (writing database records) and `getSessions` (querying sessions filtered by `UserId`). | 20/05/2026 | 20/05/2026 | [AWS SDK for JavaScript v3](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/) |
| Thu | - Configure the `NodejsFunction` construct in the AWS CDK to leverage `esbuild` for automated compilation, bundling, and minification of TypeScript Lambda source code. | 21/05/2026 | 21/05/2026 | [AWS CDK Nodejs Library](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_lambda_nodejs-readme.html) |
| Fri | - Deploy the CDK stack containing DynamoDB, Cognito, and the two Lambda functions to AWS. <br> - Verify Lambda handlers using test events in the AWS Console. <br> - Write the Week 5 worklog. | 22/05/2026 | 22/05/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **DynamoDB Schema Design:** Learned to identify efficient Partition/Sort Keys to optimize read/write capacities and prevent hot partitions.
* **Cognito Authentication Flows:** Configured registration, login, and JWT validation workflows powered by Cognito User Pools.
* **TypeScript Lambda Bundling:** Mastered integrating `esbuild` within CDK to compile TypeScript source code, exclude the AWS SDK (provided natively in the Lambda environment), and reduce file size to mitigate cold starts.

### Challenges & Solutions:
* **Challenge:** The CDK deployment process failed with the error `esbuild is not installed`, because the CDK CLI local environment could not locate the `esbuild` compiler required for compiling the TypeScript handlers.
* **Solution:** Remedied this by adding `esbuild` as a local `devDependency` in the package's root configuration (`npm install -D esbuild`). This approach ensures that any team member pulling the codebase can deploy successfully after running a standard `npm install`, avoiding the need for globally installed tools.

### Week 5 Achievements:
* Designed and deployed the DynamoDB session and chat tables.
* Configured the Amazon Cognito User Pool for registration and user authentication.
* Developed and deployed the initial `createSession` and `getSessions` Lambda functions with automated TypeScript-to-JS compilation.
