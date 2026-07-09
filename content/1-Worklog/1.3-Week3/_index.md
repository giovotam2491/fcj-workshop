---
title: "Week 3 Worklog"
date: 2026-05-04
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Week 3 Objectives:

* Understand the basic network architecture on AWS (VPC, subnet, route table, internet gateway).
* Learn how to control network access with Security Groups and configure networking for EC2.
* Grasp the core S3 concepts and successfully host a static website on Amazon S3.
* Understand the role of DNS and Route 53 in routing access for a cloud system.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the VPC (Virtual Private Cloud) architecture: CIDR ranges, how to split public/private subnets <br> - Understand route tables and how traffic is routed between subnets <br> - Understand the role of the Internet Gateway in allowing a public subnet to reach the Internet | 04/05/2026 | 04/05/2026 | <https://www.youtube.com/watch?v=P8g7Z4NYk3Q&t=3s> |
| Tuesday | - Learn Security Groups: the concept of a stateful firewall, inbound/outbound rules <br> - Distinguish Security Groups from Network ACLs <br> - **Practice:** configure basic networking for EC2 (attach a subnet, open SSH/HTTP ports via a Security Group) and test connectivity | 05/05/2026 | 05/05/2026 | <https://youtu.be/TtlKFgfN3PU?si=xMyuJQQ7oQDA6043> <https://000003.awsstudygroup.com/1-introduce/> |
| Wednesday | - Learn the basic S3 concepts: bucket, object, key, region <br> - Understand the permission mechanisms: bucket policy, ACL and Block Public Access <br> - Understand the object storage model and the S3 storage classes | 06/05/2026 | 06/05/2026 | <https://000057.awsstudygroup.com/> |
| Thursday | - **Practice:** create an S3 bucket and upload the website's HTML/CSS files <br> - Enable Static Website Hosting and configure the index/error documents <br> - Adjust the bucket policy to allow public access and verify the website via the endpoint | 07/05/2026 | 07/05/2026 | <https://000057.awsstudygroup.com/> |
| Friday | - Learn Route 53: the concepts of DNS, hosted zone, record types (A, CNAME, Alias) <br> - Learn the basic routing policies <br> - Understand how to point a domain name to an S3 website <br> - Consolidate and write the Week 3 worklog | 08/05/2026 | 08/05/2026 | <https://www.youtube.com/watch?v=6BoTfTtNsGU> <https://000010.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **VPC:** understand how to design a virtual private network on AWS with CIDR ranges, public/private subnets, route tables and an Internet Gateway.
* **Network security:** distinguish Security Groups (stateful) from Network ACLs (stateless) and know how to open only the necessary ports following the least-privilege principle.
* **S3:** grasp the object storage model (bucket/object/key), the permission mechanisms (bucket policy, ACL, Block Public Access) and the storage classes.
* **Static Website Hosting:** know how to host a complete static website on S3, from uploading files to configuring public access.
* **DNS & Route 53:** understand the role of DNS, the record types and how to point a domain name to AWS resources.

### Week 3 Achievements:

* Understood the basic network structure on AWS (VPC, subnet, route table, internet gateway).
* Configured basic networking and Security Groups for EC2.
* Successfully hosted a static website using Amazon S3.
* Grasped the role of DNS and Route 53 in a cloud system.
