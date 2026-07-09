---
title: "Week 7 Worklog"
date: 2026-06-01
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Week 7 Objectives:

* Build the core serverless backend for the project (Lambda + DynamoDB).
* Learn the serverless CRUD model and how to process data through Lambda functions.
* Understand how API Gateway connects the frontend with the backend via a REST API.
* Design the table structure for the system's database.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Monday | - Learn the serverless CRUD model (Create, Read, Update, Delete) with Lambda + DynamoDB <br> - Understand how each CRUD operation maps to a handler function and a DynamoDB query <br> - Understand the processing flow: request → Lambda → DynamoDB → response | 01/06/2026 | 01/06/2026 | <https://000133.awsstudygroup.com/> |
| Tuesday | - **Practice:** create a new Lambda function, configure the runtime and IAM execution role <br> - Write a basic handler to receive an event and return a response <br> - Practice testing the function with a test event in the Console and reading the logs in CloudWatch | 02/06/2026 | 02/06/2026 | <https://000133.awsstudygroup.com/2-create-lambda-functions/> |
| Wednesday | - **Practice:** connect Lambda with DynamoDB using the AWS SDK <br> - Write code to put an item (save) and get an item (read) data <br> - Grant Lambda access to DynamoDB via an IAM policy <br> - Verify the data is written/read correctly in the DynamoDB table | 03/06/2026 | 03/06/2026 | <https://000133.awsstudygroup.com/> |
| Thursday | - Learn about Amazon API Gateway: the concepts of REST API, resource, method (GET/POST/PUT/DELETE) <br> - Understand how to integrate API Gateway with Lambda (Lambda proxy integration) <br> - Learn about stages, deployments and how API Gateway connects the frontend with the backend | 04/06/2026 | 04/06/2026 | <https://000135.awsstudygroup.com/> |
| Friday | - Design the database tables: identify entities, attributes, partition key/sort key <br> - Review the relationships between tables and how data is queried <br> - Prepare the initial content for the step-by-step section in the report <br> - Consolidate and write the Week 7 worklog | 05/06/2026 | 05/06/2026 | |

### Knowledge Gained This Week:

* **Serverless CRUD model:** understand how to build Create/Read/Update/Delete operations with Lambda and DynamoDB without managing servers.
* **Hands-on Lambda:** know how to create a function, write a handler, configure an execution role and test/debug with CloudWatch Logs.
* **Lambda–DynamoDB connection:** grasp how to use the AWS SDK to read/write data and grant least-privilege access via an IAM policy.
* **API Gateway:** understand how to expose a Lambda backend as a REST API for the frontend to call, through resources, methods and Lambda proxy integration.
* **Database design:** know how to model data, choose partition keys/sort keys and organize tables to fit the queries.

### Week 7 Achievements:

* The serverless backend can process basic data (CRUD).
* Successfully tested the flow: request → process (Lambda) → save/read data (DynamoDB) → response.
* Grasped how API Gateway connects the frontend with the backend.
* Completed the table design for the database.
* Prepared the initial content for the step-by-step section in the report.
