---
title: "Week 8 Worklog"
date: 2026-06-08
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

* Add user authentication to the project using Amazon Cognito.
* Learn the login mechanism for a Single Page Application (SPA).
* Connect the frontend with the serverless API and test the login flow.
* Learn about AWS Amplify for supporting storage/auth on the frontend.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn about Amazon Cognito: the concepts of User Pool and Identity Pool <br> - Learn the sign-up, email confirmation and sign-in flows <br> - Understand the token mechanism (ID token, Access token, Refresh token) granted after a successful login | 08/06/2026 | 08/06/2026 | <https://000081.awsstudygroup.com/> |
| Tuesday | - Learn the login mechanism for a Single Page Application (SPA) <br> - Learn how the frontend stores and sends the token in the header when calling the API <br> - Understand the JWT concept and how the backend/API Gateway validates the token | 09/06/2026 | 09/06/2026 | <https://000055.awsstudygroup.com/> |
| Wednesday | - Learn about AWS Amplify at a conceptual level: support for auth, storage and hosting for the frontend <br> - Understand how Amplify integrates natively with Cognito and S3 <br> - Compare using Amplify versus configuring the individual services manually | 10/06/2026 | 10/06/2026 | <https://000134.awsstudygroup.com/> |
| Thursday | - **Practice:** connect the frontend with the serverless API (API Gateway + Lambda) <br> - Configure a Cognito authorizer for API Gateway to protect the endpoints <br> - Test the login flow: sign in → receive token → call an authenticated API <br> - Capture screenshots of the steps as documentation for the report | 11/06/2026 | 11/06/2026 | <https://000079.awsstudygroup.com/> |
| Friday | - Research more deeply how the frontend connects and authenticates with the serverless backend <br> - Review the end-to-end flow: user → Cognito → frontend → API Gateway → Lambda → DynamoDB <br> - Handle CORS and authentication errors that arise <br> - Consolidate and write the Week 8 worklog | 12/06/2026 | 12/06/2026 | <https://000079.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **Amazon Cognito:** understand the role of the User Pool/Identity Pool, the sign-up/sign-in flow and the ID/Access/Refresh token mechanism.
* **SPA authentication:** grasp how the frontend stores and sends a JWT token when calling the API, and how the backend validates the token.
* **Cognito Authorizer:** know how to protect API Gateway endpoints with a Cognito authorizer to allow only authenticated requests.
* **AWS Amplify:** understand the role of Amplify in providing built-in auth/storage/hosting for the frontend.
* **End-to-end flow:** visualize the entire flow from the user through authentication to data processing, including CORS error handling.

### Week 8 Achievements:

* The project now has a frontend connected to the backend via the serverless API.
* Integrated user authentication using Amazon Cognito.
* Successfully tested the login flow and calling a token-protected API.
* Understood basic user authentication (Cognito + JWT).
* Obtained additional screenshots and notes as documentation for the workshop section of the report.
