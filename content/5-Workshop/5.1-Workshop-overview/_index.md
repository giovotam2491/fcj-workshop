---
title : "Introduction"
date : 2024-01-01 
weight : 1 
chapter : false
pre : " <b> 5.1. </b> "
---

#### RAG and AWS Serverless Overview

+ **RAG (Retrieval-Augmented Generation)** is a technique used to optimize the output of Large Language Models (LLMs) by querying authoritative external knowledge bases (such as internal documents) before generating responses. This helps mitigate hallucination and safeguards sensitive organizational assets.
+ **AWS Serverless** provides a flexible cloud computing environment with no servers to provision or manage. It scales dynamically based on load with pay-as-you-go pricing, maximizing operational cost-efficiency.

#### Workshop overview
In this workshop, you will build an **Internal Document Q&A Chatbot** running entirely on a Serverless AWS architecture.

The user client is developed in React (Vite) styled with a modern 3D Glassmorphism interface secured by Amazon Cognito. At the core, Amazon Bedrock Knowledge Base coordinates with S3 Vectors to automate the RAG pipeline (parsing, chunking, and embedding with Titan Text V2, and generating answers using Nova Lite).

The lab session is divided into the following sections:
1. **Prerequisites:** Setting up an AWS account, installing Node.js, and structuring user credentials under IAM.
2. **Database & Auth Setup:** Constructing Amazon DynamoDB tables for application metadata and configuring a Cognito User Pool for user sessions and permissions.
3. **AI Core Setup (Bedrock KB):** Setting up the Bedrock Knowledge Base, connecting S3 source buckets, and deploying a lightweight vector store on S3 Vectors.
4. **Backend API & Serverless:** Deploying AWS Lambda functions (Node.js/TypeScript) behind Amazon API Gateway to handle RAG orchestration and Role-Based Access Control (RBAC).
5. **Frontend Client:** Connecting the React front-end to our back-end API routes to verify private conversation history, upload documents, and stream Q&A citations.
6. **Monitoring & Cost Control:** Integrating system telemetry in CloudWatch and configuring AWS Budgets for real-time alert thresholds.

![overview](/images/2-Proposal/architecture.png)