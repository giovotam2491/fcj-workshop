---
title: "Blog 2"
date: 2026-06-29
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---


# Secure multi-tenant RAG with Amazon Bedrock and Verified Permissions

Large organizations building internal generative AI applications keep running into the same challenge: controlling which teams or departments can access which documents, without duplicating infrastructure for each group. Within a single tenant, employees of a given department should only reach documents assigned to that department. Executives, however — with a broader span of control — need access to documents that span multiple departments. **Retrieval Augmented Generation (RAG)** is one of several complementary techniques (alongside fine-tuning and continued pre-training) for tailoring a generative AI application's answers with your own data.

In an enterprise context, where data changes fast and there are many users, RAG offers a balance between cost and performance. This post shows you how to use a single shared **Knowledge Base (KB)** to reduce cost and complexity compared with standing up separate instances. You can update access rules in minutes without redeploying code, while keeping a detailed audit trail for every authorization decision. You run one RAG application that serves many departments, with document access evaluated at retrieval time.

![Figure 1](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-1.png)
> *Figure 1. Role-based access to the organization's shared data and resources.*

An earlier post, *Multi-tenancy in RAG applications in a single Amazon Bedrock knowledge base with metadata filtering*, showed how to use Amazon Simple Storage Service (Amazon S3) folder structures and metadata filtering to separate data between tenants within a single knowledge base. That pattern works well for broad, tenant-level boundaries, where the filter value is known at design time and embedded in application code. Within a single tenant, though, different departments or roles often need different document visibility, and executives may need access that cuts across multiple boundaries. This post extends that foundation by **externalizing** the filter selection logic into **Cedar** policies managed by **Amazon Verified Permissions**, enabling dynamic, runtime-evaluated authorization decisions.

This pattern lets a single RAG application serve many departments while keeping each department's documents isolated, without standing up a separate knowledge base per team. It builds on metadata filtering in **Amazon Bedrock Knowledge Bases** — a fully managed RAG capability that handles ingestion, retrieval, and prompt augmentation through a single API. Metadata filtering is a solid foundation, but it leaves a gap: the filter selection logic has no external governance, and changing the rules means redeploying code.

When authorization logic lives in code, rules can drift out of consistency over time and require a full deployment cycle to change. **Amazon Verified Permissions** addresses this by providing fine-grained authorization and scalable permissions management for custom applications. Cedar policies externalized in Verified Permissions are auditable, version-controlled, and updatable at runtime.

This post walks you through a two-layer, **defense-in-depth** authorization pattern for fine-grained, intra-tenant access control in a RAG application. Defense in depth is a security strategy that uses multiple independent layers of protection. Each layer operates independently. If one layer is misconfigured, the other still enforces access control. The pattern runs on **Amazon Bedrock** — a fully managed service that offers a choice of high-performing foundation models (FMs) from Amazon and leading AI companies through a single API, plus a broad set of capabilities for building generative AI applications with security, privacy, and responsible AI.

In this post, you learn how to:

1. Enforce fine-grained, document-level access control at retrieval time using a single Amazon Bedrock Knowledge Bases instance.
2. Evaluate Cedar policies at runtime to dynamically build the metadata filter passed to the *RetrieveAndGenerate* API.
3. Update authorization rules without changing application code or triggering a deployment.
4. Design a **deny-by-default** authorization system that's designed to block access when the authorization service is unavailable.

---

## Isolation model and scope

This pattern provides **filter-level (logical) isolation**, not **IAM-enforced (infrastructure) isolation**. The metadata filter controls which documents are returned at retrieval time, but the underlying knowledge base is still a shared resource. If the middleware logic that builds the filter fails open, another group's documents could be exposed.

This pattern is designed for fine-grained access control **within a single tenant** — for example, controlling which departments, teams, or roles inside one organization can access which documents. It is **not** a substitute for hard tenant isolation in a multi-tenant SaaS product. For cross-tenant isolation, where you need a compliance boundary between separate customers or organizations, provision a separate knowledge base per tenant with IAM-enforced resource boundaries. Inside each tenant's knowledge base, you can then layer this filter-based pattern for finer-grained control.

**Use this pattern when:**

- You need to control document access between departments, teams, or roles within the same organization.
- Access rules change frequently and you want to update them without redeploying code.
- You want to use a single knowledge base instance to reduce cost and operational overhead for intra-tenant document separation.

**Do not use this pattern when:**

- You need hard isolation between separate customers or organizations (use a knowledge base per tenant with IAM boundaries).
- Your compliance or audit requirements mandate infrastructure-level separation between datasets.
- A filter mechanism failure would constitute a regulatory violation (filter-level isolation is a logical boundary, not a physical one).

{{% notice info %}}
**Residual risk — ingestion race condition:** There's a brief window between uploading a document and creating its sidecar file. The ingestion safeguard (Step 1) mitigates this by excluding documents without a sidecar. If you tune the ingestion schedule to run continuously or with a short cadence, verify that the batching window in Amazon Simple Queue Service (Amazon SQS) — 30 seconds by default — gives the tagging Lambda enough time to finish before the next ingestion cycle.
{{% /notice %}}

---

## Prerequisites

Before deploying this pattern, you need:

1. Familiarity with Python, AWS Lambda, and infrastructure-as-code concepts.
2. An AWS account with AWS Identity and Access Management (AWS IAM) permissions to create AWS Lambda functions, an Amazon API Gateway REST API, an Amazon Cognito user pool, a Verified Permissions policy store, and Amazon Bedrock Knowledge Bases.
3. Amazon Cognito configured with department-based user groups (such as `dept-a`, `dept-b`, `dept-c`), with `cognito:groups` included in the JSON Web Token (JWT) issued to the client.
4. Amazon Bedrock model access for the FMs you intend to use (such as Anthropic Claude 3 Haiku and Amazon Nova Lite 2).
5. A Verified Permissions policy store created and a Cedar schema defined (principals, resources, actions) before deployment.
6. AWS Cloud Development Kit (AWS CDK) or AWS CloudFormation to deploy the infrastructure described in this post.
7. Sample documents prepared with department prefixes (such as `docs/dept-a/report.pdf`) to upload to Amazon S3.

{{% notice warning %}}
**Important:** Deploying this pattern creates billable AWS resources, including Amazon Bedrock Knowledge Bases, AWS Lambda, Amazon API Gateway, Amazon S3, Amazon Cognito, Amazon Verified Permissions, Amazon EventBridge, Amazon SQS, Amazon DynamoDB, AWS WAF, and Amazon CloudFront. Costs vary with usage and AWS Region. Review AWS Pricing for each service before deploying, and see the **Clean up resources** section at the end to remove resources after testing.
{{% /notice %}}

---

## Solution overview

Serving many departments from one application doesn't mean giving every department access to every document. This solution keeps each department's documents isolated inside a single Amazon Bedrock Knowledge Bases instance, so you avoid the cost and operational burden of standing up a knowledge base per team within that tenant. Metadata tags separate the documents logically, and Verified Permissions acts as an externalized policy enforcement point, deciding which documents a user's group or role is allowed to see on each request.

A key goal is to serve many departments within the same tenant from a single Knowledge Base instance. Provisioning a separate instance per department would multiply the infrastructure: separate data sources, separate ingestion pipelines, and separate management overhead. A single knowledge base with metadata-filtered access avoids this duplication, while providing logical document isolation within the tenant boundary. Documents are logically separated by metadata tags, and Verified Permissions provides a reliable externalized policy enforcement point to determine which tags a user — in a specific group or role — is permitted to access.

The ingestion pipeline tags documents with department metadata. Verified Permissions evaluates Cedar policies at query time to determine which department tags you're allowed to see. A middleware service (implemented as AWS Lambda) turns that policy decision into a metadata filter and uses it on Amazon Bedrock's *RetrieveAndGenerate* API. This API combines the retrieval step and the generation step, returning a grounded answer based on the filtered document set. The FM only processes documents that passed the filter.

**Two independent layers enforce authorization:**

- **Layer 1 (API access):** A Lambda Authorizer on Amazon API Gateway calls Verified Permissions to decide whether you're allowed to call the API.
- **Layer 2 (document access):** A middleware Lambda, used to orchestrate the call to the Knowledge Base, also calls Verified Permissions to determine which Knowledge Base resources your department is allowed to query, then builds the corresponding metadata filter.

Neither layer depends on the other for correctness. If Layer 1 is bypassed, Layer 2 is still designed to enforce document-level isolation at the KB's metadata filter.

![Figure 2](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-2.png)
> *Figure 2. Ingestion pipeline: a document upload to Amazon S3 triggers Amazon EventBridge, which routes through Amazon SQS to an AWS Lambda function that writes metadata. A scheduled Lambda then triggers an Amazon Bedrock Knowledge Bases ingestion job.*

![Figure 3](/images/3-BlogsTranslated/3.2-Blog2/ARCHBLOG-1492-3.png)
> *Figure 3. Query flow: a user request passes through Amazon CloudFront and AWS WAF to Amazon API Gateway, where the Lambda Authorizer evaluates Layer 1 (API-level) authorization with Verified Permissions. If allowed, the middleware Lambda evaluates Layer 2 (document-level) authorization, builds the metadata filter, and calls RetrieveAndGenerate.*

### Authorization decision flow

| **Step** | **Component**                  | **Decision**                                | **On denial**                    |
| -------- | ------------------------------ | ------------------------------------------- | -------------------------------- |
| 1        | AWS WAF                        | Rate limiting and rule inspection            | Request blocked                  |
| 2        | Lambda Authorizer (Layer 1)    | Are you allowed to call the API?             | Returns 403                      |
| 3        | Middleware Lambda (Layer 2)    | Which departments can you access?            | Empty result set                 |
| 4        | Amazon Bedrock Knowledge Bases | Applies the metadata filter at retrieval     | Excludes unauthorized documents  |
| 5        | Guardrails for Amazon Bedrock  | Is the answer grounded in the context?       | Blocks or edits the answer       |

---

## Key AWS services

| **Layer**                      | **Service**                    | **Role**                                                                                             |
| ------------------------------ | ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| Identity layer                 | Amazon Cognito                 | Issues JWTs with department group claims (`cognito:groups`) from a user pool                          |
| API security layer             | AWS WAF                        | Applies rate limiting, IP filtering, and managed-rule evaluation at the edge                          |
| Layer 1 authorization          | Amazon Verified Permissions    | Evaluates Cedar policies for API-level access decisions in the Lambda Authorizer                      |
| Layer 2 authorization          | Amazon Verified Permissions    | Evaluates Cedar policies for document-level access and drives metadata-filter construction            |
| RAG retrieval and FM invocation| Amazon Bedrock Knowledge Bases | Fully managed RAG capability that handles retrieval with metadata filtering and FM generation in one call |
| Ingestion layer                | Amazon EventBridge, Amazon SQS | Event-driven pipeline for sidecar tagging; Amazon SQS buffers upload bursts and routes failures to a DLQ |

---

## Technical implementation

The walkthrough covers the ingestion pipeline first, to tag and index documents. You then move to the Query Flow, which contains the core authorization pattern. Each section below corresponds to a distinct component in the architecture.

### Step 1: Set up the event-driven ingestion pipeline with metadata tagging

For the metadata filter to work at query time, documents need to be tagged with a department before they're indexed. The ingestion pipeline handles this in two phases.

**Phase 1 (event-driven):** When a document is uploaded to Amazon S3 under a department prefix (such as `docs/dept-a/report.pdf`), Amazon EventBridge emits an *ObjectCreated* event. The event routes through Amazon SQS to an AWS Lambda function, which writes a *.metadata.json* sidecar file next to the document. The Amazon SQS queue buffers large upload bursts and routes failed tagging attempts to a dead-letter queue for retry.

**Phase 2 (scheduled):** An Amazon EventBridge schedule triggers an ingestion Lambda every 5 minutes; this Lambda calls the *StartIngestionJob* API on the Amazon Bedrock Knowledge Bases data source. Amazon Bedrock reads the documents and their sidecars from Amazon S3, chunks them, generates embeddings, and indexes the vectors with the department attribute.

```python
# Validate that the sidecar exists before ingestion
objects = s3.list_objects_v2(Bucket=BUCKET, Prefix=f"docs/{dept}/")
docs = [o["Key"] for o in objects.get("Contents", []) if not o["Key"].endswith(".metadata.json")]
for doc_key in docs:
    sidecar_key = f"{doc_key}.metadata.json"
    try:
        s3.head_object(Bucket=BUCKET, Key=sidecar_key)
    except s3.exceptions.ClientError:
        logger.warning(f"Skipping {doc_key}: no metadata sidecar found")
        docs.remove(doc_key)
```

**Detecting metadata sidecar tampering:** Enable S3 Versioning on the document bucket and configure AWS CloudTrail S3 data events to log *PutObject* and *DeleteObject* calls. Create an Amazon CloudWatch Alarms metric filter to alert when a *PutObject* to a *.metadata.json* key originates from a principal other than the tagging Lambda's role. For workloads with strict compliance requirements, consider using S3 Object Lock in compliance mode so that a sidecar becomes immutable after creation — note that reclassifying a document then requires a deliberate process to create a new version.

**Enforcing the prefix at upload:** The metadata tagging Lambda infers the department label from the S3 key prefix (for example, `docs/dept-a/` → `department: dept-a`). To help prevent a user or process from uploading documents under another department's prefix, restrict upload permissions with a condition in the IAM policy:

```json
{
    "Effect": "Allow",
    "Action": "s3:PutObject",
    "Resource": "arn:aws:s3:::your-doc-bucket/docs/dept-a/*",
    "Condition": {
        "StringEquals": {
            "aws:PrincipalTag/department": "dept-a"
        }
    }
}
```

Every uploading principal (whether a user role, a CI/CD pipeline, or an application) should carry a department tag that matches its allowed prefix. This helps prevent the tagging Lambda from being tricked into mislabeling a document when someone uploads to the wrong path.

**Ingestion safeguard:** Before triggering an ingestion job, the scheduling Lambda lists the objects under each department prefix and checks that every document has a corresponding *.metadata.json* sidecar. Documents without a sidecar are excluded from the ingestion scope and logged to CloudWatch as "untagged." This helps prevent an untagged document from being indexed without a department attribute, which could otherwise slip past the metadata filter at query time. If your workload needs stronger guarantees, move untagged documents to a quarantine prefix and alert through Amazon Simple Notification Service (Amazon SNS).

**Restricting writes to S3:** Limit the *s3:PutObject* permission on the document bucket to only the IAM execution role of the metadata tagging Lambda. Every other principal — including application roles, CI/CD pipelines, and operators — should have at most *s3:GetObject* and *s3:ListBucket*. This helps prevent accidental or deliberate edits to the *.metadata.json* sidecar files, which could retag documents to a different department and expose them to unauthorized users on the next ingestion cycle. Use a bucket policy with an explicit deny for *s3:PutObject*, exempting only the tagging Lambda's role ARN:

```json
{
    "Effect": "Deny",
    "Principal": "*",
    "Action": ["s3:PutObject", "s3:DeleteObject"],
    "Resource": "arn:aws:s3:::your-doc-bucket/*",
    "Condition": {
        "StringNotEquals": {
            "aws:PrincipalArn": "arn:aws:iam::123456789012:role/MetadataTaggingLambdaRole"
        }
    }
}
```

The 30-second batching window on the Amazon SQS event source means large upload bursts are processed together rather than one document per Lambda invocation. Splitting the work into two phases helps ensure the department metadata sidecar is written before the ingestion job runs, which reduces the risk of a race condition where a document could be indexed without a department tag.

```python
# metadata_lambda/handler.py
import boto3, json

def handler(event, context):
    for record in event["Records"]:
        body = json.loads(record["body"])
        s3_key = body["detail"]["object"]["key"]
        # Extract the department from the prefix: docs/dept-a/report.pdf -> dept-a
        dept = s3_key.split("/")[1]
        meta_key = s3_key + ".metadata.json"
        s3 = boto3.client("s3")
        s3.put_object(
            Bucket=BUCKET,
            Key=meta_key,
            Body=json.dumps({"metadataAttributes": {"department": dept}})
        )
```

**Deployment note:** Deploy this as the handler for the metadata tagging AWS Lambda function (Python 3.12 runtime). Set the `BUCKET` environment variable. The function's IAM execution role needs `s3:PutObject` permission on the document bucket.

### Step 2: Configure Amazon Bedrock Knowledge Bases

With Amazon Bedrock Knowledge Bases, you configure document chunking, choose the embedding model, the vector index, and metadata filtering without managing the underlying infrastructure. You configure a data source backed by the same Amazon S3 bucket used for the ingestion pipeline.

Amazon Bedrock Knowledge Bases chunks documents at 300 tokens with 20% overlap (the defaults, which work well for structured enterprise documents). An embedding model, such as Amazon Titan Text Embeddings V2, generates the embeddings. The metadata attributes defined in the *.metadata.json* sidecar file are indexed alongside the vectors, making them available as a pre-filter on the *RetrieveAndGenerate* call.

### Step 3: Define the Cedar schema and policies in Amazon Verified Permissions

Verified Permissions provides fine-grained authorization through **Cedar** — a purpose-built policy language. The Cedar schema defines three entity types for this solution:

1. **Principal:** `GenAIApp::UserGroup` (the department group extracted from the JWT).
2. **Action:** `query` (retrieve documents from the knowledge base) and `invokeModel` (invoke an FM).
3. **Resource:** `GenAIApp::KnowledgeBase` and `GenAIApp::Model`.

The `GenAIApp` namespace is a custom prefix you define when you create the Cedar schema. You can replace it with your own application namespace (such as `MyCompany` or `RAGApp`).

The following six policies cover the three departments used in this post. Department C has a cross-department grant covering all Knowledge Base resources, which suits an executive or leadership group:

```
// dept-a: query its own KB + use Claude 3 Haiku
permit(
    principal in GenAIApp::UserGroup::"dept-a",
    action == GenAIApp::Action::"query",
    resource == GenAIApp::KnowledgeBase::"dept-a"
);

permit(
    principal in GenAIApp::UserGroup::"dept-a",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"anthropic.claude-3-haiku-20240307-v1:0"
);

// dept-b: query its own KB + use Claude 3 Haiku
permit(
    principal in GenAIApp::UserGroup::"dept-b",
    action == GenAIApp::Action::"query",
    resource == GenAIApp::KnowledgeBase::"dept-b"
);

permit(
    principal in GenAIApp::UserGroup::"dept-b",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"anthropic.claude-3-haiku-20240307-v1:0"
);

// dept-c: cross-department access + use Amazon Nova Lite 2
permit(
    principal in GenAIApp::UserGroup::"dept-c",
    action == GenAIApp::Action::"query",
    resource
);

permit(
    principal in GenAIApp::UserGroup::"dept-c",
    action == GenAIApp::Action::"invokeModel",
    resource == GenAIApp::Model::"amazon.nova-2-lite-v1:0"
);
```

To adapt these policies for your organization, replace the department identifiers (`dept-a`, `dept-b`, `dept-c`) with your own group names. The pattern supports several access styles: by team, by project, or hierarchical. For temporary access grants, add a Cedar policy with a `when` condition that evaluates a time-based attribute, and remove that policy when the grant should expire.

Policy changes take effect on the next API call. You don't need to redeploy the Lambda or update the AWS CDK.

**Policy governance:** Because Cedar policy changes take effect immediately, restrict who can modify policies in production environments. Apply IAM conditions to *verifiedpermissions:CreatePolicy*, *UpdatePolicy*, and *DeletePolicy* so that only a dedicated CI/CD role or a small group of authorized administrators can change the policy store. Enable AWS CloudTrail for Verified Permissions API calls and create a CloudWatch Alarm that fires on policy-change events outside your change-management process. For production deployments, test Cedar policies against test scenarios in a non-production policy store before promoting them. Treat policy changes as seriously as you treat application code deployments.

### Step 4: Configure Layer 1 — API-level authorization (Lambda Authorizer)

When a request reaches Amazon API Gateway, the Lambda Authorizer runs before your application logic. It validates the JWT signature against Amazon Cognito's JSON Web Key Set (JWKS) endpoint, then calls Verified Permissions *IsAuthorized* with your group membership. This is the traditional "access/authorization" check: it verifies whether you're allowed to call the API, not which documents you can access.

The authorizer denies access by default. If Verified Permissions is unavailable, the function throws an exception and Amazon API Gateway returns 403.

```python
import boto3, json

avp = boto3.client("verifiedpermissions")

def handler(event, context):
    token = event["authorizationToken"]
    claims = decode_and_verify_jwt(token)  # validate against the Amazon Cognito JWKS
    groups = claims.get("cognito:groups", [])

    if not groups:
        raise Exception("Unauthorized")

    # Evaluate each group; allow if any group has a permit policy
    allowed = False
    for group in groups:
        response = avp.is_authorized(
            policyStoreId=POLICY_STORE_ID,
            principal={"entityType": "GenAIApp::UserGroup", "entityId": group},
            action={"actionType": "GenAIApp::Action", "actionId": "query"},
            resource={"entityType": "GenAIApp::Application", "entityId": "api"}
        )
        if response["decision"] == "ALLOW":
            allowed = True
            break

    if not allowed:
        raise Exception("Unauthorized")

    return generate_policy("Allow", event["methodArn"], claims)
```

**Deployment note:** Deploy this as a Lambda Authorizer (TOKEN type) on your Amazon API Gateway REST API. Set the authorizer cache TTL to 0 during testing so policy changes take effect immediately.

**Cache TTL in production:** In production, the API Gateway authorizer cache TTL determines how quickly policy revocations take effect. A TTL of 0 means every request triggers a fresh Verified Permissions evaluation. Revocation takes effect immediately, but latency increases. A TTL of 300 seconds (the API Gateway default) improves latency but means a revoked policy could still allow access for up to 5 minutes. For workloads that need timely revocation (for example, when an employee leaves or during incident response), set the TTL to 0 or to a deliberately short value (for example, 30–60 seconds) and accept the additional Verified Permissions API calls. The claim that "policy changes take effect on the next API call" only holds when the authorizer cache TTL is 0 or the cache entry has expired.

**Multiple group membership:** A user can belong to multiple Cognito groups — for example, an employee in both `dept-a` and a cross-department leadership group. The authorizer evaluates the groups present in the JWT and allows the API call if any group has a matching Cedar permit policy. This helps avoid arbitrarily restricting access based on the order in which groups appear in the token. Document-level access is then determined independently at Layer 2, where the middleware evaluates each department resource against the user's groups to build the appropriate metadata filter.

For error handling, implement exponential backoff with jitter for Verified Permissions API calls. Log authorization decisions to Amazon CloudWatch for monitoring and audit.

### Step 5: Configure Layer 2 — document-level authorization (middleware Lambda)

After a request passes Layer 1, the middleware Lambda runs a second, independent Verified Permissions evaluation. This time, it checks which KB resources you're allowed to query based on your group membership, then translates that decision directly into a metadata filter on the *RetrieveAndGenerate* call.

Amazon Bedrock Knowledge Bases applies the metadata filter *before* running the vector similarity search. That means the FM only processes documents you're allowed to access. The filter helps prevent unauthorized documents from appearing in the retrieval set.

**Access model by department**

| **Group** | **Foundation model** | **Knowledge base access**       |
| --------- | -------------------- | ------------------------------- |
| dept-a    | Claude 3 Haiku       | Department A documents only     |
| dept-b    | Claude 3 Haiku       | Department B documents only     |
| dept-c    | Amazon Nova Lite 2   | Multiple departments (A, B, C)  |

```python
def build_filter_and_invoke(user_group, query, session_id):
    permitted_depts = []
    for dept in ["dept-a", "dept-b", "dept-c"]:
        resp = avp.is_authorized(
            policyStoreId=POLICY_STORE_ID,
            principal={"entityType": "GenAIApp::UserGroup",
                       "entityId": user_group},
            action={"actionType": "GenAIApp::Action",
                    "actionId": "query"},
            resource={"entityType": "GenAIApp::KnowledgeBase",
                      "entityId": dept}
        )
        if resp["decision"] == "ALLOW":
            permitted_depts.append(dept)

    if not permitted_depts:
        raise PermissionError("No permitted Knowledge Base resources")

    # Implement retry with exponential backoff for avp.is_authorized calls

    # Build the metadata filter from the permitted departments
    if len(permitted_depts) == 1:
        kb_filter = {"equals": {"key": "department",
                                "value": permitted_depts[0]}}
    else:
        kb_filter = {"orAll": [{"equals": {"key": "department",
                                           "value": d}}
                               for d in permitted_depts]}

    return bedrock_agent.retrieve_and_generate(
        input={"text": query},
        retrieveAndGenerateConfiguration={
            "type": "KNOWLEDGE_BASE",
            "knowledgeBaseConfiguration": {
                "knowledgeBaseId": KB_ID,
                "modelArn": get_permitted_model(user_group),
                "retrievalConfiguration": {
                    "vectorSearchConfiguration": {
                        "filter": kb_filter
                    }
                }
            }
        }
    )
```

**Deployment note:** Deploy this as the handler for the middleware AWS Lambda function (Python 3.12 runtime). Set the `POLICY_STORE_ID` and `KB_ID` environment variables. The function's IAM execution role needs the *verifiedpermissions:IsAuthorized* and *bedrock:RetrieveAndGenerate* permissions.

FM selection uses the same Verified Permissions policy store. The Cedar policies that grant *invokeModel* determine which model ID the middleware passes to Amazon Bedrock, so model access is governed by the same externalized policies as document access.

**Security note:** The metadata filter excludes unauthorized documents from the retrieval set before the FM processes them. If a user queries another department's data, the request returns no relevant results. To monitor for anomalous retrieval behavior, use Amazon CloudWatch logging on the middleware AWS Lambda function.

#### Benefits of two independent authorization layers

|                          | **Layer 1 (API Gateway)**      | **Layer 2 (Middleware Lambda)**                    |
| ------------------------ | ------------------------------ | -------------------------------------------------- |
| **Question answered**    | Are you allowed to call the API? | Which documents can your department access?        |
| **Enforcement point**    | Before application logic runs  | At the Amazon Bedrock KB metadata filter           |
| **Failure mode**         | Returns 403 to you             | Empty or filtered result set                       |

**Availability trade-off:** Both layers depend on Amazon Verified Permissions. If the service is throttled or unavailable, the deny-by-default design means users are denied access. This is the correct, deliberate security behavior. For most workloads, a brief denial is preferable to failing open. If your application has strict availability requirements, consider implementing exponential backoff with jitter on all *IsAuthorized* calls (in both the Lambda Authorizer and the middleware Lambda) to handle transient throttling gracefully. A circuit breaker that falls back to cached "last-known-good" authorization decisions can improve availability, but it creates a window where a revoked grant could still be accepted. Document this trade-off if you adopt it, and make sure cached decisions expire under a short TTL.

### Step 6: Add Guardrails for Amazon Bedrock as an output safety layer

**Guardrails for Amazon Bedrock** applies contextual source-fidelity checks and content filtering as an additional safety layer. While Verified Permissions controls which documents the FM can access, Guardrails evaluates the FM's answer before it's returned to you.

The contextual source-fidelity check helps ensure the answer stays grounded in the retrieved documents rather than drawing on the FM's pre-training data. Combine this with the metadata filter from Layer 2 for a complete defense-in-depth approach: authorization limits the retrieval set, and Guardrails validates the generated output.

Configuring the Guardrail in the *RetrieveAndGenerate* call applies two checks:

1. **Contextual grounding:** Helps limit answers that infer beyond the retrieved context, supporting factual accuracy anchored to your documents.
2. **Content filtering:** Blocks answers containing harmful or inappropriate content based on the thresholds you configure.

You apply the Guardrail in the *RetrieveAndGenerate* call by passing the *guardrailConfiguration* parameter with the Guardrail ID and version. Contextual grounding helps mitigate prompt injection by constraining answers to the retrieved context, but it doesn't eliminate every injection vector. For additional defense, validate input length and sanitize the query before passing it to *RetrieveAndGenerate*.

### Step 7: Test the end-to-end authorization flow

With the solution deployed, here's what happens when a `dept-a` user submits a query:

1. The user sends a query through the web application with `Authorization: Bearer`, passing through Amazon CloudFront to AWS WAF.
2. AWS WAF applies rate limiting and managed rules, then forwards clean traffic to Amazon API Gateway.
3. The Lambda Authorizer validates the JWT and calls Verified Permissions. The `dept-a` group has a permit policy for `query`, so the call is allowed.
4. The middleware Lambda calls Verified Permissions for each Knowledge Base resource. Only `dept-a` is permitted, so the filter `{"equals": {"key": "department", "value": "dept-a"}}` is built.
5. The middleware calls *RetrieveAndGenerate* with the applied metadata filter. Amazon Bedrock Knowledge Bases filters the document set before running the vector similarity search.
6. Department B and C documents are excluded from the search space. The FM generates an answer grounded only in Department A documents.
7. The answer is checked by Guardrails for Amazon Bedrock before it's returned.

To test, use the following `curl` command with a valid JWT from Amazon Cognito:

```bash
curl -X POST https://<api-id>.execute-api.<region>.amazonaws.com/prod/query \
  -H "Authorization: Bearer <id_token>" \
  -d '{"query": "Summarize the latest department report"}'
```

A successful `dept-a` request returns an answer grounded only in Department A documents. If authorization fails at Layer 1, you get a 403. If Layer 2 finds no permitted resources, the function returns a `PermissionError`.

Monitor authorization decisions in Amazon CloudWatch Logs for both the Lambda Authorizer and the middleware function. Set up CloudWatch metric filters and alarms for the following:

- **Authorization denial rate** (Layer 1 and Layer 2) — a sudden spike could indicate credential probing, a misconfigured client, or a policy error.
- **Verified Permissions latency** — a sustained increase could signal throttling.
- **SQS dead-letter queue message count** — messages in the DLQ indicate failed metadata tagging events that need handling.
- **Ingestion job failure rate** — alerts on documents that weren't indexed.

AWS CloudTrail automatically records Verified Permissions *IsAuthorized* calls, providing an audit trail for every authorization decision with no additional configuration.

To observe live policy-update behavior, grant `dept-a` a Cedar policy that allows access to the `dept-b` resource, then resubmit the query immediately. The next API call reflects the change. Revoke the policy and the constraint is restored on the following call.

### A single knowledge base with metadata-based isolation

Adding a department requires only a new Cedar policy and tagging the new documents. You don't provision additional infrastructure, deploy a new stack, or manage a separate ingestion pipeline. The FM is given only the permitted subset of documents based on the applied metadata pre-filter.

For session management, use an Amazon DynamoDB table with a session TTL to maintain conversation context across requests. The *RetrieveAndGenerate* API accepts a *sessionId* parameter to manage multi-turn context automatically. Generate the session ID with a cryptographically random value (for example, *uuid4*) and bind each session to the authenticated user's identity and groups at creation time.

On each subsequent request, verify that the bearer token's subject and group claims match the session owner before continuing the conversation. Invalidate the session when the user's group membership changes or the token is revoked, and set a TTL appropriate for your use case (for example, 30 minutes of inactivity).

---

## Clean up resources

If you deployed resources individually, delete them in the following order to avoid dependency errors:

1. **Amazon CloudFront distribution** and **AWS WAF web ACL.**
2. **Amazon API Gateway REST API** (also detach the Lambda Authorizer).
3. **The AWS Lambda functions** (metadata tagging, authorizer, middleware).
4. **Amazon Bedrock Knowledge Base** and its associated data source.
5. **Amazon S3 bucket** — empty the bucket first. If versioning is enabled, delete all object versions and delete markers before removing the bucket.
6. **Amazon EventBridge rule** and **Amazon SQS queue.**
7. **Amazon DynamoDB table.**
8. **Amazon Verified Permissions policy store.**
9. **Amazon Cognito user pool** (if created specifically for this pattern).
10. **The IAM roles and policies** created for the Lambda functions and API Gateway.
11. **The Amazon CloudWatch log groups** for each Lambda function.

{{% notice warning %}}
⚠️ Deleting these resources is **irreversible**. Back up any documents in S3, DynamoDB data, or Verified Permissions policies you may need before proceeding.
{{% /notice %}}

---

## Conclusion

You now have a working defense-in-depth authorization pattern for fine-grained, intra-tenant document access control in a RAG application built on Amazon Bedrock. With this approach, you can: change access policies at runtime without redeploying code; maintain logical document-level isolation that remains effective even if the API layer is misconfigured; and audit every authorization decision from a single Verified Permissions policy store.

### Key takeaways

- **Updates without redeployment.** Cedar policies in Verified Permissions are human-readable, version-controlled outside the Lambda code, and take effect on the next API call. You can revoke a department's access or grant cross-department access to a leadership group just by updating a policy in the Verified Permissions console.
- **Cost-efficient document isolation, no infrastructure duplication.** A single Amazon Bedrock Knowledge Bases instance with metadata pre-filtering provides logical isolation between departments within a tenant, at a fraction of the cost and operational overhead of separate instances. Remember this is filter-level, not infrastructure-level, isolation — for hard tenant boundaries, use a separate knowledge base per tenant.
- **Independent enforcement layers reduce single-point-of-failure risk.** Layer 1 (the Lambda Authorizer) and Layer 2 (the middleware Lambda) enforce independent policy checks. Both call Verified Permissions separately, and both fail closed (deny by default).

---

## Next steps

To extend this pattern further:

1. **First step: Test with your own documents.** Replace the sample department documents with your content, upload it under the appropriate prefix, and verify that the metadata filter isolates them correctly.
2. **Add a fourth department.** Create a new Cedar policy, add a user group in Amazon Cognito, and upload tagged documents to validate that the pattern scales without code changes.
3. **Extend to agent tool authorization with Amazon Bedrock AgentCore.** The Policy feature uses the same Cedar language to enforce fine-grained authorization on an agent's tool and gateway calls.
4. **Add attribute-based access control (ABAC).** Extend the Cedar policies to evaluate user attributes beyond group membership, such as project assignment, clearance level, or geographic location.
5. **Integrate with your identity provider.** Replace Amazon Cognito with your enterprise identity provider (such as Okta or Microsoft Entra ID) by configuring a Verified Permissions identity source.
6. **Automate policy testing.** Build a CI/CD pipeline that validates Cedar policies against test scenarios before deploying them to the policy store.

---

## Related resources

- [Multi-tenancy in RAG applications in a single Amazon Bedrock knowledge base with metadata filtering](https://aws.amazon.com/blogs/machine-learning/multi-tenancy-in-rag-applications-in-a-single-amazon-bedrock-knowledge-base-with-metadata-filtering/) (AWS Machine Learning Blog, April 2025)
- [Amazon Verified Permissions documentation](https://docs.aws.amazon.com/verifiedpermissions/)
- [Amazon Bedrock Knowledge Bases documentation](https://docs.aws.amazon.com/bedrock/latest/userguide/knowledge-base.html)
- [Amazon Bedrock Knowledge Bases with metadata filtering](https://aws.amazon.com/blogs/machine-learning/metadata-filtering-for-tabular-data-with-knowledge-bases-for-amazon-bedrock/) (AWS Machine Learning Blog, July 2024)
- [Design secure generative AI application workflows with Amazon Verified Permissions and Amazon Bedrock Agents](https://aws.amazon.com/blogs/machine-learning/design-secure-generative-ai-application-workflows-with-amazon-verified-permissions-and-amazon-bedrock-agents/) (AWS Machine Learning Blog, October 2024)
- [Authorizing access to data with RAG implementations](https://aws.amazon.com/blogs/security/authorizing-access-to-data-with-rag-implementations/) (AWS Security Blog, September 2025)
- [Cedar policy language documentation](https://docs.cedarpolicy.com/)
