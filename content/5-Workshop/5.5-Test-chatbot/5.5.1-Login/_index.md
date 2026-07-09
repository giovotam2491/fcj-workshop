---
title : "Login"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.5.1 </b> "
---

This step demonstrates the **authentication flow** (steps 1–3 of the query diagram): CloudFront/S3 serving the app, Cognito issuing a JWT, and the frontend calling the API with that token.

#### Step 1 — Open the app and log in

Open the app URL (local `http://localhost:5173` or your CloudFront domain) and sign in with the admin account printed by `npm run provision`:

```
Email: <SEED_ADMIN_EMAIL>
Password: <TEST_PASSWORD>
```

![login form](/images/5-Workshop/5.5-Test-chatbot/login-form.png)

#### Step 2 — Inspect the Cognito request

Open DevTools (F12) → **Network** tab **before** logging in. After submitting, find the request to `cognito-idp.us-east-1.amazonaws.com` (`InitiateAuth`). The response contains an `IdToken` (JWT).

![cognito request](/images/5-Workshop/5.5-Test-chatbot/cognito-network.png)

{{% notice tip %}}
Copy the `IdToken` value and paste it into [jwt.io](https://jwt.io) to inspect the decoded claims (`sub`, `email`, expiry) — useful evidence that Cognito really issued the token.
{{% /notice %}}

![jwt decoded](/images/5-Workshop/5.5-Test-chatbot/jwt-io.png)

#### Step 3 — Confirm you land on the chat home screen

After a successful login you should be redirected to the main chat/document dashboard.

![chat home](/images/5-Workshop/5.5-Test-chatbot/chat-home.png)
