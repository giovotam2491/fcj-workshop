---
title: "Blog 1"
date: 2026-06-29
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Log analysis with facets, correlation, enrichment, and automation in Amazon CloudWatch Log Analytics

Teams running distributed applications tend to accumulate logs spread across many log groups, including application logs, access logs, and audit trails. When something needs investigating, an engineer opens the console and starts writing a query from scratch. The same query gets written differently by different people. Results come back missing context, because a log event itself doesn't carry information about who owns a service or which environment it belongs to. Checking two log groups means running two separate queries and comparing the results by hand. And once the investigation is over, the query vanishes from the console history. These are common pain points when working with logs at scale. **Amazon CloudWatch Log Analytics** now includes capabilities that address them directly.

1. **Facets** — explore log data visually without writing a query.
2. **Lookup tables** — enrich query results with external metadata.
3. **Parameterized queries** — save reusable query templates with fill-in variables.
4. **JOIN and sub-queries** — correlate data across log groups in a single query.
5. **Scheduled queries** — run queries automatically and deliver results to Amazon Simple Storage Service (Amazon S3) or Amazon EventBridge.

This walkthrough covers each capability with a hands-on example. It names the customer problem each one solves, how to set it up, and how to save it so your team can reuse it. At the end, the capabilities are combined in a short example to show how they work together, including how these reusable templates fit into AI-assisted operational workflows (**AIOps**).

---

## Where these capabilities fit in your observability strategy

The **AWS Observability Maturity Model** defines four stages of observability maturity. The capabilities in this post support **Stage 3 (Advanced Observability)**, where teams correlate signals across sources, detect anomalies, and interpret issues in context. They complement the tools you use in earlier stages.

| Stage                          | Focus                                                    | AWS tooling                                                                                            |
| ------------------------------ | -------------------------------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **Stage 1: Foundational**      | Collect telemetry (metrics, logs, traces)                | Amazon CloudWatch Metrics, Amazon CloudWatch Logs, AWS X-Ray                                           |
| **Stage 2: Intermediate**      | Analyze, alert, visualize                                | Amazon CloudWatch Alarms, Amazon CloudWatch Dashboards, Amazon GuardDuty                               |
| **Stage 3: Advanced**          | Cross-signal correlation, contextual enrichment, anomaly detection | Amazon CloudWatch Log Analytics (JOIN, sub-queries, lookup, parameterized queries, scheduled queries) |
| **Stage 4: Proactive**         | AI/ML root-cause analysis, automated remediation         | Amazon CloudWatch Anomaly Detection                                                                    |

At Stage 3, teams move beyond looking at individual metrics or traces in isolation and correlate data across sources using custom logic. The capabilities in this post help you do that correlation right inside your log data.

To trace requests across services, use AWS X-Ray or OpenTelemetry. For automated threat detection, use Amazon GuardDuty. The Log Analytics capabilities covered here are for custom log analysis that spans multiple log groups. They apply your organization's business rules and produce reusable templates your team can share.

---

## Prerequisites

To follow this walkthrough, you need:

- An **AWS account** with structured logs flowing into at least two **Amazon CloudWatch** log groups (this post uses `/demo/security/api-activity` and `/demo/security/access-logs`).
- **AWS Identity and Access Management (IAM)** permissions for the following actions:
  - `logs:PutQueryDefinition`
  - `logs:StartQuery`
  - `logs:DescribeQueryDefinitions`
  - `logs:GetQueryResults`
  - `logs:PutIndexPolicy`
  - `logs:DeleteQueryDefinition`
  - `logs:DeleteIndexPolicy`
- **AWS CLI** version 2.34.56 or later, installed and configured.
- **Node.js** 18 or later and the **AWS Cloud Development Kit (AWS CDK)** version 2.100 or later, installed.
- A basic understanding of **CloudWatch Logs Insights** query syntax (fields, filter, stats, sort).

---

## Deploy the example environment

To deploy the example environment with sample log data, use the CDK stack included in the repository. The stack creates both log groups (`/demo/security/api-activity` and `/demo/security/access-logs`) and pushes in sample events that match the schema used in this post.

Clone the repository and deploy the stack:

```bash
git clone https://github.com/aws-samples/sample-cloudwatch-logs-advanced-analysis-environment.git
cd sample-cloudwatch-logs-advanced-analysis-environment
npm install
npx cdk bootstrap
npx cdk deploy
```

The `cdk bootstrap` command only needs to run once per account and Region. After the stack deploys successfully (about 5 minutes), the log groups start receiving sample events. Wait 2–3 minutes for log data to accumulate before running the queries below.

Set the Region for this terminal session so you can omit `--region` from the CLI commands that follow:

```bash
export AWS_REGION=<your-region>
```

Replace `<your-region>` with the Region where you deployed the CDK stack. This variable only lives in the current terminal session.

> **Alternative:** If you already have structured logs flowing into CloudWatch, you can skip the deployment step and use your own log groups throughout the walkthrough.

---

## The example environment

The walkthrough uses two log groups:

- **`/demo/security/api-activity`** — API activity events with fields such as `eventSource`, `eventName`, `sourceIPAddress`, `errorCode`, and `userIdentity_principalId`.
- **`/demo/security/access-logs`** — application access logs with fields such as `userId`, `resource`, `action`, and `sourceIP`.
- A **lookup table** (created in the [Lookup tables](#lookup-tables-for-context-enrichment) section) that maps account IDs to organizational metadata.
![Figure 1](/images/3-BlogsTranslated/3.1-Blog1/figure-01-architecture-cloudops-2335-1.png)
> *Figure 1. The example environment showing the log sources, key fields, and the lookup table.*

---

## Facets for visual exploration

> **Customer problem:** "I want to see what patterns exist in the logs before I write a query. I don't yet know what to filter on."

With facets, you first run a query to load log events, and CloudWatch Log Analytics shows interactive facets in the right panel based on the indexed fields. Each facet shows the value distribution for that field. You can select a facet value to add it as a filter, then rerun the query to see only the matching results.

### Explore logs with facets

1. Open the **CloudWatch console**, and under **Logs**, choose **Log Analytics**.
2. In Log Analytics, run the following query, which uses the `errorCode` facet filtered by `AccessDenied`. Replace `123456789012` with your AWS account number:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
fields @timestamp, eventSource, eventName, errorCode, sourceIPAddress
| sort @timestamp desc
| limit 50
| filterIndex errorCode in ["AccessDenied"]
```

3. Run the query. The results now show only events whose `errorCode` is `AccessDenied`, along with the corresponding `sourceIPAddress` values for each denied request.
![Figure 2](/images/3-BlogsTranslated/3.1-Blog1/figure-02-facets-errorcode-distribution-cloudops-2335-1.png)
> *Figure 2. Facets showing the distribution of `errorCode` with `AccessDenied` selected as a filter.*

### Default facets

Every log group ships with four default facets:

- `@aws.region` — the AWS Region that produced the log event
- `@data_format` — the log data format
- `@data_source_name` — the log group name
- `@data_source_type` — the log source type (for example, Lambda, ECS, EC2)

These facets work immediately, with no additional configuration.

![Figure 3](/images/3-BlogsTranslated/3.1-Blog1/figure-03-default-facets-cloudops-2335-1.png)
> *Figure 3. The default facets panel showing `@aws.region`, `@data_format`, `@data_source_name`, and `@data_source_type`.*

### Create custom facets with an index policy

If you deployed the accompanying CDK stack, index policies for `/demo/security/api-activity` and `/demo/security/access-logs` already exist. The commands below show what the stack created, so you can inspect or recreate them in your own environment.

Default facets are useful but generic. For your application logs, you'll want facets on the fields that actually matter to your team. Index policies are defined per log group, so create one for each log group with the fields relevant to its content.

For the API activity log group, index fields such as `eventSource`, `eventName`, `errorCode`, and `sourceIPAddress`:

```bash
aws logs put-index-policy \
  --log-group-identifier "/demo/security/api-activity" \
  --policy-document '{
    "FieldsV2": {
      "eventSource": {"type": "FACET"},
      "eventName": {"type": "FACET"},
      "errorCode": {"type": "FACET"},
      "sourceIPAddress": {"type": "FACET"},
      "userIdentity_accountId": {"type": "FACET"},
      "userIdentity_principalId": {"type": "FIELD_INDEX"}
    }
  }'
```

For the access log group, index fields that are useful for tracking who accessed what:

```bash
aws logs put-index-policy \
  --log-group-identifier "/demo/security/access-logs" \
  --policy-document '{
    "FieldsV2": {
      "userId": {"type": "FACET"},
      "resource": {"type": "FACET"},
      "action": {"type": "FACET"},
      "sourceIP": {"type": "FACET"}
    }
  }'
```

After a few minutes, these fields appear as selectable facets in the Log Analytics console. An engineer can run a query, then choose `errorCode` in the right-hand facet panel to see the value distribution. Choosing a specific value like `AccessDenied` adds it as a filter. Rerunning the query returns only matching events, without manually rewriting the query syntax.

### Best practices for choosing facet fields

Index policies work best on **low-cardinality fields** (few distinct values). A facet with more than 100 unique values per day is treated as high cardinality, and its values won't be displayed in the console. This walkthrough uses `eventSource`, `errorCode`, and `userIdentity_accountId` because they have a small number of distinct values and are useful for visual exploration.

---

## Lookup tables for context enrichment

> **Customer problem:** "My log events contain a service name or account ID, but I need to know who owns it, which environment it belongs to, and whom to contact. That information lives in a spreadsheet, not in the logs."

With lookup tables, you can enrich query results with external metadata. Upload a CSV file once, and any query can reference it to add columns that don't exist in the original log events.

### Download the CSV file

This walkthrough maps account IDs to organizational context. Download the `account-metadata.csv` file from the repository and save it locally.

### Upload the lookup table

Upload the CSV file as a lookup table named `account_metadata`:

1. Open the **CloudWatch console**.
2. In the navigation pane, choose **Settings**, then the **Logs** tab.
3. Scroll to **Lookup tables** and choose **Manage**.
4. Choose **Create lookup table**.
5. Enter `account_metadata` as the table name.
6. Upload the `account-metadata.csv` file you just downloaded, then choose **Create lookup table**.

To confirm the table is queryable, run the following query in Log Analytics. Replace `123456789012` with your AWS account number:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-10800s END=0s |
fields @timestamp, userIdentity_principalId, userIdentity_accountId, eventName
| filter ispresent(userIdentity_accountId)
| lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner
| fields @timestamp, userIdentity_principalId, userIdentity_accountId, account_name, environment, owner
| limit 10
```

If `account_name`, `environment`, and `owner` appear in the results, the table is working.

![Figure 4](/images/3-BlogsTranslated/3.1-Blog1/figure-04-lookup-query-results-cloudops-2335-1.png)
> *Figure 4. Lookup query results showing the `account_name` and `owner` columns enriched from the CSV file.*

---

## Parameterized queries for reusable templates

> **Customer problem:** "I write the same query every time with only the values changing. New team members don't know which query to run. There's no shared, useful query library."

With parameterized queries, you save a query template with `{{parameter_name}}` placeholders. When someone loads the saved query from the console, the parameters are populated with default values that they can adjust before running. The query appears in the **Saved and sample queries** panel, where the whole team can find it.

### Create a parameterized query

If you deployed the accompanying CDK stack, the `Security/PrincipalActivityAudit` query definition already exists. The command below shows what the stack deployed, so you can inspect or recreate it in your own environment.

This query shows all activity for a given principal, enriched with account metadata from the lookup table. The `{{principalId}}` parameter lets any team member run this query for any principal without rewriting it.

```
SOURCE "/demo/security/api-activity" START=-604800s END=0s |
fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode, userIdentity_accountId
| filter userIdentity_principalId = "{{principalId}}"
| lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner
| sort @timestamp desc
```

**Parameters**

| Parameter     | Description                     | Default value           |
| ------------- | ------------------------------- | ----------------------- |
| `principalId` | The principal ID to investigate | `AIDA1234567890EXAMPLE` |

To make this query available to your whole team, save it as a query definition with the following CLI command:

```bash
aws logs put-query-definition \
  --name "Security/PrincipalActivityAudit" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode, userIdentity_accountId | filter userIdentity_principalId = "{{principalId}}" | lookup account_metadata account_id as userIdentity_accountId OUTPUTNEW account_name, environment, owner | sort @timestamp desc' \
  --parameters '[{"name":"principalId","defaultValue":"AIDA1234567890EXAMPLE","description":"The principal ID to investigate"}]'
```

To view the saved query, open **Saved and sample queries** in the Log Analytics console, go to the **Security** folder, and choose `PrincipalActivityAudit`. The query loads with the `principalId` parameter field prefilled with the default value `AIDA1234567890EXAMPLE`.

![Figure 5](/images/3-BlogsTranslated/3.1-Blog1/figure-05-parameterized-query-input-cloudops-2335-1.png)
> *Figure 5. The saved parameterized query showing the `principalId` input field in the Log Analytics console.*

Now any team member can open **Saved and sample queries**, find the **Security** folder, choose `PrincipalActivityAudit`, adjust the principal ID, and run — without writing any query.

### Enable correlated anomaly data

The remaining sections (JOIN, sub-queries, and scheduled queries) correlate data across both log groups. For meaningful results, all three log generators need anomaly mode enabled to produce correlated events from IP `198.51.100.42`. Run the following commands in **AWS CloudShell** in the same Region where you deployed the CDK stack.

```bash
aws lambda update-function-configuration \
  --function-name demo-anomaly-generator \
  --environment "Variables={API_LOG_GROUP=/demo/security/api-activity,ACCESS_LOG_GROUP=/demo/security/access-logs,ANOMALY_MODE=true}"
```

```bash
aws lambda update-function-configuration \
  --function-name demo-cloudtrail-generator \
  --environment "Variables={LOG_GROUP_NAME=/demo/security/api-activity,ANOMALY_MODE=true}"
```

```bash
aws lambda update-function-configuration \
  --function-name demo-access-log-generator \
  --environment "Variables={LOG_GROUP_NAME=/demo/security/access-logs,ANOMALY_MODE=true}"
```

Wait 5–10 minutes for correlated events to accumulate before moving to the JOIN section.

---

## JOIN for cross-log-group correlation

> **Customer problem:** "I need to see data from two log groups at once. Right now I have to run two separate queries and compare the results manually."

With JOIN, you can correlate events across multiple log groups in a single query. The main query runs against one log group. The JOIN pulls in matching events from a second log group based on a shared field, such as an IP address, user ID, or request ID.

### Create a JOIN query

This query shows both which API calls were made and which application resources were accessed from a given IP, in the same result set. Run the following in Log Analytics. Replace `123456789012` with your AWS account number:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode
| join type=inner left=api right=access where api.sourceIPAddress=access.sourceIP (SOURCE "/demo/security/access-logs")
| filter api.sourceIPAddress = "198.51.100.42"
| fields api.@timestamp, api.eventName, api.eventSource, api.errorCode, access.userId, access.resource, access.action
| sort api.@timestamp desc
| limit 10
```

**Parameters**

| Parameter      | Description                                     | Default value   |
| -------------- | ----------------------------------------------- | --------------- |
| `suspiciousIP` | The IP address to investigate across log sources | `198.51.100.42` |

Save this query as a reusable definition with the `put-query-definition` CLI command:

```bash
aws logs put-query-definition \
  --name "Security/CrossSourceCorrelation" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress, errorCode | join type=inner left=api right=access where api.sourceIPAddress=access.sourceIP (SOURCE "/demo/security/access-logs") | filter api.sourceIPAddress = "{{suspiciousIP}}" | fields api.@timestamp, api.eventName, api.eventSource, api.errorCode, access.userId, access.resource, access.action | sort api.@timestamp desc | limit 100' \
  --parameters '[{"name":"suspiciousIP","defaultValue":"198.51.100.42","description":"IP address to investigate across log sources"}]'
```
![Figure 6](/images/3-BlogsTranslated/3.1-Blog1/figure-06-join-correlation-cloudops-2335-1.png)
> *Figure 6. JOIN results showing API calls and resource access correlated by source IP.*

---

## Sub-queries for multi-step analysis

> **Customer problem:** "I want to find entities that match a condition in one log group, then look up their activity in another. That currently takes two queries."

With sub-queries, the output of one query feeds into the filter of another. The inner query defines a set of values (like principal IDs or service names), and the outer query uses those values to retrieve related data.

### Create a sub-query

This example finds principals with repeated access-denied errors, then retrieves all of their successful actions. Run the following in Log Analytics. Replace `123456789012` with your AWS account number:

```
SOURCE "arn:aws:logs:us-east-1:123456789012:log-group:/demo/security/api-activity" START=-3600s END=0s |
filter userIdentity_principalId in (
    SOURCE "/demo/security/api-activity"
    | filter errorCode = "AccessDenied"
    | stats count(*) as denied_count by userIdentity_principalId
    | filter denied_count > 3
    | fields userIdentity_principalId
)
| filter errorCode != "AccessDenied"
| stats count(*) as successful_actions by userIdentity_principalId, eventSource, eventName
| sort successful_actions desc
```

**Parameters**

| Parameter         | Description                                              | Default value |
| ----------------- | ------------------------------------------------------- | ------------- |
| `deniedThreshold` | Minimum number of denied actions to flag a principal    | `3`           |

Save this query as a reusable definition with the `put-query-definition` CLI command:

```bash
aws logs put-query-definition \
  --name "Security/DeniedThenSucceeded" \
  --log-group-names "/demo/security/api-activity" \
  --query-string 'filter userIdentity_principalId in (SOURCE "/demo/security/api-activity" | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId | filter denied_count > {{deniedThreshold}} | fields userIdentity_principalId) | filter errorCode != "AccessDenied" | stats count(*) as successful_actions by userIdentity_principalId, eventSource, eventName | sort successful_actions desc' \
  --parameters '[{"name":"deniedThreshold","defaultValue":"3","description":"Minimum denied actions to flag a principal"}]'
```
![Figure 7](/images/3-BlogsTranslated/3.1-Blog1/figure-07-subquery-results-cloudops-2335-1.png)
> *Figure 7. Sub-query results showing successful actions by principals that had repeated access-denied errors.*

---

## Scheduled queries for automated analysis

> **Customer problem:** "I want this query to run on a schedule and alert me if it finds something. I don't want to open the console to check."

With scheduled queries, you run Log Analytics queries on a recurring schedule and deliver results to Amazon S3, Amazon EventBridge, or both. Scheduled queries support **CloudWatch Logs Insights QL**, **OpenSearch PPL**, and **OpenSearch SQL**.

### Create a scheduled query

This example schedules a query to run every 5 minutes and flag principals with repeated access-denied errors. When results are found, they're delivered to Amazon S3. You can then configure an Amazon S3 event notification or use Amazon EventBridge to trigger downstream alerts.

The CDK stack does not create scheduled queries. Before creating one, retrieve the stack outputs needed for the CLI command:

```bash
aws cloudformation describe-stacks \
  --stack-name SecurityLogsInsightsStack \
  --query "Stacks[0].Outputs" \
  --output table
```

From the output, note the value of `ScheduledQueryRoleArn` (the execution role ARN) and `ResultsBucketName` (the S3 destination). Use these values to fill the corresponding placeholders in the following command:

```bash
aws logs create-scheduled-query \
  --name "ProactiveAccessDeniedDetection" \
  --query-language "CWLI" \
  --query-string 'fields @timestamp, userIdentity_principalId, eventSource, eventName, sourceIPAddress | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId, sourceIPAddress | filter denied_count > 10 | sort denied_count desc' \
  --log-group-identifiers "/demo/security/api-activity" \
  --schedule-expression "cron(0/5 * * * ? *)" \
  --start-time-offset 600 \
  --execution-role-arn "<ScheduledQueryRoleArn>" \
  --destination-configuration '{"s3Configuration":{"destinationIdentifier":"s3://<ResultsBucketName>/scheduled-query-results","roleArn":"<ScheduledQueryRoleArn>"}}'
```

To verify it, open the CloudWatch console, go to **Logs**, then **Log Analytics**. Choose the **Scheduled Queries** icon, and in the pop-up you'll see `ProactiveAccessDeniedDetection`. Choose it to view the schedule details.

![Figure 8](/images/3-BlogsTranslated/3.1-Blog1/figure-08-scheduled-query-config-cloudops-2335-1.png)
> *Figure 8. Scheduled query configuration in the CloudWatch console.*

The execution role needs the `logs:StartQuery`, `logs:GetQueryResults`, and `s3:PutObject` permissions. The schedule expression uses cron syntax; `cron(0/5 * * * ? *)` runs every 5 minutes.

### Weekly summary report to Amazon S3

Optionally, you can also set up a weekly summary report that groups access-denied events by principal and account and delivers the results to Amazon S3. This scheduled query is not created by the CDK stack. Run the following command, reusing the stack output values for `<ScheduledQueryRoleArn>` and `<ResultsBucketName>` that you retrieved earlier:

```bash
aws logs create-scheduled-query \
  --name "WeeklyAccessDeniedReport" \
  --query-language "CWLI" \
  --query-string 'fields @timestamp, userIdentity_principalId, userIdentity_accountId, eventSource, eventName, sourceIPAddress | filter errorCode = "AccessDenied" | stats count(*) as denied_count by userIdentity_principalId, userIdentity_accountId, eventSource | sort denied_count desc' \
  --log-group-identifiers "/demo/security/api-activity" \
  --schedule-expression "cron(0 8 ? * MON *)" \
  --start-time-offset 604800 \
  --execution-role-arn "<ScheduledQueryRoleArn>" \
  --destination-configuration '{"s3Configuration":{"destinationIdentifier":"s3://<ResultsBucketName>/weekly-reports","roleArn":"<ScheduledQueryRoleArn>"}}'
```

This query runs at 08:00 UTC every Monday and scans the previous 7 days (604,800 seconds).

---

## Putting it all together

The following example shows how the capabilities work together. This is not a prescriptive method — it just illustrates how facets, parameterized queries, lookup, JOIN and sub-queries, and scheduled queries operate together in a single workflow.

![Figure 9](/images/3-BlogsTranslated/3.1-Blog1/figure-09-investigation-flow-cloudops-2335.png)
> *Figure 9. The example workflow showing the capabilities used in combination.*

**Scenario:** A scheduled query flags a principal with several repeated access-denied errors from an unfamiliar IP. An engineer begins investigating.

1. **Scheduled query (detection).** The `ProactiveAccessDeniedDetection` scheduled query runs on a 5-minute cadence and flags principal `AIDA9876543210SUSPECT` with access-denied errors from IP `198.51.100.42`. Results are delivered to Amazon S3, and an EventBridge event notifies the team.
2. **Facets (triage).** The engineer opens CloudWatch Log Analytics and chooses the `/demo/security/api-activity` log group. The `errorCode` facet in the right panel shows elevated `AccessDenied` events. They choose that value to filter the results and identify which IPs and principals are involved.
3. **Parameterized query + lookup (audit).** The engineer opens the **Saved and sample queries** panel, navigates to the **Security** folder, and chooses `PrincipalActivityAudit`. The query loads with the `principalId` parameter prefilled. They change it to the flagged principal and run the query. The results show every API call by that principal, enriched with account name and environment from the lookup table.
4. **JOIN (correlate).** The engineer chooses `CrossSourceCorrelation` from the same **Saved and sample queries** panel. The query loads with the `suspiciousIP` parameter (the IP address to investigate across both log groups). They set it to the flagged IP and run the query. The results correlate API activity with application access logs from the same IP in a single view.
5. **Sub-query (scope).** The engineer chooses `DeniedThenSucceeded` from the **Saved and sample queries** panel. The query loads with the `deniedThreshold` parameter (the minimum number of denied actions to flag a principal). They set it to 3 and run the query. The results show what the flagged principal successfully did after the denials.

Each step builds on the last. In this example, the engineer wrote no query from scratch. They used saved templates, adjusted parameters, and got back enriched, correlated results.

---

## From query templates to AI-assisted operations

The parameterized queries and workflows in this post aren't only for human engineers running queries manually. They're structured, API-accessible building blocks that AI agents and automation tools can use directly.

Each saved query definition has a name, parameters with descriptions, and a target log group. An AI agent can load a saved query by name and fill in the parameters based on an alert or a user request. It then runs the query through the CloudWatch Logs API and interprets the results. The agent doesn't need to know the query syntax. It only needs to know which query to run and what parameters to provide.

For example, in **Kiro** (an AI-assisted development environment), you can create a "skill." The skill contains the names of the saved queries, parameter descriptions, an investigation workflow, and syntax rules. When you ask Kiro to "investigate IP `198.51.100.42`," it knows to run `Security/CrossSourceCorrelation` with that parameter, interpret the correlated results, and suggest next steps. Other AI tools (ChatOps bots, custom automation, or any agent with access to the CloudWatch Logs API) can use the same saved query definitions.

Here's the path from manual investigation to AIOps:

1. **Manual.** An engineer writes a query from scratch every time.
2. **Templated.** The team saves parameterized queries. An engineer picks a template and fills in values.
3. **Automated.** Scheduled queries run on a cadence with no human intervention.
4. **AI-assisted.** An engineer asks an AI tool (such as Kiro) to investigate a principal or IP. The tool knows which saved query to run, fills in the parameters, executes it, and presents the results with context.

The capabilities in this post take you from step 1 to step 3. The query definitions, lookup tables, and structured workflows you create become the "skills" an AI agent can use in step 4. The investment in reusable templates pays off twice: once for your team today, and again when you integrate AI-assisted tooling.

---

## Cost considerations

CloudWatch Log Analytics charges based on the amount of data each query scans. For current pricing, see **Amazon CloudWatch pricing**.

**Optimization tips**

- Place the `filter` command as early as possible in the query to reduce the amount of data scanned. For JOIN queries, place the filter after the JOIN using the aliased field name (for example, `filter api.sourceIPAddress = "value"`).
- A JOIN scans both the main log group and the log group in SOURCE. Choose specific fields in the initial `fields` command to limit what gets processed.
- Lookup table data doesn't count toward scanned-data charges.
- Use `stats` to aggregate before a join, rather than joining raw events.
- For scheduled queries, set `start-time-offset` to the minimum time window that still ensures reliable detection.
- Field indexes can speed up queries and reduce cost by skipping log events that don't match an indexed field. Only index fields you'll use as facets or frequently filter on.
- Parameterized templates encourage targeted, pre-filtered queries rather than broad exploratory scans, which reduces total scan cost over time.

---

## Clean up resources

To avoid ongoing charges from the resources created in this post, delete the following. The accompanying CDK stack continuously generates log data, incurring CloudWatch Logs ingestion and storage charges as long as it's deployed.

- If you deployed the example environment with the CDK stack, run `npx cdk destroy` to delete the stack's resources (log groups, Lambda functions, index policies, sample data generators, and the `PrincipalActivityAudit` query definition).

{{% notice warning %}}
⚠️ This action **cannot be undone** and will delete all log data in the log groups the stack created.
{{% /notice %}}

Delete the following resources you created manually in this post:

- **Lookup tables.** Delete the `account_metadata` lookup table through the CloudWatch console.
- **Scheduled queries.** Delete `ProactiveAccessDeniedDetection` and `WeeklyAccessDeniedReport`. To find the query IDs, run the following command, then delete each one with its scheduled query ID:

```bash
aws logs describe-scheduled-queries
aws logs delete-scheduled-query --scheduled-query-id <query-id>
```

---

## Conclusion

This walkthrough covered each new CloudWatch Log Analytics capability with a hands-on example, naming the customer problem each one solves:

1. **Facets** let engineers explore patterns in logs visually without writing a query.
2. **Lookup tables** enrich results with organizational context that lives outside the logs.
3. **Parameterized queries** turn ad-hoc queries into reusable, shareable templates.
4. **JOIN and sub-queries** correlate data across log groups and chain multi-step analysis without manual copy-paste between queries.
5. **Scheduled queries** automate recurring analysis and deliver results without anyone opening the console.

These capabilities support Stage 3 (Advanced Observability) of the **AWS Observability Maturity Model**, where teams correlate signals across sources and detect anomalies with contextual enrichment. They complement CloudWatch Metrics and Alarms (for known thresholds), distributed tracing with AWS X-Ray (for cross-service request correlation), and Amazon GuardDuty (for automated threat detection).

To get started, create an index policy on one of your log groups and explore the facets that appear. From there, save your first parameterized query and share it with your team.

---

## Additional resources

- [AWS Observability Maturity Model](https://aws-observability.github.io/observability-best-practices/guides/observability-maturity-model/)
- [Amazon CloudWatch Logs Insights query syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html)
- [Save and rerun Logs Insights queries](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_Insights-Saving-Queries.html)
- [CloudWatch Logs lookup tables](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Lookup.html)
- [CloudWatch Logs Insights JOIN command](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Join.html)
- [CloudWatch Logs Insights sub-queries](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-Subqueries.html)
- [CloudWatch Logs scheduled queries](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/ScheduledQueries.html)
- [CloudWatch Logs facets](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CloudWatchLogs-Facets.html)
- [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)
