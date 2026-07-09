---
title : "Configure Environment"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.4.2 </b> "
---

`npm run provision` (section 5.3.1) already generated `frontend/.env` for you. In this step you just verify it points to the right resources.

#### Step 1 — Open `frontend/.env`

It should look like this (values are unique to your account):

```bash
VITE_API_BASE_URL=https://xxxxxxxxx.execute-api.us-east-1.amazonaws.com
VITE_COGNITO_USER_POOL_ID=us-east-1_xxxxxxxxx
VITE_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
VITE_AWS_REGION=us-east-1
```

![frontend env](/images/5-Workshop/5.4-Deploy-frontend/frontend-env.png)

#### Step 2 — Cross-check against the provision output

Compare each value with the **API endpoint**, **User Pool ID**, and **App Client ID** printed by `npm run provision` in section 5.3.1. They must match exactly.

{{% notice warning %}}
If you deploy the frontend behind a custom domain (CloudFront/S3) later, remember to add that domain to the **CORS allow-list** of the API Gateway and of the data-source S3 bucket — otherwise browser requests will be blocked by CORS.
{{% /notice %}}
