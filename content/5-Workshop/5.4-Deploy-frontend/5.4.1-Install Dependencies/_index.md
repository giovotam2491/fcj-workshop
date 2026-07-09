---
title : "Install Dependencies"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1 </b> "
---

The frontend lives in the `frontend/` folder of the same repository you already cloned in [5.3.1](../../5.3-Deploy%20Backend/5.3.1-deploy-lambda/).

#### Step 1 — Go to the frontend folder

```bash
cd rag-chatbot-aws-serverless/frontend
```

#### Step 2 — Install dependencies

```bash
npm install
```

![npm install](/images/5-Workshop/5.4-Deploy-frontend/npm-install.png)

{{% notice note %}}
No manual Cognito/API setup is needed here — `frontend/.env` was already generated and filled in automatically by `npm run provision` in the backend step (`VITE_API_BASE_URL`, `VITE_COGNITO_USER_POOL_ID`, `VITE_COGNITO_CLIENT_ID`). You will double-check these values in the next step.
{{% /notice %}}

{{% notice tip %}}
🔍 **Verify on Console**: open **Cognito → User pools → `rag-chatbot-pool` → Applications → App clients**, and confirm the **Client ID** shown there matches `VITE_COGNITO_CLIENT_ID` in `frontend/.env`.
{{% /notice %}}

![cognito app client](/images/5-Workshop/5.4-Deploy-frontend/cognito-app-client.png)
