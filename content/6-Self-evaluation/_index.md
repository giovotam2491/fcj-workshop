---
title: "Self-Assessment"
date: 2026-07-10
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Throughout my internship at **Amazon Web Services Vietnam Co., Ltd.**, under the **Workforce Bootcamp – First Cloud AI Journey (FCAJ)** program, from **20/04/2026** to **10/07/2026**, I had the opportunity to learn, grow, and apply academic knowledge to a real-world professional environment.

Over 12 weeks, I progressed from mastering AWS foundational services (IAM, S3, DynamoDB, Lambda, API Gateway) to designing, building, and deploying a complete serverless system — an **Internal Document Retrieval Chatbot using Retrieval-Augmented Generation (RAG) on AWS Serverless**, powered by Amazon Bedrock Knowledge Base, Titan Text Embeddings V2, and the Amazon Nova Lite LLM. Through this project, I significantly improved my skills in cloud architecture design, serverless backend development (Lambda, API Gateway, DynamoDB), user authentication with Amazon Cognito, Infrastructure as Code using AWS CDK (TypeScript), system monitoring via CloudWatch, IAM security auditing based on the Principle of Least Privilege, and bilingual technical documentation writing.

In terms of professional conduct, I consistently strived to complete tasks on schedule, adhere to program regulations, and actively engage with my mentor and fellow interns to maximize work efficiency.

To reflect objectively on my internship journey, I would like to evaluate myself based on the following criteria:

| No. | Criteria | Description | Good | Fair | Average |
| --- | --- | --- | --- | --- | --- |
| 1 | **Professional Knowledge & Skills** | Strong grasp of relevant AWS services (Lambda, DynamoDB, Bedrock Knowledge Base, Cognito, CloudFront, WAF); applied knowledge to deploy the full RAG pipeline; TypeScript and AWS CDK skills met project standards; code quality was clean and well-structured | ✅ | ☐ | ☐ |
| 2 | **Ability to Learn** | Rapidly absorbed entirely new concepts: serverless architectures, RAG pipelines, vector embeddings, LLMs, and Amazon Bedrock — none of which were covered in university; independently consulted AWS documentation and external technical resources | ✅ | ☐ | ☐ |
| 3 | **Proactiveness** | Self-researched IAM Best Practices and investigated error solutions before asking the mentor; proactively proposed the DynamoDB table schema design, API Gateway structure, and custom Prompt Template configurations | ☐ | ✅ | ☐ |
| 4 | **Sense of Responsibility** | Completed all 12 weekly worklog entries on schedule; ensured technical content quality; delivered fully documented hand-over materials before the final demo session | ✅ | ☐ | ☐ |
| 5 | **Discipline** | Maintained schedules, followed program regulations, and consistently submitted weekly reports; however, a few weeks ran close to deadlines when Frontend integration proved more complex than estimated | ☐ | ✅ | ☐ |
| 6 | **Progressive Mindset** | Openly received mentor feedback on code style and architecture decisions (e.g., Lambda handler organization, DynamoDB query optimization) and made continuous improvements throughout each sprint | ✅ | ☐ | ☐ |
| 7 | **Communication** | Clearly presented ideas and reported progress in weekly check-ins; however, articulating architectural decision rationale to non-technical stakeholders is an area requiring further development | ☐ | ✅ | ☐ |
| 8 | **Teamwork** | Collaborated effectively with my mentor and admin team; actively participated in FCAJ community activities; shared debugging experiences and solutions with fellow interns in the group | ✅ | ☐ | ☐ |
| 9 | **Professional Conduct** | Respected mentors, colleagues, and the professional work environment; maintained a positive attitude even during extended debugging sessions and tight deadline pressure | ✅ | ☐ | ☐ |
| 10 | **Problem-Solving Skills** | Identified and resolved numerous real-world technical issues: IAM Policy `AccessDenied` misconfigurations, CORS failures caused by missing Lambda response headers, wrong Cognito token type (`Access Token` vs `ID Token`), Lambda cold start latency of 3.5s, and Bedrock API `ThrottlingException` | ☐ | ✅ | ☐ |
| 11 | **Contribution to Project** | Delivered a fully functional end-to-end RAG chatbot with 12 Lambda functions handling distinct business operations; deployed the frontend to S3 + CloudFront + WAF; RAG Groundedness Score achieved ~92%; complete bilingual documentation | ✅ | ☐ | ☐ |
| 12 | **Overall** | Successfully completed a real-world AI/Cloud project from scratch to a fully operational Serverless RAG system within 12 weeks — the most valuable technical and professional experience I have gained throughout my time as a student | ✅ | ☐ | ☐ |

### Areas for Improvement

* **Research Independence:** Need to more thoroughly explore AWS Documentation and experiment with solutions before consulting the mentor. For example, when encountering IAM `AccessDenied` errors, consulting the *IAM Policy Elements Reference* first — rather than immediately asking for help — would build stronger independent debugging skills over time.
* **Project Time Management:** Need to distribute workload more evenly to avoid compressing multiple large-scope tasks (Cognito SDK integration, GSAP animations, CloudFront + WAF deployment) into a single week. Setting daily sub-milestones rather than only weekly targets would help surface schedule risks earlier.
* **Technical Communication:** Improve the ability to present and defend architecture decisions (Architecture Decision Records) to non-technical stakeholders; practice giving concise, value-focused demos rather than leading with deep technical implementation details.
