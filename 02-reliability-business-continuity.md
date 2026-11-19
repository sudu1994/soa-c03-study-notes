# Domain 2: Reliability and Business Continuity (22%)

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> **About 11 of the 50 scored questions.**
>
> Tests whether you can keep workloads up as load changes (scaling, [caching](00-cloudops-basics.md#caching), database scaling), survive component and Availability Zone (AZ) failures (load balancer and Route 53 health checks, [Multi-AZ](00-cloudops-basics.md#multi-az)), and recover data and whole environments (backups, [snapshots](00-cloudops-basics.md#backup-vs-snapshot-vs-replication), [point-in-time](00-cloudops-basics.md#point-in-time-recovery-pitr) restore, [versioning](00-cloudops-basics.md#versioning), Disaster Recovery [DR] strategies).
>
> [Tags](00-cloudops-basics.md#tags) like `[also: DOP]` mark material you met in another exam. It is still covered in full here.

## Table of contents

- [Exam tasks and skills](#exam-tasks-and-skills)
- [Task 2.1: Scalability and elasticity](#task-21-scalability-and-elasticity)
- [Task 2.2: Highly available and resilient environments](#task-22-highly-available-and-resilient-environments)
- [Task 2.3: Backup and restore strategies](#task-23-backup-and-restore-strategies)
- [Gotchas and exam traps](#gotchas-and-exam-traps)
- [Quick-recall summary](#quick-recall-summary)

## Exam tasks and skills

> Summarized from the official SOA-C03 exam guide (v1.2). Exact wording: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain2.html

### Task 2.1: Implement scalability and elasticity
- **2.1.1** Configure and manage scaling mechanisms in compute environments.
- **2.1.2** Implement caching to enhance dynamic scalability (CloudFront, ElastiCache).
- **2.1.3** Configure and manage scaling in managed databases (Relational Database Service [RDS], DynamoDB).

### Task 2.2: Implement highly available and resilient environments
- **2.2.1** Configure and troubleshoot Elastic Load Balancing (ELB) and Route 53 health checks.
- **2.2.2** Configure [fault-tolerant](00-cloudops-basics.md#high-availability-vs-fault-tolerance) systems (Multi-AZ deployments).

### Task 2.3: Implement backup and restore strategies
- **2.3.1** Automate snapshots and backups (Elastic Compute Cloud [EC2], RDS, Elastic Block Store [EBS], Simple Storage Service [S3], DynamoDB) using Amazon Web Services (AWS) Backup and other services.
- **2.3.2** Restore databases (point-in-time restore and others) to meet Recovery Time Objective (RTO), [Recovery Point Objective [RPO]](00-cloudops-basics.md#rpo-and-rto) and cost requirements.
- **2.3.3** Implement versioning for storage services (S3, FSx).
- **2.3.4** Follow DR procedures and best practices (backup and restore, pilot light, [warm standby](00-cloudops-basics.md#disaster-recovery-strategies), active/active).

---

## Task 2.1: Scalability and elasticity

### Amazon EC2 Auto Scaling (skill 2.1.1) `[also: DOP, SAA]`

- **What it is & key terms:**
  - **Auto Scaling group (ASG)**: a fleet with **min / desired / max** capacity across subnets in multiple Availability Zones (AZs).
  - **Launch template** (versioned: Amazon Machine Image [AMI], instance type(s), key pair, Security Groups [SGs], Identity and Access Management [IAM] instance profile, user data, EBS, metadata options). **Launch configurations are deprecated** (can't use new instance types/features).
  - **Mixed instances policy**: multiple instance types/attribute-based selection, On-Demand base + percentage, **Spot allocation strategy** (`price-capacity-optimized` recommended), **capacity rebalancing** for Spot.
  - **Health checks**: EC2 status checks (default) + **ELB health checks** (enable so app failures replace instances) + custom (`SetInstanceHealth`); **[health check](00-cloudops-basics.md#health-check) grace period** (default 300 s in console) avoids replacing booting instances.
- **Scaling policies:**
  - **Target tracking** (keep `ASGAverageCPUUtilization`, `ALBRequestCountPerTarget`, network, or a custom [metric](00-cloudops-basics.md#metric) at a target; AWS creates and manages the alarms; don't edit them).
  - **Step scaling** (different adjustments per [alarm](00-cloudops-basics.md#alarm) breach size; uses **instance warm-up**).
  - **Simple scaling** (one adjustment then **cooldown**, default 300 s; legacy).
  - **Scheduled actions** ([cron](00-cloudops-basics.md#cron-and-rate-expressions), time zone; change min/max/desired).
  - **Predictive scaling** (forecast from 14 days of history; **forecast only** mode to evaluate first; launches capacity ahead of cycles).
  - **Queue-based**: target tracking on a **custom "backlog per instance"** metric (`ApproximateNumberOfMessagesVisible` ÷ InService instances) vs acceptable backlog.
- **Lifecycle features:** **lifecycle hooks** (`Pending:Wait` / `Terminating:Wait`, default heartbeat 1 hour, complete with `CompleteLifecycleAction`; e.g., install software, drain connections, ship logs via EventBridge → Lambda/SSM), **warm pools** (pre-initialized instances kept `Stopped`, `Running` or `Hibernated` for fast scale-out), **instance refresh** ([rolling](00-cloudops-basics.md#deployment-strategies) replacement to a new launch template/AMI with **minimum healthy percentage**, checkpoints, **skip matching**, **auto rollback** on CloudWatch alarm), **instance maintenance policy** (launch before terminate vs terminate before launch), **scale-in protection**, **standby** state (troubleshoot without termination), **suspend processes** (e.g., suspend `ReplaceUnhealthy`/`HealthCheck` while debugging, `AZRebalance`), **termination policies** (default: AZ balance → oldest launch template/config → closest to next billing hour; custom: `OldestInstance`, `NewestInstance`, `OldestLaunchTemplate`, `AllocationStrategy`, custom Lambda).
- **Troubleshooting scaling (common):**
  - Instances launch then terminate in a loop → failing **ELB health checks** (wrong path/port, Security Group [SG] blocks LB, app slow → raise grace period).
  - ASG not launching → **InsufficientInstanceCapacity** (add instance types/AZs), **virtual CPU (vCPU) service [quota](00-cloudops-basics.md#service-quotas)** reached, invalid AMI/key pair/SG, launch template missing permissions (Key Management Service [KMS] key for encrypted EBS: the **service-linked role** needs key access), subnet out of IP addresses (IPs).
  - Not scaling out though Central Processing Unit (CPU) high → max capacity reached, cooldown/warm-up, alarm on wrong metric/dimension, suspended processes.
  - Scaling too slowly → long boot (bake Amazon Machine Images [AMIs], warm pools), predictive scaling, smaller step thresholds, detailed monitoring.
- **Other compute scaling:**
  - **Elastic Container Service (ECS) service auto scaling** (Application Auto Scaling: target tracking on `ECSServiceAverageCPUUtilization`/memory/ALB request count; **capacity providers** + **cluster auto scaling** to scale EC2 instances to fit tasks; Fargate needs no instance scaling).
  - **Elastic Kubernetes Service (EKS)**: [Horizontal](00-cloudops-basics.md#horizontal-vs-vertical-scaling) Pod Autoscaler (pods), **Karpenter** or Cluster Autoscaler (nodes), **EKS Auto Mode** (AWS-managed nodes).
  - **Lambda**: scales automatically per request; **reserved concurrency** (cap/guarantee), **provisioned concurrency** (pre-warmed; can auto scale with Application Auto Scaling on schedule/target tracking); account concurrency quota (default 1,000; request increase).
  - **AWS Auto Scaling scaling plans** to manage multiple resource types together.
- **Exam triggers:** "keep average CPU at 50%" → **target tracking**. "Traffic surge every Monday 9 AM" → **scheduled** (or predictive). "Instances need 10 minutes to boot and scale-out is too slow" → **warm pool**. "Run a script before an instance terminates" → **lifecycle hook**. "Roll out a new AMI to the ASG gradually and roll back on alarms" → **instance refresh** with auto rollback.

### Caching for dynamic scalability (skill 2.1.2) `[also: SAA]`

- **Amazon CloudFront** (edge caching): serves cached responses from edge locations, absorbing traffic so the origin scales less. Levers: **cache policies** (TTLs, [cache key](00-cloudops-basics.md#cdn-origin-and-cache-key) = path + selected headers/cookies/query strings), **origin request policies** (what's forwarded without being in the key), `Cache-Control`/`Expires` headers from the origin, **Origin Shield** (extra regional caching layer that collapses requests), compression, caching **dynamic content** with short TTLs, **stale-while-revalidate**/`stale-if-error`. Monitor **`CacheHitRate`** (enable additional metrics), `OriginLatency`, `4xx/5xxErrorRate`. Details and troubleshooting in Domain 5.
- **Amazon ElastiCache** (in-memory cache):
  - **Engines**: **Valkey** and **Redis OSS** (replication, Multi-AZ automatic failover, cluster mode sharding, persistence, rich data types, pub/sub) and **Memcached** (multi-threaded, simple, no replication/persistence).
  - **Deployment**: **serverless** (auto-scales; pay for data stored and ElastiCache Processing Units [ECPUs]) or **node-based clusters** (choose node type, shards, replicas).
  - **Scaling node-based**: **vertical** (change node type, online for Redis/Valkey), **horizontal** (add/remove **shards** with online resharding in cluster mode; add **read replicas**, up to 5 per shard), **ElastiCache auto scaling** (target tracking on CPU/memory for shards and replicas, cluster mode enabled).
  - **Key metrics**: `CPUUtilization` / **`EngineCPUUtilization`** (Redis/Valkey are mostly single-threaded per shard → watch engine CPU), `DatabaseMemoryUsagePercentage`, **`Evictions`** (cache full → scale up/out or tune Time To Live [TTL]), `CurrConnections`, `CacheHits`/`CacheMisses` (hit ratio), `ReplicationLag`, `SwapUsage`.
  - **Patterns**: [lazy loading](00-cloudops-basics.md#lazy-loading-from-snapshots) (cache-aside), write-through, TTL, session store.
  - **Global Datastore** (cross-Region replication for Valkey/Redis).
- **Other caches**: **DynamoDB Accelerator (DAX)** for DynamoDB (Task 2.1.3), **Application Programming Interface (API) Gateway caching** (per stage), read replicas.
- **Exam triggers:** "reduce DB load from repeated reads" → **ElastiCache**. "High `Evictions`" → bigger nodes/more shards or shorter data lifetime. "One Redis node CPU high while others idle" → hot key or not using cluster mode → **shard**. "Static assets overload web servers" → **CloudFront**.

### Scaling managed databases (skill 2.1.3) `[also: SAA]`

- **Amazon RDS:**
  - **Vertical**: modify instance class (apply immediately or in maintenance window; brief downtime; **Multi-AZ** reduces it to a failover).
  - **Storage**: increase size/[Input/Output Operations Per Second [IOPS]](00-cloudops-basics.md#iops-throughput-and-latency) online; **storage autoscaling** (when free space < 10%, for 5+ minutes, at least 6 hours since last change; set **maximum storage threshold**). Storage can't be decreased.
  - **Read replicas**: up to 15 (MySQL, MariaDB, PostgreSQL; fewer for Oracle/Structured Query Language [SQL] Server), async, cross-Region, promotable; monitor **`ReplicaLag`**; app must route reads.
  - **RDS Proxy**: connection scaling.
  - **Blue/Green Deployments** for low-downtime changes (engine upgrades, parameter changes) with switchover.
- **Amazon Aurora:**
  - **Aurora Replicas** (up to 15, shared storage, low lag) behind the **reader endpoint**; **Aurora Auto Scaling** adds/removes replicas with target tracking on average CPU or connections.
  - **Aurora Serverless v2**: capacity in **ACUs** (Aurora capacity units; each ≈ 2 GiB memory + CPU/network), set **min/max ACUs**, scales in fine increments within seconds; **min 0 ACUs = auto-pause** when idle; mix serverless and provisioned instances in one cluster (e.g., serverless readers).
  - Storage auto-grows (up to 128 TiB); **Aurora I/O-Optimized** for I/O-heavy cost; **Global Database** for cross-Region read scaling.
- **Amazon DynamoDB:**
  - **On-demand** mode: instantly accommodates traffic up to double the previous peak, then scales further; pay per request; optional **maximum throughput** limits.
  - **Provisioned** mode + **auto scaling** (target tracking on consumed/provisioned ratio, e.g., 70%; min/max RCU/WCU; applies to tables and **Global Secondary Indexes [GSIs]**; reacts in minutes, so sudden spikes can still throttle; burst capacity holds up to 300 s of unused capacity).
  - Switch between modes (limited number of times per 24 hours).
  - **Throttling causes**: **hot partition key**, Global Secondary Index (GSI) under-provisioned (**GSI [throttling](00-cloudops-basics.md#throttling) throttles base table writes**), exceeding per-partition limits (3,000 Read Capacity Unit [RCU] / 1,000 Write Capacity Unit [WCU] per partition).
  - **DAX**: microsecond cached reads, reduces RCU use (eventually consistent reads only).
  - **Global tables** for multi-Region scale; **Standard-Infrequent Access (Standard-IA) table class** for cold tables.
  - Metrics: `ConsumedReadCapacityUnits`, `ThrottledRequests`, `ReadThrottleEvents`/`WriteThrottleEvents`, `OnlineIndexThrottleEvents`; **CloudWatch Contributor Insights for DynamoDB** finds hot keys.
- **Exam triggers:** "RDS storage nearly full, avoid manual intervention" → **storage autoscaling**. "Aurora read traffic fluctuates" → **Aurora Auto Scaling replicas** or Serverless v2 readers. "DynamoDB throttles during unpredictable spikes" → **on-demand**. "Throttling only on certain keys" → **hot partition**; better key design/write sharding. "Writes throttled though table has capacity" → **GSI under-provisioned**.

---

## Task 2.2: Highly available and resilient environments

### Elastic Load Balancing health checks (skill 2.2.1) `[also: SAA, DOP]`

- **Configuration (target group):** protocol (HTTP/HTTPS/TCP), [port](00-cloudops-basics.md#ports-and-protocols) (traffic port or override), **path** (`/health`), **success codes** (Application Load Balancer [ALB]: e.g., `200-299`), **interval** (5–300 s, default 30), **timeout** (2–120 s), **healthy threshold** (consecutive successes, default 5 ALB), **unhealthy threshold** (consecutive failures, default 2).
- **Target states**: `initial` (registering), `healthy`, `unhealthy`, `unused` (target's AZ not enabled / not used), `draining` (deregistration delay, default 300 s), `unavailable` (health checks disabled).
- **Behavior**: ELB routes only to healthy targets; if **all targets are unhealthy**, ALB/Network Load Balancer (NLB) **fail open** (route to all) rather than black-holing; ALB returns **[503](00-cloudops-basics.md#http-status-codes)** when the target group is empty.
- **Troubleshooting unhealthy targets:**
  1. **Security group** on the target doesn't allow the health check port **from the load balancer's SG** (most common).
  2. Network Access Control Lists (NACLs) block health check or ephemeral return ports.
  3. Wrong **path/port**, app returns 301/302/401/404 → adjust path or success codes (ALB) or make `/health` return 200.
  4. App start time > grace period/thresholds; dependency checks too strict (health check fails when the DB is slow → whole fleet unhealthy; keep "shallow" vs "deep" checks in mind).
  5. Timeout too short for slow responses.
  6. Target in an AZ not enabled on the LB (`unused`).
  7. Check **target health reason codes** (`Target.ResponseCodeMismatch`, `Target.Timeout`, `Target.FailedHealthChecks`, `Elb.InitialHealthChecking`) in the console/`DescribeTargetHealth`.
- **ASG integration**: ASG health check type **ELB** so failing app instances get **replaced**, not just removed from rotation.
- **Exam triggers:** "targets show unhealthy but app works when accessed directly" → **SG doesn't allow LB → health check port** or wrong path. "Instances keep getting replaced right after launch" → raise **health check grace period**.

### Route 53 health checks (skill 2.2.1) `[also: ANS, SAA]`

- **Types:**
  - **Endpoint** health checks: Hypertext Transfer Protocol (HTTP), Hypertext Transfer Protocol Secure (HTTPS), Transmission Control Protocol (TCP) from **global health checkers** (≈15 locations); optional **string matching** (first 5,120 bytes of response); interval **30 s (standard)** or **10 s (fast, extra cost)**; **failure threshold** (default 3); healthy if ≥ 18% of checkers report healthy; latency graphs optional.
  - **Calculated** health checks: combine child checks with AND/OR/at-least-N.
  - **CloudWatch alarm**-based: health follows an alarm's state; use for **private resources** (in a Virtual Private Cloud [VPC], unreachable by internet checkers) and custom metrics.
- **Used with routing**: **failover** (primary/secondary), weighted, latency, geolocation, multivalue: unhealthy records are skipped. **Alias records** to AWS resources (ELB, CloudFront) use **Evaluate Target Health** instead of separate checks.
- **Notifications**: health checks publish `HealthCheckStatus` metric (us-east-1) → alarm → Simple Notification Service (SNS).
- **Troubleshooting:** endpoint's **firewall/SG/Network Access Control List (NACL) must allow Route 53 health checker Internet Protocol (IP) ranges** (published in `ip-ranges.json`, service `ROUTE53_HEALTHCHECKS`); HTTPS checks don't validate [certificates](00-cloudops-basics.md#tls-and-certificates); check fails if response is slower than 4 s (connection) / 2 s (first byte) or string not found; private IPs can't be checked directly → CloudWatch alarm check; [Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl) failover delay = interval × threshold + **record TTL** cached by resolvers (keep TTL low, e.g., 60 s).
- **Amazon Application Recovery Controller (ARC)**: **readiness checks** (verify standby has matching capacity/quotas/config), **routing controls** (highly available on/off switches for failover via Route 53 health checks, controlled from a 5-Region cluster), **zonal shift** and **zonal autoshift** (move load balancer/ASG traffic away from an impaired AZ, manually or automatically by AWS), **Region switch** (orchestrated multi-Region failover plans).
- **Exam triggers:** "fail over to DR Region when primary unhealthy" → **failover routing + health check**. "Health check a resource in a [private subnet](00-cloudops-basics.md#public-vs-private-subnet)" → **CloudWatch alarm health check**. "Health checks fail although the site works" → firewall blocks **Route 53 checker IPs**. "Shift traffic away from a degraded AZ" → **ARC zonal shift**.

### Multi-AZ and fault-tolerant configuration (skill 2.2.2) `[also: SAA]`

| Component | Multi-AZ / fault-tolerant configuration |
|---|---|
| EC2 compute | ASG across ≥ 2 (ideally 3) AZs behind ELB; enable cross-zone LB; static stability (enough capacity if one AZ fails) |
| Load balancers | Enable subnets in multiple AZs (ALB requires ≥ 2) |
| Network Address Translation (NAT) | **One NAT gateway per AZ**; each private [route table](00-cloudops-basics.md#route-table-and-longest-prefix-match) → its own AZ's NAT |
| RDS | **Multi-AZ DB instance** (synchronous standby, failover 60–120 s, standby not readable) or **Multi-AZ DB cluster** (2 readable standbys, failover typically < 35 s) |
| Aurora | Replicas in other AZs (failover ~30 s, priority tiers 0–15); storage already 6 copies / 3 AZs |
| ElastiCache | Replication group with **Multi-AZ + automatic failover** (replica promoted) |
| DynamoDB, S3, Simple Queue Service (SQS), SNS, Lambda, Elastic File System (EFS) Regional | Multi-AZ by default |
| EFS | Regional file system + mount target in each AZ |
| FSx | **Multi-AZ** deployment type (Windows, ONTAP, OpenZFS); Lustre is single-AZ |
| Redshift | Multi-AZ (RA3) or snapshots + relocation |
| OpenSearch | Multi-AZ with standby / zone awareness, 3 dedicated master nodes |
| Virtual Private Network (VPN) | Two tunnels per connection; second customer gateway device |
| Direct Connect | Two connections in different Direct Connect (DX) locations; VPN backup |
| EBS | AZ-scoped → snapshots/AWS Backup for recovery elsewhere |

- **RDS Multi-AZ failover triggers**: AZ outage, primary instance failure, storage failure, Operating System (OS) patching/maintenance, instance class change, manual **reboot with failover**. The **DNS endpoint stays the same** (apps must reconnect; keep DNS TTL caching low in clients; RDS Proxy smooths it). Event `RDS-EVENT-0049` etc. via RDS event subscriptions.
- **Convert Single-AZ → Multi-AZ**: modify the instance (no downtime; snapshot taken and standby built; some I/O impact).
- **Testing**: reboot with failover; **AWS Fault Injection Service (FIS)** experiments (AZ power interruption scenario, stop instances, throttle Application Programming Interfaces [APIs]); ARC zonal shift drills.
- **Exam triggers:** "database must stay available if an AZ fails, automatic" → **RDS Multi-AZ**. "Test that failover works" → **reboot with failover** / FIS. "Private subnets lose internet when one AZ fails" → single NAT gateway → **NAT per AZ**.

---

## Task 2.3: Backup and restore strategies

### AWS Backup (skill 2.3.1) `[also: DOP, SAA]`

- **What it is & key terms:**
  - **Backup plan**: one or more **backup rules** (schedule cron/frequency down to hourly, **backup window** start + completion window, **lifecycle**: transition to **cold storage** after N days and delete after M days; cold storage needs ≥ 90 days there), **continuous backup** for Point-In-Time Recovery [PITR] (RDS, Aurora, S3, SAP HANA on EC2; up to 35 days), **copy rules** to another Region/account, tags.
  - **Resource assignment**: by **tags** (e.g., `backup=daily`) or resource IDs/types; uses an **IAM service role** (`AWSBackupDefaultServiceRole`).
  - **Backup vault**: encrypted container for **recovery points** (KMS key per vault; for many services the backup uses the source's encryption); **vault access policy** (e.g., deny `DeleteRecoveryPoint`).
  - **Backup Vault Lock**: Write Once, Read Many (WORM): **governance mode** (privileged users can remove) or **[compliance](00-cloudops-basics.md#compliance-as-code--continuous-compliance) mode** ([immutable](00-cloudops-basics.md#immutable-vs-mutable-infrastructure) after the cool-off grace period, min 72 h); min/max retention enforced.
  - **Logically air-gapped vault**: isolated, AWS-owned-key-encrypted vault that can be shared to a recovery account via RAM.
  - **Cross-Region / cross-account copy** (Organizations), **organization backup policies** (deploy plans to all accounts from the management account).
  - **Backup Audit Manager**: frameworks of controls (e.g., resources protected by a plan, minimum frequency/retention, cross-Region copy, encryption) with reports.
  - **Restore testing**: scheduled automatic restores to validate recoverability and measure restore time.
  - **Supported**: EC2 (AMI-based), EBS, RDS, Aurora, DynamoDB, EFS, FSx (all types), S3, Storage Gateway volumes, DocumentDB, Neptune, Redshift, Timestream, CloudFormation stacks, SAP HANA, VMware Virtual Machines (VMs), EKS (newer), and more.
  - **Notifications**: vault events to SNS; job state to EventBridge; job monitoring in console/CloudWatch metrics (`NumberOfBackupJobsFailed`).
- **Native alternatives:**
  - **EBS**: snapshots (incremental); **Amazon Data Lifecycle Manager (DLM)** policies to create/retain/copy (cross-Region, cross-account) snapshots and **EBS-backed AMIs** on schedules, by tags; **Fast Snapshot Restore**; **Snapshot Archive**; **Recycle Bin** rules (recover deleted snapshots/AMIs within retention); **multi-volume crash-consistent snapshots** of an instance; application-consistent snapshots via Volume Shadow Copy Service [VSS] (Windows) / pre-post scripts in DLM or Backup.
  - **RDS**: automated backups (daily snapshot + transaction logs every 5 min; retention 0–35 days; 0 disables and deletes them), **manual snapshots** (kept until deleted; copy cross-Region/account; share; encrypted snapshots need KMS key sharing), **automated backup replication** to another Region, **export snapshot to S3** (Parquet).
  - **Aurora**: continuous backups (1–35 days), manual cluster snapshots, **Backtrack** (Aurora MySQL), **cloning**.
  - **DynamoDB**: **PITR** (continuous, 1–35 days configurable recovery period), **on-demand backups** (kept until deleted), **export to S3**.
  - **S3**: **versioning**, **replication** (CRR/Same-Region Replication [SRR], Replication Time Control [RTC]), **Object Lock**, AWS Backup for S3 (continuous + periodic).
  - **EC2**: create AMI (`CreateImage`, reboot vs no-reboot consistency), DLM AMI policies.
- **Exam triggers:** "centrally manage backups for EBS, RDS, DynamoDB and EFS with retention policies across accounts" → **AWS Backup** (+ organization backup policies). "Backups must be immutable even to admins" → **Vault Lock compliance mode**. "Prove backups meet policy" → **Backup Audit Manager**. "Automate EBS snapshots by tag cheaply" → **DLM**. "Recover a snapshot someone deleted" → **Recycle Bin** (if a rule existed).

### Restoring databases to meet RTO, RPO and cost (skill 2.3.2) `[also: SAA]`

| Method | RPO | RTO | Notes |
|---|---|---|---|
| **RDS point-in-time restore** | ~5 minutes (latest restorable time) | Minutes to hours (size dependent) | Restores to a **new DB instance** (new endpoint) any second within retention; update app connection or rename instances |
| **RDS snapshot restore** | Time of snapshot | Minutes–hours | New instance; choose parameter/option groups, SGs; restored volumes lazy-load from S3 |
| **Promote read replica** | Seconds (replica lag) | Minutes | Breaks replication; becomes standalone writer (cross-Region DR) |
| **Multi-AZ failover** | 0 | 1–2 min | Not a backup (corruption/deletes replicate) |
| **Aurora PITR / snapshot** | ~seconds–5 min | Minutes (new cluster; add instances) | |
| **Aurora Backtrack** | Seconds | Minutes, **in place** | Aurora MySQL only; up to 72 h window; no new cluster |
| **Aurora cloning** | n/a | Minutes | Copy-on-write test copies (investigate without impacting prod) |
| **Aurora Global Database failover/switchover** | ~1 s (switchover 0) | ~1 min | Cross-Region DR |
| **DynamoDB PITR** | Seconds (to any second in window) | Minutes–hours | Restores to a **new table** (re-create auto scaling, TTL, streams, tags, IAM policies manually); same or other Region |
| **DynamoDB on-demand backup restore** | Backup time | Minutes–hours | New table |
| **AWS Backup restore** | Per plan | Per resource | Restore testing measures it |

- **Restore gotchas**: restored RDS instances get the **default security group/parameter group** unless specified; encrypted snapshot restore needs access to the KMS key; you can't restore into an existing instance (create new, then switch endpoints or use DNS Canonical Name [CNAME]); deleted DB with automated backups → retained automated backups (if chosen) or final snapshot.
- **Cost choices**: snapshot frequency vs RPO; cold storage tiers for long retention; cross-Region copies only where DR needs them; PITR cost per GB.
- **Exam triggers:** "accidental delete of rows 2 hours ago, minimal data loss" → **PITR to just before the event** (new instance), then copy data back or cut over. "Undo a bad change on Aurora MySQL quickly without a new cluster" → **Backtrack**. "Test a fix on production-like data without affecting prod" → **Aurora clone**.

### Versioning for storage services (skill 2.3.3) `[also: SAA]`

- **Amazon S3 versioning**:
  - Bucket states: **unversioned → enabled → suspended** (can't return to unversioned).
  - Every overwrite creates a new version ID; **delete** adds a **delete marker** (object appears gone; delete the marker to restore); permanently deleting a version requires specifying the version ID.
  - **[Multi-Factor Authentication [MFA]](00-cloudops-basics.md#mfa) Delete** (root-only, Command Line Interface [CLI]) for extra protection.
  - Costs: every version is billed → **lifecycle rules for noncurrent versions** (transition/expire after N days, keep the latest N noncurrent versions) and **expired object delete marker** cleanup.
  - Required for **replication**, **Object Lock**.
  - Restore an old version: copy that version over the current object, or delete newer versions/markers.
- **Amazon FSx versioning features**:
  - **FSx for Windows File Server**: **shadow copies** (Windows Volume Shadow Copy Service snapshots on a schedule; users restore previous versions of files via Windows Explorer "Previous Versions"), plus automatic daily backups and user-initiated backups.
  - **FSx for NetApp ONTAP**: **volume snapshots** (snapshot policies; users browse `.snapshot` directories), **FlexClone**, SnapMirror replication.
  - **FSx for OpenZFS**: snapshots and clones.
  - **FSx for Lustre**: backups for persistent file systems; data versioning via the linked S3 bucket's versioning.
- **EFS**: no file versioning → use AWS Backup (incremental, PITR-like daily) or EFS replication (replication isn't versioning).
- **Exam triggers:** "users accidentally overwrite S3 objects and need to recover them" → **versioning**. "Versioned bucket storage keeps growing" → **noncurrent version expiration**. "Windows users restore their own previous file versions" → **FSx for Windows shadow copies**.

### Disaster recovery procedures and best practices (skill 2.3.4) `[also: SAA]`

| Strategy | DR Region contents | RPO / RTO | Operational procedure |
|---|---|---|---|
| **Backup and restore** | Backups only (AWS Backup copies, snapshots/AMIs copied, S3 Cross-Region Replication [CRR]) | Hours / hours–day | Restore data, deploy infra from [Infrastructure as Code [IaC]](00-cloudops-basics.md#infrastructure-as-code-iac) (CloudFormation/StackSets), update DNS |
| **Pilot light** | Data replicated live (RDS cross-Region replica, Aurora Global DB, S3 CRR, DynamoDB global table); core infra deployed but **compute off/zero** | Minutes / tens of minutes | Promote DB, scale ASG from 0, switch Route 53 |
| **Warm standby** | **Scaled-down full stack running** | Seconds–minutes / minutes | Scale up, fail over DNS/Global Accelerator |
| **Active/active (multi-site)** | Full capacity serving traffic | Near zero / near zero | Route 53 latency/weighted + health checks, global tables, Aurora Global DB with write forwarding |

- **Best practices**:
  - Define RPO/RTO per workload; choose the cheapest strategy that meets them.
  - Keep IaC and AMIs ready in the DR Region (**AMI copy**, **Elastic Container Registry [ECR] replication**, **CloudFormation StackSets**, Parameter Store/Secrets Manager **replication** of secrets).
  - Pre-raise **service quotas** in the DR Region; **Capacity Reservations** for critical capacity.
  - Use **AWS Elastic Disaster Recovery** for servers (continuous block replication to a low-cost staging area, launch recovery instances in minutes, failback).
  - **Test regularly** (game days, FIS, AWS Backup restore testing, ARC readiness checks); document [runbooks](00-cloudops-basics.md#runbook-and-playbook) (Systems Manager [SSM] Automation, ARC Region switch plans).
  - Protect backups from the same blast radius: **separate account + Region**, Vault Lock, logically air-gapped vaults.
  - Monitor replication: `ReplicaLag`, S3 replication metrics (`ReplicationLatency`, `OperationsPendingReplication`), Aurora `AuroraGlobalDBReplicationLag`, DynamoDB `ReplicationLatency`.
- **Exam triggers:** "lowest cost DR, RTO 24 h" → **backup and restore**. "RPO minutes, RTO < 1 h, minimize cost" → **pilot light**. "RTO minutes, some traffic immediately" → **warm standby**. "Zero downtime" → **active/active**. "Ensure DR environment has enough capacity limits" → **raise quotas / ARC readiness check**.

---

## Gotchas and exam traps

- **Launch configurations are deprecated** → launch templates.
- ASG replaces instances on failed **ELB** health checks only if the ASG health check type includes ELB.
- Target tracking creates its own alarms: **don't edit them**.
- **Lifecycle hooks** default 1-hour timeout; complete them or they time out (default action CONTINUE/ABANDON as set).
- ALB **fails open** when all targets are unhealthy; 503 when none registered.
- Unhealthy targets → usually **SG not allowing the LB** or wrong health check path/code.
- Route 53 health checkers need **firewall access**; private endpoints need **CloudWatch alarm** health checks; DNS TTL delays failover.
- **RDS PITR and snapshot restores create a new instance** (new endpoint, default SG/parameter group unless set).
- **DynamoDB restores create a new table**; reconfigure auto scaling, streams, TTL, tags.
- **Aurora Backtrack** = in place, Aurora MySQL only.
- **Replication isn't backup** (deletes/corruption replicate).
- S3 versioning can only be **suspended**, not disabled; delete markers hide objects.
- **GSI throttling** throttles base-table writes.
- **ElastiCache Evictions** = cache too small; Redis/Valkey watch **EngineCPUUtilization**.
- RDS storage can grow but **never shrink**.

## Quick-recall summary

- **Scale**: ASG (target tracking, step, scheduled, predictive, queue backlog; lifecycle hooks, warm pools, instance refresh), ECS/EKS/Lambda scaling, CloudFront and ElastiCache caching, RDS (class, storage autoscaling, read replicas, Proxy), Aurora (replica auto scaling, Serverless v2 ACUs, auto-pause), DynamoDB (on-demand vs provisioned + auto scaling, DAX, hot keys).
- **Stay up**: ELB health checks (settings, states, troubleshooting), Route 53 health checks (endpoint, calculated, alarm-based; failover), ARC (readiness, routing control, zonal shift), Multi-AZ for every tier, FIS testing.
- **Recover**: AWS Backup (plans, vaults, Vault Lock, cross-Region/account copy, audit, restore testing), DLM, Recycle Bin, RDS/Aurora/DynamoDB PITR and snapshots, Backtrack, clones, S3 and FSx versioning, DR strategies with RPO/RTO and tested runbooks.
