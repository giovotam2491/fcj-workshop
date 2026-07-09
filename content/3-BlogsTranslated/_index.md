---
title: "Translated Blogs"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


###  [Blog 1 - Log analysis with facets, correlation, enrichment, and automation in Amazon CloudWatch Log Analytics](3.1-Blog1/)
This blog introduces new features in Amazon CloudWatch Log Analytics that enable more efficient log analysis at scale. You will learn about Facets — which allow for visual log data exploration without writing queries; Lookup tables — for enriching query results with external metadata; Parameterized queries — for saving reusable query templates with pre-filled variables; JOIN and sub-queries — for correlating data across multiple log groups within a single query; and Scheduled queries — for automating queries on a schedule and sending the results to Amazon S3 or EventBridge. The article walks through each feature with real-world examples, highlighting the specific problems each one solves, how to set them up, and how to save them for team sharing — highly useful for operations engineers, DevOps, and security teams during incident investigations or security audits.

###  [Blog 2 - Secure multi-tenant RAG with Amazon Bedrock and Verified Permissions](3.2-Blog2/)
This blog guides you through building a secure multi-tenant RAG (Retrieval-Augmented Generation) application for large enterprises, where a common challenge is controlling which department can access which documents without replicating infrastructure for each team. The article presents a two-layer authorization model based on a defense-in-depth strategy — each layer operates independently, so if one is misconfigured, the other still ensures access control. The solution combines metadata filtering in Amazon Bedrock Knowledge Bases with Cedar policies managed by Amazon Verified Permissions, enabling dynamic authorization decisions at runtime — thereby allowing a single RAG application to serve multiple departments while isolating each department's documents, and allowing access policies to be updated without redeploying code.

###  [Blog 3 - Automate medical record digitization with Amazon Bedrock Data Automation and AWS HealthLake](3.3-Blog3/)
This blog provides a guide on building a serverless pipeline to automatically convert scanned PDF medical records into standard FHIR R4 data using Amazon Bedrock Data Automation and AWS HealthLake — solving the costly, error-prone, and hard-to-scale challenge of digitizing paper records in the healthcare industry. You will explore an architecture that includes: Amazon Bedrock Data Automation to extract over 50 structured clinical data fields from scanned PDFs (patient information, diagnoses with ICD-10 codes, medications, vital signs, lab results); AWS Lambda to orchestrate the pipeline; Amazon S3 to trigger automated processing when a new file is uploaded; and AWS HealthLake — a HIPAA-eligible, FHIR R4-compliant data store — to validate, index, and serve data via standard FHIR APIs. The article also covers security best practices using least-privilege IAM roles, avoiding hardcoded credentials, and deploying the entire infrastructure automatically using CloudFormation in just 15–20 minutes.