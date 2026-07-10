---
title: "Week 10 Worklog"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:
* Complete the Chatbot User Interface: implement support for Markdown rendering and citation sources overlay.
* Apply GSAP timelines to program smooth visual transitions: text-typing mockups and slide-in citations drawer panels.
* Deploy Frontend infrastructure using AWS CDK: S3 Bucket (Static Web Hosting) integrated with Amazon CloudFront CDN.
* Bind AWS WAF (Web Application Firewall) to protect web distribution endpoints from common cyber threats and rate-limit abuses.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Install `react-markdown` and dependencies to format chatbot output blocks. <br> - Build citation card components: selecting source tags opens document preview panels hosted on S3. | 22/06/2026 | 22/06/2026 | [React Markdown NPM](https://www.npmjs.com/package/react-markdown) |
| Tue | - Use GSAP Timelines to animate typewriter text output simulations when receiving stream results from APIs. <br> - Code smooth slide-in animations for the right-hand side Citations panel. | 23/06/2026 | 23/06/2026 | Intern Worklog |
| Wed | - Program CDK definitions for S3 static buckets and CloudFront Distribution routing. <br> - Configure Origin Access Control (OAC) to secure the S3 origin (restricting direct S3 bucket read access). | 24/06/2026 | 24/06/2026 | [CDK CloudFront Construct](https://docs.aws.amazon.com/cdk/api/v2/docs/aws-cdk-lib.aws_cloudfront-readme.html) |
| Thu | - Configure an AWS WAF WebACL stack in the CDK. <br> - Enable AWS Managed Rule Groups (including CommonRuleSet and SQLiRuleSet) to prevent XSS and SQL Injection. <br> - Bind the WAF WebACL to the CloudFront CDN distribution instance. | 25/06/2026 | 25/06/2026 | [AWS WAF Developer Guide](https://docs.aws.amazon.com/waf/) |
| Fri | - Build the React client app bundle (`npm run build`) and upload static assets to S3. <br> - Debug SPA route-rendering bugs on CloudFront. <br> - Write the Week 10 worklog. | 26/06/2026 | 26/06/2026 | Developer Notes |

### Knowledge Gained This Week:
* **CloudFront + S3 OAC Security:** Learned how Origin Access Control (OAC) prevents external users from bypassing CDN nodes to download assets directly from backend storage buckets.
* **AWS WAF Rules Integration:** Learned defensive rule bindings to block OWASP Top 10 vulnerabilities at edge routing layers.
* **SPA Routing on CDNs:** Understood client-side React Router routing mechanics vs. server-side directory path lookups.

### Challenges & Solutions:
* **Challenge:** Root URL visits `https://domain.cloudfront.net` loaded correctly, but reloading the browser (F5) on secondary pages like `https://domain.cloudfront.net/login` returned an `Access Denied (403)` error from S3 since the login folder did not physically exist.
* **Solution:** Identified this as an SPA routing artifact. Configured Custom Error Responses on the CloudFront distribution via CDK: mapped S3 origin errors (`403 Forbidden` and `404 Not Found`) to redirect to `/index.html` with an HTTP status code of `200 OK`. The client-side React Router then processed the path and rendered the matching component.

### Week 10 Achievements:
* Completed Chatbot UI styling with markdown parsing and Citation panel drawer integrations.
* Deployed the frontend application to S3, fronted by a global CloudFront CDN.
* Hardened edge networks with AWS WAF blocking SQLi and XSS scripts.
