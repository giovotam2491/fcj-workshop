---
title: "Blog 3"
date: 2026-06-29
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Automate medical record digitization with Amazon Bedrock Data Automation and AWS HealthLake

Healthcare organizations manage millions of paper medical records that stay disconnected from modern clinical systems. Clinicians make decisions without a patient's full history, organizations spend millions on manual data entry, and critical information stays trapped in formats that modern applications can't read. The technical challenge is clear: how do you transform unstructured, scanned documents into standardized, interoperable health data at scale — without building custom machine learning (ML) models or hand-coding a parser for every form type.

In this post, you learn how to build an automated, serverless pipeline that converts scanned PDF medical records into **FHIR R4**-compliant data using **Amazon Bedrock Data Automation** and **AWS HealthLake**. We walk through the architecture, explain how each AWS service connects to the next, show you what the pipeline looks like when it runs, and get you deployed in under 20 minutes. For advanced configuration, troubleshooting, and customization options, see the solution's GitHub repository.

---

## The challenge with paper medical records

Healthcare organizations face a compounding problem. Paper records don't only create storage challenges; they create care gaps. When a patient arrives at a new facility, clinicians often proceed with incomplete information because retrieving and interpreting historical records takes too long. Manual digitization is expensive, error-prone, and doesn't scale.

The solution requires more than scanning documents. It requires extracting structured, clinically meaningful data and storing it in a format that integrates with existing systems. This is where **Fast Healthcare Interoperability Resources (FHIR)** comes in. FHIR is the healthcare industry's standard for exchanging electronic health information.

---

## Solution overview

This solution uses a serverless, event-driven architecture to automate the entire journey from PDF upload to queryable FHIR data. No custom machine learning models or manual template configuration required.

**AWS services used:**

1. **Amazon Bedrock Data Automation (BDA):** Extracts over 50 structured clinical fields from scanned PDFs using advanced AI capabilities, including patient demographics, diagnoses with ICD-10 codes, medications, vital signs, and lab results.
2. **AWS Lambda:** Two serverless functions orchestrate the pipeline: a *BDA Trigger* function that fires when a PDF is uploaded, and a *FHIR Processor* function that converts the extracted JSON into FHIR R4 format.
3. **Amazon Simple Storage Service (Amazon S3):** Input and output buckets with event notifications drive the pipeline automatically, with no polling or scheduled jobs.
4. **AWS HealthLake:** A FHIR R4-compliant, HIPAA-eligible data store that validates, indexes, and exposes data through standard FHIR API endpoints.
5. **AWS CloudFormation:** Provisions the entire infrastructure as code in a single automated deployment (approximately 15–20 minutes).
6. **Amazon CloudWatch and AWS CloudTrail:** Provide end-to-end monitoring, logging, and an audit trail across every component of the pipeline.
7. **AWS Key Management Service (AWS KMS):** Encrypts AWS HealthLake data at rest with a customer managed key.

{{% notice warning %}}
**Important:** This solution is a demonstration sample, designed to be used with **synthetic data** only. It is **not production-ready** for real Protected Health Information (PHI) without additional HIPAA security controls. Review the **Security considerations** section before deploying in any environment with real patient data.
{{% /notice %}}

---

## Architecture

![Figure 1](/images/3-BlogsTranslated/3.3-Blog3/ARCHBLOG-1364-1.png)

> *Figure 1. The end-to-end architecture showing the event-driven pipeline, from PDF upload to FHIR-compliant data storage.*

The pipeline runs in three phases, each building on the last.

### Phase 1: Infrastructure deployment

AWS CloudFormation provisions every required resource in a single stack: the Amazon S3 input and output buckets, the two Lambda functions, IAM roles with least-privilege permissions, the AWS KMS key, the CloudWatch log groups, and an AWS HealthLake FHIR R4 data store. The entire environment — including every cross-service permission — is version-controlled and reproducible.

### Phase 2: Event-driven data processing

The pipeline is fully event-driven. No scheduler or orchestration service is required. Each step automatically triggers the next:

1. PDF upload → S3 Input Bucket
2. S3 Event → Triggers the BDA Lambda function
3. BDA Processing → Extracts over 50 clinical fields with confidence scores
4. JSON Storage → S3 Output Bucket
5. S3 Event → Triggers the FHIR Processor Lambda function
6. FHIR Conversion → Creates a FHIR R4 Bundle (JSON + NDJSON)
7. HealthLake Import → Automatically imports and validates the NDJSON
8. FHIR API Access → Query through the HealthLake endpoints

### Phase 3: Query and analysis

Once the data is in AWS HealthLake, it can be queried immediately through standard FHIR R4 API endpoints. Python scripts authenticate with AWS Signature Version 4 (SigV4) and support searching by patient, condition, medication, or lab result type.

---

## How the services connect

Understanding how the services link together is key to customizing or extending this solution.

### Amazon S3 as the pipeline backbone

Amazon S3 plays a dual role: an entry point for raw PDFs and a handoff layer between processing stages. Amazon S3 event notifications eliminate the need for polling. When a PDF lands in the input bucket, the BDA Lambda fires immediately. When BDA writes its JSON result to the output bucket, the FHIR Processor Lambda triggers automatically. This decoupled design lets each stage scale independently.

### Amazon Bedrock Data Automation as the intelligence layer

BDA acts as the intelligence layer. When the Lambda triggers an extraction job, BDA retrieves the PDF from Amazon S3 and applies a custom medical *blueprint* — a schema that defines the over 50 clinical fields to extract. The service understands the document structure without templates or training data. Each extracted field returns with a confidence score (0.0–1.0), which the FHIR Processor Lambda uses to apply validation thresholds before conversion.

### AWS Lambda as the transformation layer

The two Lambda functions are deliberately narrow in scope:

- ***BDA Trigger Lambda*** receives the Amazon S3 event, builds the BDA API call, and submits the processing job.
- ***FHIR Processor Lambda*** reads the BDA JSON result, maps each extracted field to the appropriate FHIR R4 resource type, assembles a FHIR Bundle, outputs NDJSON, and triggers an AWS HealthLake import job.

This separation of concerns lets each function be tested and replaced independently.

### AWS HealthLake as the FHIR data store

AWS HealthLake receives the NDJSON import, validates each resource against the FHIR R4 specification, creates relationships between resources (for example, linking a `Condition` resource to its `Patient`), indexes the data for efficient querying, and generates unique FHIR resource IDs. The result is a fully queryable FHIR data store, accessed through authenticated API calls.

### IAM roles as the security fabric

Each service communicates with the next using IAM roles with least-privilege permissions. There are no hardcoded credentials and no overly broad policies. The Lambda functions assume roles that grant only the specific actions they need (for example, `bedrock-data-automation:InvokeDataAutomationAsync` and `s3:GetObject` for the BDA Trigger Lambda).

---

## Hands-on walkthrough

This walkthrough takes you from prerequisites through deployment and verification.

### Prerequisites

Before deploying, confirm you have the following:

**Required software:**

- Python 3.10 or later.
- Poetry (Python dependency management tool).
- AWS Command Line Interface (AWS CLI) configured with appropriate credentials.

**Check your Poetry installation:**

```bash
poetry --version
```

**If you need to install Poetry:**

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

**Required AWS permissions:**

You need IAM permissions for the following services:

- Amazon Bedrock Data Automation.
- AWS CloudFormation (create, update, and delete stacks).
- Amazon S3 (create buckets, upload and download objects).
- AWS Lambda (create and update functions).
- AWS Identity and Access Management (IAM) (create roles and policies).
- AWS HealthLake (create data stores).

**Supported AWS Regions:**

This solution currently supports `us-east-1` (US East N. Virginia) and `us-west-2` (US West Oregon) only. These are the Regions where Amazon Bedrock Data Automation is available.

---

### Deploy the pipeline

Deployment takes approximately 15–20 minutes. Run the following four commands to go from zero to a fully deployed pipeline:

```bash
# 1. Clone the repository and install dependencies
git clone <repository-url>
cd Medical-Record-Digitization-and-FHIR-Integration-Pipeline
poetry install

# 2. Configure your environment
poetry run python src/utils/setup_env.py

# 3. Deploy the CloudFormation stack (approximately 15 minutes)
poetry run python src/automation/deploy.py

# 4. Verify the deployment
aws cloudformation describe-stacks \
  --stack-name bda-medical-records-stack \
  --query 'Stacks[0].StackStatus'
# Expected output: "CREATE_COMPLETE"
```

The deployment creates the following resources:

- An Amazon Bedrock Data Automation blueprint and project (a custom medical records schema with over 50 fields).
- Amazon S3 input and output buckets with automatic event notifications.
- Two AWS Lambda functions (BDA Trigger and FHIR Processor).
- An AWS HealthLake FHIR R4 data store.
- IAM roles and policies with least-privilege permissions.
- Amazon CloudWatch log groups for every Lambda execution.

> For manual environment configuration, advanced deployment options, and troubleshooting, see the GitHub repository.

---

### See the pipeline in action

After deployment, upload a sample medical record to trigger the full pipeline. You can use the sample file included in the GitHub repository.

```bash
# Get the input bucket name from the CloudFormation stack outputs
INPUT_BUCKET=$(aws cloudformation describe-stacks \
  --stack-name bda-medical-records-stack \
  --query 'Stacks[0].Outputs[?OutputKey==`InputBucketName`].OutputValue' \
  --output text)

# Upload a sample PDF (use the synthetic record included in the repository)
aws s3 cp samples/medical-record-sample.pdf s3://$INPUT_BUCKET/

# Track the BDA processing jobs
poetry run python src/utils/track_bda_jobs.py
```

Within 2–3 minutes, Amazon Bedrock Data Automation processes the PDF and the FHIR Processor Lambda imports the result into HealthLake. View the extracted data:

```bash
poetry run python src/utils/view_results.py
```

![Hình 2](/images/3-BlogsTranslated/3.3-Blog3/vidu-2.png)

> *Figure 2. Sample extraction results — patient information, conditions with ICD-10 codes, medications, and lab results with confidence scores.*

**Example output:**

```plaintext
Found 8 result files in output bucket
Processing: medical-record-sample_results.json

Patient Information:
---------------------
Name: Wilkins, Samantha
Patient ID: A1B2C3D4
Date of Birth: 10/28/1953

Conditions (5 found):
- Hypothyroidism (ICD-10: E03.9) - Confidence: 0.98
- Vitamin D Deficiency (ICD-10: E55.9) - Confidence: 0.95
- Hypertension (ICD-10: I10) - Confidence: 0.97
- Osteoarthritis (ICD-10: M19.90) - Confidence: 0.92
- Gastroesophageal Reflux Disease (ICD-10: K21.9) - Confidence: 0.96

Medications (4 found):
- Levothyroxine 100 mcg daily
- Vitamin D3 2000 IU daily
- Lisinopril 10 mg daily
- Omeprazole 20 mg daily
Lab Results (16 tests):
TSH: 2.3 mIU/L (Normal range: 0.4-4.0) ✓
Vitamin D: 28 ng/mL (Normal range: 30-100) ⚠️
Blood Pressure: 128/82 mmHg (Stage 1 Hypertension) ⚠️

[✅] FHIR conversion complete
[✅] Imported to HealthLake datastore: ds-abc123xyz456
```

---

### Query FHIR data from AWS HealthLake

Once the data is loaded, query it using the interactive FHIR query interface:

```bash
poetry run python src/utils/query_medical_data.py
```

**Supported FHIR query patterns:**

```plaintext
# Search by patient name
Patient?name=Wilkins

# Get the conditions for a specific patient
Condition?patient=Patient/47ef817a-9826-4498-b693-2af5eb2b5250

# Get lab results only
Observation?category=laboratory

# Get vital signs only
Observation?category=vital-signs

# Get all medications
MedicationRequest
```

**Python example — an authenticated FHIR API call:**

```python
import boto3, requests, os
from botocore.auth import SigV4Auth
from botocore.awsrequest import AWSRequest

session = boto3.Session()
credentials = session.get_credentials()
region = os.environ.get('AWS_REGION', 'us-west-2')
datastore_id = os.environ.get('DATASTORE_ID')

url = f'https://healthlake.{region}.amazonaws.com/datastore/{datastore_id}/r4/Patient?name=Wilkins'
request = AWSRequest(method='GET', url=url, headers={'Accept': 'application/fhir+json'})
SigV4Auth(credentials, 'healthlake', region).add_auth(request)

response = requests.get(url, headers=dict(request.headers))
print(response.json())
```

---

## Security considerations

This is a demonstration sample for synthetic data only. **Do not** use it with real Protected Health Information (PHI) without implementing the controls listed in the sections below.

**Security measures already included in this sample:**

- IAM roles with least-privilege permissions.
- Amazon S3 bucket access controls (private by default).
- AWS KMS encryption for AWS HealthLake data at rest.
- Cross-service authorization with IAM roles.
- Amazon CloudWatch logging for an audit trail.

**Additional measures required for production PHI workloads:**

AWS HealthLake is a HIPAA Eligible Service. Customers must review the **AWS Shared Responsibility Model** to understand their security and compliance obligations. Before processing real patient data, implement:

1. **AWS Business Associate Addendum (BAA):** Required under HIPAA before processing PHI on AWS.
2. **Amazon Virtual Private Cloud (Amazon VPC) isolation:** Place the Lambda functions and AWS HealthLake in private subnets with AWS PrivateLink.
3. **Comprehensive logging:** AWS CloudTrail, AWS Config, Amazon S3 access logs, and Amazon VPC flow logs.
4. **Encryption in transit:** TLS 1.2 or later. Use Amazon VPC endpoints to avoid exposure to the public internet.
5. **Access controls:** Multi-factor authentication (MFA), role-based access control (RBAC), temporary credentials, and periodic access reviews.
6. **Compliance monitoring:** AWS Security Hub with HIPAA compliance checks.
7. **Data lifecycle management:** Retention policies, secure deletion, and data loss prevention (DLP) measures.

For complete guidance, see the **AWS HIPAA Compliance** page.

---

## Cost

The following estimates apply to testing with approximately 100 medical records per month in the US West (Oregon) Region:

| Service                        | Usage                                              | Estimated monthly cost |
| ------------------------------ | -------------------------------------------------- | ---------------------- |
| Amazon Bedrock Data Automation | 100 pages (approx. $0.20–$0.30/page)               | $20–$30                |
| AWS HealthLake                 | 5 GB storage + 100 queries                          | $15–$20                |
| AWS Lambda                     | 200 invocations (512 MB, approx. 30 s average)      | $5–$10                 |
| Amazon S3                      | 1 GB storage + 200 requests                         | $1–$2                  |
| AWS KMS                        | 1 customer managed key                              | $1                     |
| **Total**                      |                                                    | **approx. $50–$100/mo** |

For a production workload processing 10,000 records per month, budget in the range of $2,000–$3,000/month. The main cost drivers are BDA (charged per page), HealthLake (charged per search request), and VPC endpoints (hourly PrivateLink charges in a production deployment).

**Cost optimization tips:**

- Delete the CloudFormation stack when you're not actively testing: `aws cloudformation delete-stack --stack-name bda-medical-records-stack`.
- Set up AWS Budgets alerts to catch unexpected costs early.
- Monitor Lambda duration in CloudWatch to optimize function execution time.

---

## Clean up resources

To avoid ongoing charges, delete the CloudFormation stack when you're done:

```bash
aws cloudformation delete-stack --stack-name bda-medical-records-stack
aws cloudformation wait stack-delete-complete --stack-name bda-medical-records-stack
```

To clean up manually created Amazon Bedrock Data Automation projects and S3 bucket contents, see the GitHub repository.

---

## Next steps

After deployment, you can extend this foundation to:

- Integrate with existing electronic health record (EHR) systems through FHIR APIs.
- Build analytics dashboards with Amazon QuickSight.
- Add natural-language search with Amazon Kendra.
- Add Amazon Simple Queue Service (Amazon SQS) as a buffer between the Amazon S3 event and the BDA Trigger Lambda to handle upload bursts and manage BDA concurrency limits at scale.
- Orchestrate with AWS Step Functions for error handling, retry logic, and routing low-confidence extractions to human review.
- Implement real-time, high-volume processing with Amazon Kinesis Data Streams for continuous ingestion from multiple sources.

---

## Conclusion

In this post, you saw how Amazon Bedrock Data Automation, AWS Lambda, Amazon S3, and AWS HealthLake work together to automate the conversion of scanned medical records into FHIR R4-compliant data. The event-driven architecture eliminates manual data entry, scales without custom machine learning models, and makes legacy records available to modern care systems.

**Key takeaways:**

- **Amazon Bedrock Data Automation** extracts over 50 structured clinical fields from PDFs without template configuration.
- **AWS Lambda** orchestrates the pipeline with two focused, event-driven functions.
- **Amazon S3 event notifications** decouple each stage, so each stage can scale independently.
- **AWS HealthLake** validates, indexes, and exposes FHIR R4 data through standard APIs.
- **Security controls** are the customer's responsibility under the AWS Shared Responsibility Model.

To explore the complete source code, advanced configuration options, and customization guidance, visit the solution's GitHub repository.

> *This solution is for educational purposes, using synthetic data. Review the security considerations and consult your compliance team before deploying in any environment with real patient data.*

---

## Additional resources

- [Amazon Bedrock Data Automation documentation](https://docs.aws.amazon.com/bedrock/)
- [AWS HealthLake documentation](https://docs.aws.amazon.com/healthlake/)
- [FHIR R4 specification](https://hl7.org/fhir/R4/)
- [AWS HIPAA Compliance](https://aws.amazon.com/compliance/hipaa-compliance/)
- [AWS Architecture Center: Healthcare solutions](https://aws.amazon.com/architecture/healthcare/)
- [Solution GitHub repository](https://github.com/aws-samples/sample-bedrock-data-automation-fhir-pipeline)
