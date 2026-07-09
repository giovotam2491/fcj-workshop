---
title : "Run Project Locally"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.4.3 </b> "
---

#### Step 1 — Start the dev server

```bash
npm run dev
```

Open **http://localhost:5173** in your browser.

![vite dev server](/images/5-Workshop/5.4-Deploy-frontend/vite-dev.png)

#### Step 2 — Confirm the login page loads

You should see the login screen of the 3D glass UI. If the page fails to load, double-check `frontend/.env` (previous step) and make sure the backend passed all checks in [5.3.4](../../5.3-Deploy%20Backend/5.3.4-Verify%20Backend/).

![login page](/images/5-Workshop/5.4-Deploy-frontend/login-page.png)