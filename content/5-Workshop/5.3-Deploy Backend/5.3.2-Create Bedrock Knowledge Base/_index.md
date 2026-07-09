---
title : "Create Bedrock Knowledge Base"
date : 2024-01-01
weight : 2
chapter : false
pre : " <b> 5.3.2 </b> "
---

This is the **only manual step** in the whole deployment. It must be created in the exact same region as everything else.

{{% notice warning %}}
The Knowledge Base **must** be created in **us-east-1 (N. Virginia)** — the same region as every other resource. Creating it in a different region causes a `Knowledge Base ... does not exist` error when chatting.
{{% /notice %}}

#### Step 1 — Open the Bedrock console and pick "Unstructured Vector Store KB"

Navigate to **Amazon Bedrock → Knowledge Bases (KB)**. AWS now defaults the orange **Create Managed KB** button to a fully-managed flow that hides the embeddings model and vector store choice — that is **not** what this project needs. Click the small **▾** dropdown arrow next to the button and, under **Self-managed KB**, choose **Unstructured Vector Store KB** (full control over indexing — matches this project's manual S3 Vectors + Titan Text Embeddings V2 setup).

![create kb](/images/5-Workshop/5.3-Deploy%20Backend/kb-create-start.png)

#### Step 2 — Provide Knowledge Base details

On the "Provide Knowledge Base details" page:
- **KB name**: e.g. `rag-kb`.
- **IAM permissions**: keep **Create and use a new service role**.
- **Choose data source type**: **Amazon S3** (already selected by default).

Leave Tags and Log deliveries empty, then click **Next**.

![kb details](/images/5-Workshop/5.3-Deploy%20Backend/kb-details.png)

#### Step 3 — Configure data source

- **Data source name**: any name.
- **S3 URI**: click **Browse S3** and select the bucket you created as `DOCS_BUCKET` (previous step).

{{% notice warning %}}
After picking the bucket, you **must manually append the `documents/` suffix** to the S3 URI, e.g. `s3://rag-docs-your-name/documents/`. This makes sure the Knowledge Base only ingests files under the same key prefix used by the upload Lambda.
{{% /notice %}}

- **Parsing strategy**: keep **Amazon Bedrock default parser**.
- **Chunking strategy**: keep **Default chunking**.

Click **Next**.

![data source](/images/5-Workshop/5.3-Deploy%20Backend/kb-data-source.png)

#### Step 4 — Configure data storage and processing

- **Embeddings model**: click **Select model → Amazon → Titan Text Embeddings V2 → Apply**.
- Click the **pencil (edit) icon** next to the model chip to expand **Additional configurations**:
  - **Embeddings type**: keep **Floating-point vector embeddings**.
  - **Vector dimensions**: change from the default `1024` to **`512`**.
- **Vector store creation method**: keep **Quick create a new vector store - Recommended**.
- **Vector store type**: choose **Amazon S3 Vectors - new**.

{{% notice tip %}}
Using 512 dimensions instead of the default 1024 roughly halves vector storage/query cost with negligible quality loss.
{{% /notice %}}

![vector config](/images/5-Workshop/5.3-Deploy%20Backend/kb-vector-config.png)

#### Step 5 — Review and create

Review the summary and click **Create Knowledge Base**. Wait for the status to turn green.

![kb created](/images/5-Workshop/5.3-Deploy%20Backend/kb-created.png)

#### Step 6 — Sync the data source

Click **Sync** once in the top-right corner to activate the data source (an empty bucket still syncs quickly).

![kb sync](/images/5-Workshop/5.3-Deploy%20Backend/kb-sync.png)

#### Step 7 — Copy the IDs

On the Knowledge Base overview page, copy the **Knowledge Base ID** and, from the data source tab, the **Data source ID**. You will need both in the next step.

![kb ids](/images/5-Workshop/5.3-Deploy%20Backend/kb-ids.png)
