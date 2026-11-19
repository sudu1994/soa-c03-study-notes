# Domain 3: Deployment, Provisioning, and Automation (22%)

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> **About 11 of the 50 scored questions.**
>
> Tests whether you can build images, provision and share infrastructure with code (CloudFormation, Cloud Development Kit [CDK], [Terraform](00-cloudops-basics.md#terraform-basics)), fix failed deployments, roll out changes safely, and automate day-to-day operations with Systems Manager and event-driven triggers.
>
> [Tags](00-cloudops-basics.md#tags) like `[also: DOP]` mark material you met in another exam. It is still covered in full here.

## Table of contents

- [Exam tasks and skills](#exam-tasks-and-skills)
- [Task 3.1: Provision and maintain cloud resources](#task-31-provision-and-maintain-cloud-resources)
- [Task 3.2: Automate the management of existing resources](#task-32-automate-the-management-of-existing-resources)
- [Gotchas and exam traps](#gotchas-and-exam-traps)
- [Quick-recall summary](#quick-recall-summary)

## Exam tasks and skills

> Summarized from the official SOA-C03 exam guide (v1.2). Exact wording: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain3.html

### Task 3.1: Provision and maintain cloud resources
- **3.1.1** Create and manage Amazon Machine Images (AMIs) and container images (Elastic Compute Cloud [EC2] Image Builder).
- **3.1.2** Create and manage resources with CloudFormation and the Amazon Web Services (AWS) CDK.
- **3.1.3** Identify and remediate deployment issues ([subnet sizing](00-cloudops-basics.md#cidr-and-subnet-sizing), CloudFormation errors, permissions).
- **3.1.4** Provision and share resources across Regions and accounts (AWS RAM, CloudFormation StackSets).
- **3.1.5** Implement deployment strategies and services.
- **3.1.6** Use and manage third-party tools to automate deployment (Terraform, [Git](00-cloudops-basics.md#git-basics)).

### Task 3.2: Automate the management of existing resources
- **3.2.1** Use AWS services to automate operational processes (Systems Manager).
- **3.2.2** Implement event-driven automation (Lambda, Simple Storage Service [S3] Event Notifications, EventBridge, AWS DevOps Agent).

---

## Task 3.1: Provision and maintain cloud resources

### AMIs (skill 3.1.1) `[also: DOP, SAA]`

- **What it is & key terms:** an **Amazon Machine Image** = root volume template (Elastic Block Store [EBS] [snapshot](00-cloudops-basics.md#backup-vs-snapshot-vs-replication)(s) or instance-store bundle) + launch permissions + block device mapping. **Regional**: copy to other Regions with `CopyImage`.
  - **Create**: from a running/stopped instance (`CreateImage`; by default **reboots** for file-system consistency; `--no-reboot` risks inconsistency), from snapshots (`RegisterImage`), or with Image Builder/automation.
  - **Share**: launch permissions to specific accounts, Organizational Units (OUs) or the organization; **public** sharing (block with the account-level **block public access for AMIs**, on by default for new accounts, or a [declarative](00-cloudops-basics.md#declarative-vs-imperative) policy). Sharing an Amazon Machine Image (AMI) with **encrypted snapshots** requires sharing the **customer managed Key Management Service (KMS) key** (AWS managed keys can't be shared → copy re-encrypting with a Customer Master Key (now 'customer managed key') [CMK] first).
  - **Lifecycle**: **deprecate** (hide from new launches after a date), **disable**, **deregister** (snapshots remain until deleted: cost!), **Recycle Bin** for deregistered AMIs, **AMI lineage/Allowed AMIs** setting (restrict which AMIs accounts may launch).
  - **[Golden AMI](00-cloudops-basics.md#golden-image-golden-ami) pipeline**: base Operating System (OS) → patch → harden (CIS) → install agents (Systems Manager [SSM], CloudWatch) → test → distribute → update launch templates → instance refresh.
- **Exam triggers:** "share an AMI with another account fails" → encrypted with AWS managed key → **copy with CMK** and share key. "AMI needed in another Region" → **copy AMI**. "Stop teams using outdated AMIs" → **deprecation** / Allowed AMIs.

### EC2 Image Builder (skill 3.1.1) `[also: DOP]`

- **What it is & key terms:** managed service to **build, test and distribute** patched AMIs and **container images** automatically.
  - **Components**: [YAML Ain't Markup Language [YAML]](00-cloudops-basics.md#yaml-and-json) documents with build/test steps (AWS-managed, e.g., `update-linux`, `amazon-cloudwatch-agent-linux`, CIS hardening via Security Technical Implementation Guide [STIG] components; or your own).
  - **Image recipe** (AMI: parent image + components + block devices) / **container recipe** (base image + components + Dockerfile template + target **Elastic Container Registry [ECR]** repository).
  - **Infrastructure configuration**: instance type, subnet/SG, Identity and Access Management (IAM) **instance profile** (needs `EC2InstanceProfileForImageBuilder` + SSM core), S3 logging, Simple Notification Service (SNS) topic, terminate-on-failure setting (keep the instance for debugging).
  - **Distribution settings**: target Regions, accounts/OUs (launch permissions), AMI name/tags, KMS keys, **launch template updates**, Fast Launch (Windows), export to S3.
  - **Image pipeline**: schedule ([cron](00-cloudops-basics.md#cron-and-rate-expressions), or **only when dependency updates are available**), with test stage; **image lifecycle policies** deprecate/delete old images and snapshots; **image scanning** with Amazon Inspector; workflows.
  - Builds run Systems Manager on a temporary build instance, so it needs a path to SSM endpoints and the internet/repos for packages.
- **Exam triggers:** "monthly patched golden AMI distributed to 3 Regions and 20 accounts automatically" → **Image Builder pipeline** with distribution settings. "Rebuild container base images when base updates and push to ECR" → Image Builder **container recipe**. "Build fails; need to inspect" → disable terminate-on-failure, check S3/CloudWatch logs.
- **Vs SSM `AWS-UpdateLinuxAmi`**: works but Image Builder adds testing, distribution, scheduling and lifecycle as a managed service.

### Container images and Amazon ECR (skill 3.1.1) `[also: DOP, SAA]`

- **ECR**: private registry (repositories per image); **IAM + repository policies** (cross-account pull); **image tag immutability**; **lifecycle policies** (expire untagged images or keep last N); **scanning** (basic on push; **enhanced** continuous scanning via Inspector); **cross-Region and cross-account replication**; **pull-through cache** for public registries (Docker Hub, ECR Public, Quay, GitHub); KMS encryption; Virtual Private Cloud (VPC) interface endpoints (`ecr.api`, `ecr.dkr`) + **S3 gateway endpoint** (layers are in S3) for [private subnets](00-cloudops-basics.md#public-vs-private-subnet).
- Build with Docker/`docker buildx` or CodeBuild/Image Builder; [authenticate](00-cloudops-basics.md#authentication-vs-authorization) with `aws ecr get-login-password`.
- **Exam triggers:** "Elastic Container Service (ECS) tasks in private subnets can't pull images" → missing **ECR interface endpoints + S3 gateway endpoint** (or Network Address Translation [NAT]), or task **execution role** lacks ECR permissions. "Storage costs from thousands of old images" → **lifecycle policy**.

### AWS CloudFormation (skill 3.1.2) `[also: DOP]`

- **Template anatomy:** `AWSTemplateFormatVersion`, `Description`, `Metadata` (incl. `AWS::CloudFormation::Interface`, `AWS::CloudFormation::Init`), `Parameters` (types like `AWS::EC2::KeyPair::KeyName`, `AWS::SSM::Parameter::Value<String>` for latest AMI from Parameter Store; `NoEcho`, `AllowedValues`), `Rules`, `Mappings` (Region → AMI), `Conditions` (create resources only in prod), `Transform` (`AWS::Serverless`, `AWS::Include`, macros), **`Resources`** (only required section), `Outputs` (with `Export` for cross-stack references).
- **Intrinsic functions:** `Ref`, `Fn::GetAtt`, `Fn::Sub`, `Fn::Join`, `Fn::Select`, `Fn::Split`, `Fn::FindInMap`, `Fn::ImportValue`, `Fn::If`/`Equals`/`And`/`Or`/`Not`, `Fn::GetAZs`, `Fn::Base64` (user data), `Fn::Cidr`, `Fn::Length`, `Fn::ToJsonString`. **Pseudo parameters**: `AWS::Region`, `AWS::AccountId`, `AWS::StackName`, `AWS::Partition`, `AWS::NoValue`.
- **Dynamic references**: `{{resolve:ssm:/path}}`, `{{resolve:ssm-secure:...}}` (some resources), `{{resolve:secretsmanager:secret:SecretString:password}}` → no secrets in templates.
- **Stack operations:**
  - **Change sets**: preview what an update will add/modify/**replace**/delete before executing.
  - **Update behaviors** per property: no interruption, some interruption, **replacement** (new physical resource; e.g., changing a Relational Database Service [RDS] `DBInstanceIdentifier` or some EC2 properties).
  - **Rollback**: automatic on failure; **rollback triggers** (CloudWatch alarms monitored during and after update for N minutes); `--disable-rollback` / **preserve successfully provisioned resources** to debug; **retry** after fixing.
  - **[Drift](00-cloudops-basics.md#drift) detection**: compare actual vs template (per stack, StackSets); fix by updating template or re-applying; **resource import** brings existing resources under management.
  - **Stack policies**: JavaScript Object Notation (JSON) preventing updates to critical resources (e.g., deny `Update:Replace` on the DB).
  - **DeletionPolicy**: `Delete` (default for most), **`Retain`**, **`Snapshot`** (EBS, RDS, ElastiCache, Redshift, Neptune, DocumentDB); `RetainExceptOnCreate`. **UpdateReplacePolicy** for replacement events. Default for `AWS::RDS::DBCluster` and some others is `Snapshot`.
  - **Termination protection** prevents stack deletion.
  - **DependsOn** (explicit ordering), `CreationPolicy` + **`cfn-signal`** (wait for instances to signal success; with `ResourceSignal` count/timeout), `WaitCondition`, **`UpdatePolicy`** for Auto Scaling groups [ASGs] (`AutoScalingRollingUpdate`, `AutoScalingReplacingUpdate`, `AutoScalingScheduledAction`).
  - **Helper scripts** (in `aws-cfn-bootstrap`): **`cfn-init`** (apply `AWS::CloudFormation::Init` metadata: packages, files, services, commands), **`cfn-signal`**, **`cfn-hup`** (daemon that detects metadata changes and re-runs config), `cfn-get-metadata`.
  - **Nested stacks** (reuse components; `AWS::CloudFormation::Stack`) vs **cross-stack references** (`Export`/`ImportValue`; can't delete/modify an export while imported).
  - **Custom resources** (Lambda- or SNS-backed; must send SUCCESS/FAILED to the presigned S3 Uniform Resource Locator [URL] or the stack hangs ~1 hour), **resource types/modules** from the registry, **hooks** (proactive checks, e.g., Guard rules) and **CloudFormation Guard** ([policy-as-code](00-cloudops-basics.md#compliance-as-code--continuous-compliance) validation), **[Infrastructure as Code [IaC]](00-cloudops-basics.md#infrastructure-as-code-iac) generator** (create templates from existing resources), **service role** (stack uses a role you pass instead of the caller's permissions).
- **Exam triggers:** "preview whether an update replaces the database" → **change set**. "Keep the DB when stack is deleted" → **`DeletionPolicy: Retain`/`Snapshot`**. "Detect manual changes" → **drift detection**. "Instances must finish bootstrapping before stack completes" → **CreationPolicy + cfn-signal**. "Pass secrets without exposing in template" → **dynamic references**. "Prevent accidental stack deletion" → **termination protection**.

### AWS CDK (skill 3.1.2) `[also: DOP]`

- **What it is & key terms:** define infrastructure in **TypeScript, Python, Java, C#, Go**; synthesizes **CloudFormation** templates.
  - **Constructs**: **L1** (`Cfn*` 1:1 CloudFormation resources), **L2** (curated defaults, e.g., `s3.Bucket` with helper methods like `bucket.grantRead(role)`), **L3 / patterns** (multi-resource, e.g., `ApplicationLoadBalancedFargateService`).
  - **App → stacks → constructs**; **environments** (account/Region).
  - **Commands**: `cdk init`, **`cdk bootstrap`** (one-time per account/Region: creates the **CDKToolkit** stack with an S3 bucket/ECR repo for assets and deployment IAM roles; required before deploying; cross-account deploys need `--trust`), **`cdk synth`** (generate template), **`cdk diff`** (compare with deployed), **`cdk deploy`**, `cdk destroy`, `cdk watch`/hotswap (dev only), `cdk import`, `cdk drift`.
  - **Context** (`cdk.json`, lookups cached in `cdk.context.json`), **assets** (Lambda code, Docker images uploaded during deploy), **Aspects** (apply checks/changes to all constructs, e.g., cdk-nag), **CDK Pipelines** for self-mutating Continuous Integration / Continuous Delivery (CI/CD).
- **Troubleshooting**: "SSM parameter `/cdk-bootstrap/hnb659fds/version` not found" / "is this account bootstrapped?" → run **`cdk bootstrap`**; asset publishing failures → bootstrap roles/permissions; deploy to another account → bootstrap target with trust.
- **Exam triggers:** "developers want to define infra in Python with reusable components" → **CDK**. "First CDK deploy fails in a new account/Region" → **bootstrap**.

### Identifying and remediating deployment issues (skill 3.1.3)

**CloudFormation stack states and fixes:**

| Symptom / state | Cause | Fix |
|---|---|---|
| `CREATE_FAILED` → `ROLLBACK_COMPLETE` | A resource failed (permissions, limit, invalid property, name already exists) | Read **stack events** (first failure reason); fix; **delete** the stack (a `ROLLBACK_COMPLETE` stack can only be deleted) and recreate, or use "preserve successfully provisioned resources" then retry |
| `UPDATE_ROLLBACK_FAILED` | Rollback couldn't restore a resource (e.g., it was changed/deleted manually, or a dependency is gone) | Fix the underlying resource, then **`ContinueUpdateRollback`** (optionally **skip resources** that can't roll back) |
| `DELETE_FAILED` | Resource can't be deleted (non-empty S3 bucket, Security Group [SG] in use by another resource/Elastic Network Interface [ENI], export still imported, termination protection on instance) | Empty bucket/remove dependency; retry delete with **retain** for problem resources |
| Stack hangs in `CREATE_IN_PROGRESS` for ~1 h | **Custom resource** didn't respond, or `cfn-signal` never sent (instance can't reach CloudFormation endpoint, script failed) | Fix response/signal; check `/var/log/cfn-init.log`, `cfn-init-cmd.log`; VPC endpoint/NAT for CloudFormation |
| "Resource already exists" | Hard-coded physical names collide | Let CloudFormation generate names or use `Fn::Sub` with stack name |
| "Export cannot be updated/deleted as it is in use" | Cross-stack import | Update importing stacks first |
| `Insufficient permissions` / `AccessDenied` | Caller or **stack service role** lacks permissions; missing **`iam:PassRole`**; `CAPABILITY_IAM`/`CAPABILITY_NAMED_IAM`/`CAPABILITY_AUTO_EXPAND` not acknowledged | Add permissions/capabilities |
| Limit exceeded | [Service quotas](00-cloudops-basics.md#service-quotas) (Virtual Private Clouds [VPCs], Elastic IP addresses [EIPs], virtual CPUs [vCPUs]) | Request quota increase |

**Other deployment issues:**

- **Subnet sizing / Internet Protocol (IP) exhaustion**: launches fail with "insufficient free addresses in subnet"; affects Auto Scaling group (ASG) scaling, **Lambda in VPC** (Hyperplane Elastic Network Interfaces [ENIs]), **Elastic Kubernetes Service (EKS) pods** (VPC CNI assigns VPC IP addresses [IPs] per pod), Elastic Load Balancing (ELB) scaling (Application Load Balancer [ALB] needs free IPs, at least /27 subnets), RDS, interface endpoints. Fix: **add secondary VPC Classless Inter-Domain Routing (CIDR) + new larger subnets**, use more Availability Zones (AZs)/subnets, EKS **prefix delegation** or custom networking, Internet Protocol version 6 (IPv6), clean up unused ENIs. Remember **5 reserved IPs** per subnet. **VPC IP Address Manager (IPAM)** to plan and monitor utilization.
- **Capacity**: `InsufficientInstanceCapacity` → try other AZs/instance types, flexible ASGs, Capacity Reservations.
- **Quotas**: `VcpuLimitExceeded`, Elastic IP address (EIP) limits, Lambda concurrency → Service Quotas increase (and alarms on quota utilization with CloudWatch usage metrics).
- **Permissions**: IAM role for EC2/Lambda/ECS missing permissions; KMS key policy doesn't allow the service-linked role (ASG launching encrypted volumes fails); Service Control Policies (SCPs) blocking; `iam:PassRole` missing; check **CloudTrail `errorCode`**, `sts decode-authorization-message` for encoded failures, **IAM policy simulator**.
- **User data/bootstrap**: logs in `/var/log/cloud-init-output.log`; runs once at first boot by default; needs outbound access for package downloads.
- **AMI/launch template**: wrong architecture (Advanced RISC Machine [ARM] AMI on x86 type), AMI not shared/deregistered, Elastic Network Adapter (ENA) not supported.
- **Exam triggers:** "EKS pods stuck pending with IP errors" → **subnet exhaustion** → add CIDR/subnets/prefix delegation. "Stack in UPDATE_ROLLBACK_FAILED" → fix + **ContinueUpdateRollback**. "Stack creation fails: requires capabilities" → acknowledge **CAPABILITY_NAMED_IAM**.

### Provisioning and sharing across Regions and accounts (skill 3.1.4) `[also: DOP]`

- **CloudFormation StackSets:**
  - Deploy one template as **stack instances** across many **accounts and Regions** from an administrator account.
  - **Permission models**: **self-managed** (you create `AWSCloudFormationStackSetAdministrationRole` in the admin account and `AWSCloudFormationStackSetExecutionRole` in each target) or **service-managed** (with **AWS Organizations**: target OUs, **automatic deployment** to new accounts joining the Organizational Unit [OU], optional **delegated administrator**).
  - **Deployment options**: maximum concurrent accounts (number/%), **failure tolerance** (number/%), **Region concurrency** (sequential or parallel), Region order; **retain stacks** on removal; **drift detection** for StackSets; **stack instance overrides** for parameters.
  - Note: service-managed StackSets **don't deploy to the management account**.
- **AWS Resource Access Manager (RAM):** share resources with other accounts, OUs or the whole organization (enable sharing with Organizations to skip invitations) without copying. Shareable examples: **VPC subnets** (VPC sharing), **Transit Gateways**, **Route 53 Resolver rules** and **[Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl) Firewall rule groups**, **managed prefix lists**, **IPAM pools**, License Manager configurations, **Capacity Reservations**, Outposts, Aurora DB clusters (for cloning), Image Builder components/recipes, Network Firewall policies, Glue catalogs, **AWS Private Certificate Authority (CA)**, Resource Groups, and more.
  - **VPC sharing**: owner manages the VPC/subnets/route tables/Network Access Control Lists (NACLs); **participants** launch their own resources (EC2, RDS, Lambda) into shared subnets, manage their own resources and Security Groups (SGs), but can't modify the VPC. Same-AZ IDs matter.
- **Other multi-account/Region provisioning**: **Service Catalog** (approved products shared to accounts via portfolios; launch constraints with roles), **Control Tower Account Factory** (+ Account Factory for Terraform, Account Factory for Terraform [AFT]), **AMI copy/share**, **ECR replication**, **Systems Manager** multi-account Automation and Quick Setup, **Secrets Manager replication**, **Backup cross-account copy**, CloudFormation **Exports per Region**.
- **Exam triggers:** "deploy a baseline (Config rules, IAM roles, CloudTrail) to every account including future ones" → **service-managed StackSets with automatic deployment**. "Central network team owns VPCs; app teams launch into them" → **RAM VPC sharing**. "Share a Transit Gateway with 30 accounts" → **RAM**. "Share Resolver forwarding rules" → **RAM**.

### Deployment strategies and services (skill 3.1.5) `[also: DOP]`

| Strategy | How | Downtime | Rollback | Cost |
|---|---|---|---|---|
| **All at once / in place** | Update every instance | Yes (brief) | Redeploy old version | Lowest |
| **[Rolling](00-cloudops-basics.md#deployment-strategies)** | Batches of instances | No (reduced capacity) | Redeploy | Low |
| **Rolling with additional batch** | Add a batch first | No (full capacity) | Redeploy | Slightly higher |
| **[Immutable](00-cloudops-basics.md#immutable-vs-mutable-infrastructure)** | New instances/ASG, swap when healthy | No | Terminate new ones (fast) | Double briefly |
| **Blue/green** | New full environment; switch traffic (DNS, LB listener, Canonical Name [CNAME] swap, target group weights) | No | Switch back | Double during switch |
| **Canary** | Small % first, then all | No | Shift back | Medium |
| **Linear** | Equal % steps over time | No | Shift back | Medium |

- **Services:**
  - **AWS CodeDeploy**: deploys to **EC2/on-prem** (in-place or blue/green with ASG/ELB; `appspec.yml` lifecycle hooks: `ApplicationStop`, `BeforeInstall`, `AfterInstall`, `ApplicationStart`, `ValidateService`; agent required), **Lambda** (alias traffic shifting: `Canary10Percent5Minutes`, `Linear10PercentEvery1Minute`, `AllAtOnce`; `BeforeAllowTraffic`/`AfterAllowTraffic` hooks), **ECS** (blue/green with two target groups, test listener). **Automatic rollback** on failure or **CloudWatch alarms**.
  - **ECS native deployments**: rolling update (`minimumHealthyPercent`/`maximumPercent`), **deployment circuit breaker** (auto-rollback on failing tasks), native **blue/green**, canary and linear strategies.
  - **Elastic Beanstalk**: all at once, rolling, rolling with additional batch, **immutable**, **traffic splitting** (canary), **blue/green via environment CNAME swap**; `.ebextensions`, saved configurations.
  - **Lambda**: versions + **aliases** with **weighted routing**; **Serverless Application Model (SAM)** `DeploymentPreference` uses CodeDeploy.
  - **ASG instance refresh** (rolling AMI updates with checkpoints and auto rollback).
  - **CloudFormation** `UpdatePolicy` for ASGs; rollback triggers.
  - **Route 53 weighted records**, **ALB weighted target groups** for blue/green and canary traffic shifting.
  - **AWS AppConfig** (feature flags, config deployment strategies) is out of scope for SOA-C03 but appears in DOP.
- **Exam triggers:** "zero downtime, instant rollback" → **blue/green**. "Shift 10% traffic to new Lambda version for 5 minutes then all" → **CodeDeploy canary** on alias. "Beanstalk update must not reduce capacity and must allow quick rollback" → **immutable**. "Stop a bad ECS deployment automatically" → **deployment circuit breaker**.

### Third-party tools: Terraform and Git (skill 3.1.6)

- **Terraform**:
  - **HashiCorp Configuration Language (HCL)** configuration with **providers** (`hashicorp/aws`), **resources**, **data sources** (look up existing things, e.g., latest AMI), **variables**/`tfvars`, **outputs**, **modules** (reusable; Terraform Registry).
  - **Workflow**: `terraform init` (download providers, configure backend) → `fmt`/`validate` → **`plan`** (preview; save with `-out`) → **`apply`** → `destroy`. `import` brings existing resources under management; `state mv/rm` refactors.
  - **State**: maps config to real resources; **remote backend in S3** ([versioning](00-cloudops-basics.md#versioning) + encryption enabled) with **state locking** (S3 native lock file `use_lockfile`, or legacy DynamoDB table) to stop concurrent applies; never edit state by hand; protect it (contains secrets).
  - **Drift**: `terraform plan` shows differences from real infrastructure (`-refresh-only` to update state).
  - **Workspaces** (multiple states per config, e.g., dev/prod), **for_each/count**, `lifecycle { prevent_destroy, create_before_destroy, ignore_changes }`.
  - **AWS auth**: IAM role (assume role in provider), OpenID Connect (OIDC) from CI (GitHub Actions → `AssumeRoleWithWebIdentity`), never long-lived keys in code.
  - **AWS integrations**: Service Catalog supports Terraform products; **Control Tower Account Factory for Terraform (AFT)**; **AWS Cloud Control Application Programming Interface (API)** provider (`awscc`) for new resources.
  - Troubleshooting: "Error acquiring the state lock" → another apply running or a stale lock (`force-unlock` carefully); provider version constraints; permission errors from AWS API (check CloudTrail).
- **Git**:
  - Repository, **commit**, **branch**, **merge**, **pull/merge request** (review + CI checks before merge), **tags** (versions), `.gitignore` (exclude `.terraform/`, state files, secrets).
  - **Git workflows**: trunk-based (short-lived branches) or GitFlow; protect main with required reviews.
  - **GitOps**: Git is the source of truth; CI/CD (CodePipeline, GitHub Actions, GitLab CI) runs `plan` on PRs and `apply`/`cdk deploy`/`aws cloudformation deploy` on merge. **CodeConnections** (formerly CodeStar Connections) links GitHub/GitLab/Bitbucket to AWS services; **CloudFormation Git sync** deploys stacks automatically from a repository.
  - Never commit secrets (use Secrets Manager/Parameter Store; scan with git-secrets).
- **Exam triggers:** "two engineers' Terraform applies corrupted state" → **S3 backend with state locking**. "Infrastructure changes must be reviewed before applying" → **PR with plan output** / change sets. "Keep CloudFormation stack synced with a GitHub repo" → **Git sync**.

---

## Task 3.2: Automate the management of existing resources

### AWS Systems Manager for operational automation (skill 3.2.1) `[also: DOP]`

- **Managed nodes**: EC2, on-prem servers and Virtual Machines (VMs), edge devices registered with SSM. Requirements: **SSM Agent** (preinstalled on Amazon Linux, Windows, Ubuntu AMIs), **IAM instance profile** with `AmazonSSMManagedInstanceCore` (or **Default Host Management Configuration**, which grants permissions account-wide without per-instance roles), **network path** to `ssm`, `ssmmessages`, `ec2messages` endpoints (NAT or interface VPC endpoints). On-prem: **hybrid activation** (activation code/ID). Node shows as "managed" in Fleet Manager.
- **Capabilities (what to pick):**

| Need | Capability |
|---|---|
| Run a command/script across many instances now, no Secure Shell (SSH) | **Run Command** (Command documents like `AWS-RunShellScript`, `AWS-RunPowerShellScript`; rate control; output to S3/CloudWatch Logs; SNS notifications) |
| Keep instances in a [desired state](00-cloudops-basics.md#desired-state-configuration) on a schedule | **State Manager** associations (e.g., keep CloudWatch agent installed and configured, join domain, run Ansible playbooks) |
| Patch OS and apps | **Patch Manager**: **patch baselines** (approval rules, auto-approve after N days, approved/rejected lists, severity), **patch groups** (tag `Patch Group`), `AWS-RunPatchBaseline` (Scan or Install), **patch policies** via **Quick Setup** (org-wide), compliance reporting |
| Run disruptive tasks only at set times | **Maintenance Windows** (schedule, duration, cutoff; targets; tasks: Run Command, Automation, Lambda, Step Functions) |
| Multi-step workflows calling AWS Application Programming Interfaces (APIs) | **Automation** [runbooks](00-cloudops-basics.md#runbook-and-playbook) (Domain 1) |
| Shell/port forwarding without bastion/SSH keys | **Session Manager** (logging to S3/CloudWatch Logs, KMS encryption, IAM control, [port](00-cloudops-basics.md#ports-and-protocols) forwarding) |
| Software inventory | **Inventory** (applications, files, network config, Windows updates) + **Resource Data Sync** to S3 (query with Athena) |
| Configuration data and secrets | **Parameter Store** (String, StringList, SecureString; standard/advanced tiers; hierarchies; versioning; parameter policies on advanced: expiration/notification) |
| Distribute and install software packages | **Distributor** |
| Operational issues | **OpsCenter** (OpsItems) and **Explorer** (dashboard) |
| Incidents | **[Incident](00-cloudops-basics.md#incident-mttd-and-mttr) Manager** |
| Changes with approvals and freeze windows | **Change Manager** and **Change Calendar** |
| Manage nodes visually (files, processes, users, logs) | **Fleet Manager** |
| One-click setups (patching, agent install, Host Management, Config recording) | **Quick Setup** (org-wide) |
| Node health and compliance | **Compliance** (patch and association compliance) |

- **Exam triggers:** "patch all Linux instances every Sunday 2 AM, critical patches auto-approved after 7 days" → **Patch Manager** baseline + **Maintenance Window** (or patch policy). "Install the CloudWatch agent on every new instance automatically" → **State Manager** association by tag. "Instance doesn't appear as managed" → agent, **instance profile**, or **network path to SSM endpoints**. "Audit which software versions are installed fleet-wide" → **Inventory + Resource Data Sync + Athena**.

### Event-driven automation (skill 3.2.2) `[also: DOP]`

- **AWS Lambda** as the glue: triggered by EventBridge rules, S3 events, SNS, Simple Queue Service (SQS), CloudWatch Logs subscriptions, DynamoDB/Kinesis streams, alarms (direct action), Config custom rules, scheduled (EventBridge Scheduler). Execution role needs permissions for the actions; resource-based policy for the invoking service; set timeout/memory; DLQ/on-failure destinations for async invocations; [idempotent](00-cloudops-basics.md#idempotent) code.
- **S3 Event Notifications**: events (`s3:ObjectCreated:*`, `s3:ObjectRemoved:*`, `s3:ObjectRestore:*`, `s3:Replication:*`, `s3:LifecycleExpiration:*`, `s3:ObjectTagging:*`, `s3:IntelligentTiering`) with **prefix/suffix filters** → **Lambda, SQS, SNS** directly, or turn on **EventBridge** for richer filtering and more targets. Gotchas: destination **resource policy** must allow `s3.amazonaws.com`; **overlapping prefix/suffix rules for the same event type aren't allowed**; avoid **recursive loops** (Lambda writing back to the same bucket/prefix that triggers it); delivery is at least once (typically seconds), occasionally duplicated; SQS First-In, First-Out (FIFO) isn't supported as a direct destination.
- **Amazon EventBridge**: scheduled automation (**EventBridge Scheduler**: one-time/cron/rate, time zones, flexible windows, retries, Dead-Letter Queue [DLQ], targets 270+ services via universal targets), state-change rules (EC2, ASG, ECS task stopped, RDS events, Health, Config, CodePipeline, GuardDuty), CloudTrail API rules; targets SSM Automation/Run Command, Lambda, Step Functions.
- **AWS Step Functions** for multi-step automation with retries/branching/human approval (e.g., incident response workflow).
- **AWS DevOps Agent**: can run **custom Site Reliability Engineering (SRE) agents on a schedule or on events** and starts **automated investigations** from incident tickets (PagerDuty, ServiceNow) or chat, producing root cause and mitigation plans; operators approve actions.
- **Typical event-driven automations:**
  - EC2 state change to `stopped` for prod-tagged instance → Lambda restarts + SNS notify.
  - New S3 object → Lambda processes/tags; or → Step Functions pipeline.
  - ASG lifecycle hook → EventBridge → Lambda/SSM drains connections, registers in DNS.
  - Security group change via CloudTrail event → Lambda reverts unauthorized rules.
  - AWS Health scheduled maintenance event → Automation stop/start to migrate host.
  - Config non-compliance → auto-remediation (SSM).
  - Cost: Budget action → stop instances / apply Service Control Policy (SCP).
  - Nightly stop of dev instances → Scheduler → `AWS-StopEC2Instance` by tag (or **Instance Scheduler on AWS** solution).
- **Exam triggers:** "process files as soon as they land in S3" → **S3 event → Lambda/SQS**. "Many consumers/advanced filtering for S3 events" → **EventBridge**. "Run a runbook every night against tagged instances" → **EventBridge Scheduler → SSM Automation**. "Lambda triggered by S3 keeps invoking itself" → **recursive loop** (write to a different bucket/prefix; Lambda recursive loop detection stops it).

---

## Gotchas and exam traps

- AMIs are **Regional** → copy; encrypted AMI sharing needs a **customer managed key**.
- **Image Builder** = managed golden AMI/container pipeline with test + distribution.
- Private-subnet ECS/EKS image pulls need **ECR API + DKR interface endpoints + S3 gateway endpoint**.
- **Change sets** show replacements; **stack policies** protect resources; **DeletionPolicy Retain/Snapshot** keeps data.
- `ROLLBACK_COMPLETE` stacks can only be deleted; `UPDATE_ROLLBACK_FAILED` → **ContinueUpdateRollback**.
- Stack stuck ~1 hour → **custom resource** didn't respond or **cfn-signal** missing.
- IAM resources in templates need **CAPABILITY_IAM / CAPABILITY_NAMED_IAM**; passing roles needs **iam:PassRole**.
- **CDK needs `cdk bootstrap`** per account/Region.
- **Service-managed StackSets** auto-deploy to new OU accounts but not to the management account.
- **RAM** shares subnets/Transit Gateway (TGW)/Resolver rules/prefix lists; participants can't modify shared VPCs.
- **Subnet IP exhaustion** breaks scaling, Lambda in VPC, EKS pods, Application Load Balancers (ALBs) → add CIDR/subnets.
- Terraform state in **S3 + locking**; never in Git.
- SSM managed node requires **agent + instance profile + endpoint access**.
- S3 event notifications: no overlapping filters for same event; watch **recursive invocations**; destination policy required.

## Quick-recall summary

- **Images**: AMIs (create, copy, share, deprecate, KMS), Image Builder (components, recipes, infra config, distribution, pipelines, lifecycle), ECR (lifecycle, scanning, replication, pull-through cache, endpoints).
- **IaC**: CloudFormation (sections, functions, dynamic refs, change sets, drift, rollback triggers, stack policies, DeletionPolicy, cfn-init/signal/hup, nested vs exports, custom resources, failure states), CDK (constructs, bootstrap, synth/diff/deploy), Terraform (state, locking, plan/apply, modules), Git (branches, PRs, GitOps, Git sync).
- **Multi-account/Region**: StackSets (self vs service-managed, auto-deploy, concurrency/tolerance), RAM sharing, Service Catalog, AFT.
- **Deploy safely**: all-at-once, rolling, immutable, blue/green, canary, linear via CodeDeploy, ECS, Beanstalk, Lambda aliases, ASG instance refresh.
- **Automate**: Systems Manager (Run Command, State Manager, Patch Manager, Maintenance Windows, Automation, Session Manager, Inventory, Parameter Store, OpsCenter, Quick Setup), event-driven (Lambda, S3 events, EventBridge rules/Scheduler, Step Functions, DevOps Agent).
