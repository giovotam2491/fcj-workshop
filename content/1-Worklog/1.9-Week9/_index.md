---
title: "Week 9 Worklog"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:

* Operate and manage AWS resources via the command line (AWS CLI) instead of manual operations in the Console.
* Understand the concept of Infrastructure as Code (IaC) and the deployment tools (CloudFormation, CDK, SAM).
* Learn the CI/CD process for serverless applications to automate build and deployment.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn AWS CLI: installation and configuring credentials (aws configure), access key, region, output format <br> - Practice basic commands to manage S3, EC2, IAM (e.g. aws s3 ls, aws ec2 describe-instances) <br> - Understand the benefits of the CLI for automation and scripting | 15/06/2026 | 15/06/2026 | <https://000011.awsstudygroup.com/> |
| Tuesday | - Learn AWS CloudFormation: the concept of Infrastructure as Code, templates (YAML/JSON) and stacks <br> - Learn the structure of a template: Resources, Parameters, Outputs <br> - Understand how CloudFormation creates/updates/deletes resources from a template consistently | 16/06/2026 | 16/06/2026 | <https://000037.awsstudygroup.com/> |
| Wednesday | - Learn AWS CDK (Cloud Development Kit): defining infrastructure with a programming language (TypeScript/Python) <br> - Compare CDK with CloudFormation: CDK generates a CloudFormation template but is written in code <br> - Understand the concepts of construct, stack and app in CDK | 17/06/2026 | 17/06/2026 | <https://000038.awsstudygroup.com/> |
| Thursday | - Learn AWS SAM (Serverless Application Model): a framework dedicated to serverless applications <br> - Learn the SAM template structure and the commands sam build, sam deploy, sam local <br> - Understand how SAM simplifies defining Lambda, API Gateway and DynamoDB | 18/06/2026 | 18/06/2026 | <https://000080.awsstudygroup.com/> |
| Friday | - Learn CI/CD for serverless applications: the concept of a pipeline, the build → test → deploy stages <br> - Learn the related AWS services (CodePipeline, CodeBuild, CodeDeploy) <br> - Understand the benefits of deployment automation over manual operations <br> - Consolidate and write the Week 9 worklog | 19/06/2026 | 19/06/2026 | <https://000084.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **AWS CLI:** know how to configure credentials and use the command line to manage resources, enabling automation and scripting.
* **Infrastructure as Code:** understand the IaC philosophy — managing infrastructure with declarative files for consistent reproduction and version control.
* **CloudFormation:** grasp the template structure (Resources, Parameters, Outputs) and the stack mechanism for creating/updating/deleting resources.
* **CDK & SAM:** distinguish CDK (defining infrastructure in code) from SAM (a framework optimized for serverless), and understand that both compile down to CloudFormation.
* **CI/CD:** understand the build → test → deploy pipeline process and the AWS Code* services for automating deployment.

### Week 9 Achievements:

* Learned how to operate and manage AWS using the CLI.
* Understood Infrastructure as Code at a basic level and distinguished between CloudFormation, CDK and SAM.
* Grasped the CI/CD process for serverless applications.
* The project now has a more professional deployment approach, with automation rather than just manual operations in the Console.
