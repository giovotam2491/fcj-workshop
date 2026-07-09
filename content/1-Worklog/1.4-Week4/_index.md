---
title: "Week 4 Worklog"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:

* Understand relational databases (Amazon RDS) and NoSQL databases (Amazon DynamoDB) on AWS.
* Practice creating sample databases, connecting and testing data read/write operations.
* Distinguish when to use a relational database and when to use NoSQL.
* Understand the role of a caching layer (ElastiCache) in improving system performance.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn Amazon RDS: the supported database engines (MySQL, PostgreSQL, MariaDB, SQL Server, Oracle) <br> - Understand the concepts of DB instance, instance class and storage <br> - Learn the automated backup mechanism, manual snapshots and data restore <br> - Understand Multi-AZ (high availability) and Read Replica (read scaling) | 11/05/2026 | 11/05/2026 | <https://000005.awsstudygroup.com/> |
| Tuesday | - **Practice:** create a sample RDS DB instance (choose engine, instance class, configure Security Group) <br> - Connect to the database with a client (e.g. MySQL Workbench/CLI) <br> - Create tables, insert data and test basic read/write queries <br> - Practice creating a snapshot and reviewing the restore process | 12/05/2026 | 12/05/2026 | <https://000005.awsstudygroup.com/> |
| Wednesday | - Learn DynamoDB: the NoSQL table model, items and attributes <br> - Understand partition keys and sort keys and how they determine data distribution and querying <br> - Distinguish the two capacity modes: on-demand and provisioned (RCU/WCU) <br> - Learn the concept of indexes (LSI, GSI) at a basic level | 13/05/2026 | 13/05/2026 | <https://000060.awsstudygroup.com/> |
| Thursday | - **Practice:** create a DynamoDB table (define partition key, sort key) <br> - Perform put/get/update/delete item operations in the Console <br> - Try querying data with query and scan and compare the difference | 14/05/2026 | 14/05/2026 | <https://000060.awsstudygroup.com/> |
| Friday | - Learn about ElastiCache at a conceptual level: the role of caching in reducing database load <br> - Distinguish the two engines Redis and Memcached <br> - Understand the cache-aside pattern and when to use a cache <br> - Consolidate and write the Week 4 worklog | 15/05/2026 | 15/05/2026 | <https://000061.awsstudygroup.com/> |

### Knowledge Gained This Week:

* **Amazon RDS:** understand the managed relational database model, the supported engines, the backup/snapshot mechanism, Multi-AZ and Read Replica.
* **Amazon DynamoDB:** grasp the NoSQL key-value model, the role of partition keys/sort keys and the two capacity modes on-demand/provisioned.
* **RDS vs DynamoDB comparison:** know that RDS suits structured data with complex relationships and SQL queries, while DynamoDB suits large-scale, key-based access with low latency and high scalability.
* **Query vs Scan:** understand the difference in performance and cost between a query (by key) and a scan (full-table scan) in DynamoDB.
* **ElastiCache:** understand the role of an in-memory caching layer (Redis/Memcached) in speeding up access and reducing database load.

### Challenges This Week:

* Had difficulty distinguishing between the storage and usage methods of RDS and DynamoDB.

### Week 4 Achievements:

* Distinguished between RDS (relational) and DynamoDB (NoSQL) in terms of storage model and usage.
* Created a basic RDS database and DynamoDB table and practiced reading/writing data.
* Understood when to choose a relational database and when to choose NoSQL.
* Grasped the role of ElastiCache in optimizing system performance.
