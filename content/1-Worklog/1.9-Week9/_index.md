---
title: "Week 9 Worklog"
date: 2026-06-15
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives:
* Integrate the Amazon Cognito Identity SDK (`amazon-cognito-identity-js`) into the React Frontend application.
* Design and implement login, sign-up, logout, and email-delivered OTP verification screens.
* Setup Axios Interceptors in the Frontend to automatically append Cognito JWT tokens to API request headers.
* Configure a silent Token Refresh mechanism using Refresh Tokens to automatically fetch new credentials upon expiration.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Install `amazon-cognito-identity-js` in the React frontend codebase. <br> - Configure environment variables (`UserPoolId` and `ClientId` parameters) inside the `.env` local file. | 15/06/2026 | 15/06/2026 | [Cognito Auth SDK NPM](https://www.npmjs.com/package/amazon-cognito-identity-js) |
| Tue | - Program a React `AuthContext` and `AuthProvider` using the React Context API to manage user session states globally. | 16/06/2026 | 16/06/2026 | [React Context Docs](https://react.dev/reference/react/useContext) |
| Wed | - Build form components: Login, Registration, and OTP code verification panels. <br> - Apply page transition animations using GSAP. | 17/06/2026 | 17/06/2026 | Intern Worklog |
| Thu | - Configure the global Axios client instance. <br> - Program Axios Request Interceptors to insert `Authorization: Bearer <ID_TOKEN>` headers. <br> - Program Axios Response Interceptors to capture `401 Unauthorized` responses and initiate silent token refreshes. | 18/06/2026 | 18/06/2026 | [Axios Interceptors Docs](https://axios-http.com/docs/interceptors) |
| Fri | - Verify the complete authentication workflow from client register -> OTP input -> verification check -> login -> auth token API fetch. <br> - Write the Week 9 worklog. | 19/06/2026 | 19/06/2026 | Developer Notes |

### Knowledge Gained This Week:
* **Amazon Cognito SDK:** Learned to trigger sign-up, sign-in, and verification commands directly from React Frontend code without requiring middle-tier backend servers.
* **Axios Interceptors:** Mastered intercepting and modifying outbound requests and inbound responses, facilitating transparent authentication token refreshes for seamless UX.
* **JWT Client Storage:** Learned strategies for securely storing Cognito identity tokens in browser `LocalStorage` or `SessionStorage`.

### Challenges & Solutions:
* **Challenge:** The Frontend received `401 Unauthorized` errors from API Gateway when calling the `/sessions` route, even though the user had successfully logged in and fetched their JWT tokens.
* **Solution:** Inspected the JWT tokens on `jwt.io` and detected two structural errors:
  1. The client was sending Cognito's `Access Token` instead of the required `ID Token` (API Gateway Cognito Authorizers require ID Tokens to validate user identity contexts).
  2. The Authorization header prefix format was mismatched: API Gateway was configured to check raw token strings. Corrected the Axios Interceptor config to send the raw ID Token (`Authorization: idToken`) without the `Bearer ` prefix and updated the API Gateway parameters to match.

### Week 9 Achievements:
* Successfully wired Amazon Cognito User Pool Authentication into the React Frontend.
* Designed responsive Login, Sign-up, and OTP verification layouts.
* Configured Axios Interceptors to transparently manage JWT token validation and silent token refreshes.
