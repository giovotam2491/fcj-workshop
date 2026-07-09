---
title: "Week 2 Worklog"
date: 2026-04-27
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

* Gain a deeper understanding of how AWS manages access permissions through IAM Roles and Policies.
* Learn the core EC2 components and successfully create and connect to an EC2 instance.
* Learn how to attach an IAM Role to EC2 (instance profile) for secure access to AWS services instead of hard-coding access keys.
* Get familiar with the cloud development environment (AWS Cloud9).

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Review IAM: practice creating Roles and Policies and attaching a Policy to a Role <br> - Clearly distinguish User and Role: a User is for a person/fixed application, a Role grants temporary permissions to a service or another user <br> - Learn the concepts of trust policy and the assume-role mechanism | 27/04/2026 | 27/04/2026 | <https://youtu.be/mCLYcsJ0GXQ?si=Y_CHiyxREA6GTUkZ> |
| Tuesday | - Learn the core EC2 components: instance type (family, vCPU, RAM), AMI (Amazon Machine Image), EBS (attached storage) and key pair (SSH key) <br> - Understand how to choose an instance type suited to workload and cost <br> - Understand the role of Security Groups in controlling access to an instance | 28/04/2026 | 28/04/2026 | <https://youtu.be/Dc0t4LDOySY?si=TTeWvYwVEjmFrz7M> |
| Wednesday | - **Practice:** create an EC2 instance (choose AMI, instance type, key pair, Security Group) <br> - Connect to the instance via SSH using the created key pair <br> - Create and attach an additional EBS volume to the instance and practice mounting the disk <br> - Check the instance status and details in the Console | 29/04/2026 | 29/04/2026 | <https://000004.awsstudygroup.com/> |
| Thursday | - Learn about IAM Roles attached to EC2 (instance profiles) and how EC2 automatically obtains temporary credentials <br> - Practice creating a Role with access to a service (e.g. S3) and attaching it to EC2 <br> - Understand why Roles should be used instead of hard-coding access keys in source code (secure, automatic credential rotation) | 30/04/2026 | 30/04/2026 | <https://000048.awsstudygroup.com/> |
| Friday | - Get familiar with the AWS Cloud9 development environment: create an environment, use the integrated terminal and editor <br> - Learn how Cloud9 connects to EC2 and uses IAM credentials <br> - Consolidate and write the Week 2 worklog | 01/05/2026 | 01/05/2026 | <https://000049.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **Advanced IAM:** clearly distinguish User and Role, understand trust policies and the assume-role mechanism for granting temporary permissions securely.
* **EC2:** grasp the core components (instance type, AMI, EBS, key pair, Security Group) and how they work together to form a virtual server.
* **Connectivity & storage:** know how to connect via SSH with a key pair and attach an additional EBS volume to expand storage.
* **Instance Profile:** understand the mechanism of attaching an IAM Role to EC2 so applications can access AWS services without storing access keys in code.
* **Cloud9:** get familiar with a cloud IDE, how it integrates a terminal and editor and uses IAM credentials to interact with AWS.

### Challenges This Week:

* Need more time to get familiar with Cloud9 and test EC2 access permissions via IAM Roles.

### Week 2 Achievements:

* Gained a deeper understanding of how AWS manages access permissions using IAM Roles and Policies.
* Learned the core EC2 components and successfully created and connected to an EC2 instance.
* Practiced attaching an additional EBS volume and connecting to the instance via SSH.
* Understood why IAM Roles (instance profiles) should be used instead of hard-coding access keys.
* Took the first steps in getting familiar with the AWS Cloud9 development environment.
