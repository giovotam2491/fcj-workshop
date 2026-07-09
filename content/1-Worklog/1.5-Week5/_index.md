---
title: "Week 5 Worklog"
date: 2026-05-18
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

* Learn how to monitor resources and applications using Amazon CloudWatch (Logs, Metrics, Alarms).
* Understand the auto-scaling mechanism (EC2 Auto Scaling) to ensure performance and optimize cost.
* Learn how to deliver content globally using a CDN (Amazon CloudFront).
* Consolidate foundational knowledge to choose a direction for the final project.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn Amazon CloudWatch: distinguish Logs, Metrics and Alarms <br> - Understand how CloudWatch collects metrics from services (EC2, RDS...) <br> - Understand the concepts of namespace, dimension and statistics (Average, Sum, Max) <br> - Learn about CloudWatch Dashboards to visualize data | 18/05/2026 | 18/05/2026 | <https://000008.awsstudygroup.com/> |
| Tuesday | - **Practice:** view the logs and metrics of an EC2 instance in the Console <br> - Create a simple alarm (e.g. alert when CPU exceeds a threshold) <br> - Configure an action to send a notification via SNS when the alarm triggers <br> - Check the alarm states (OK / ALARM / INSUFFICIENT_DATA) | 19/05/2026 | 19/05/2026 | <https://000008.awsstudygroup.com/> |
| Wednesday | - Learn about EC2 Auto Scaling: launch templates, Auto Scaling Group (ASG) <br> - Learn the scaling policy types: target tracking, step scaling, scheduled scaling <br> - Understand the concepts of desired/min/max capacity and health checks <br> - Learn how to combine an ASG with a Load Balancer to distribute traffic | 20/05/2026 | 20/05/2026 | <https://000006.awsstudygroup.com/> |
| Thursday | - Complete the networking workshop based on FCJ documentation: build a VPC, subnets, route tables and connect the components <br> - Apply the networking knowledge learned last week in a complete lab <br> - Test connectivity and troubleshoot any configuration errors | 21/05/2026 | 21/05/2026 | <https://000092.awsstudygroup.com/1-introduce/> |
| Friday | - Learn about Amazon CloudFront (CDN): the concepts of edge locations and caching <br> - Understand the role of a CDN in reducing latency and load on the origin (S3/EC2) <br> - Learn how to create a distribution and configure the origin and cache behavior <br> - Consolidate and write the Week 5 worklog | 22/05/2026 | 22/05/2026 | <https://000094.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **CloudWatch:** distinguish Logs, Metrics and Alarms; understand how to monitor resources, create alarms and send notifications via SNS.
* **Auto Scaling:** understand the auto-scaling mechanism through launch templates, Auto Scaling Groups and scaling policies; know how to maintain performance as load changes.
* **Load Balancing:** grasp the idea of combining an ASG with a Load Balancer to distribute traffic and increase availability.
* **CloudFront (CDN):** understand how a CDN uses edge locations to cache and deliver content, reducing latency for end users and load on the origin.
* **Integrated application:** through the networking workshop, learn how to combine multiple services (VPC, EC2, monitoring) into a complete system.

### Challenges This Week:

* Found it difficult to visualize the operating mechanisms of CloudWatch, Auto Scaling, and CloudFront during the initial practice.

### Week 5 Achievements:

* Learned how to view logs and metrics and create monitoring alarms using CloudWatch.
* Understood the application auto-scaling mechanism and traffic distribution on AWS.
* Grasped the role of CloudFront in delivering content globally.
* Completed the networking workshop and gained foundational knowledge to choose an appropriate project direction.
