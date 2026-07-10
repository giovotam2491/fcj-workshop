---
title: "Week 1 Worklog"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:
* Survey & Design the overall architecture of the Serverless RAG Chatbot system on AWS.
* Set up the AWS account and configure basic security settings (Root MFA, IAM User Admin).
* Establish AWS Budgets to alert on costs, preventing unexpected bills when running AI services (Amazon Bedrock).
* Initialize the Frontend source code using React + Vite + TypeScript and integrate the GSAP animation library.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Participate in the project kick-off meeting, receive the Serverless RAG Chatbot topic. <br> - Read and understand the guidelines, weekly worklog regulations, and project requirements. | 20/04/2026 | 20/04/2026 | FCAJ Guidebook |
| Tue | - Research the theory behind RAG (Retrieval-Augmented Generation). <br> - Analyze the serverless architecture on AWS: Cognito (Auth), API Gateway (API Entry), Lambda (Logic), DynamoDB (Chat History), Bedrock KB (RAG). <br> - Sketch the draft architecture diagram. | 21/04/2026 | 21/04/2026 | [RAG Architecture on AWS](https://aws.amazon.com/blogs/machine-learning/) |
| Wed | - Register a new AWS Free Tier account. <br> - Enable MFA (Multi-Factor Authentication) for the Root Account. <br> - Create an IAM Group `AdminGroup` and an IAM User `intern-admin` for daily usage. | 22/04/2026 | 22/04/2026 | [AWS Security Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| Thu | - Configure AWS Budgets with a monthly spending limit of $10. <br> - Set alert thresholds at 80% ($8) for both actual and forecasted costs. <br> - Link the alerts to Amazon SNS to send notifications directly to my personal email. | 23/04/2026 | 23/04/2026 | [AWS Budgets Guide](https://docs.aws.amazon.com/cost-management/latest/userguide/) |
| Fri | - Initialize the React frontend using Vite and TypeScript. <br> - Install Tailwind CSS for styling and GSAP for smooth interactive animations. <br> - Run the project locally and inspect the folder structure. | 24/04/2026 | 24/04/2026 | [Vite Guide](https://vitejs.dev/) & [GSAP Docs](https://gsap.com/docs/) |

### Knowledge Gained This Week:
* **RAG (Retrieval-Augmented Generation) Concept:** Understood how linking internal document search (Retrieval) with LLM response generation (Generation) minimizes model hallucinations.
* **IAM Security:** Understood the principle of least privilege, distinguishing between the Root account and an IAM User. Learned how to apply MFA to protect credentials.
* **AWS Budgets:** Mastered budget automation on AWS to prevent billing surprises at the end of the month.
* **Vite + React + TS Setup:** Organized a modern Single Page Application with TypeScript for type safety.

### Challenges & Solutions:
* **Challenge:** Did not receive test emails from AWS Budgets despite setting up the thresholds.
* **Solution:** Discovered that the Amazon SNS topic used for routing alerts requires the recipient to confirm their subscription via an automated verification email. After clicking the confirmation link, budget notifications began arriving as expected.

### Week 1 Achievements:
* The Serverless RAG Chatbot architecture design draft was approved by the mentor.
* The AWS account was secured following industry best practices (Root MFA + IAM admin user).
* AWS Budgets alert system is live and verified.
* Successfully set up the React + Vite + TypeScript frontend repository.
