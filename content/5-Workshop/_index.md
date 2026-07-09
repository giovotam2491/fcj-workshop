---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Internal Document Q&A Chatbot — RAG on AWS Serverless

#### Overview

In this hands-on workshop, you will deploy a complete **Retrieval-Augmented Generation (RAG) chatbot** running entirely on **AWS Serverless** services. The system lets internal users upload documents (PDF/TXT) and ask natural-language questions, receiving answers grounded in — and citing — the uploaded content.

The architecture combines three layers:
+ **Edge / Interface** — CloudFront + private S3 (React SPA) and Amazon Cognito for authentication.
+ **API & Processing (Serverless)** — API Gateway (HTTP API) with a JWT authorizer, AWS Lambda (Node.js/TypeScript), and DynamoDB for application data.
+ **AI — Amazon Bedrock** — a Knowledge Base backed by **S3 Vectors**, **Titan Text Embeddings V2**, and **Amazon Nova Lite** for retrieval and answer generation.

By the end of this workshop, you will have provisioned the full stack in your own AWS account, deployed the frontend, tested the chat flow end-to-end, and cleaned up every resource to avoid unnecessary cost.

#### Content

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Deploy Backend](5.3-Deploy%20Backend/)
4. [Deploy Frontend](5.4-Deploy-frontend/)
5. [Test the Chatbot](5.5-Test-chatbot/)
6. [Clean up](5.6-Cleanup/)
