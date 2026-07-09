---
title: "Week 1 Worklog"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

* Attend the FCAJ opening ceremony and understand the requirements of the internship program and the final project.
* Learn the AWS overview and the main service categories (Compute, Storage, Networking, Database).
* Create, configure and secure the first AWS account following best practices (MFA, IAM, least privilege).
* Set up cost monitoring and control mechanisms from the start to avoid unexpected charges.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Attend the FCAJ opening ceremony at the university; hear the introduction to the program goals, the 12-week roadmap and the evaluation criteria <br> - Read and take notes on the program rules, worklog regulations and the final project deliverables <br> - Get familiar with the working environment, the report submission process and the management tool (Notion) | 20/04/2026 | 20/04/2026 | <https://app.notion.com/p/Group-description-TP-HCM-347df829a730809a8f63d39505644917> |
| Tuesday | - Learn the overview of cloud computing and service models (IaaS, PaaS, SaaS) <br> - Study the 4 main AWS service categories: Compute (EC2, Lambda), Storage (S3, EBS), Networking (VPC, Route 53), Database (RDS, DynamoDB) <br> - Understand the concepts of Region and Availability Zone and how to choose a suitable Region | 21/04/2026 | 21/04/2026 | <https://cloudjourney.awsstudygroup.com/1-explore/> |
| Wednesday | - Register and create a new AWS account, verify the email and billing information <br> - Enable MFA (Multi-Factor Authentication) for the root account to increase security <br> - Create a dedicated IAM user with admin privileges for daily use instead of the root account <br> - Set up an account alias and test logging in with the IAM user | 22/04/2026 | 22/04/2026 | <https://000001.awsstudygroup.com/> |
| Thursday | - Learn the core IAM concepts: User, Group, Role and Policy, and distinguish the role of each component <br> - Practice creating a Group, attaching a Policy and adding a User to the Group <br> - Understand the structure of an IAM Policy (Effect, Action, Resource) <br> - Apply the principle of least privilege when granting permissions | 23/04/2026 | 23/04/2026 | <https://000002.awsstudygroup.com/> |
| Friday | - Set up AWS Budgets to define budget thresholds and receive email alerts when costs exceed the limit <br> - Configure Cost Explorer to track spending per service <br> - Learn about AWS Support plans and the Free Tier, and how to check free usage <br> - Consolidate and write the Week 1 worklog | 24/04/2026 | 24/04/2026 | <https://000007.awsstudygroup.com/> <https://000009.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **AWS overview:** understand the cloud computing model, distinguish IaaS/PaaS/SaaS and grasp the role of the 4 core service categories (Compute, Storage, Networking, Database).
* **Region & Availability Zone:** understand how AWS organizes its physical infrastructure and the criteria for choosing a Region (latency, cost, legal compliance).
* **Account security:** know why the root account should not be used daily, how to enable MFA and create a replacement admin IAM user.
* **IAM:** distinguish User, Group, Role and Policy; read and understand the Policy structure (Effect – Action – Resource) and apply the least privilege principle.
* **Cost management:** know how to use AWS Budgets, Cost Explorer and the Free Tier to monitor and control costs and avoid unexpected charges.

### Week 1 Achievements:

* Clearly understood the internship objectives, the program roadmap and the final project requirements.
* Grasped the AWS overview and the main service categories.
* Successfully created and secured the first AWS account (root MFA + admin IAM user).
* Mastered IAM basics (User / Group / Role / Policy) and the least privilege principle.
* Configured budget alerts to proactively control and prevent unexpected costs.
