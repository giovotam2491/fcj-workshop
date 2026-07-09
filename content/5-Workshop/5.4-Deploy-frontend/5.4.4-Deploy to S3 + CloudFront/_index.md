---
title : "Deploy to S3 + CloudFront (optional)"
date : 2024-01-01
weight : 4
chapter : false
pre : " <b> 5.4.4 </b> "
---

Running with `npm run dev` is enough to validate the workshop end-to-end. This step shows how to host the app for real, the same way production static sites are typically deployed on AWS — the same setup used for this project's own live demo.

#### Step 1 — Build the production bundle

```bash
cd frontend
npm run build
```

This outputs static files into `frontend/dist/` (`index.html` + hashed JS/CSS assets).

![vite build](/images/5-Workshop/5.4-Deploy-frontend/vite-build.png)

#### Step 2 — Create a private S3 bucket for web hosting

1. **S3 Console → Create bucket**. Bucket name must be **globally unique**, e.g. `rag-web-<your-name>` (must start with `rag-web-` to match the `S3ProjectBuckets` statement in the IAM policy from section 5.2).
2. **AWS Region**: **US East (N. Virginia) us-east-1**.
3. **Block Public Access settings for this bucket**: keep **all 4 checkboxes ON** (the default) — the bucket stays fully private; CloudFront will reach it through OAC, never through direct public access.
4. Leave everything else at default → **Create bucket**.
5. Upload the build output:

```bash
aws s3 sync dist/ s3://<your-web-bucket-name>
```

![s3 web bucket](/images/5-Workshop/5.4-Deploy-frontend/s3-web-bucket.png)

#### Step 3 — Create a CloudFront distribution

AWS reworked **Create distribution** into a 6-page wizard. Walk through it like this:

1. **Choose a plan**: select **Pay as you go** (bottom option). The flat-fee Free/Pro/Business/Premium plans bundle extra features this workshop doesn't need; Pay-as-you-go has no monthly commitment and stays effectively **$0** for a low-traffic demo thanks to CloudFront's standing free tier (1 TB data transfer + 10M requests/month, account-wide, separate from these named plans).
2. **Get started**:
   - **Distribution name**: e.g. `rag-chatbot-frontend`.
   - **Distribution type**: keep **Single website or app**.
   - **Domain – optional**: leave blank — this project doesn't use a custom Route 53 domain; the default `*.cloudfront.net` URL is enough.
3. **Specify origin**:
   - **Origin type**: **Amazon S3**.
   - **S3 origin**: click **Browse S3** and pick your web-hosting bucket.
   - **Settings**: keep **"Allow private S3 bucket access to CloudFront – Recommended"** checked. This single checkbox replaces the old manual "create an OAC, then copy-paste the bucket policy" process — CloudFront now creates the Origin Access Control *and* writes the correct S3 bucket policy for you automatically.
   - Leave **Origin settings** and **Cache settings** on their recommended defaults.

![cloudfront oac](/images/5-Workshop/5.4-Deploy-frontend/cloudfront-oac.png)

4. **Enable security**: under **Web Application Firewall (WAF)**, choose **Enable security protections** — this matches the architecture diagram, which shows WAF sitting in front of CloudFront. This bundles the common-vulnerability/malicious-actor/threat-intelligence protections shown on screen automatically — there's no separate "add a managed rule group" step to do afterward anymore. Leave **Use monitor mode** unchecked (so it actually blocks, not just logs) and leave **Protection against Layer 7 DDoS attacks** off (extra cost, unnecessary for a demo).

![waf managed rules](/images/5-Workshop/5.4-Deploy-frontend/waf-managed-rules.png)

{{% notice warning %}}
This step shows a live **price estimate** (e.g. "~$14 per 10 million requests/month") — enabling WAF is *not* fully free the way skipping it would be, but cost scales with request volume: a handful of test requests for a workshop demo costs a tiny fraction of a cent. If you'd rather guarantee $0 cost, pick **Do not enable security protections** instead and note in your report that the architecture supports WAF but it's disabled to control cost.
{{% /notice %}}

5. **Get TLS certificate**: nothing to configure since no custom domain was entered — CloudFront uses its own default certificate for `*.cloudfront.net`. Click **Next**.
6. **Review and create**: confirm **Origin → Grant CloudFront access to origin: Yes** (proof the bucket policy was auto-applied) and **Security → Security protections: Enabled**, then click **Create distribution**.

![s3 bucket policy oac](/images/5-Workshop/5.4-Deploy-frontend/s3-bucket-policy-oac.png)

Wait for the distribution's **Status** to flip from **Deploying** to **Enabled** (usually 3–10 minutes).

{{% notice tip %}}
If the app uses client-side routing and refreshing a sub-page (e.g. `/documents`) shows CloudFront's default 403/404 error page instead of the app, add a **Custom error response** on the distribution (**Error pages tab → Create custom error response**): HTTP error code `403` (and `404`) → Response page path `/index.html` → HTTP Response code `200`. This lets the SPA's own router handle the URL instead of CloudFront erroring out.
{{% /notice %}}

#### Step 4 — Update CORS

The CloudFront domain is a **new browser origin** — both backend endpoints the frontend calls must explicitly allow it:

1. **API Gateway → `rag-chatbot-api` → CORS** (left sidebar of the API) → add your CloudFront URL (e.g. `https://dxxxxxxxxxxxxx.cloudfront.net`) to **Access-Control-Allow-Origin** → **Save**.
2. **S3 → your docs bucket (`DOCS_BUCKET`) → Permissions → Cross-origin resource sharing (CORS) → Edit** → add the same CloudFront URL into the `AllowedOrigins` array (needed for presigned PUT uploads to work from the browser) → **Save changes**.

![cors update](/images/5-Workshop/5.4-Deploy-frontend/cors-update.png)

#### Step 5 — Verify

Open the CloudFront URL shown in the distribution's **Domain name** field (e.g. `https://dxxxxxxxxxxxxx.cloudfront.net`) and confirm:

- The login page loads correctly (proves `index.html` + hashed assets are being served).
- DevTools → Network → the document response shows a `via: 1.1 ....cloudfront.net (CloudFront)` header — proof the app is served entirely through CloudFront, never directly from S3.
- Open the S3 object URL directly (or the bucket's own endpoint) — it should return `AccessDenied`, confirming the bucket really is private and OAC is doing its job.
- If you enabled WAF: open **WAF & Shield Console → your Web ACL → Sampled requests** and confirm your own browsing traffic shows up with **Action: ALLOW** — proof the Web ACL is actively inspecting live requests, not just sitting there unused.

![cloudfront live](/images/5-Workshop/5.4-Deploy-frontend/cloudfront-live.png)
