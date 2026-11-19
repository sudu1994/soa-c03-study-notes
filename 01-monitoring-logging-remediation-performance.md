# Domain 1: Monitoring, Logging, Analysis, Remediation, and Performance Optimization (22%)

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> **About 11 of the 50 scored questions.**
>
> Tests whether you can see what's happening (metrics, logs, [traces](00-cloudops-basics.md#traces), [dashboards](00-cloudops-basics.md#dashboards)), get alerted correctly, automatically fix common problems (EventBridge, Systems Manager Automation), and tune compute, storage and databases.
>
> [Tags](00-cloudops-basics.md#tags) like `[also: DOP]` mark material you met in another exam. It is still covered in full here.

## Table of contents

- [Exam tasks and skills](#exam-tasks-and-skills)
- [Task 1.1: Metrics, alarms and filters](#task-11-metrics-alarms-and-filters)
- [Task 1.2: Identify and remediate issues](#task-12-identify-and-remediate-issues)
- [Task 1.3: Performance optimization](#task-13-performance-optimization)
- [Gotchas and exam traps](#gotchas-and-exam-traps)
- [Quick-recall summary](#quick-recall-summary)

## Exam tasks and skills

> Summarized from the official Amazon Web Services (AWS) Certified CloudOps Engineer – Associate (SOA-C03) exam guide (v1.2, October 2026). Exact wording: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain1.html

### Task 1.1: Implement metrics, alarms and filters using AWS monitoring and logging services
- **1.1.1** Use CloudWatch, CloudTrail and Amazon Managed Service for Prometheus to monitor and log workloads (serverless, compute, Artificial Intelligence [AI]).
- **1.1.2** Configure and manage the CloudWatch agent to collect metrics and logs from Elastic Compute Cloud (EC2), Elastic Container Service (ECS) clusters or Elastic Kubernetes Service (EKS) clusters.
- **1.1.3** Configure, identify and troubleshoot CloudWatch alarms that invoke AWS services directly or through EventBridge (composite alarms and their invokable actions).
- **1.1.4** Create and manage customizable, shareable CloudWatch dashboards across multiple accounts and Regions.
- **1.1.5** Configure AWS services to send notifications to Simple Notification Service (SNS), and alarms that notify SNS.

### Task 1.2: Identify and remediate issues using monitoring and availability metrics
- **1.2.1** Analyze performance metrics and automate [remediation](00-cloudops-basics.md#remediation) (CloudWatch, Lambda, Systems Manager, CloudTrail, Kiro, AWS DevOps Agent).
- **1.2.2** Use EventBridge to route, enrich and deliver events, and troubleshoot event bus rules.
- **1.2.3** Create or run custom and predefined Systems Manager Automation [runbooks](00-cloudops-basics.md#runbook-and-playbook) (via Software Development Kits [SDKs] or scripts).

### Task 1.3: Implement performance optimization for compute, storage and database
- **1.3.1** Optimize compute using performance metrics, resource tags and AWS tools.
- **1.3.2** Analyze Elastic Block Store (EBS) performance metrics, troubleshoot, and choose volume types for performance and cost.
- **1.3.3** Optimize Simple Storage Service (S3) performance (DataSync, Transfer Acceleration, multipart uploads, Lifecycle policies).
- **1.3.4** Select and optimize shared storage (Elastic File System [EFS], FSx, Amazon S3 Files; EFS lifecycle policies).
- **1.3.5** Monitor Relational Database Service [RDS] (Performance Insights, CloudWatch alarms) and tune it (proactive recommendations, RDS Proxy).
- **1.3.6** Implement, monitor and optimize EC2 instances and their storage and networking (placement groups).

---

## Task 1.1: Metrics, alarms and filters

### Amazon CloudWatch metrics (skill 1.1.1) `[also: DOP]`

- **What it is & key terms:**
  - **Namespace**: container for metrics (`AWS/EC2`, `AWS/ApplicationELB`, `CWAgent`, custom `MyApp`).
  - **Metric**: name + namespace + **dimensions** (name/value pairs, up to 30 per [metric](00-cloudops-basics.md#metric)). Each unique dimension combination is a separate metric. Metrics can't be deleted; they expire when no new data arrives.
  - **Data point**: value + timestamp (+ unit). **Statistic**: Average, Sum, Min, Max, SampleCount, [percentiles](00-cloudops-basics.md#statistics-and-percentiles) (p50, p90, p99), trimmed mean.
  - **Resolution**: standard (1 minute) or **[high resolution](00-cloudops-basics.md#period-and-resolution)** (1 second; custom metrics via `PutMetricData` with `StorageResolution=1`).
  - **Basic vs detailed monitoring (EC2)**: basic = 5-minute metrics, free; **detailed = 1-minute**, paid. Most other services publish 1-minute metrics by default.
  - **Retention (automatic roll-up)**: sub-minute data 3 hours; 1-minute data **15 days**; 5-minute data **63 days**; 1-hour data **455 days (15 months)**.
  - **Custom metrics**: `PutMetricData` (API/CLI/Software Development Kit [SDK]), the CloudWatch agent, or **Embedded Metric Format (EMF)**: structured JavaScript Object Notation (JSON) log lines that CloudWatch Logs turns into metrics automatically (great for Lambda: no extra Application Programming Interface [API] calls). Timestamps accepted from 2 weeks in the past to 2 hours in the future.
  - **Metric math**: expressions across metrics (e.g., error rate = `100 * errors / requests`), usable in graphs and alarms. **Metrics Insights**: SQL-like queries over metrics (e.g., top 10 instances by Central Processing Unit [CPU]) for dashboards/alarms.
  - **[Anomaly detection](00-cloudops-basics.md#baseline-and-anomaly)**: Machine Learning (ML) band of expected values; [alarm](00-cloudops-basics.md#alarm) when outside the band.
  - **Contributor Insights**: top-N contributors from logs (e.g., IP addresses [IPs] causing most 5xx, heaviest DynamoDB keys).
- **Metrics EC2 does *not* send by default (exam favorite):** **memory utilization, disk space used, swap, per-process metrics**. These need the **CloudWatch agent**. EC2 does send CPU, network in/out, packets, disk read/write ops/bytes (instance store only), status checks, CPU credit metrics (T family), and EBS metrics come from `AWS/EBS`.
- **Exam triggers:** "monitor memory usage of EC2" → **CloudWatch agent**. "1-minute granularity for EC2" → **detailed monitoring**. "Sub-minute custom metric" → **high-resolution** custom metric. "Lambda emits business metrics without extra [latency](00-cloudops-basics.md#iops-throughput-and-latency)" → **EMF**.

### Monitoring by workload type (skill 1.1.1)

| Workload | Key signals and tools |
|---|---|
| **EC2** | `CPUUtilization`, `StatusCheckFailed_System` / `_Instance` / `_AttachedEBS`, `CPUCreditBalance` (T), network; agent for memory/disk; CloudWatch Logs via agent |
| **Lambda** | `Invocations`, `Errors`, `Throttles`, `Duration`, `ConcurrentExecutions`, `IteratorAge` (stream sources), `AsyncEventAge`, `DeadLetterErrors`; logs automatically to `/aws/lambda/<name>`; **Lambda Insights** (system-level metrics via extension); **X-Ray active tracing**; JSON log format and log-level filtering |
| **API Gateway** | `Count`, `4XXError`, `5XXError`, `Latency`, `IntegrationLatency`, `CacheHitCount`; access logs and execution logs per stage |
| **ECS / EKS** | **Container Insights** (cluster/service/task/pod CPU, memory, network, restarts; enhanced observability for EKS via the CloudWatch Observability add-on); Prometheus metrics; FireLens/Fluent Bit for logs |
| **Application Load Balancer (ALB)/Network Load Balancer (NLB)** | `RequestCount`, `TargetResponseTime`, `HTTPCode_Target_5XX_Count`, `HTTPCode_ELB_5XX_Count`, `HealthyHostCount`, `UnHealthyHostCount`, `RejectedConnectionCount`, `SurgeQueueLength` (CLB); access logs to S3 |
| **RDS/Aurora** | `CPUUtilization`, `FreeableMemory`, `DatabaseConnections`, `ReadIOPS`/`WriteIOPS`, `ReadLatency`/`WriteLatency`, `FreeStorageSpace`, `ReplicaLag`, `DiskQueueDepth`, `BurstBalance`; **Enhanced Monitoring** (OS-level, down to 1 s, from an agent on the host); **Performance Insights / Database Insights** (DB load by waits, Structured Query Language [SQL], users) |
| **DynamoDB** | `ConsumedRead/WriteCapacityUnits`, `ThrottledRequests`, `ReadThrottleEvents`, `SystemErrors`, `SuccessfulRequestLatency`; Contributor Insights for hot keys |
| **Simple Queue Service (SQS)** | `ApproximateNumberOfMessagesVisible`, `ApproximateAgeOfOldestMessage`, `NumberOfMessagesSent/Deleted` |
| **AI (Amazon Bedrock)** | `Invocations`, `InvocationLatency`, `InvocationThrottles`, `InvocationClientErrors`/`ServerErrors`, `InputTokenCount`/`OutputTokenCount`; **model invocation logging** (full requests/responses to CloudWatch Logs and/or S3, off by default); CloudTrail for Bedrock API calls; CloudWatch [generative AI](00-cloudops-basics.md#generative-ai-and-foundation-models) observability dashboards for agent workloads |

### Amazon Managed Service for Prometheus and Amazon Managed Grafana (skill 1.1.1)

- **Prometheus**: open-source monitoring for containers/Kubernetes: apps expose metrics; a scraper collects them; **Prometheus Query Language (PromQL)** queries them; **Alertmanager** handles alert routing.
- **Amazon Managed Service for Prometheus (AMP)**: serverless, Prometheus-compatible **workspace** storing metrics (150-day default retention, configurable); ingest via **remote write** from Prometheus servers, the **AWS Distro for OpenTelemetry (ADOT) collector**, or the **AMP managed (agentless) collector** for EKS; alerting rules and Alertmanager to SNS; Identity and Access Management [IAM] (SigV4) auth; Virtual Private Cloud (VPC) endpoints.
- **Amazon Managed Grafana (AMG)**: managed **Grafana** dashboards with data sources CloudWatch, AMP, X-Ray, OpenSearch, Athena, Timestream, Redshift and third-party; users via IAM Identity Center or Security Assertion Markup Language (SAML).
- **Exam triggers:** "EKS cluster already instrumented with Prometheus; want managed storage and alerting" → **AMP** (+ AMG for dashboards). "Unified dashboards over CloudWatch and Prometheus" → **AMG**.
- **Vs CloudWatch Container Insights:** Container Insights is AWS-native and turnkey; AMP fits teams standardized on Prometheus/PromQL.

### AWS CloudTrail for monitoring (skill 1.1.1) `[also: DOP]`

- **What it is & key terms:** records **API calls** (who, what, when, from which Internet Protocol [IP], with which credentials, result/`errorCode`).
  - **Event history**: 90 days of **management events**, free, per Region, viewable/searchable in console.
  - **Trails**: deliver events to an **S3 bucket** (typically within 5 minutes; not real time), optionally to **CloudWatch Logs** (for metric filters/alarms) and EventBridge. **Multi-Region trails** (recommended) and **organization trails** (all accounts; members can't modify).
  - **Event types**: **management** (control plane, e.g., `RunInstances`, `PutBucketPolicy`; read/write selectable), **data events** (high volume: S3 object-level, Lambda `Invoke`, DynamoDB item-level, others; off by default, extra cost; **advanced event selectors** filter them), **network activity events** (calls through VPC endpoints), **Insights events** (unusual API call rate or error rate).
  - **Log file integrity validation**: hourly **digest files** (SHA-256 hashes, signed) prove logs weren't modified or deleted; validate with `aws cloudtrail validate-logs`.
  - **CloudTrail Lake**: managed event data store with SQL queries, long retention, can ingest non-AWS events and Config items.
  - Encrypt with **Server-Side Encryption with KMS keys (SSE-KMS)**; protect the bucket (Object Lock, [Multi-Factor Authentication [MFA]](00-cloudops-basics.md#mfa) Delete, separate log-archive account).
- **Exam triggers:** "alert within minutes when someone deletes a security group / root logs in / IAM policy changes" → trail to **CloudWatch Logs + metric filter + alarm → SNS** or an **EventBridge rule** on the CloudTrail API event. "Prove logs weren't tampered with" → **log file validation**. "S3 object access audit" → **data events**.
- **Gotchas:** global service events (IAM, Security Token Service [STS], CloudFront) are logged in **us-east-1**. If a trail's bucket policy doesn't allow `cloudtrail.amazonaws.com` to write, delivery fails (check `LatestDeliveryError`).

### Amazon CloudWatch Logs (skill 1.1.1) `[also: DOP]`

- **What it is & key terms:**
  - **Log group** (e.g., `/aws/lambda/app`) → **log streams** (one per source, e.g., per instance/container) → **log events**.
  - **Retention**: default **never expire**; set 1 day to 10 years per group (cost control).
  - **Log classes**: **Standard** (all features) and **Infrequent Access** (cheaper ingestion, fewer features; for logs rarely queried).
  - **Metric filters**: turn matching log events into metrics (count `ERROR`, extract latency); **not retroactive** (only new events after creation).
  - **Subscription filters**: stream log events in near real time to **Lambda, Kinesis Data Streams, Data Firehose or OpenSearch** (up to 2 per log group), including cross-account destinations. Account-level subscription policies exist.
  - **CloudWatch Logs Insights**: interactive query language (`fields`, `filter`, `stats`, `sort`, `parse`) across log groups; saved queries; natural-language query generation.
  - **Live Tail**: stream logs live in the console while debugging.
  - **Export to S3**: `CreateExportTask` batch export (can take up to 12 hours; one active task per account); for continuous delivery use a **Firehose subscription** instead.
  - **Encryption**: associate a **Key Management Service (KMS) key** with the log group (key policy must allow `logs.<region>.amazonaws.com`).
  - **Data protection policies**: detect and **mask** sensitive data (Personally Identifiable Information [PII], credentials) in logs.
  - **Log anomaly detection** and **pattern analysis** surface unusual log patterns.
- **Exam triggers:** "count application errors and alarm" → **metric filter + alarm**. "Stream logs to OpenSearch/S3 continuously" → **subscription filter** (Firehose). "Logs costs too high" → retention + Infrequent Access class. "Search last hour of logs across services quickly" → **Logs Insights**.

### The CloudWatch agent (skill 1.1.2) `[also: DOP]`

- **What it is:** the unified agent that collects **system-level metrics** (memory, disk, swap, netstat, processes via `procstat`, Graphics Processing Unit [GPU] via nvidia-smi), **custom metrics** (StatsD, collectd), and **log files** from EC2, on-prem servers, ECS and EKS, publishing to the `CWAgent` namespace (configurable).
- **Install and configure (EC2/on-prem):**
  1. **IAM**: instance role with **`CloudWatchAgentServerPolicy`** (and `AmazonSSMManagedInstanceCore` to manage via Systems Manager). On-prem: an IAM user/role credentials profile or **Systems Manager hybrid activation**.
  2. **Install**: Systems Manager **Run Command `AWS-ConfigureAWSPackage`** (package `AmazonCloudWatchAgent`) or Distributor, State Manager association to keep it installed, or packages directly.
  3. **Configure**: JSON config file (sections `agent`, `metrics`, `logs`) created with the **configuration wizard** (`amazon-cloudwatch-agent-config-wizard`) and **stored in Systems Manager (SSM) Parameter Store** (name starts with `AmazonCloudWatch-`) so the whole fleet pulls the same config.
  4. **Start/fetch config**: `amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c ssm:<parameter-name> -s` or the `AmazonCloudWatch-ManageAgent` SSM document.
- **Config details:** `metrics_collection_interval` (down to 1 s for high resolution), `append_dimensions` (e.g., `InstanceId`, `AutoScalingGroupName`), `aggregation_dimensions` (e.g., aggregate by Auto Scaling group [ASG]), `logs_collected.files.collect_list` (file path, log group, stream name like `{instance_id}`), Windows Event Logs and performance counters.
- **Containers:** **ECS**: enable **Container Insights** on the cluster (and the agent as a daemon service for EC2 launch type instance-level metrics); logs via the `awslogs` driver or **FireLens** (Fluent Bit). **EKS**: **Amazon CloudWatch Observability EKS add-on** installs the agent + Fluent Bit as DaemonSets for **Container Insights with enhanced observability** and Application Signals; IAM Roles for Service Accounts (IRSA)/Pod Identity for permissions.
- **Troubleshooting:** no metrics → check agent running (`amazon-cloudwatch-agent-ctl -a status`), IAM permissions, config errors in `/opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log`, network path to CloudWatch endpoints (Network Address Translation [NAT] or **interface VPC endpoints** `monitoring` and `logs`), correct Region, time sync.
- **Exam triggers:** "collect memory/disk metrics from 500 instances with one standard configuration" → agent config in **Parameter Store** + install via **Run Command/State Manager**. "Instances in [private subnet](00-cloudops-basics.md#public-vs-private-subnet) without internet don't send metrics" → **VPC endpoints** for CloudWatch/Logs (or NAT).

### CloudWatch alarms (skill 1.1.3) `[also: DOP]`

- **What it is & key terms:**
  - **States**: `OK`, `ALARM`, `INSUFFICIENT_DATA`.
  - **Period**, **evaluation periods** (N most recent periods), **datapoints to alarm** (M of N; e.g., 3 of 5 to avoid flapping).
  - **Missing data treatment**: `missing` (default; keeps state / insufficient data), `notBreaching` (treat as good), `breaching` (treat as bad), `ignore` (keep current state). Pick `notBreaching` for sparse metrics like error counts that only report when errors happen; `breaching` when silence means failure (heartbeat metrics).
  - **Threshold types**: static, **anomaly detection band**, **metric math** expression, **Metrics Insights query** alarms (one alarm across many resources).
  - **High-resolution alarms**: periods of 10 or 30 s (on high-resolution metrics).
  - **Percentile alarms**: option to evaluate low-sample-count percentiles.
- **Alarm actions (what alarms can invoke directly):**
  - **SNS** topic (notify; fan out to email/SMS/Lambda/SQS/HTTP/chat).
  - **EC2 Auto Scaling** policies (step/simple scaling) and **Application Auto Scaling**.
  - **EC2 actions**: **stop, terminate, reboot, recover** (recover only on `StatusCheckFailed_System`; keeps instance ID, private IP, Elastic IP address [EIP], metadata; EBS-backed instances).
  - **Lambda function** (direct invocation).
  - **Systems Manager**: create an **OpsCenter OpsItem** or start an **Incident Manager** [incident](00-cloudops-basics.md#incident-mttd-and-mttr).
  - Different actions per state (ALARM, OK, INSUFFICIENT_DATA).
  - Anything else → **EventBridge**: every alarm state change emits an event (`CloudWatch Alarm State Change`) that rules can route to Step Functions, SSM Automation, etc.
- **Composite alarms:** combine other alarms with a **rule expression** (`ALARM("HighCPU") AND ALARM("HighLatency")`, OR, NOT) to cut noise; **actions suppression** (mute during a known condition, e.g., deployment). Composite alarm actions: **SNS, Lambda, Systems Manager OpsItems/incidents**; they **can't** perform EC2 or Auto Scaling actions directly (use the underlying metric alarms or EventBridge for those).
- **Troubleshooting alarms:**
  - Stuck in **INSUFFICIENT_DATA** → metric not published (agent down, wrong dimension, wrong Region, basic monitoring 5-min metric with 1-min period), or missing data treatment.
  - Alarm in ALARM but **no notification** → SNS subscription not confirmed, SNS topic access policy, **SSE-KMS-encrypted SNS topic using the AWS managed key** (`aws/sns`) which CloudWatch can't use → use a **customer managed key** whose policy allows `cloudwatch.amazonaws.com` to `kms:Decrypt`/`GenerateDataKey*`.
  - Alarm flaps → increase evaluation periods / M-of-N, use anomaly detection, composite alarm.
  - EC2 recover action not available → instance type/storage not supported (instance store) or wrong metric.
  - Alarm history in console/`DescribeAlarmHistory` shows state changes and action failures.
- **Exam triggers:** "reboot an instance automatically when the instance status check fails" → alarm on `StatusCheckFailed_Instance` with **reboot** action. "Hardware/host failure → move to healthy host" → `StatusCheckFailed_System` + **recover** (default **automatic instance recovery** is also on for supported types). "Only alert when CPU high AND latency high" → **composite alarm**. "Run SSM Automation when alarm fires" → alarm → **EventBridge rule** → Automation (or OpsItem/Incident Manager runbook).

### CloudWatch dashboards (skill 1.1.4) `[also: DOP]`

- **What it is:** customizable pages of widgets (line, number, gauge, bar, pie, text, logs query, alarm status, explorer). Global (not Regional): one dashboard can show **metrics from multiple Regions and accounts**.
- **Cross-account**: **CloudWatch cross-account observability**: link **source accounts** to a **monitoring account** (via Observability Access Manager **sinks** and **links**, set up for the whole organization) to view metrics, logs, traces and Application Signals centrally. (Legacy: the CloudWatch cross-account console with a sharing role.)
- **Sharing**: share a dashboard with people **without AWS console access** (public link, or specified email addresses with Cognito-backed sign-in, or Single Sign-On [SSO] via SAML Identity Provider [IdP]).
- **Automatic dashboards** per service; **variables** (switch between instances/Regions in one dashboard); **dashboards as code** (CloudFormation `AWS::CloudWatch::Dashboard`, `PutDashboard` API with JSON body).
- Pricing: per dashboard per month beyond free tier (3 dashboards).
- **Exam triggers:** "operations team needs one dashboard for metrics in 20 accounts and 3 Regions" → **cross-account observability + cross-Region dashboard**. "Give external stakeholders read-only dashboard access without IAM users" → **dashboard sharing**.

### Sending notifications to Amazon SNS (skill 1.1.5) `[also: DOP]`

- **What it is:** SNS **topics** push messages to subscriptions: email, SMS, HTTP/S, Lambda, SQS, Firehose, mobile push; chat integrations through **Amazon Q Developer in chat applications** (formerly AWS Chatbot) for Slack/Teams.
- **AWS services that publish to SNS (directly)**: CloudWatch alarms, Auto Scaling group notifications (launch/terminate/fail), CloudFormation stack events, **S3 event notifications**, RDS **event subscriptions**, ElastiCache events, AWS Budgets, AWS Config (delivery channel), **AWS Backup** vault notifications, Elastic Beanstalk, CodePipeline/CodeCommit notifications, Systems Manager (Run Command/Maintenance Windows status), Database Migration Service (DMS), Redshift events, Directory Service. Everything else (e.g., **AWS Health** events, **GuardDuty findings**, Security Hub, Trusted Advisor) → **EventBridge rule → SNS**.
- **Permissions:** the **topic access policy** must allow the publishing service principal (e.g., `s3.amazonaws.com`, `events.amazonaws.com`) with `aws:SourceArn`/`aws:SourceAccount` conditions; if the topic is encrypted, the **KMS key policy** must allow that service too (AWS managed `aws/sns` key can't be used by other services).
- **Message filtering** (subscription filter policies) and **message formatting** per protocol; **delivery retries** and **Dead-Letter Queue (DLQ)** per subscription; delivery status logging to CloudWatch Logs.
- **Exam triggers:** "notify ops when an RDS failover happens" → **RDS event subscription → SNS**. "Notify when AWS schedules maintenance on our instances" → **EventBridge rule on AWS Health → SNS**. "S3 event notification to SNS fails with permission error" → fix **topic policy** (and KMS key policy).

---

## Task 1.2: Identify and remediate issues

### Analyze metrics and automate remediation (skill 1.2.1) `[also: DOP]`

Common detect → act pipelines:

| Detect | Route | Remediate |
|---|---|---|
| CloudWatch alarm (metric) | Alarm action / EventBridge | SNS, EC2 reboot/recover, Auto Scaling, Lambda, SSM Automation, OpsItem, Incident Manager response plan |
| CloudTrail API event (e.g., security group opened) | EventBridge rule | Lambda or SSM Automation reverts the change |
| AWS Config rule non-compliant | Config **automatic remediation** | SSM Automation document (e.g., enable S3 encryption, close Secure Shell [SSH]) |
| AWS Health event (scheduled retirement) | EventBridge | Automation stops/starts instance to move hosts |
| GuardDuty/Security Hub finding | EventBridge | Lambda/Step Functions isolates instance, revokes keys |
| Log pattern (errors) | Metric filter → alarm, or subscription filter → Lambda | Restart service via Run Command |
| Trusted Advisor check | EventBridge | Lambda remediation |

- **Systems Manager OpsCenter**: central place for **OpsItems** (operational issues) created by alarms, EventBridge, Config, Security Hub; shows related resources, runbooks to run.
- **Systems Manager Incident Manager**: incident response: **response plans**, escalation/on-call contacts, chat channels, **runbook automation**, post-incident analysis; triggered by alarms or EventBridge.
- **AWS DevOps Agent** (in scope since v1.1): an AWS "[frontier agent](00-cloudops-basics.md#ai-agents)" for production operations and release management. It **investigates incidents automatically** (triggered from tickets in PagerDuty/ServiceNow or from chat), maps your application topology, correlates alarms, finds **root causes**, proposes **mitigation plans** (including rollbacks), and recommends improvements in observability, infrastructure, deployment pipelines and resilience. It integrates with CloudWatch, Datadog, Dynatrace, New Relic, Splunk, Grafana, GitHub/GitLab/Azure DevOps, Slack/Teams, and can be extended through [Model Context Protocol [MCP]](00-cloudops-basics.md#mcp) servers. Exam angle: "accelerate root cause analysis and mitigation of production incidents with an AI agent" → DevOps Agent.
- **Kiro** (in scope since v1.1): AWS's **agentic AI development environment** (IDE, Command Line Interface [CLI], web). It works from **specs** (requirements → design → tasks), **steering** files (project rules) and **hooks** (automations triggered by events like file save), can run headless in CI, and connects to tools through **MCP**. Operational angle: generate or fix [Infrastructure as Code [IaC]](00-cloudops-basics.md#infrastructure-as-code-iac), scripts and runbooks, and turn investigation findings into code changes with review.
- **AI-assisted analysis features in CloudWatch**: natural-language query generation for Logs Insights/Metrics Insights, log anomaly detection, CloudWatch investigations (AI-assisted root-cause investigation of alarms).
- **Exam triggers:** "automatically restart the web service when its process dies" → agent `procstat` metric → alarm → EventBridge → **Run Command/Automation**. "Ensure every new S3 bucket has encryption; fix automatically" → **Config rule + auto-remediation**.

### Amazon EventBridge: route, enrich, deliver, troubleshoot (skill 1.2.2) `[also: DOP]`

- **What it is & key terms:**
  - **Event bus**: default (AWS service events), custom (your apps), partner (SaaS). **Cross-account/cross-Region** delivery by targeting another bus (receiving bus policy must allow).
  - **Rule**: **event pattern** (JSON matching on `source`, `detail-type`, `detail` fields; supports prefix, suffix, anything-but, numeric ranges, exists, wildcard, IP [Classless Inter-Domain Routing [CIDR]](00-cloudops-basics.md#cidr-and-subnet-sizing) matching) or **schedule** (legacy; prefer **EventBridge Scheduler**).
  - **Targets** (up to 5 per rule): Lambda, SNS, SQS, Step Functions, Kinesis, Firehose, ECS tasks, Batch, SSM Automation/Run Command/OpsItem, CodePipeline/CodeBuild, API destinations (Hypertext Transfer Protocol [HTTP] endpoints with auth and rate limits), another event bus, CloudWatch Logs, Redshift Data API, SageMaker pipelines, Incident Manager.
  - **Enrich/transform**: **input transformer** (pick fields, build a custom message), constant input, input path. **EventBridge Pipes** for source → filter → **enrichment** (Lambda, Step Functions, API Gateway, API destination) → target.
  - **Delivery**: retry policy (default **24 hours, up to 185 attempts** with backoff), **DLQ (SQS) per target**, **archive and replay**.
  - **Schema registry/discovery** for event structures.
- **Event examples:** `aws.ec2` "EC2 Instance State-change Notification", `aws.autoscaling` lifecycle actions, `aws.health` "AWS Health Event", `aws.guardduty` "GuardDuty Finding", `aws.config` "Config Rules Compliance Change", `aws.cloudwatch` "CloudWatch Alarm State Change", `aws.s3` "Object Created" (requires **EventBridge enabled on the bucket**), and **"AWS API Call via CloudTrail"** for nearly any API.
- **Troubleshooting rules (skill 1.2.2):**
  1. **Pattern doesn't match**: field names/values are **case-sensitive** and must match the event JSON exactly; values must be in arrays (`"source": ["aws.ec2"]`); test with the **sandbox / `TestEventPattern`**.
  2. **Wrong bus or Region**: rule must be on the bus/Region where the event is emitted; IAM/CloudFront/STS API events appear in **us-east-1**.
  3. **CloudTrail-based events missing**: "AWS API Call via CloudTrail" events require **CloudTrail** management event logging for those calls (a trail); read-only (`Get*`/`Describe*`/`List*`) calls aren't delivered by default.
  4. **S3 events missing**: turn on **Amazon EventBridge** notifications in the bucket properties.
  5. **Target permissions**: Lambda/SNS/SQS need a **resource-based policy** allowing `events.amazonaws.com` (console adds it; IaC must add it); Kinesis, Step Functions, ECS, SSM Automation, API destinations, cross-account buses need an **IAM role** that EventBridge assumes. Encrypted SQS/SNS targets need a KMS key policy for EventBridge.
  6. **Metrics**: `MatchedEvents`, `TriggeredRules`, `Invocations`, **`FailedInvocations`**, `ThrottledRules`, `DeadLetterInvocations`, `InvocationsSentToDlq`; configure a DLQ to capture failed deliveries with error codes.
  7. Rule **disabled**, or input transformer produces invalid JSON.
- **Exam triggers:** "invoke automation whenever an EC2 instance stops" → rule on `aws.ec2` state change. "React to API call `AuthorizeSecurityGroupIngress`" → CloudTrail-based rule. "Send to a third-party HTTP API with auth" → **API destination**. "Rule matches but Lambda never runs" → missing **Lambda resource policy**.

### Systems Manager Automation runbooks (skill 1.2.3) `[also: DOP]`

- **What it is & key terms:**
  - **SSM documents** (JSON/YAML) types: **Command** (Run Command, State Manager: scripts on instances), **Automation** (runbooks: multi-step workflows calling AWS Application Programming Interfaces [APIs]), **Session**, **Package** (Distributor), **Policy**, **Change Calendar**.
  - **Automation runbook actions**: `aws:executeAwsApi` (call any API), `aws:executeScript` (Python/PowerShell inline), `aws:runCommand` (run Command doc on instances), `aws:invokeLambdaFunction`, `aws:createImage`, `aws:runInstances`, `aws:changeInstanceState`, `aws:waitForAwsResourceProperty`, `aws:assertAwsResourceProperty`, `aws:branch` (conditional), `aws:approve` (human approval via SNS), `aws:sleep`, `aws:executeStateMachine`, `aws:createStack`/`aws:deleteStack`.
  - **Parameters** and **`AutomationAssumeRole`** (IAM role the runbook uses; needs `iam:PassRole` for the caller).
  - **Predefined runbooks** (owned by Amazon), e.g., `AWS-RestartEC2Instance`, `AWS-StopEC2Instance`, `AWS-CreateImage`, `AWS-CreateSnapshot`, `AWS-UpdateLinuxAmi` / `AWS-UpdateWindowsAmi` (patch and create a new Amazon Machine Image [AMI]), `AWS-PatchInstanceWithRollback`, `AWS-DisablePublicAccessForSecurityGroup`, `AWSSupport-ExecuteEC2Rescue` (fix unreachable instances), `AWSSupport-TroubleshootSSH`, `AWSSupport-ResetAccess` (reset lost key/password), many `AWSConfigRemediation-*` runbooks.
  - **Execution modes**: simple, **rate control** (targets by tags/resource groups/parameter values, **concurrency** and **error threshold** to roll out safely across a fleet), **multi-account and multi-Region** (from a delegated admin/management account with execution roles in targets), manual step-by-step.
  - **Ways to start**: console, CLI/SDK (`StartAutomationExecution`), **EventBridge** target, **Config remediation**, **State Manager association** (scheduled), **Maintenance Windows** task, Incident Manager response plan, OpsCenter, Change Manager (with approvals), CloudFormation.
  - **Custom runbooks**: write [YAML Ain't Markup Language [YAML]](00-cloudops-basics.md#yaml-and-json), share across accounts (document sharing) or publicly; version documents; Automation Document Builder.
- **Related capabilities (operational automation):** **Run Command** (one-off commands across fleets without SSH, rate control, output to S3/CloudWatch Logs), **State Manager** ([desired state](00-cloudops-basics.md#desired-state-configuration) associations on a schedule), **Maintenance Windows** (time windows for disruptive tasks), **Patch Manager**, **Change Calendar** (block changes during freeze periods), **Change Manager** (approval workflows).
- **Exam triggers:** "create a patched [golden AMI](00-cloudops-basics.md#golden-image-golden-ami) monthly" → **`AWS-UpdateLinuxAmi`** on a schedule (or EC2 Image Builder). "Run a fix across 1,000 instances, 10% at a time, stop if 5% fail" → Automation/Run Command **rate control**. "Reset SSH key on an unreachable instance" → **`AWSSupport-ResetAccess`/EC2Rescue**. "Need approval before the runbook terminates resources" → **`aws:approve`** step.

---

## Task 1.3: Performance optimization

### Compute optimization (skill 1.3.1)

- **AWS Compute Optimizer**: ML recommendations from 14 days (up to 93 with enhanced metrics) of CloudWatch data for **EC2 instances, Auto Scaling groups (ASGs), EBS volumes, Lambda memory, ECS on Fargate task sizes, RDS/Aurora instances, commercial software licenses, idle resources**; classifies as under-provisioned, over-provisioned, optimized; shows performance risk; include **memory metrics from the CloudWatch agent** for accurate sizing; organization-wide via delegated admin.
- **Resource tags for optimization**: tag by app/env/owner to find and act on waste (stop `Environment=dev` at night with Instance Scheduler or SSM; group in **Resource Groups**; target patching/automation by tag; cost allocation tags in Cost Explorer).
- **Instance choices**: switch to newer generations/Graviton for price-performance; T instances: watch **`CPUCreditBalance`** (zero → throttled to baseline; unlimited mode adds charges); memory-bound → R; CPU-bound → C.
- **Lambda**: right-size memory (CPU scales with memory), provisioned concurrency for latency-sensitive cold starts, reserved concurrency to protect downstream.
- **Exam triggers:** "find over-provisioned instances across accounts" → **Compute Optimizer**. "T3 instance slows down every afternoon" → **[CPU credits](00-cloudops-basics.md#burst-credits) exhausted** → unlimited mode or M instance.

### EBS performance (skill 1.3.2) `[also: SAA]`

- **Volume types:**

| Type | Input/Output Operations Per Second (IOPS) | Throughput | Notes |
|---|---|---|---|
| **gp3** | 3,000 baseline at any size; up to 16,000 (raised to 80,000 in 2025) | 125 MiB/s baseline; up to 1,000 (raised to 2,000) | Default; set IOPS/throughput independently of size |
| **gp2** | 3 IOPS/GiB, min 100, max 16,000; burst to 3,000 for < 1 TiB via **BurstBalance** | up to 250 MiB/s | Size-linked; migrate to gp3 |
| **io2 Block Express** | up to 256,000 (1,000 IOPS/GiB) | up to 4,000 MiB/s | Sub-ms latency, 99.999% durability, Multi-Attach |
| **io1** | up to 64,000 | up to 1,000 MiB/s | Legacy Provisioned IOPS (PIOPS) |
| **st1** | — | up to 500 MiB/s (burst-credit based) | Sequential big data; not bootable |
| **sc1** | — | up to 250 MiB/s | Cold data; not bootable |

- **Metrics (`AWS/EBS`)**: `VolumeReadOps`/`VolumeWriteOps` (÷ period seconds = IOPS), `VolumeReadBytes`/`VolumeWriteBytes` (throughput), `VolumeTotalReadTime`/`WriteTime` (latency), **`VolumeQueueLength`** (pending I/O; consistently high = volume saturated), **`BurstBalance`** (gp2/st1/sc1 credits %), `VolumeIdleTime`, **`VolumeThroughputPercentage`** and **`VolumeConsumedReadWriteOps`** (io1/io2), `VolumeStalledIOCheck`. **Instance-level** EBS limits: `EBSIOBalance%`, `EBSByteBalance%` (smaller instances burst EBS bandwidth).
- **Troubleshooting:**
  - Performance dropped after a while on gp2 → **BurstBalance** hit 0 → switch to **gp3** with provisioned IOPS (or bigger gp2).
  - IOPS capped below the volume's rating → **instance EBS bandwidth limit** (choose an EBS-optimized/larger instance) or I/O size mismatch.
  - Slow first reads on a volume restored from [snapshot](00-cloudops-basics.md#backup-vs-snapshot-vs-replication) → **[lazy loading](00-cloudops-basics.md#lazy-loading-from-snapshots)** → **initialize** (read all blocks with `fio`/`dd`) or enable **Fast Snapshot Restore**.
  - High latency + high queue length → provision more IOPS / io2 / Redundant Array of Independent Disks (RAID) 0 striping.
- **Optimize cost and performance**: **Elastic Volumes** (change type/size/IOPS online; wait 6 hours between modifications; extend the file system after growing), gp2→gp3 (~20% cheaper), st1/sc1 for sequential/cold, delete unattached volumes, snapshot archive.
- **Exam triggers:** "database volume latency spikes, `VolumeQueueLength` high, gp2 BurstBalance 0" → **gp3/io2 with provisioned IOPS**. "Change volume type without downtime" → **Elastic Volumes**.

### S3 performance strategies (skill 1.3.3) `[also: SAA]`

- **Request scaling**: ≥ **3,500 PUT/COPY/POST/DELETE** and **5,500 GET/HEAD** per second **per prefix**; spread keys across prefixes for higher totals; `503 Slow Down` → retry with [exponential backoff](00-cloudops-basics.md#throttling) while S3 scales.
- **Multipart upload**: recommended > 100 MB, required > 5 GB; parallel parts (5 MB–5 GB, up to 10,000); retry failed parts; CLI does it automatically (`multipart_threshold`, `max_concurrent_requests` settings); lifecycle rule to **abort incomplete multipart uploads**.
- **S3 Transfer Acceleration**: edge-optimized uploads/downloads over long distances (`s3-accelerate` endpoint); test with the speed comparison tool.
- **Byte-range fetches**: parallel partial downloads.
- **AWS DataSync**: high-speed, scheduled, verified transfer from on-prem NFS/SMB/Hadoop Distributed File System (HDFS)/object storage or other clouds into S3/EFS/FSx; parallel, compression, integrity checks, bandwidth throttle, incremental; also S3-to-S3 across Regions/accounts.
- **S3 Lifecycle policies**: move cold data to IA/Glacier classes, expire old data/versions, abort incomplete uploads (efficiency and cost).
- **Others**: **CloudFront** for read-heavy global access, **S3 Express One Zone** for single-digit-ms access in one Availability Zone (AZ), **S3 Batch Operations** for bulk actions, **S3 Inventory** instead of huge LIST operations, SSE-KMS **Bucket Keys** to avoid KMS throttling at high request rates.
- **Exam triggers:** "users worldwide upload large videos slowly" → **Transfer Acceleration + multipart**. "Migrate 200 TB of Network File System (NFS) data to S3 over the network with verification" → **DataSync**. "KMS throttling on S3 uploads" → **S3 Bucket Keys**.

### Shared storage selection and optimization (skill 1.3.4) `[also: SAA]`

| Need | Choose | Optimization levers |
|---|---|---|
| Shared Linux NFS, elastic, multi-AZ | **Amazon EFS** | **Lifecycle policies** (Standard → Infrequent Access [IA] → Archive after N days; back to Standard on access), **Elastic throughput** (default) vs Provisioned vs Bursting, General Purpose performance mode, One Zone for non-critical, access points |
| Windows Server Message Block (SMB), Active Directory (AD), Distributed File System (DFS) | **FSx for Windows File Server** | Solid-State Drive (SSD) vs Hard Disk Drive (HDD), throughput capacity setting, data deduplication, shadow copies, [Multi-AZ](00-cloudops-basics.md#multi-az) for High Availability (HA) |
| HPC/ML, S3-linked, hundreds of GB/s | **FSx for Lustre** | Scratch vs persistent, throughput per TiB, data repository association with S3 |
| NetApp, NFS+SMB+Internet Small Computer Systems Interface (iSCSI) | **FSx for NetApp ONTAP** | SSD tier + **capacity pool tiering**, dedup/compression, SnapMirror |
| File access to data that lives in S3 | **Amazon S3 Files** | S3 Files is a **[shared file system](00-cloudops-basics.md#block-file-and-object-storage) that connects AWS compute (instances, containers, functions) directly to data in S3**, giving full file-system semantics and low-latency access **without the data leaving S3**. Choose it when apps need file APIs over existing S3 datasets instead of copying data into EFS/FSx |

- **EFS metrics**: `PercentIOLimit` (General Purpose mode near 100% → consider Max I/O/Elastic), `BurstCreditBalance` (Bursting mode), `MeteredIOBytes`, `ClientConnections`, `StorageBytes` by class. Bursting mode running out of credits → switch to **Elastic** throughput.
- **FSx metrics**: `DataReadBytes`/`DataWriteBytes`, throughput utilization, `FreeStorageCapacity`; increase throughput capacity or storage as needed.
- **Exam triggers:** "EFS costs high, most files untouched after 30 days" → **EFS lifecycle to IA/Archive**. "EFS throughput throttled, spiky workload" → **Elastic throughput**. "Windows app needs shared storage with AD permissions" → **FSx for Windows**.

### RDS monitoring and tuning (skill 1.3.5) `[also: SAA]`

- **CloudWatch metrics** (above) + alarms on `CPUUtilization`, `FreeableMemory`, `FreeStorageSpace`, `DatabaseConnections`, `ReplicaLag`, `ReadLatency`/`WriteLatency`, `DiskQueueDepth`.
- **Enhanced Monitoring**: OS-level metrics (processes, memory breakdown, per-process CPU) at 1–60 s granularity, delivered to CloudWatch Logs (`RDSOSMetrics`); needs an IAM role.
- **Performance Insights**: visualizes **DB load** (average active sessions) broken down by **wait events, SQL statements, hosts, users**; find the top SQL causing load; **proactive recommendations** (detects issues like high load or bad configuration and suggests fixes). Its capabilities are being consolidated into **CloudWatch Database Insights** (standard and advanced modes, fleet-wide views).
- **RDS recommendations** in the console (e.g., upgrade engine, enable Multi-AZ, storage autoscaling).
- **Tuning moves**: scale up instance class (memory for buffer cache), **read replicas** for read load, **RDS Proxy** for [connection storms](00-cloudops-basics.md#connection-pooling)/Lambda and faster failover, **Provisioned IOPS/gp3 IOPS** for I/O waits, **storage autoscaling**, parameter group tuning (e.g., `max_connections`, which depends on instance memory), indexes/query fixes found via Performance Insights, **ElastiCache** for repeated reads, **Optimized Reads/Writes**.
- **RDS Proxy**: pooled connections, IAM auth, Secrets Manager credentials, reduces failover time (up to 66%), works with Lambda; monitor `DatabaseConnections`, `ClientConnections`, `QueryDatabaseResponseLatency`.
- **Exam triggers:** "identify which SQL query drives high CPU" → **Performance Insights / Database Insights**. "Per-process Operating System (OS) metrics on RDS" → **Enhanced Monitoring**. "Too many connections from Lambda" → **RDS Proxy**.

### EC2 instance, storage and network optimization (skill 1.3.6) `[also: SAA]`

- **Placement groups**: **cluster** (one AZ, low latency/high throughput, High-Performance Computing [HPC]), **spread** (separate hardware, max 7 running instances per AZ per group), **partition** (separate racks per partition, up to 7 per AZ, for HDFS/Cassandra/Kafka). Errors: `InsufficientInstanceCapacity` in cluster groups → launch all instances in one request, use one instance type, retry, or stop/start all instances. Move an existing instance into a placement group: stop it, `modify-instance-placement`, start.
- **Networking**: **Enhanced networking** with **Elastic Network Adapter [ENA]** (up to 100+ Gbps, low latency; needs ENA driver and supported instance), **Elastic Fabric Adapter (EFA)** for HPC/ML (OS-bypass, Message Passing Interface [MPI]/NCCL), jumbo frames (Maximum Transmission Unit [MTU] 9001) inside VPC, instance-type network bandwidth limits (`NetworkPacketsOut`, `bw_in_allowance_exceeded` ENA metrics show throttling), same-AZ placement for chatty traffic.
- **Storage**: EBS-optimized instances (dedicated EBS bandwidth), instance store Non-Volatile Memory Express (NVMe) for scratch, RAID 0 for more EBS performance.
- **CPU options**: change virtual CPU (vCPU) count/disable hyperthreading (licensing per core), Nitro-based instances.
- **Monitoring**: detailed monitoring, status checks, agent metrics, Compute Optimizer.
- **Exam triggers:** "lowest latency between HPC nodes" → **cluster placement group + EFA/ENA**. "Instances must not share hardware" → **spread**. "Network throughput lower than expected" → **ENA not enabled / instance bandwidth limit / cross-AZ placement**.

---

## Gotchas and exam traps

- **Memory and disk metrics need the CloudWatch agent**.
- EC2 basic monitoring = **5 minutes**; detailed = 1 minute.
- **Metric filters aren't retroactive**; export to S3 isn't real time (use Firehose subscription).
- Alarm stuck in **INSUFFICIENT_DATA** → metric not arriving or period mismatch.
- **Composite alarms** can't do EC2/Auto Scaling actions; they can notify SNS, invoke Lambda, create OpsItems/incidents.
- **Recover** action only on `StatusCheckFailed_System`; **reboot** for instance check failures.
- Encrypted SNS topic + CloudWatch alarm → needs a **customer managed KMS key** allowing CloudWatch.
- EventBridge rule not firing → case-sensitive pattern, wrong Region/bus (global services in **us-east-1**), missing target permissions, S3 needs EventBridge enabled, API events need CloudTrail.
- CloudTrail S3 delivery ≈ 5 minutes; for fast alerts use EventBridge or CloudWatch Logs metric filters.
- **gp2 BurstBalance 0** → performance cliff; migrate to gp3.
- Volumes from snapshots are **lazy-loaded**; use Fast Snapshot Restore (FSR) or initialize.
- **Performance Insights** = which SQL/waits; **Enhanced Monitoring** = OS-level.
- Automation runbooks need an **assume role** and the caller needs **`iam:PassRole`**.

## Quick-recall summary

- **See**: CloudWatch metrics (namespaces, dimensions, resolutions, retention), agent (memory/disk/logs, config in Parameter Store), Logs (retention, metric/subscription filters, Insights, Live Tail, masking), CloudTrail (management/data events, validation, Lake), AMP/AMG, X-Ray, Container Insights, Lambda Insights, Bedrock metrics/invocation logs, dashboards (cross-account observability, sharing).
- **Alert**: alarms (M of N, missing data, anomaly detection, composite), actions (SNS, EC2 reboot/recover/stop/terminate, Auto Scaling, Lambda, OpsItem/Incident), SNS permissions (topic + KMS policies).
- **Fix automatically**: EventBridge (patterns, targets, transformers, Pipes, DLQ, troubleshooting), SSM Automation (predefined/custom runbooks, rate control, approvals, multi-account), Config remediation, OpsCenter, Incident Manager, DevOps Agent, Kiro.
- **Tune**: Compute Optimizer, tags, T credits; EBS types/metrics/Elastic Volumes/FSR; S3 prefixes/multipart/Transfer Acceleration/DataSync/lifecycle; EFS lifecycle/throughput modes, FSx, S3 Files; RDS Performance Insights/Enhanced Monitoring/RDS Proxy; placement groups, ENA/EFA.
