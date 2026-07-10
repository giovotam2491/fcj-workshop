---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:
* Develop the core backend Lambda function (`chatWithKB`) handling user query routing for RAG.
* Utilize the AWS SDK v3 (`@aws-sdk/client-bedrock-agent-runtime`) to connect directly to the Bedrock Knowledge Base.
* Define custom System Prompt Templates for the Amazon Nova Lite LLM at the API layer to control reply constraints.
* Persist chat history logs (both user questions and assistant answers) inside the DynamoDB database.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Install the SDK dependency `@aws-sdk/client-bedrock-agent-runtime` for the backend project. <br> - Research Bedrock client methods and request/response structures for `RetrieveAndGenerateCommand`. | 01/06/2026 | 01/06/2026 | [AWS SDK Bedrock Agent Runtime](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/client/bedrock-agent-runtime/) |
| Tue | - Code the `chatWithKB` Lambda handler linked to the POST `/sessions/{sessionId}/chat` API. <br> - Configure connection variables: Knowledge Base ID, Amazon Nova Lite Model ARN, and search type `KNOWLEDGE_BASE`. | 02/06/2026 | 02/06/2026 | AWS Console Configurations |
| Wed | - Embed custom prompt templates in the `generationConfiguration` parameters of the API call. <br> - Instruct the LLM to cite specific sources, avoiding making up information not mentioned in the source context. | 03/06/2026 | 03/06/2026 | [Bedrock Custom Prompt Guide](https://docs.aws.amazon.com/bedrock/) |
| Thu | - Implement data persistence logic: extract textual responses and citation metadata array returned from Bedrock. <br> - Commit two database items (User Message and Bot Message with citation context) concurrently to DynamoDB. | 04/06/2026 | 04/06/2026 | Developer Notes |
| Fri | - Configure IAM policies granting the Lambda execution role rights to call `bedrock:RetrieveAndGenerate`. <br> - Deploy the CDK stack and test the API using Postman. <br> - Write the Week 7 worklog. | 05/06/2026 | 05/06/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **AWS SDK v3 for Bedrock:** Initialized and configured the Bedrock Agent Runtime client in TypeScript.
* **Mechanism of the `RetrieveAndGenerate` API:** Understood how AWS handles the two-step RAG flow under the hood: retrieving relevant contexts from the vector index (Retrieve) and passing them along with the user prompt to the LLM to generate replies (Generate) in a single API call.
* **Citations & Metadata:** Analyzed the `citations` array returned by Bedrock to display which PDF file and what paragraph on S3 the answer was derived from.

### Challenges & Solutions:
* **Challenge:** The Lambda function crashed during Bedrock API execution raising `AccessDeniedException: User: arn:aws:sts::...:assumed-role/LambdaRole is not authorized to perform: bedrock:RetrieveAndGenerate`.
* **Solution:** Identified that the default Lambda Execution Role lacked permissions to invoke Bedrock. Updated the CDK stack to add a custom IAM Policy statement allowing the `bedrock:RetrieveAndGenerate` action, attaching it to the Lambda execution role:
  ```typescript
  lambdaFunction.addToRolePolicy(new iam.PolicyStatement({
    actions: ['bedrock:RetrieveAndGenerate'],
    resources: ['*'], // Can be restricted to the specific Knowledge Base ARN
  }));
  ```

### Week 7 Achievements:
* Integrated Bedrock Knowledge Base RAG pipeline into the API Gateway and Lambda backend stack.
* Completed the `/chat` API: handles user input, queries the S3 data source, prompts the Nova Lite LLM, maps citation details, and records history logs to DynamoDB.
