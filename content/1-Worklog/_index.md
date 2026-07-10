---
title: "Worklog"
date: 2026-04-20
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

This page contains the detailed worklog of my **12-week internship** as an AWS Cloud / RAG Solution Research Intern. 

The practical research and implementation project is titled: **"Internal Company Document Retrieval System (RAG Chatbot)"** built entirely on an **AWS Serverless** architecture.

### 12-Week Project Roadmap Overview:

* **[Week 1](1.1-week1/):** Survey & Architecture Design. Establish initial secure AWS IAM permissions and configure AWS Budgets to control intern account costs. Initialize the React + Vite + TypeScript frontend project and integrate the GSAP animation library.
* **[Week 2](1.2-week2/):** Design advanced IAM access permission matrices. Construct a rough static chat layout and research integrating GSAP animations for smooth user interactions.
* **[Week 3](1.3-week3/):** Start writing Infrastructure as Code (IaC) using AWS CDK with TypeScript to create a secure S3 Bucket for document storage. Research the RAG mechanism of Amazon Bedrock Knowledge Base & the Titan Embeddings V2 model.
* **[Week 4](1.4-week4/):** Deploy a complete Amazon Bedrock Knowledge Base, connect the S3 Data Source and Vector Store (Amazon OpenSearch Serverless). Run and test the data ingestion sync pipeline.
* **[Week 5](1.5-week5/):** Design the DynamoDB database schema for chat history. Initialize Amazon Cognito User Pool for user authentication. Write the first set of Lambda APIs (session management).
* **[Week 6](1.6-week6/):** Design the API Gateway REST API. Integrate Cognito User Pool Authorizer to secure endpoints, and deploy Lambda functions handling CRUD for sessions and chat history.
* **[Week 7](1.7-week7/):** Connect Backend to the RAG Pipeline. Write the core chat Lambda calling the `RetrieveAndGenerate` API from Amazon Bedrock Agent Runtime using the Amazon Nova Lite LLM.
* **[Week 8](1.8-week8/):** Finetune RAG responses using a Custom Prompt Template to mitigate hallucination. Manage short-term conversation state (session context).
* **[Week 9](1.9-week9/):** Integrate Cognito SDK into the React Frontend to handle registration, login, and OTP verification. Configure an Axios Interceptor to attach JWT tokens to API Headers.
* **[Week 10](1.10-week10/):** Complete UI/UX Chatbot development (Markdown rendering, citation sources) combined with smooth animations using GSAP. Deploy the Frontend on S3 Static Web Hosting + CloudFront CDN + secured with AWS WAF.
* **[Week 11](1.11-week11/):** Set up monitoring dashboards and queries using CloudWatch Logs & Metrics. Perform RAG quality evaluations (accuracy, groundedness) and review IAM policies using the Principle of Least Privilege.
* **[Week 12](1.12-week12/):** Analyze total spending through AWS Budgets. Optimize performance (reduce Lambda cold starts to < 600ms, enable CloudFront caching). Package deliverables and compile hand-over documentation.
