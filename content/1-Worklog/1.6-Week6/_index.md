---
title: "Week 6 Worklog"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:
* Design and configure Amazon API Gateway (REST API) as the public entry point for backend services.
* Integrate Amazon Cognito User Pool Authorizer into API Gateway to protect all API endpoints.
* Configure CORS (Cross-Origin Resource Sharing) on API Gateway to allow safe local frontend API calls.
* Deploy the first API methods for the `/sessions` resource (GET/POST) integrated with their respective Lambda handlers.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Write CDK code defining the API Gateway REST API. <br> - Configure the `/sessions` resource and integrate with Lambdas using Lambda Proxy Integration. | 25/05/2026 | 25/05/2026 | [CDK API Gateway Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_apigateway-readme.html) |
| Tue | - Create a Cognito User Pool Authorizer inside the API Gateway CDK configuration. <br> - Attach the Authorizer to the `/sessions` resource methods (GET/POST) and set the token header key to `Authorization`. | 26/05/2026 | 26/05/2026 | [API Gateway Cognito Authorizer Docs](https://docs.aws.amazon.com/apigateway/) |
| Wed | - Code the `getChatHistory` Lambda function to query DynamoDB records and retrieve all messages associated with a specific `SessionId` (GET `/sessions/{sessionId}/messages`). | 27/05/2026 | 27/05/2026 | Developer Notes |
| Thu | - Configure CORS configurations by mapping out standard OPTIONS methods and authorization header access lists. <br> - Test API Gateways using Postman: generate a Cognito ID Token using CLI tools and attach it in the header as `Authorization: Bearer <ID_TOKEN>`. | 28/05/2026 | 28/05/2026 | Postman Reference |
| Fri | - Test and debug CORS header issues raised in Lambda responses, completing API Gateway deployment. <br> - Consolidate and write the Week 6 worklog. | 29/05/2026 | 29/05/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **API Gateway Lambda Proxy Integration:** Understood how HTTP requests are passed verbatim to Lambda handlers as a structured JSON object (`event`), letting Lambda control HTTP response codes and payloads completely.
* **JWT Token Validation:** Learned how API Gateway automatically validates Cognito JWT token signatures using OIDC keys at the Gateway level, removing manual verification logic from application Lambdas.
* **CORS Mechanics:** Distinguished between Gateway-level CORS (answering browser preflight OPTIONS requests) and Application-level CORS (returning credentials and headers in actual API responses).

### Challenges & Solutions:
* **Challenge:** Encountered CORS errors when triggering API requests from local hosts. The browser blocked API responses stating `No 'Access-Control-Allow-Origin' header is present on the requested resource`, even though CORS was configured on API Gateway.
* **Solution:** Discovered that when Lambda threw business logic exceptions (e.g., returning 400 Bad Request or 500 Server Error), the Lambda handler outputted a response JSON that lacked the required CORS headers. Under Proxy Integration, API Gateway forwards Lambda responses verbatim. Fixed this by updating the backend's shared response helper to always embed the following headers in all Lambda outputs (for both success and error responses):
  ```json
  "headers": {
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token",
    "Access-Control-Allow-Methods": "DELETE,GET,HEAD,OPTIONS,PATCH,POST,PUT"
  }
  ```

### Week 6 Achievements:
* Successfully deployed API Gateway REST API secured with a Cognito User Pool Authorizer.
* Completed endpoints for session management and chat history queries.
* Resolved all CORS issue sources across both API Gateway preflights and Lambda proxy response codes.
