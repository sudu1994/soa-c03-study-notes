# Domain 4: Security and Compliance (16%)

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> **About 8 of the 50 scored questions.**
>
> Tests whether you can configure and troubleshoot identity and access, run multi-account guardrails, act on security checks and findings, enforce [compliance](00-cloudops-basics.md#compliance-as-code--continuous-compliance) continuously, and protect data with [classification](00-cloudops-basics.md#data-classification), encryption and secrets management.
>
> [Tags](00-cloudops-basics.md#tags) like `[also: DOP]` mark material you met in another exam. It is still covered in full here.

## Table of contents

- [Exam tasks and skills](#exam-tasks-and-skills)
- [Task 4.1: Security and compliance tools and policies](#task-41-security-and-compliance-tools-and-policies)
- [Task 4.2: Protect data and infrastructure](#task-42-protect-data-and-infrastructure)
- [Gotchas and exam traps](#gotchas-and-exam-traps)
- [Quick-recall summary](#quick-recall-summary)

## Exam tasks and skills

> Summarized from the official SOA-C03 exam guide (v1.2). Exact wording: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain4.html

### Task 4.1: Implement and manage security and compliance tools and policies
- **4.1.1** Implement Identity and Access Management (IAM) features (password policies, [Multi-Factor Authentication [MFA]](00-cloudops-basics.md#mfa), roles, [federated](00-cloudops-basics.md#federation) identity, resource policies, policy conditions).
- **4.1.2** Troubleshoot and audit access issues (CloudTrail, IAM Access Analyzer, IAM policy simulator).
- **4.1.3** Implement multi-account strategies securely (Organizations, Service Control Policies [SCPs], IAM Identity Center).
- **4.1.4** Remediate based on Amazon Web Services (AWS) Trusted Advisor security checks.
- **4.1.5** Enforce compliance requirements and continuous monitoring (Region and service selections, Config conformance packs).

### Task 4.2: Implement strategies to protect data and infrastructure
- **4.2.1** Implement and enforce a data classification scheme.
- **4.2.2** Implement, configure and troubleshoot [encryption at rest](00-cloudops-basics.md#encryption-at-rest-and-in-transit) (KMS).
- **4.2.3** Implement, configure and troubleshoot encryption in transit (ACM).
- **4.2.4** Securely store secrets.
- **4.2.5** Configure reports and remediate findings (Security Hub, GuardDuty, Config, Inspector, AWS Security Agent).

---

## Task 4.1: Security and compliance tools and policies

### IAM features (skill 4.1.1) `[also: DOP, SAA]`

- **Identities**: **root user** (protect with MFA, no access keys, use only for root-only tasks; centrally managed root access in Organizations), **IAM users** (long-term credentials: password and/or up to 2 access keys), **groups**, **roles** (temporary credentials via Security Token Service [STS]; **trust policy** says who may assume; **instance profiles** for Elastic Compute Cloud [EC2]; **service-linked roles**), **IAM Identity Center** users (workforce Single Sign-On [SSO]).
- **Account password policy**: minimum length, character types, **max age (expiration)**, **prevent reuse** (last N), allow users to change own password, **hard expiry** (admin reset required). Applies to IAM users' console passwords only.
- **MFA**: virtual authenticator apps, **FIDO2 passkeys/security keys**, hardware Time-based One-Time Password (TOTP) tokens; up to 8 MFA devices per user/root. **Enforce MFA** with a policy that denies everything except MFA self-management when `aws:MultiFactorAuthPresent` is false; Command Line Interface (CLI) users then call `sts get-session-token` with the MFA code.
- **Roles and temporary credentials**: AssumeRole (cross-account, role switching), **role max session duration** 1–12 h, role chaining max 1 h; **IAM Roles Anywhere** for on-prem workloads using X.509 [certificates](00-cloudops-basics.md#tls-and-certificates).
- **Federated identity**: **Security Assertion Markup Language (SAML) 2.0** (Active Directory [AD] FS, Okta, Entra ID) → IAM role per account or **IAM Identity Center** (preferred central option); **OpenID Connect (OIDC)** web identity (Cognito, GitHub Actions, Elastic Kubernetes Service [EKS] IAM Roles for Service Accounts [IRSA]); IAM identity providers must be created for SAML/OIDC in the account when federating directly.
- **Policies**: identity-based (managed AWS/customer, inline), **resource-based** (Simple Storage Service [S3] bucket, KMS key, Simple Queue Service [SQS], Simple Notification Service [SNS], Lambda, Secrets Manager, Elastic Container Registry [ECR]…), **permissions boundaries**, **SCPs/RCPs**, **session policies**, Access Control Lists [ACLs] (legacy).
- **Policy conditions (common keys):** `aws:SourceIp`, `aws:SourceVpc`, `aws:SourceVpce`, `aws:PrincipalOrgID`, `aws:PrincipalTag/…`, `aws:ResourceTag/…`, `aws:RequestTag/…`, `aws:TagKeys`, `aws:RequestedRegion`, `aws:MultiFactorAuthPresent`, `aws:MultiFactorAuthAge`, `aws:SecureTransport`, `aws:CurrentTime`, `aws:PrincipalArn`, `aws:SourceArn`/`aws:SourceAccount` (confused deputy protection), `aws:CalledVia`, `aws:ViaAWSService`; service keys such as `s3:x-amz-server-side-encryption`, `ec2:InstanceType`, `kms:ViaService`. Operators: `StringEquals`, `StringLike`, `ArnLike`, `IpAddress`, `Bool`, `DateGreaterThan`, `…IfExists`, `ForAnyValue`/`ForAllValues`.
- **Evaluation**: [explicit deny](00-cloudops-basics.md#explicit-vs-implicit-deny) > SCP/Resource Control Policy (RCP) allow required > identity or resource allow (same account) / both (cross-account) > boundary > session policy.
- **Exam triggers:** "force password rotation every 90 days and prevent reuse" → **password policy**. "Users must use MFA for console and CLI" → MFA-enforcing **deny policy with `aws:MultiFactorAuthPresent`**. "Allow access only from corporate IP addresses (IPs)" → `aws:SourceIp` condition (careful: calls made by AWS services on your behalf don't come from your Internet Protocol [IP]; use `aws:ViaAWSService`).

### Troubleshooting and auditing access (skill 4.1.2) `[also: DOP]`

- **AWS CloudTrail**: find the denied call: `errorCode` = `AccessDenied` / `UnauthorizedOperation` / `Client.UnauthorizedOperation`, `errorMessage` (often states which policy type denied: "explicit deny in a service control policy", "no identity-based policy allows", "permissions boundary"), `userIdentity` (who: role session name, source identity), `sourceIPAddress`, `eventSource`. Event history (90 days) or CloudTrail Lake/Athena on trail logs.
- **Encoded authorization failure messages** (EC2 and others): decode with **`aws sts decode-authorization-message`** (caller needs `sts:DecodeAuthorizationMessage`) to see the exact failing context.
- **IAM policy simulator**: test identity-based policies (and boundaries, Service Control Policy [SCP] effects in Organizations context, resource policies for some services) against specific actions/resources/condition values **without making real calls**; shows which statement allowed or denied.
- **IAM Access Analyzer**:
  - **External access findings**: resources (S3 buckets, KMS keys, IAM roles' trust policies, Lambda, SQS, Secrets Manager, SNS, EBS/RDS [snapshots](00-cloudops-basics.md#backup-vs-snapshot-vs-replication), ECR, Elastic File System [EFS], DynamoDB) shared outside the **zone of trust** (account or organization).
  - **Internal access findings**: which principals inside the org can reach critical resources.
  - **Unused access findings**: unused roles, access keys, passwords, permissions (paid) → remove for [least privilege](00-cloudops-basics.md#least-privilege).
  - **Policy validation** (grammar, security warnings, best practices) and **custom policy checks** (`CheckNoNewAccess`, `CheckAccessNotGranted`, `CheckNoPublicAccess`) in Continuous Integration / Continuous Delivery (CI/CD).
  - **Policy generation** from CloudTrail activity (least-privilege policy based on what a role actually used).
  - **Archive rules** to suppress expected findings.
- **Other tools**: **IAM Access Advisor / last accessed information** (services and actions last used per identity → trim permissions), **credential report** (all users, password/key age, MFA status, last use) for audits, **Organizations SCP last accessed**, **AWS Config** for IAM configuration history.
- **Common access problems:**
  - Explicit deny in an **SCP**, **permissions boundary**, **session policy**, **Virtual Private Cloud (VPC) endpoint policy**, **bucket policy** or **KMS key policy** (a KMS deny shows up as KMS AccessDenied even if S3 permissions are fine).
  - Cross-account: missing one side (resource policy *and* identity policy); object owned by another account (S3 Object Ownership).
  - Role trust policy doesn't list the principal; missing `iam:PassRole`; MFA condition not satisfied; wrong Region/condition key values; tag-based condition mismatch.
  - Policy changes not yet propagated (IAM is eventually consistent).
- **Exam triggers:** "test whether a new policy allows `s3:PutObject` before attaching" → **policy simulator**. "Find S3 buckets and KMS keys shared outside the organization" → **Access Analyzer**. "Generate a least-privilege policy for a role based on 90 days of use" → **Access Analyzer policy generation**. "EC2 launch error with an encoded message" → **`decode-authorization-message`**. "Which IAM users haven't rotated keys / lack MFA" → **credential report**.

### Multi-account strategy (skill 4.1.3) `[also: DOP, SAA]`

- **AWS Organizations**: management account, member accounts, **Organizational Units [OUs]** (e.g., Security, Infrastructure, Workloads/Prod, Workloads/Dev, Sandbox, Suspended), **consolidated billing**, **trusted access** for services (CloudTrail org trail, Config aggregator, Security Hub, GuardDuty, Backup, IAM Identity Center, RAM, StackSets), **delegated administrators** (run security tooling from a Security/Audit account, not the management account), **policies**: SCPs, Resource Control Policies (RCPs), [declarative](00-cloudops-basics.md#declarative-vs-imperative) policies, tag policies, backup policies, Artificial Intelligence (AI) opt-out.
- **SCPs**: maximum permissions for principals in member accounts (including root); **don't affect the management account or service-linked roles**; don't grant. Patterns: deny leaving the org; deny disabling CloudTrail/GuardDuty/Config/Security Hub; **deny unapproved Regions** (`aws:RequestedRegion` with `NotAction` exemptions for global services); deny root user actions; deny creating IAM users/access keys; deny instance types; require tags on create (`aws:RequestTag`); protect security roles (deny changes except by `aws:PrincipalArn` of admin role).
- **RCPs**: maximum permissions on resources (S3, STS, KMS, SQS, Secrets Manager…) → data perimeter (deny access from outside the org).
- **IAM Identity Center**: central workforce access; identity source (built-in, AD via AD Connector/Managed AD, external Identity Provider [IdP] with SAML + System for Cross-domain Identity Management [SCIM]); **permission sets** → roles in accounts; **assignments** to users/groups per account; MFA settings; **Attribute-Based Access Control (ABAC) with attributes**; session duration; CLI v2 SSO profiles; **delegated admin** for Identity Center.
- **AWS Control Tower**: landing zone (Log Archive + Audit accounts, org CloudTrail, Config), **controls** ([preventive](00-cloudops-basics.md#detective-vs-preventive-vs-responsive-controls) = SCPs/RCPs, detective = Config rules, proactive = CloudFormation hooks), **Account Factory**, [drift](00-cloudops-basics.md#drift) detection and repair, Region deny control.
- **Exam triggers:** "prevent member accounts from disabling GuardDuty" → **SCP**. "Workforce SSO to all accounts with Entra ID groups" → **Identity Center + SCIM**. "Security team manages GuardDuty for the org without using the management account" → **delegated administrator**.

### Trusted Advisor security checks and remediation (skill 4.1.4)

- **What it is:** best-practice checks in categories **cost optimization, performance, security, fault tolerance, service limits (quotas), operational excellence**. **Basic/Developer Support**: core security checks + [service quotas](00-cloudops-basics.md#service-quotas); **Business, Enterprise On-Ramp, Enterprise Support**: all checks, Application Programming Interface (API) access, organizational view, **Trusted Advisor Priority** (prioritized recommendations from your account team).
- **Core security checks and [remediation](00-cloudops-basics.md#remediation):**

| Check | Remediation |
|---|---|
| **Security groups – specific ports unrestricted** (e.g., 22/3389/3306 open to 0.0.0.0/0) | Restrict source Classless Inter-Domain Routing ranges (CIDRs)/Security Groups (SGs); use Session Manager instead of Secure Shell (SSH); Config rule `restricted-ssh` + auto-remediation |
| **Amazon S3 bucket permissions** (open access) | Enable **S3 Block Public Access**, fix bucket policy/ACLs |
| **MFA on root account** | Enable root MFA |
| **IAM use** (at least one IAM user/role besides root) | Use IAM/Identity Center identities |
| **Amazon Elastic Block Store (EBS) public snapshots** / **Relational Database Service (RDS) public snapshots** | Make snapshots private (and enable EBS block public access for snapshots) |
| **Exposed access keys** (found on public repos) | Rotate/delete keys immediately; AWS may quarantine the user (`AWSCompromisedKeyQuarantine` policy) |
| **IAM access key rotation**, **IAM password policy** (full checks) | Rotate keys, set password policy |
| **CloudTrail logging**, **Elastic Load Balancing (ELB) security policies** / listener security, **CloudFront certificates**, Route 53 Mail Exchange (MX)/Sender Policy Framework (SPF) records (full checks) | Enable org trail; use current Transport Layer Security (TLS) policies; renew certs |

- **Automation**: Trusted Advisor check results → **EventBridge** (check refresh events, us-east-1) → Lambda/SSM remediation; **Trusted Advisor API** (Business+) to refresh and read checks; organizational view reports; integration with **Security Hub** findings.
- **Exam triggers:** "Trusted Advisor flags unrestricted SSH" → restrict Security Group [SG] (and Config remediation to keep it fixed). "Automatically act when a check changes to red" → **EventBridge rule → Lambda**. "Need all Trusted Advisor checks" → **Business Support or higher**.

### Compliance enforcement and continuous monitoring (skill 4.1.5) `[also: DOP]`

- **AWS Config:**
  - **Configuration recorder** (all or selected resource types; continuous or **periodic/daily** recording to cut cost), **delivery channel** (S3 + optional SNS), **configuration items** and **timeline**, **relationships**.
  - **Config rules**: **AWS managed** (e.g., `restricted-ssh`, `s3-bucket-public-read-prohibited`, `encrypted-volumes`, `rds-storage-encrypted`, `cloudtrail-enabled`, `required-tags`, `approved-amis-by-id`, `iam-password-policy`, `root-account-mfa-enabled`, `desired-instance-type`, `vpc-flow-logs-enabled`), **custom** (Lambda or **Guard** policy-as-code). Trigger types: **configuration change** and/or **periodic**. **Proactive** evaluation mode (check before provisioning).
  - **Remediation**: manual or **automatic** with Systems Manager (SSM) Automation documents (e.g., `AWS-DisablePublicAccessForSecurityGroup`, `AWS-EnableS3BucketEncryption`, `AWSConfigRemediation-*`), retries; needs an Automation assume role.
  - **Conformance packs**: collections of rules + remediation as one [YAML Ain't Markup Language [YAML]](00-cloudops-basics.md#yaml-and-json) package (templates for **Center for Internet Security [CIS]**, **Payment Card Industry Data Security Standard [PCI DSS]**, **Health Insurance Portability and Accountability Act [HIPAA]**, **National Institute of Standards and Technology [NIST]**, operational best practices); deploy across the org (`PutOrganizationConformancePack`) or with StackSets; compliance score.
  - **Aggregators**: multi-account, multi-Region view (org-wide with Organizations); **advanced queries** (Structured Query Language [SQL] over current config).
  - **Organization rules** deployed from a delegated admin.
- **Region and service selection enforcement**: **SCP Region deny** (`aws:RequestedRegion`), Control Tower **Region deny control**, SCP denying non-approved services (allow-list), Config rules to detect resources in unapproved Regions (aggregator), **service-linked conditions** like `ec2:InstanceType`; opt-in Regions disabled by default (enable only needed ones); data residency via KMS keys in-Region.
- **Other continuous compliance**: **Security Hub** standards (CIS, AWS Foundational Security Best Practices, Payment Card Industry [PCI] DSS, NIST 800-53) built on Config; **AWS Audit Manager** (collect evidence mapped to frameworks for auditors); **CloudTrail Lake**; **AWS Artifact** (AWS compliance reports).
- **Exam triggers:** "continuously check all accounts against PCI DSS controls with one package" → **organization conformance pack**. "Automatically encrypt any unencrypted S3 bucket" → **Config rule + automatic remediation**. "Block use of Regions outside the EU" → **SCP Region deny**. "Single view of compliance across 50 accounts" → **Config aggregator** / Security Hub.

---

## Task 4.2: Protect data and infrastructure

### Data classification scheme (skill 4.2.1)

- **Define levels** (e.g., Public, Internal, Confidential, Restricted) with required controls per level (encryption with Customer Master Key (now 'customer managed key') [CMK], access groups, retention, logging, residency).
- **Discover and label**: **Amazon Macie** (automated sensitive data discovery in S3: Personally Identifiable Information [PII], financial, credentials; managed + custom data identifiers; findings to EventBridge/Security Hub), **tags** (`DataClassification=Confidential`) on buckets, databases, volumes; Glue/Lake Formation **LF-Tags** for data lakes.
- **Enforce**: **tag policies** (standard tag keys/values), SCPs/IAM **ABAC conditions** on `aws:ResourceTag/DataClassification`, **Config rules** (`required-tags`, encryption rules), separate accounts/buckets per classification, S3 Block Public Access, **CloudWatch Logs data protection** to mask sensitive data in logs.
- **Exam triggers:** "identify buckets with PII automatically and tag/restrict them" → **Macie → EventBridge → Lambda** (tag, apply policy). "Ensure every resource carries a classification tag" → **tag policies + Config `required-tags`** (+ SCP requiring tags on create).

### Encryption at rest with AWS KMS (skill 4.2.2) `[also: DOP, SAA]`

- **Key types**: AWS owned (invisible), **AWS managed** (`aws/<service>`; auto-rotated yearly; can't change policy; **can't share cross-account**), **customer managed keys** (full control: key policy, grants, rotation, enable/disable, deletion; cross-account). Symmetric (AES-256; for service integrations), asymmetric (RSA/ECC), HMAC. **Multi-Region keys** (same key material in several Regions). Imported key material (BYOK) and custom key stores (CloudHSM, external).
- **Access control**: **key policy** (required; default statement enables IAM policies in the account), **IAM policies** (only if key policy allows the account), **grants** (temporary delegations used by services like EBS/RDS), conditions like **`kms:ViaService`** (only via a specific service) and `kms:EncryptionContext`. Separate **key administrators** from **key users**.
- **Rotation**: automatic for customer managed symmetric keys (90–2,560 days; default 365) and on-demand; key ID unchanged, old material kept; imported/asymmetric keys → manual rotation via **aliases**.
- **Deletion**: schedule with **7–30 days** waiting period (cancel during it); **disable** instead to test impact; CloudWatch [alarm](00-cloudops-basics.md#alarm) on CloudTrail events for attempted use of a pending-deletion key.
- **Service setup**:
  - **EBS**: **encryption by default** per Region (choose default key); encrypt existing volume → snapshot → copy with encryption → new volume (or launch with encryption override).
  - **RDS/Aurora**: encryption only at creation → snapshot → copy encrypted → restore; read replicas follow source; cross-Region replicas use a destination-Region key.
  - **S3**: default encryption (Server-Side Encryption with S3-managed keys [SSE-S3] or SSE-KMS/Dual-layer Server-Side Encryption with KMS keys [DSSE-KMS]), **Bucket Keys**, bucket policy to require encryption headers; existing objects re-encrypted with **S3 Batch Operations** copy.
  - **EFS** (at creation), **DynamoDB** (owned/managed/CMK, switchable), **SQS/SNS** (SSE-KMS), **CloudWatch Logs** (associate key), **Secrets Manager**, **Backup vaults**, **ECR**, **Lambda environment variables**.
- **Troubleshooting KMS:**
  - `AccessDeniedException` / `KMS.AccessDenied`: key policy doesn't allow the principal or the account; IAM policy lacks `kms:Decrypt`/`GenerateDataKey`; **grant missing** for a service; `kms:ViaService` condition mismatch; cross-account key not shared on both sides.
  - `DisabledException` / `KMSInvalidStateException`: key **disabled** or **pending deletion** → re-enable/cancel deletion (Lambda env vars, EBS volumes, RDS instances become unusable).
  - `ThrottlingException`: KMS request quota (per account/Region; shared across services) → S3 Bucket Keys, [data key](00-cloudops-basics.md#envelope-encryption) [caching](00-cloudops-basics.md#caching), request quota increase.
  - Can't share snapshot/AMI → encrypted with **AWS managed key** → re-encrypt with CMK.
  - Auto Scaling group (ASG) fails to launch instances with encrypted EBS using a CMK → key policy must allow the **Auto Scaling service-linked role** (and grant creation).
  - Wrong Region: keys are Regional (except multi-Region keys).
- **Exam triggers:** "all new EBS volumes encrypted automatically" → **EBS encryption by default**. "Audit and control who uses the key, share with another account" → **customer managed key**. "Lambda can't decrypt env vars after key change" → key disabled/pending deletion or policy.

### Encryption in transit with ACM (skill 4.2.3) `[also: SAA]`

- **AWS Certificate Manager**: free **public certificates** (domain validated), **private certificates** via **AWS Private Certificate Authority (CA)**, **imported certificates** (third-party CA). Deploy on **ELB (ALB/Network Load Balancer [NLB] listeners)**, **CloudFront**, **API Gateway** custom domains, App Runner, Cognito, Amplify, Elastic Beanstalk (via ELB). **Exportable public certificates** (paid) allow use on EC2/on-prem; otherwise public certs can't be exported.
- **Validation**: **[Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl)** (Canonical Name [CNAME] record; one-click in Route 53; enables **automatic renewal** as long as the record exists and the cert is in use) or **email** (renewal requires action).
- **Renewal**: ACM tries renewing managed certs **60 days before expiry**; **imported certificates are never renewed by ACM** (re-import). **Expiry events** via **EventBridge** ("ACM Certificate Approaching Expiration", starting 45 days before, configurable) and **AWS Health**; **`DaysToExpiry`** CloudWatch [metric](00-cloudops-basics.md#metric); AWS Config rule `acm-certificate-expiration-check`.
- **Region rules**: **CloudFront** certs must be in **us-east-1**; ALB/API Gateway regional certs in the resource's Region.
- **Enforcing TLS**: ALB/NLB **security policies** (`ELBSecurityPolicy-TLS13-1-2-2021-06` etc.; choose TLS 1.2+/1.3, Perfect Forward Secrecy [PFS]), **Hypertext Transfer Protocol (HTTP)→Hypertext Transfer Protocol Secure (HTTPS) redirect** listener rule, CloudFront **viewer protocol policy** (redirect/HTTPS only) + **minimum TLS version** + [origin](00-cloudops-basics.md#cdn-origin-and-cache-key) protocol policy, S3 bucket policy `aws:SecureTransport=false` deny, RDS `rds.force_ssl` (PostgreSQL/SQL Server) / `require_secure_transport` (MySQL), ElastiCache in-transit encryption, Virtual Private Network [VPN] (IPsec), **mutual TLS** (Application Load Balancer [ALB], API Gateway) with trust stores.
- **Troubleshooting**: cert stuck **Pending validation** → CNAME not created or wrong hosted zone/Certification Authority Authorization (CAA) record blocking Amazon; renewal failed → DNS record removed or domain not resolving; "certificate not available in the CloudFront dropdown" → not in us-east-1; browser warnings → hostname mismatch (missing SAN or wildcard) or incomplete chain on imported cert; old clients fail → too-strict TLS policy.
- **Exam triggers:** "certificates must renew automatically" → **ACM + DNS validation**. "Alert 30 days before an imported certificate expires" → **EventBridge/DaysToExpiry alarm**. "Force HTTPS for an S3 bucket" → **`aws:SecureTransport` deny**.

### Securely storing secrets (skill 4.2.4) `[also: DOP, SAA]`

- **AWS Secrets Manager**: secrets encrypted with KMS; **automatic rotation** (managed rotation for RDS/Aurora/Redshift/DocumentDB and others, or Lambda rotation functions; single-user vs **alternating-users** strategy); **staging labels** (`AWSCURRENT`, `AWSPENDING`, `AWSPREVIOUS`); **resource policies** for cross-account; **multi-Region replication**; [versioning](00-cloudops-basics.md#versioning); **RDS-managed master user passwords**; retrieval via Software Development Kit (SDK), Lambda **Parameters and Secrets extension** (caching), Elastic Container Service (ECS)/EKS secret injection, CloudFormation dynamic references.
- **Systems Manager Parameter Store**: `String`, `StringList`, **`SecureString`** (KMS); **standard** (free, 4 KB, 10,000 params) and **advanced** tiers (8 KB, 100,000 params, **parameter policies**: expiration, expiration notification, no-change notification); hierarchies (`/prod/app/db-url`), versioning, labels; can reference Secrets Manager secrets (`/aws/reference/secretsmanager/...`); public parameters (latest Amazon Machine Image [AMI] IDs: `/aws/service/ami-amazon-linux-latest/...`).
- **Troubleshooting**: rotation failures (Lambda in VPC can't reach Secrets Manager endpoint or the database → VPC endpoint/SG; Lambda permissions; DB user privileges); app using cached old password after rotation → refresh cache/handle `AWSPREVIOUS`; KMS key permissions for decrypt.
- **Never**: hard-code secrets in code, user data, Amazon Machine Images (AMIs), CloudFormation parameters (use `NoEcho` + dynamic references), plain environment variables, [Git](00-cloudops-basics.md#git-basics).
- **Exam triggers:** "rotate DB credentials every 30 days automatically" → **Secrets Manager**. "Store app config and some secure strings cheaply" → **Parameter Store SecureString**. "Notify when a parameter hasn't changed in 90 days" → **advanced tier parameter policy**.

### Reports and remediation of security findings (skill 4.2.5) `[also: DOP, SAA]`

- **Amazon GuardDuty**: threat detection from CloudTrail management events, VPC Flow Logs, DNS logs (no setup on sources) + optional protection plans (S3, EKS audit logs, **Runtime Monitoring** agent for EC2/ECS/EKS, **Malware Protection** for EC2 EBS volumes and S3 uploads, RDS login activity, Lambda network activity); **Extended Threat Detection** (multi-stage attack sequences). Findings have **severity** (low/medium/high/critical) and types (e.g., `UnauthorizedAccess:EC2/SSHBruteForce`, `CryptoCurrency:EC2/BitcoinTool`, `InstanceCredentialExfiltration`, `Recon:EC2/PortProbeUnprotectedPort`). Multi-account via **delegated administrator** (auto-enable for new accounts). **Suppression rules**, **trusted IP lists** and **threat lists**. Export findings to S3; EventBridge for automation.
  - Typical remediation: isolate instance (swap to a **quarantine SG** with no rules), snapshot EBS for forensics, revoke role sessions/rotate keys, block IP in Network Access Control List (NACL)/Web Application Firewall (WAF)/Network Firewall.
- **Amazon Inspector**: continuous **vulnerability scanning** of **EC2** (SSM agent-based or **agentless** via EBS snapshots), **ECR images** (on push + continuous), **Lambda** (packages and code); **risk score** combines Common Vulnerability Scoring System (CVSS) with network reachability and exploitability; **suppression rules**; Software Bill of Materials (SBOM) export; CIS benchmark assessments; findings to Security Hub/EventBridge. Remediation: **Patch Manager** patching, rebuild images (Image Builder), update Lambda dependencies.
- **AWS Config**: non-compliant resources → **remediation actions** (manual or automatic SSM documents); compliance reports via aggregators/conformance packs.
- **AWS Security Hub**: **CSPM** (cloud security posture management) with **standards** (AWS Foundational Security Best Practices, CIS, PCI DSS, NIST) → **security score**; aggregates **findings** (AWS Security Finding Format [ASFF]/Open Cybersecurity Schema Framework [OCSF] formats) from GuardDuty, Inspector, Macie, Access Analyzer, Config, Firewall Manager, Health, partners; **cross-Region aggregation**, **central configuration** across the org via delegated admin; **automation rules** (update/suppress findings automatically, e.g., set severity, assign); **custom actions** → EventBridge → remediation; **Automated Security Response on AWS** solution for prebuilt remediations. Newer Security Hub correlates signals into exposures and attack paths across these services.
- **AWS Security Agent** (now marketed as part of **AWS Continuum**): an AI "[frontier agent](00-cloudops-basics.md#ai-agents)" for application security. Its **penetration testing** capability runs automated pen tests against **verified domains** you own (web applications or [generative AI](00-cloudops-basics.md#generative-ai-and-foundation-models) applications), optionally inside your VPC and with test credentials, logs to a CloudWatch log group, is billed per task hour, and can open **pull requests with code fixes**. Its code capability proves exploitability of vulnerabilities in a sandbox and proposes patches. Best practices: test **non-production** environments, use an IAM service role, don't upload production credentials or PII, keep humans approving actions. Exam angle: "continuously pen-test our web app and get remediation PRs with minimal security team effort" → Security Agent.
- **Reporting**: Security Hub [dashboards](00-cloudops-basics.md#dashboards)/insights, Config aggregator reports, GuardDuty/Inspector exports to S3 → Athena/Quick, **Audit Manager** assessment reports, **Detective** for investigation of GuardDuty findings.
- **Exam triggers:** "single place to see findings and compliance scores across all accounts and Regions" → **Security Hub** (delegated admin + cross-Region aggregation). "Automatically isolate an EC2 instance when GuardDuty reports crypto-mining" → **GuardDuty → EventBridge → Lambda/SSM** (quarantine SG). "Patch EC2 vulnerabilities Inspector found" → **Patch Manager**. "Suppress known false positives" → **suppression rules / Security Hub automation rules**.

---

## Gotchas and exam traps

- Password policy applies only to **IAM users' console passwords**.
- MFA enforcement via **`aws:MultiFactorAuthPresent`** deny policy; CLI uses `get-session-token`.
- **Policy simulator** tests without real calls; **Access Analyzer** finds external sharing, unused access, generates policies; **credential report** for key age/MFA audits.
- Encoded EC2 errors → **`sts decode-authorization-message`**.
- **SCPs** don't affect the management account or service-linked roles; never grant.
- Trusted Advisor full checks need **Business Support+**; events go to **EventBridge in us-east-1**.
- **Conformance packs** = rules + remediation bundles; **aggregators** = multi-account view.
- **AWS managed KMS keys** can't be shared; disabled/pending-deletion keys break services.
- ASG + encrypted EBS with CMK → key policy must allow the **Auto Scaling service-linked role**.
- **ACM imported certs don't auto-renew**; CloudFront certs in **us-east-1**; DNS validation enables auto-renewal.
- **Secrets Manager** rotates; **Parameter Store** advanced tier offers expiration policies but no rotation.
- **GuardDuty** detects, doesn't block; remediate via EventBridge.

## Quick-recall summary

- **IAM**: password policy, MFA (enforce with conditions), roles and federation (SAML/OIDC, Identity Center, Roles Anywhere), resource policies, condition keys, evaluation logic.
- **Troubleshoot access**: CloudTrail error codes, decode-authorization-message, policy simulator, Access Analyzer (external/internal/unused, validation, generation), Access Advisor, credential report.
- **Multi-account**: Organizations (OUs, SCPs, RCPs, delegated admin), Identity Center, Control Tower.
- **Checks and compliance**: Trusted Advisor security checks + EventBridge automation, Config rules/remediation/conformance packs/aggregators, Region deny SCPs, Security Hub standards, Audit Manager.
- **Data protection**: classification (Macie, tags, tag policies, ABAC), KMS (key types, policies, grants, rotation, troubleshooting), ACM (DNS validation, renewal, us-east-1, TLS policies), Secrets Manager vs Parameter Store, GuardDuty/Inspector/Security Hub/Security Agent findings and remediation.
