---
title: "Week 10 Worklog"
date: 2026-06-22
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives:

* Review the entire project and verify that the main functions work correctly.
* Add monitoring for the serverless application using CloudWatch.
* Review security (IAM, S3) according to best practices and control costs.
* Prepare and write the Clean-up section to avoid incurring costs after the project ends.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Review the entire project end-to-end, verifying each main function (Lambda, DynamoDB, API Gateway) <br> - Re-test the CRUD and authentication flows to ensure stable operation <br> - Record and fix any remaining bugs | 22/06/2026 | 22/06/2026 | |
| Tuesday | - Learn about Monitoring Serverless Applications: view Lambda logs and metrics on CloudWatch <br> - Learn how to read log groups, log streams and the key metrics (Invocations, Errors, Duration, Throttles) <br> - Practice debugging Lambda errors based on the logs | 23/06/2026 | 23/06/2026 | <https://000085.awsstudygroup.com/> |
| Wednesday | - Review the IAM Roles/Policies of each service according to the least privilege principle <br> - Check and narrow down over-granted permissions, remove unnecessary ones <br> - Ensure each Lambda/service has only the minimum permissions needed to operate | 24/06/2026 | 24/06/2026 | |
| Thursday | - Review usage costs via AWS Budgets and Cost Explorer, identify the most expensive services <br> - Learn S3 security best practices: Block Public Access, encryption (SSE), minimal bucket policies <br> - Review the security configuration of the buckets in the project | 25/06/2026 | 25/06/2026 | |
| Friday | - Write the Clean-up section for the report: list and provide guidance to delete/turn off unused resources <br> - Determine a sensible deletion order to avoid dependency errors between resources <br> - Consolidate and write the Week 10 worklog | 26/06/2026 | 26/06/2026 | |

### Knowledge Gained This Week:

* **System testing:** know how to review end-to-end and re-test the main functions to ensure stability before finalizing.
* **Serverless monitoring:** understand how to read log groups/log streams and the key Lambda metrics (Invocations, Errors, Duration, Throttles) for debugging.
* **IAM security:** grasp how to review and narrow permissions according to the least privilege principle to reduce security risk.
* **S3 security:** understand best practices such as Block Public Access, data encryption and minimal bucket policies.
* **Cost management & clean-up:** know how to track costs via Budgets/Cost Explorer and the process of cleaning up resources in the correct order to avoid incurring charges.

### Challenges This Week:

* Not yet used to reading Lambda logs on CloudWatch to troubleshoot errors.

### Week 10 Achievements:

* Reviewed and confirmed that the project's main functions (Lambda, DynamoDB, API) work correctly.
* The project has basic monitoring via CloudWatch.
* Completed the security review (IAM least privilege, S3 best practices) and the cost review.
* Wrote a Clean-up guide to avoid incurring costs after the project ends.
