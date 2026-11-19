# SOA-C03 Cheat Sheet

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> Rapid review: "if the scenario says X → do Y," top traps, and numbers. 65 questions (50 scored), 130 minutes, pass 720/1000. Domains: Monitoring/Remediation/Performance 22%, Reliability 22%, Deployment/Automation 22%, Security 16%, Networking 18%.

## Keyword → answer decoder

### Monitoring and remediation

| If the scenario says… | Do… |
|---|---|
| Monitor Elastic Compute Cloud (EC2) memory / disk space | **CloudWatch agent** (config in Parameter Store, install via Run Command/State Manager) |
| 1-minute EC2 metrics | **Detailed monitoring** |
| Sub-minute custom [metric](00-cloudops-basics.md#metric) | **[High-resolution](00-cloudops-basics.md#period-and-resolution)** metric (1 s) |
| Lambda business metrics cheaply | **Embedded Metric Format** |
| Count ERROR lines and alert | **Metric filter → [alarm](00-cloudops-basics.md#alarm) → Simple Notification Service (SNS)** |
| Stream logs to S3/OpenSearch continuously | **Subscription filter → Firehose** |
| Query logs ad hoc | **Logs Insights** |
| Alarm only when two conditions both true | **Composite alarm** |
| Reboot on instance status check failure | Alarm `StatusCheckFailed_Instance` → **reboot** |
| Host hardware failure | `StatusCheckFailed_System` → **recover** (or automatic recovery) |
| Alarm never notifies, topic encrypted | Use **customer managed Key Management Service (KMS) key** allowing CloudWatch |
| Alarm stuck INSUFFICIENT_DATA | Metric not arriving / wrong dimension / period |
| One dashboard for many accounts and Regions | **Cross-account observability** + cross-Region dashboard |
| Share dashboard with non-AWS users | **Dashboard sharing** |
| Notify on Amazon Web Services (AWS) scheduled maintenance | **EventBridge (aws.health) → SNS** |
| Relational Database Service (RDS) failover notification | **RDS event subscription → SNS** |
| React to an Application Programming Interface (API) call (e.g., Security Group [SG] change) | **EventBridge rule on CloudTrail event** → Lambda/SSM |
| EventBridge rule matches nothing | Case-sensitive pattern, wrong Region/bus, CloudTrail not on, Simple Storage Service (S3) EventBridge off |
| Rule fires but Lambda not invoked | Missing **Lambda resource policy** |
| Run a fix across 1,000 instances safely | **Systems Manager (SSM) Automation/Run Command rate control** |
| Patched golden Amazon Machine Image (AMI) on schedule | **Image Builder** (or `AWS-UpdateLinuxAmi`) |
| Unreachable instance, lost key | **`AWSSupport-ResetAccess` / EC2Rescue** |
| Which Structured Query Language (SQL) is slow on RDS | **Performance Insights / Database Insights** |
| OS-level RDS metrics | **Enhanced Monitoring** |
| Too many DB connections from Lambda | **RDS Proxy** |
| Right-size EC2/EBS/Lambda | **Compute Optimizer** |
| gp2 slow after a while ([BurstBalance](00-cloudops-basics.md#burst-credits) 0) | **gp3 with provisioned [Input/Output Operations Per Second [IOPS]](00-cloudops-basics.md#iops-throughput-and-latency)** |
| Change Elastic Block Store (EBS) type without downtime | **Elastic Volumes** |
| Slow first reads after restore | **Fast Snapshot Restore** / initialize |
| Global large uploads to S3 | **Transfer Acceleration + multipart** |
| Migrate Network File System (NFS) data to S3/EFS with verification | **DataSync** |
| Elastic File System (EFS) costs high for cold files | **EFS lifecycle → Infrequent Access (IA)/Archive** |
| Spiky EFS throughput throttled | **Elastic throughput** |
| File access to existing S3 data | **Amazon S3 Files** |
| Windows file shares with Active Directory (AD) | **FSx for Windows** |
| Lowest-latency High-Performance Computing (HPC) nodes | **Cluster placement group + Elastic Network Adapter (ENA)/Elastic Fabric Adapter (EFA)** |
| AI-assisted [incident](00-cloudops-basics.md#incident-mttd-and-mttr) investigation | **AWS DevOps Agent** |
| [Bedrock](00-cloudops-basics.md#generative-ai-and-foundation-models) usage/latency/throttle monitoring | Bedrock **CloudWatch metrics + model invocation logging** |
| Kubernetes metrics with Prometheus Query Language (PromQL) | **Managed Service for Prometheus** (+ Managed Grafana) |

### Reliability and backup

| If the scenario says… | Do… |
|---|---|
| Keep Central Processing Unit (CPU) at 50% | **Target tracking** |
| Known weekly peaks | **Scheduled / predictive scaling** |
| Slow-booting instances | **Warm pool** |
| Run script before termination | **Lifecycle hook** |
| Roll out new AMI to Auto Scaling group (ASG) gradually | **Instance refresh** (+ auto rollback) |
| Instances replaced in a loop after launch | Raise **[health check](00-cloudops-basics.md#health-check) grace period** / fix health check |
| Targets unhealthy but app works | Target **SG must allow LB**; health check path/codes |
| Cache full, high Evictions | Scale ElastiCache up/out |
| RDS storage nearly full | **Storage autoscaling** |
| Aurora read load varies | **Aurora Auto Scaling replicas / Serverless v2** |
| DynamoDB spiky [throttling](00-cloudops-basics.md#throttling) | **On-demand** (or auto scaling); check **hot keys/Global Secondary Index (GSI)** |
| Health check private resource in Route 53 | **CloudWatch alarm-based health check** |
| Route 53 checks fail but site works | Allow **Route 53 health checker IP addresses (IPs)** |
| Shift traffic away from impaired Availability Zone (AZ) | **Application Recovery Controller (ARC) zonal shift / autoshift** |
| DB survives AZ failure automatically | **RDS [Multi-AZ](00-cloudops-basics.md#multi-az)** |
| Network Address Translation (NAT) outage when one AZ fails | **NAT gateway per AZ** |
| Centralized backups across services/accounts | **AWS Backup** + org backup policies |
| [Immutable](00-cloudops-basics.md#immutable-vs-mutable-infrastructure) backups | **Backup Vault Lock ([compliance](00-cloudops-basics.md#compliance-as-code--continuous-compliance))** |
| Prove backup compliance | **Backup Audit Manager** |
| Automated EBS [snapshots](00-cloudops-basics.md#backup-vs-snapshot-vs-replication) by tag | **Data Lifecycle Manager** |
| Recover deleted snapshot/AMI | **Recycle Bin** |
| Undo bad Aurora MySQL change in place | **Backtrack** |
| Restore to before accidental delete | **[Point-In-Time Recovery [PITR]](00-cloudops-basics.md#point-in-time-recovery-pitr)** (creates new instance/table) |
| Users recover overwritten S3 objects | **[Versioning](00-cloudops-basics.md#versioning)** |
| Versioned bucket growing | **Noncurrent version expiration** |
| Windows users restore previous file versions | **FSx shadow copies** |
| Disaster Recovery (DR) cheapest / minutes Recovery Point Objective (RPO) / minutes [Recovery Time Objective [RTO]](00-cloudops-basics.md#rpo-and-rto) / zero | Backup & restore / **pilot light** / **[warm standby](00-cloudops-basics.md#disaster-recovery-strategies)** / **active-active** |

### Deployment and automation

| If the scenario says… | Do… |
|---|---|
| Preview whether update replaces resource | **Change set** |
| Keep DB when stack deleted | **DeletionPolicy Retain/Snapshot** |
| Prevent updates to critical resource | **Stack policy** |
| Prevent stack deletion | **Termination protection** |
| Detect manual changes | **[Drift](00-cloudops-basics.md#drift) detection** |
| Wait for bootstrap before stack completes | **CreationPolicy + cfn-signal** |
| Stack `UPDATE_ROLLBACK_FAILED` | Fix → **ContinueUpdateRollback** |
| Stack `ROLLBACK_COMPLETE` | Delete and recreate |
| Stack stuck ~1 h | Custom resource/cfn-signal not responding |
| Requires capabilities error | **CAPABILITY_IAM / NAMED_IAM / AUTO_EXPAND** |
| Secrets in templates | **Dynamic references** |
| First Cloud Development Kit (CDK) deploy fails | **`cdk bootstrap`** |
| Deploy baseline to all accounts incl. new | **Service-managed StackSets + auto-deploy** |
| Share subnets/Transit Gateway (TGW)/Resolver rules | **AWS RAM** |
| [Terraform](00-cloudops-basics.md#terraform-basics) state corruption from concurrent runs | **S3 backend + state locking** |
| Pods/Lambda can't get IPs | **Subnet exhaustion** → add [Classless Inter-Domain Routing [CIDR]](00-cloudops-basics.md#cidr-and-subnet-sizing)/subnets |
| Zero downtime with instant rollback | **[Blue/green](00-cloudops-basics.md#deployment-strategies)** |
| 10% then 100% Lambda traffic | **CodeDeploy canary** on alias |
| Auto-rollback failing Elastic Container Service (ECS) deployment | **Deployment circuit breaker** |
| Patch fleet on schedule | **Patch Manager + Maintenance Window** (or patch policy) |
| Keep agent installed on all instances | **State Manager association** |
| Instance not managed by SSM | Agent / **instance profile** / **SSM endpoints** |
| Nightly stop of dev instances | **EventBridge Scheduler → SSM Automation** |
| Process S3 uploads instantly | **S3 event → Lambda/Simple Queue Service [SQS]** (EventBridge for advanced) |

### Security and compliance

| If the scenario says… | Do… |
|---|---|
| Password rotation and reuse rules | **Identity and Access Management (IAM) password policy** |
| Require [Multi-Factor Authentication [MFA]](00-cloudops-basics.md#mfa) | Deny unless **`aws:MultiFactorAuthPresent`** |
| Test a policy before attaching | **IAM policy simulator** |
| Find resources shared externally | **IAM Access Analyzer** |
| Least-privilege policy from usage | **Access Analyzer policy generation** |
| Encoded authorization failure | **`sts decode-authorization-message`** |
| Audit key age/MFA for all users | **Credential report** |
| Block unapproved Regions | **Service Control Policy (SCP) with `aws:RequestedRegion`** |
| Workforce Single Sign-On (SSO) with corporate Identity Provider (IdP) | **IAM Identity Center** (+ System for Cross-domain Identity Management [SCIM]) |
| Trusted Advisor flags open Secure Shell (SSH) | Restrict SG; Config rule + [remediation](00-cloudops-basics.md#remediation) |
| Automate on Trusted Advisor results | **EventBridge (us-east-1) → Lambda** |
| Org-wide Payment Card Industry (PCI)/Center for Internet Security (CIS) rule bundle | **Config conformance pack** |
| Auto-fix non-compliant resources | **Config remediation (SSM Automation)** |
| Find Personally Identifiable Information (PII) in S3 | **Macie** |
| Enforce [classification](00-cloudops-basics.md#data-classification) [tags](00-cloudops-basics.md#tags) | **Tag policies + Config required-tags + SCP** |
| All new EBS encrypted | **EBS encryption by default** |
| Encrypt existing RDS | Snapshot → **copy encrypted** → restore |
| Share encrypted AMI/snapshot | **Customer managed key** shared |
| ASG can't launch encrypted volumes | Key policy must allow **Auto Scaling service-linked role** |
| Auto-renewing [certificates](00-cloudops-basics.md#tls-and-certificates) | **AWS Certificate Manager (ACM) + [Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl) validation** |
| Imported cert expiring | **Re-import**; alarm on `DaysToExpiry`/EventBridge |
| Rotate DB passwords | **Secrets Manager** |
| Cheap config + secure strings | **Parameter Store SecureString** |
| Isolate instance on GuardDuty finding | **EventBridge → Lambda/SSM quarantine SG** |
| Central findings + score | **Security Hub** (delegated admin, cross-Region aggregation) |
| Continuous Common Vulnerabilities and Exposures (CVE) scanning | **Inspector** |
| Automated pen testing with fix PRs | **AWS Security Agent** |

### Networking

| If the scenario says… | Do… |
|---|---|
| Private instance needs internet | **NAT gateway** ([public subnet](00-cloudops-basics.md#public-vs-private-subnet)) + route |
| Internet Protocol version 6 (IPv6) outbound only | **Egress-only Internet Gateway (IGW)** |
| S3 without internet/NAT | **Gateway endpoint** |
| SSM/CloudWatch/KMS without NAT | **Interface endpoints** (SG 443, private DNS) |
| Provide service to many Virtual Private Clouds (VPCs), overlapping Classless Inter-Domain Routing ranges (CIDRs) | **PrivateLink endpoint service** (NLB) |
| Why can't A reach B? (config) | **Reachability Analyzer** |
| Find unintended internet paths | **Network Access Analyzer** |
| Inbound ACCEPT, outbound REJECT | **Network Access Control List (NACL) [ephemeral ports](00-cloudops-basics.md#stateful-vs-stateless-filtering)** |
| On-prem resolves AWS private zone | **Resolver inbound endpoint** |
| Virtual Private Cloud (VPC) resolves on-prem domain | **Outbound endpoint + forwarding rule** |
| Log DNS queries from VPC | **Resolver query logging** |
| Apex to Application Load Balancer (ALB) | **Alias** |
| Domain doesn't resolve after new zone | Fix **registrar Name Server (NS)** records |
| Web Application Firewall (WAF) blocked legit request | **WAF logs `terminatingRuleId`**, Count mode |
| Block malicious domains at DNS | **DNS Firewall** (managed lists) |
| ALB vs target errors | **ALB access logs** / `HTTPCode_ELB_5XX` vs `HTTPCode_Target_5XX` |
| Low CloudFront hit ratio | Narrow **[cache key](00-cloudops-basics.md#cdn-origin-and-cache-key)** (cache policy), TTLs |
| Stale CloudFront content | **Versioned names** or invalidation |
| CloudFront [403](00-cloudops-basics.md#http-status-codes) with S3 | **Origin Access Control (OAC)** bucket policy / KMS policy / default root object |
| CloudFront 502 | Origin **Transport Layer Security (TLS) certificate** issue |
| CloudFront 504 | Origin timeout / firewall |
| Virtual Private Network (VPN) up, no traffic | **Route propagation**/routes, SG/NACL |
| VPN drops when idle | **Dead Peer Detection (DPD)/keepalive, startup action** |
| Client VPN users can't reach VPC | **Authorization rules + routes** |
| ISP issues affecting users | **Internet Monitor** |
| Packet loss to on-prem over Direct Connect (DX) | **Network Synthetic Monitor** |
| Retransmissions between services in AWS | **Network Flow Monitor** |
| NAT high bill from S3 traffic | **S3 gateway endpoint** |

## Top traps

1. Memory/disk metrics need the **agent**.
2. **Metric filters** aren't retroactive.
3. Composite alarms can't do **EC2/Auto Scaling actions**.
4. **Recover** = system check; **reboot** = instance check.
5. Global service events (IAM, CloudFront) in **us-east-1**.
6. EventBridge targets need **resource policies or roles**.
7. Launch **configurations** are deprecated.
8. Unhealthy targets → **SG doesn't allow LB**.
9. **PITR/snapshot restores create new resources**.
10. **Replication isn't backup**.
11. `ROLLBACK_COMPLETE` → delete; `UPDATE_ROLLBACK_FAILED` → continue rollback.
12. **CDK bootstrap** per account/Region.
13. Service Control Policies (SCPs) don't affect the **management account**.
14. **AWS managed keys** can't be shared.
15. **Imported ACM certs** don't renew; CloudFront certs in **us-east-1**.
16. **Network Access Control Lists (NACLs) stateless**; timeout = drop, refused = host reached.
17. **Gateway endpoints** don't work from on-prem.
18. Elastic Load Balancing (ELB) access logs **off by default**.
19. Low CloudFront hit ratio = cache key too broad.
20. VPN "up but no traffic" = **routes**.

## Numbers

| Item | Value |
|---|---|
| CloudWatch retention | < 60 s: 3 h; 1 min: 15 days; 5 min: 63 days; 1 h: 455 days |
| EC2 basic / detailed monitoring | 5 min / 1 min |
| Logs subscription filters | 2 per log group |
| Logs export task | up to 12 h, not real time |
| CloudTrail event history | 90 days (management events) |
| CloudTrail S3 delivery | ~5 minutes |
| EventBridge retries | up to 24 h / 185 attempts |
| ASG cooldown / grace period | 300 s default |
| Lifecycle hook timeout | 1 h default |
| ELB health check interval / deregistration delay | 30 s / 300 s |
| Route 53 health check interval | 30 s standard, 10 s fast; threshold 3 |
| RDS backup retention / Multi-AZ failover | 0–35 days / 60–120 s (cluster < 35 s) |
| Aurora replicas / failover | 15 / ~30 s; Backtrack up to 72 h |
| DynamoDB PITR | up to 35 days |
| Backup Vault Lock compliance grace | ≥ 72 h |
| KMS key deletion wait | 7–30 days; rotation 90–2,560 days (default 365) |
| ACM renewal starts / expiry events | 60 days / from 45 days before |
| Parameter Store | 4 KB standard, 8 KB advanced |
| Secrets Manager secret | 64 KB |
| Subnet reserved IPs | 5 |
| SG rules / Security Groups (SGs) per Elastic Network Interface (ENI) | 60 in + 60 out / 5 |
| NAT gateway | 5–100 Gbps, 55,000 connections per destination per Internet Protocol (IP), 350 s idle timeout |
| VPN | 2 tunnels, ~1.25 Gbps each |
| CloudFront origin response timeout | 30 s default; error [caching](00-cloudops-basics.md#caching) 10 s default |
| S3 per prefix | 3,500 writes / 5,500 reads per second |
| gp3 baseline | 3,000 IOPS / 125 MiB/s |
| Spread placement group | 7 instances per AZ |
