---
title: "Week 11 Worklog"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives:
* Design and configure monitoring dashboards using Amazon CloudWatch (Logs, Metrics, and Dashboards).
* Conduct RAG quality evaluations (RAG Evaluation) focusing on faithfulness (groundedness) and minimizing hallucination risks.
* Perform a comprehensive security audit to enforce the Principle of Least Privilege across all Lambda IAM execution roles.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Build a centralized CloudWatch Dashboard. <br> - Add widgets monitoring key KPIs: API Gateway Request Volumes, HTTP 4XX/5XX errors, Lambda Durations, and execution errors. | 29/06/2026 | 29/06/2026 | [Amazon CloudWatch Guide](https://docs.aws.amazon.com/AmazonCloudWatch/) |
| Tue | - Utilize CloudWatch Logs Insights to author query routines, identifying error signatures and compiling P99 Lambda durations. | 30/06/2026 | 30/06/2026 | [CloudWatch Logs Insights Query Syntax](https://docs.aws.amazon.com/AmazonCloudWatch/) |
| Wed | - Structure a RAG accuracy evaluation framework running 50 test queries. <br> - Evaluate Answer Relevance and Groundedness Scores manually and using LLM-as-a-judge patterns. | 01/07/2026 | 01/07/2026 | RAG Evaluation Methods |
| Thu | - Audit all CDK-generated IAM policies. <br> - Replace wildcard `*` resource blocks with specific database and storage resource ARNs. | 02/07/2026 | 02/07/2026 | [IAM Least Privilege Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/) |
| Fri | - Inspect and optimize Lambda Function permissions, pruning unused read/write capabilities. <br> - Write the Week 11 worklog. | 03/07/2026 | 03/07/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **Serverless Monitoring:** Learned to use CloudWatch dashboards to capture runtime anomalies proactively rather than relying on manual bug reports.
* **RAG Evaluation Metrics:** Mastered measuring Faithfulness (whether outputs are strictly derived from input documents) and Answer Relevance (whether generated answers directly address user intents).
* **IAM Policy Hardening:** Understood how target-specific resource ARNs mitigate access exposure compared to broad wildcards.

### Challenges & Solutions:
* **Challenge:** During the automated evaluation test runner sending 50 queries concurrently, Amazon Bedrock threw `ThrottlingException: Rate exceeded for Nova Lite model` faults, interrupting the test suite.
* **Solution:** Identified that request spikes exceeded default Transactions Per Second (TPS) quotas allocated to Bedrock models in the intern sandbox account. Resolved this by:
  1. Setting a maximum batch limit of 2 concurrent API calls inside the test runner script.
  2. Implementing a backoff-retry helper utilizing exponential waiting periods when catching HTTP 429 exceptions. The test suite successfully completed thereafter.

### Week 11 Achievements:
* Configured real-time CloudWatch Dashboards.
* Verified high accuracy of the RAG system, yielding a ~92% average Groundedness Score.
* Enforced Least Privilege configurations across all Lambda Execution Roles.
