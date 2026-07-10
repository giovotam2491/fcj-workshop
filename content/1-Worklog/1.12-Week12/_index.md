---
title: "Week 12 Worklog"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Week 12 Objectives:
* Optimize overall system response time (E2E Latency) and reduce Lambda cold start durations.
* Analyze and summarize actual project expenditures via AWS Cost Explorer compared to the original AWS Budgets plan.
* Finalize the project hand-over documentation package (architecture diagram, IaC deployment guide, API reference).
* Compile the product demo slide deck and review all worklog entries from Week 1 to Week 12 before submission.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Optimize Lambda function bundle sizes by configuring `esbuild` to exclude the AWS SDK v3 from the code zip (since it is natively available in the Lambda runtime). <br> - Increase the RAM allocation on the chat Lambda to provision additional CPU resources proportionally. | 06/07/2026 | 06/07/2026 | [AWS Lambda Performance Tuning](https://docs.aws.amazon.com/lambda/) |
| Tue | - Enable Gzip/Brotli compression on the CloudFront Distribution. <br> - Configure cache-control headers on S3 static assets to optimize the Frontend page load speed. | 07/07/2026 | 07/07/2026 | [CloudFront Caching Guide](https://docs.aws.amazon.com/AmazonCloudFront/) |
| Wed | - Use AWS Cost Explorer to query a detailed breakdown of total accumulated spending across all 12 weeks of development. <br> - Verify the current budget utilization against AWS Budgets to confirm no runaway costs occurred. | 08/07/2026 | 08/07/2026 | AWS Cost Management Console |
| Thu | - Author technical README documentation for both Frontend and Backend repositories. <br> - Draw the final architecture diagram and capture UI screenshots for the hand-over package. | 09/07/2026 | 09/07/2026 | Project Docs |
| Fri | - Perform a final review of the entire worklog report: verify formatting, navigation links, and embedded images. <br> - Submit the completed internship project report and product demo slides. | 10/07/2026 | 10/07/2026 | FCAJ Submission Portal |

### Knowledge Gained This Week:
* **Lambda Cold Start Optimization:** Understood the relationship between Lambda zip bundle size, allocated RAM/CPU, and execution environment initialization speeds, and how to systematically reduce each factor.
* **Cloud Cost Management:** Gained hands-on experience analyzing granular service-level costs via Cost Explorer, clearly seeing the efficiency of the Serverless billing model (pay-per-invocation).
* **Engineering Documentation:** Learned how to write clear, concise technical hand-over documentation that empowers future maintainers to deploy and operate the system independently.

### Challenges & Solutions:
* **Challenge:** The cold start duration of the `chatWithKB` Lambda function was initially nearly 3.5 seconds, making the application appear frozen on the first message of every new conversation.
* **Solution:** Applied two targeted optimizations:
  1. Modified the `esbuild` bundler configuration in the CDK stack to mark `@aws-sdk/client-bedrock-agent-runtime` and other AWS SDKs as `external` dependencies (excluded from the code zip because the Lambda runtime provides them natively). This reduced the deployment artifact from ~10MB down to ~780KB.
  2. Increased the chat Lambda's memory allocation from 128MB to 512MB. AWS provisions CPU proportionally to RAM, significantly accelerating container decompression and environment initialization.
  
  **Result:** Cold start time dropped from ~3.5s to under **600ms**, delivering a noticeably smoother user experience.

### Week 12 Achievements:
* Achieved full-stack performance optimization (cold start reduced from 3.5s to < 600ms; CloudFront caching live).
* Total cost across the entire 12-week internship project was only **$4.2 USD** — well within the $10 monthly budget threshold.
* Completed the product demo slide deck and the technical hand-over documentation package.
* Submitted the complete internship final report (worklog, proposal, and self-evaluation).