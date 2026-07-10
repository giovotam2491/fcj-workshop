---
title: "Week 4 Worklog"
date: 2026-05-11
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Week 4 Objectives:
* Create and configure Amazon OpenSearch Serverless (AOSS) to serve as the Vector Database for the RAG chatbot.
* Establish Amazon Bedrock Knowledge Base and link it to the Titan Embeddings V2 model.
* Connect the secure S3 Bucket as a Data Source and trigger a data synchronization (Sync) job.
* Test document retrieval and question-answering capabilities using the Amazon Nova Lite LLM directly in the AWS Console.

### Tasks Implemented This Week:
| Day | Task | Start Date | End Date | References |
| --- | --- | --- | --- | --- |
| Mon | - Create an OpenSearch Serverless collection using the `Vector search` type. <br> - Configure companion policies: Security Policies (Encryption & Network) and Data Access Policies. | 11/05/2026 | 11/05/2026 | [Amazon OpenSearch Serverless Guide](https://docs.aws.amazon.com/opensearch-service/) |
| Tue | - Set up Amazon Bedrock Knowledge Base using the AWS Console. <br> - Select the embedding model (Titan Text Embeddings V2). <br> - Bind the Knowledge Base to the OpenSearch Serverless collection vector index. | 12/05/2026 | 12/05/2026 | [Amazon Bedrock Knowledge Base Docs](https://docs.aws.amazon.com/bedrock/) |
| Wed | - Bind the S3 document storage bucket as the Data Source. <br> - Apply the default chunking strategy (300 tokens, 20% overlap). | 13/05/2026 | 13/05/2026 | AWS Console Guide |
| Thu | - Upload 5 mock internal PDF files to the S3 bucket. <br> - Trigger the Sync (Ingestion Job) in the AWS Console to process, vectorize, and index the document data. | 14/05/2026 | 14/05/2026 | AWS Console Ingestion |
| Fri | - Utilize the "Test Knowledge Base" panel in the AWS Console. <br> - Ask questions referencing the sample documents using the Amazon Nova Lite model to check RAG output. <br> - Write the Week 4 worklog. | 15/05/2026 | 15/05/2026 | Intern Worklog |

### Knowledge Gained This Week:
* **Amazon OpenSearch Serverless (AOSS):** Grasped how to manage a serverless vector database, including configuring Network Policies (public/private endpoint routing) and Data Access Policies (permission boundaries for indexes).
* **Ingestion Job Pipeline:** Understood the automation flow: downloading objects from S3, splitting text (chunking), querying the embedding API, and loading vector indexes into AOSS.
* **Amazon Nova Lite LLM:** Observed how the model evaluates retrieved contexts from internal documents to formulate concise, grounded answers.

### Challenges & Solutions:
* **Challenge:** The Ingestion Job failed with an `AccessDenied` error, stating that Bedrock does not have the required permissions to access the S3 Bucket or OpenSearch Serverless collection.
* **Solution:** Identified that the IAM service role automatically created for Bedrock KB lacked necessary permissions. Fixed this by:
  1. Updating the Bedrock service role's IAM policy to permit read permissions (`s3:GetObject`, `s3:ListBucket`) on the specific document S3 bucket.
  2. Modifying the OpenSearch Serverless Data Access Policy to add the Bedrock service role as a Principal with permissions to write/read documents (`aoss:WriteDocument`, `aoss:ReadDocument`, `aoss:CreateIndex`). Re-ran the Ingestion Job successfully.

### Week 4 Achievements:
* Provisioned a fully functioning serverless vector index using Amazon OpenSearch Serverless.
* Established an end-to-end RAG data ingestion pipeline.
* Successfully ingested and vectorized 5 sample internal PDF documents.
* Verified successful RAG retrieval in the AWS Console, receiving accurate, grounded responses from the chatbot.
