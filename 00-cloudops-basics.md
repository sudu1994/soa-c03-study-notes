# CloudOps Basics (the 101 layer)

> Plain-English explanations of the operations, monitoring, automation, networking and security ideas the SOA-C03 notes rely on. Cloud Practitioner basics are left out. In the domain files, the first mention of each concept links back here. Abbreviations are in [00-abbreviations-glossary.md](00-abbreviations-glossary.md).

## How SOA questions differ from SAA

SAA asks *which design*. Start of Authority (SOA) asks *how do you operate, fix, automate and prove it*: which metric shows the problem, which setting to change, which log tells you why, which automation fixes it without a human. Expect "the alarm doesn't fire," "the stack is stuck," "the instance can't reach the internet," "users get 403 from CloudFront" style questions.

| Constraint word | Usually means |
|---|---|
| "least operational overhead" / "automate" | Managed feature or Systems Manager/EventBridge automation over custom scripts |
| "most cost-effective" | Cheapest option that still meets *all* requirements |
| "troubleshoot" / "identify the cause" | The specific log, metric or tool that reveals it (Flow Logs, Reachability Analyzer, CloudTrail, policy simulator) |
| "across all accounts/Regions" | Organizations + StackSets / delegated admin / aggregators / cross-account observability |
| "near real time" | EventBridge, CloudWatch Logs subscription, metric filters (not CloudTrail Simple Storage Service [S3] delivery, which lags) |

---

## Monitoring concepts

### Metric
A number measured over time, e.g., `CPUUtilization` every minute. In CloudWatch a metric lives in a **namespace** (`AWS/EC2`) and is identified by **dimensions** (`InstanceId=i-123`).

### Statistics and percentiles
How data points in a period are summarized: **Average**, **Sum**, **Minimum**, **Maximum**, **SampleCount**, and **percentiles** like **p99** ("99% of requests were faster than this"). Use p99/p95 for latency (averages hide slow outliers), Sum for counts (errors, requests), Maximum for spikes.

### Period and resolution
**Period** = the time bucket a statistic covers (60 s, 300 s). **Standard resolution** = 1-minute granularity; **high resolution** = down to 1-second (custom metrics only).

### Alarm
A rule watching one metric (or a math expression) that changes state when a threshold is crossed for a number of periods, and then takes **actions** (notify, scale, stop/reboot/recover an instance, run automation).

### Logs, log aggregation and structured logs
**Logs** are timestamped event records. **Aggregation** sends them from many sources to one place (CloudWatch Logs) to search and alert. **Structured logs** (JavaScript Object Notation [JSON] fields) are much easier to query and to turn into metrics than free text.

### Traces
A record of one request's path through many services with timing for each hop. Used to find which component is slow or failing (X-Ray).

### Dashboards
A page of graphs and numbers for at-a-glance health. Good dashboards show the user-facing signals first (errors, latency, traffic, saturation: the "golden signals").

### Baseline and anomaly
A **baseline** is normal behavior for a metric (often with daily/weekly patterns). An **anomaly** is a deviation from it. Anomaly detection alarms use a learned band instead of a fixed threshold.

### Synthetic vs real-user monitoring
**Synthetic** monitoring runs scripted checks on a schedule (a fake user logs in every 5 minutes). **Real-user monitoring (RUM)** measures what actual users experience in their browsers.

### Health check
A periodic test (Hypertext Transfer Protocol [HTTP] 200 on `/health`, Transmission Control Protocol [TCP] connect) used by load balancers, Route 53 and Auto Scaling to decide whether something receives traffic or gets replaced.

### Remediation
Fixing a detected problem. **Automated remediation** = a rule detects it and a runbook or Lambda function fixes it without a human (e.g., Config detects an open security group → Systems Manager [SSM] Automation closes it).

### Runbook and playbook
A **runbook** is a step-by-step procedure for a routine task (restart service, rotate log). A **playbook** is a procedure for investigating an incident. In Amazon Web Services (AWS), **Systems Manager Automation runbooks** (documents) make runbooks executable.

### Incident, MTTD and MTTR
An **incident** is an unplanned interruption. **MTTD** (mean time to detect) and **MTTR** (mean time to recover/repair) measure operations quality; good alarms lower MTTD, automation lowers MTTR.

---

## Automation and infrastructure concepts

### Infrastructure as Code (IaC)
Describing infrastructure in files (templates/code) so it can be created, changed and deleted repeatably, reviewed in Git and rolled back. CloudFormation, Cloud Development Kit (CDK) and Terraform are Infrastructure as Code (IaC) tools.

### Declarative vs imperative
**Declarative** = describe the desired end state; the tool works out the steps (CloudFormation, Terraform). **Imperative** = list the steps to run (scripts, Command Line Interface [CLI] commands).

### Drift
When real resources no longer match their IaC definition because someone changed them by hand. Drift detection finds it.

### Idempotent
Running an operation twice gives the same result as once. IaC and good automation scripts are idempotent, so retries are safe.

### Desired state configuration
Continuously enforcing that machines stay in a defined configuration (e.g., agent installed, port closed), re-applying it if it drifts (Systems Manager State Manager).

### Golden image (golden AMI)
A pre-built, patched, hardened machine image with standard software baked in. Instances launched from it start fast and consistent. Rebuilt on a schedule as new patches arrive (Elastic Compute Cloud [EC2] Image Builder).

### Immutable vs mutable infrastructure
**Mutable**: patch and change servers in place. **Immutable**: never change running servers; build a new image and replace them. Immutable avoids configuration drift and makes rollback simple.

### Deployment strategies
- **All at once / in place**: update every server together; fastest, has downtime risk.
- **Rolling**: update in batches; capacity temporarily reduced.
- **Rolling with additional batch**: launch extra capacity first so full capacity is kept.
- **Immutable**: launch a full new set of instances, switch when healthy.
- **Blue/green**: two full environments; switch traffic from blue (old) to green (new); instant rollback.
- **Canary**: send a small % of traffic to the new version, then the rest.
- **Linear**: shift traffic in equal steps over time.

### Configuration management
Keeping software settings and installed packages consistent across fleets (Systems Manager, Ansible, Chef).

### Git basics
**Git** is version control: a **repository** holds files and their history; a **commit** is a saved change; a **branch** is a separate line of work; a **pull request (merge request)** proposes merging a branch after review. **GitOps** means the Git repository is the source of truth and pipelines apply what's merged.

### Terraform basics
HashiCorp's IaC tool using HCL (HashiCorp Configuration Language). **Providers** talk to Application Programming Interfaces [APIs] (the AWS provider). **State** records what Terraform created; store it remotely in **S3** with **state locking** (DynamoDB table or S3 native lock file) so two people can't apply at once. `terraform plan` previews, `terraform apply` changes, **modules** are reusable building blocks.

### YAML and JSON
Two text formats for structured data. CloudFormation templates, policies and SSM documents use them. YAML Ain't Markup Language (YAML) relies on indentation (mistakes cause template errors); JSON uses braces and quotes.

### Cron and rate expressions
Schedule formats. **Cron**: `cron(0 3 * * ? *)` = 03:00 UTC daily (AWS cron has six fields: minutes, hours, day-of-month, month, day-of-week, year). **Rate**: `rate(5 minutes)`.

### Tags
Key-value labels on resources (`Environment=prod`). Used for cost allocation, automation targeting (patch all `PatchGroup=web`), access control (ABAC) and organization.

---

## Reliability concepts

### Scalability vs elasticity
**Scalability**: the system *can* grow to handle more load. **Elasticity**: it grows *and shrinks* automatically with demand.

### Horizontal vs vertical scaling
**Horizontal** = more instances (scale out/in). **Vertical** = bigger instance (scale up/down), usually needs a stop/start.

### High availability vs fault tolerance
**High availability**: recovers quickly from failure, short interruption possible. **Fault tolerance**: keeps working with no interruption because redundant capacity is already running.

### Multi-AZ
Running copies in two or more Availability Zones (separate data centers in one Region) so one data-center failure doesn't take the workload down.

### RPO and RTO
**RPO (Recovery Point Objective)**: maximum acceptable data loss, measured in time. **RTO (Recovery Time Objective)**: maximum acceptable downtime. Backup frequency drives RPO; restore speed and standby resources drive RTO.

### Backup vs snapshot vs replication
A **snapshot** is a point-in-time copy of a volume/DB. A **backup** is any restorable copy kept for a retention period (snapshots are one kind). **Replication** continuously copies data elsewhere. Replication alone isn't a backup: deletes and corruption replicate too.

### Point-in-time recovery (PITR)
Restoring a database to any second within a retention window, using a full backup plus transaction logs.

### Versioning
Keeping previous versions of objects/files so overwritten or deleted data can be recovered.

### Disaster recovery strategies
From cheapest/slowest to most expensive/fastest: **backup and restore**, **pilot light** (data replicated, servers off), **warm standby** (small working copy running), **active/active** (full capacity in multiple Regions).

### Caching
Keeping copies of frequently used data in a faster layer (memory, edge) to reduce latency and load. **TTL** (time to live) controls how long a cached item is valid. **Cache hit ratio** = share of requests served from cache; higher is better.

### Throttling
A service rejecting requests above its rate limit (HTTP 429 or `ThrottlingException`). Fix with retries with **exponential backoff and jitter**, request quota increases, or smoothing traffic (queues).

### Service quotas
Per-account, per-Region limits (e.g., virtual CPU [vCPU] limits for On-Demand instances, number of Virtual Private Clouds [VPCs]). Many are adjustable via **Service Quotas**; some are hard limits.

---

## Storage and performance concepts

### Block, file and object storage
**Block** (EBS): a disk for one instance. **File** (Elastic File System [EFS], FSx, S3 Files): a shared file system mounted by many clients. **Object** (S3): files stored by key via HTTP Application Programming Interface (API).

### IOPS, throughput and latency
**Input/Output Operations Per Second (IOPS)**: number of read/write operations per second (small random I/O, databases). **Throughput**: MB/s moved (large sequential I/O). **Latency**: time per operation. **Queue length**: requests waiting; a long queue means the volume can't keep up.

### Burst credits
Some resources (T instances, gp2 volumes, EFS Bursting mode, st1/sc1) earn credits while idle and spend them to exceed their baseline. When credits run out, performance drops to baseline: a classic "it was fast, then suddenly slow" cause.

### Lazy loading from snapshots
A volume restored from a snapshot pulls blocks from S3 the first time they're read, so first access is slow. Fix by reading every block once (initialization) or enabling Fast Snapshot Restore.

### Connection pooling
Reusing a set of open database connections instead of opening new ones per request; protects databases from connection storms (Relational Database Service [RDS] Proxy).

---

## Networking concepts

### CIDR and subnet sizing
`10.0.1.0/24` = 256 addresses; AWS reserves 5 per subnet, so 251 usable. Smaller prefix number = bigger range. Running out of IP addresses (IPs) in a subnet ("insufficient free addresses") blocks launches, Lambda Elastic Network Interfaces (ENIs), Elastic Kubernetes Service (EKS) pods and load balancer scaling. Fix: larger/additional subnets, secondary Classless Inter-Domain Routing (CIDR) on the Virtual Private Cloud (VPC).

### Route table and longest prefix match
Each subnet uses a route table. The **most specific** matching route wins (`10.0.1.0/24` beats `10.0.0.0/16` beats `0.0.0.0/0`).

### Public vs private subnet
A **public** subnet routes `0.0.0.0/0` to an internet gateway (instances also need a public Internet Protocol [IP]). A **private** subnet doesn't; it reaches the internet through a Network Address Translation (NAT) gateway.

### Stateful vs stateless filtering
**Stateful** (security groups): return traffic is automatically allowed. **Stateless** (network Access Control Lists [ACLs]): you must allow return traffic yourself, usually **ephemeral ports 1024–65535**.

### Ports and protocols
TCP (reliable), User Datagram Protocol [UDP] (fast, connectionless), Internet Control Message Protocol [ICMP] (ping). Common ports: 22 Secure Shell (SSH), 80 HTTP, 443 Hypertext Transfer Protocol Secure (HTTPS), 3389 Remote Desktop Protocol (RDP), 3306 MySQL, 5432 PostgreSQL, 2049 Network File System (NFS), 53 Domain Name System (DNS), 6379 Redis.

### DNS, records and TTL
DNS turns names into IPs. Record TTL controls how long resolvers cache answers; after a change, old answers can persist until the TTL expires.

### HTTP status codes
**2xx** success; **3xx** redirect; **4xx** client-side problem (400 bad request, **401** not authenticated, **403** forbidden/denied, **404** not found, **429** throttled); **5xx** server-side problem (**500** internal error, **502** bad gateway: the proxy got an invalid response from the backend, **503** service unavailable: no healthy capacity, **504** gateway timeout: the backend took too long).

### TLS and certificates
Transport Layer Security (TLS) encrypts traffic in transit (HTTPS). A certificate, issued by a certificate authority, proves domain ownership; it expires and must be renewed. TLS termination is where traffic is decrypted (load balancer, CloudFront, instance).

### BGP
Border Gateway Protocol: routers exchange which IP ranges they can reach. Used by Direct Connect and dynamic Virtual Private Networks (VPNs); if the Border Gateway Protocol (BGP) session is down, routes disappear and traffic stops.

### CDN, origin and cache key
A Content Delivery Network [CDN] (CloudFront) serves cached copies from edge locations. The **origin** is the real source (S3, Application Load Balancer [ALB]). The **cache key** is what makes a cached object unique (Uniform Resource Locator [URL] path plus chosen headers, cookies, query strings); including too much lowers the hit ratio.

### Anycast
One IP address announced from many locations; users reach the nearest one (Global Accelerator).

---

## Security concepts

### Authentication vs authorization
**Authentication**: proving identity (password, Multi-Factor Authentication [MFA], keys). **Authorization**: what an identity may do (policies).

### Least privilege
Grant only the permissions needed, then tighten using usage data.

### Explicit vs implicit deny
Everything is **implicitly denied** unless allowed. An **explicit Deny** in any policy overrides every Allow.

### MFA
Multi-factor authentication: password plus a second factor (authenticator app, hardware key).

### Federation
Signing in with an existing identity (corporate directory via Security Assertion Markup Language [SAML] 2.0, or OpenID Connect [OIDC] providers) instead of separate AWS users.

### Encryption at rest and in transit
At rest: data stored on disk is encrypted with keys (KMS). In transit: data on the network is encrypted (TLS, Internet Protocol Security [IPsec]).

### Envelope encryption
Data is encrypted with a data key; the data key is encrypted with a KMS key. Only the small data key goes to KMS.

### Data classification
Labeling data by sensitivity (public, internal, confidential, restricted) and applying matching controls (encryption, access, retention). Implemented with tags, separate accounts/buckets, Macie discovery and policies keyed on tags.

### Compliance as code / continuous compliance
Rules that automatically and continuously check resources against requirements (AWS Config rules, conformance packs, Security Hub standards) instead of periodic manual audits.

### Detective vs preventive vs responsive controls
**Preventive** stops a bad action (Service Control Policy [SCP], Identity and Access Management [IAM] deny). **Detective** finds it after it happens (Config rule, GuardDuty). **Responsive/corrective** fixes it (automated remediation).

---

## AI in operations

### Generative AI and foundation models
Large pre-trained models (foundation models) that generate text/code. **Amazon Bedrock** provides them through an API. Operationally you monitor invocations, latency, token usage, errors and throttles like any other workload.

### AI agents
Software that uses a model to plan and take multi-step actions with tools (e.g., query logs, open tickets, create a pull request), usually with human approval steps. **AWS DevOps Agent** (incident investigation and operations), **AWS Security Agent** (penetration testing and code security; now marketed under AWS Continuum) and **Kiro** (agentic development environment) are AWS examples in the SOA-C03 scope.

### MCP
Model Context Protocol: an open standard for connecting Artificial Intelligence (AI) agents to tools and data sources.
