---
title: "Week 3 Worklog"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:
* Start writing Infrastructure as Code (IaC) using AWS CDK with TypeScript.
* Set up an Amazon S3 Bucket to store internal documents, configuring strict server-side encryption.
* Research the fundamentals of Amazon Bedrock Knowledge Base and the Titan Embeddings V2 model.
* Draft sample company documents (procedures, handbooks) in PDF/Markdown format for the RAG pipeline.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Create the `backend/infra` directory and initialize a new CDK application using `cdk init app --language typescript`. <br> - Inspect the generated project files and configurations. | 04/05/2026 | 04/05/2026 | [AWS CDK Getting Started](https://docs.aws.amazon.com/cdk/v2/guide/home.html) |
| Tue | - Write CDK code defining the document storage S3 Bucket. <br> - Apply advanced security constraints: block public access completely (`blockPublicAccess: BlockPublicAccess.BLOCK_ALL`) and enforce S3-managed SSE encryption. | 05/05/2026 | 05/05/2026 | [AWS CDK S3 Reference](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_s3-readme.html) |
| Wed | - Execute the CDK environment bootstrap process (`cdk bootstrap`). <br> - Run `cdk deploy` to provision the S3 bucket on AWS and verify the resource in the AWS Management Console. | 06/05/2026 | 06/05/2026 | AWS CDK Docs |
| Thu | - Research Amazon Bedrock Knowledge Base integration. <br> - Study chunking strategies including Default Chunking (300 tokens, 20% overlap), Fixed-size Chunking, and Hierarchical Chunking. | 07/05/2026 | 07/05/2026 | [Bedrock Knowledge Base Chunking](https://docs.aws.amazon.com/bedrock/) |
| Fri | - Create 5 mock company documents (e.g., Leave Policy, Employee Handbook) in PDF format. <br> - Research the specifications of the Titan Embeddings V2 model (1024-dimension vectors). <br> - Write the Week 3 worklog. | 08/05/2026 | 08/05/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **AWS CDK (Cloud Development Kit):** Learned to use TypeScript to construct and manage AWS cloud resources programmatically rather than writing raw CloudFormation JSON/YAML.
* **S3 Security Hardening:** Understood S3 security properties, including blocking all public read/write access and configuring Server-Side Encryption (SSE-S3).
* **Data Chunking:** Learned how and why larger documents are split into smaller chunks (enabling more accurate semantic retrieval and avoiding LLM context limit overflows).
* **Titan Embeddings V2:** Understood the mechanism of translating human text into multi-dimensional float vectors to be stored in a Vector DB.

### Challenges & Solutions:
* **Challenge:** The `cdk deploy` execution failed with `ExpiredToken: The security token included in the request is expired` because the terminal was storing outdated session tokens.
* **Solution:** Checked the active session credentials using `aws sts get-caller-identity`. Logged in again and updated the environment variables `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` using the persistent `intern-admin` IAM credentials configured for this project, then successfully ran the deployment.

### Week 3 Achievements:
* Successfully initialized and configured the AWS CDK IaC project.
* Deployed the secure S3 document storage bucket to AWS.
* Completed mock internal documentation drafts and mapped out vector embedding settings for the Titan model.
