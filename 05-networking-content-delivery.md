# Domain 5: Networking and Content Delivery (18%)

> 🔰 New to a term? Concepts link to plain-English explanations in [the basics page](00-cloudops-basics.md) on first mention, and every abbreviation is spelled out on first use and listed in the [glossary](00-abbreviations-glossary.md).

> **About 9 of the 50 scored questions.**
>
> Tests whether you can build and fix Virtual Private Cloud (VPC) networking, private connectivity, [Domain Name System [DNS]](00-cloudops-basics.md#dns-records-and-ttl) and content delivery: configure subnets, routes, firewalls and endpoints; audit network protection; cut network cost; set up Route 53 and CloudFront/Global Accelerator; and troubleshoot connectivity with logs and tools.
>
> [Tags](00-cloudops-basics.md#tags) like `[also: ANS]` mark material you met in another exam. It is still covered in full here.

## Table of contents

- [Exam tasks and skills](#exam-tasks-and-skills)
- [Task 5.1: Networking features and connectivity](#task-51-networking-features-and-connectivity)
- [Task 5.2: Domains, DNS and content delivery](#task-52-domains-dns-and-content-delivery)
- [Task 5.3: Troubleshoot network connectivity](#task-53-troubleshoot-network-connectivity)
- [Gotchas and exam traps](#gotchas-and-exam-traps)
- [Quick-recall summary](#quick-recall-summary)

## Exam tasks and skills

> Summarized from the official SOA-C03 exam guide (v1.2). Exact wording: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03-domain5.html

### Task 5.1: Implement and optimize networking features and connectivity
- **5.1.1** Configure a VPC (subnets, route tables, network Access Control Lists [ACLs], security groups, Network Address Translation [NAT] gateways, internet gateway, egress-only internet gateway).
- **5.1.2** Configure private connectivity (VPC endpoints, PrivateLink, VPC peering).
- **5.1.3** Audit network protection services in a single account (Route 53 Resolver DNS Firewall, Web Application Firewall [WAF], Shield, Network Firewall).
- **5.1.4** Optimize the cost of network architectures.

### Task 5.2: Configure domains, DNS services and content delivery
- **5.2.1** Configure DNS (Route 53 Resolver).
- **5.2.2** Implement Route 53 routing policies, configurations and query logging.
- **5.2.3** Configure content and service distribution (CloudFront, Global Accelerator).

### Task 5.3: Troubleshoot network connectivity issues
- **5.3.1** Troubleshoot VPC configurations (subnets, route tables, Network Access Control Lists [NACLs], security groups, transit gateways, NAT gateways).
- **5.3.2** Collect and interpret networking logs (VPC Flow Logs, Elastic Load Balancing [ELB] access logs, WAF web Access Control List [ACL] logs, CloudFront logs, container logs).
- **5.3.3** Identify and remediate CloudFront [caching](00-cloudops-basics.md#caching) issues.
- **5.3.4** Troubleshoot hybrid and private connectivity.
- **5.3.5** Configure and analyze CloudWatch network monitoring services.

---

## Task 5.1: Networking features and connectivity

### VPC configuration (skill 5.1.1) `[also: ANS, SAA]`

- **VPC**: Regional; Internet Protocol version 4 (IPv4) [Classless Inter-Domain Routing [CIDR]](00-cloudops-basics.md#cidr-and-subnet-sizing) **/16 to /28** (plus up to 4 secondary Classless Inter-Domain Routing ranges [CIDRs] by default, adjustable), optional Internet Protocol version 6 (IPv6) /56 (Amazon-provided or Bring Your Own IP [BYOIP]); **DNS attributes** `enableDnsSupport` (Amazon DNS resolver at VPC base +2) and `enableDnsHostnames` (public DNS names for public IP addresses [IPs]; required for private hosted zones and interface endpoint private DNS); tenancy; **VPC IP Address Manager (IPAM)** to allocate non-overlapping CIDRs from pools and monitor utilization; **DHCP options sets** (custom DNS servers, domain name).
- **Subnets**: one Availability Zone (AZ) each; **5 reserved IPs**; "auto-assign public IPv4" setting; IPv6-only or dual-stack subnets.
- **Route tables**: main + custom; one per subnet association; **local route** can't be removed (can target an appliance for more-specific inspection routing); targets: `igw-`, `nat-`, `eigw-`, `tgw-`, `pcx-` (peering), `vgw-`, `vpce-` (gateway endpoints via prefix lists, Gateway Load Balancer [GWLB] endpoints), Elastic Network Interface (ENI), Network Firewall endpoints; **gateway route tables** (associated with IGW/VGW for ingress routing to appliances); **[longest prefix match](00-cloudops-basics.md#route-table-and-longest-prefix-match)**; **propagated routes** from VGW/TGW.
- **Internet gateway (IGW)**: one per VPC, horizontally scaled, no bandwidth limit; performs 1:1 NAT for public IPv4 addresses.
- **Egress-only internet gateway**: IPv6 outbound-only ([stateful](00-cloudops-basics.md#stateful-vs-stateless-filtering)); route `::/0 → eigw`.
- **NAT gateway**: in a **[public subnet](00-cloudops-basics.md#public-vs-private-subnet)** with an **Elastic IP** (public NAT) or **private NAT** (no Elastic IP address [EIP]; for routing to other private networks with overlapping CIDRs via TGW/VGW); 5 Gbps auto-scaling to 100 Gbps; up to 8 IPs (secondary IPs for more connections: 55,000 simultaneous connections per destination per Internet Protocol [IP]); **zonal** → **one per AZ** for High Availability (HA); no Security Group (SG) on the NAT itself; metrics `ErrorPortAllocation` (out of ports → add IPs), `PacketsDropCount`, `ConnectionAttemptCount`/`ConnectionEstablishedCount`, `IdleTimeoutCount` (350 s idle timeout); Network Address Translation IPv6-to-IPv4 (NAT64) with DNS IPv6-to-IPv4 synthesis (DNS64) for IPv6-only subnets reaching IPv4.
- **Security groups**: stateful, allow-only, at ENI level; rules by CIDR, prefix list or **SG reference**; default 60 inbound/60 outbound rules, 5 Security Groups (SGs) per ENI; **security group VPC associations** let one SG be used across Virtual Private Clouds (VPCs) in the same Region; **shared SGs** in VPC sharing.
- **Network ACLs**: stateless, subnet level, numbered rules (lowest first), allow + deny, **ephemeral ports 1024–65535** for returns; default Network Access Control List (NACL) allows all; new custom NACL denies all; 20 rules per direction default.
- **Elastic IP addresses**: static public IPv4; charges for **all public IPv4** addresses (in use or not); **EIP transfer** between accounts; reverse DNS (PTR) for email; re-associate on failover.
- **Exam triggers:** "private instances need outbound internet" → **NAT gateway in public subnet + route `0.0.0.0/0 → nat`**. "IPv6 instances outbound only" → **egress-only IGW**. "Block a specific IP" → **NACL deny**. "Subnet ran out of IPs" → secondary CIDR + new subnet.

### Private connectivity (skill 5.1.2) `[also: ANS, SAA]`

- **Gateway VPC endpoints** (Simple Storage Service [S3], DynamoDB): free; route-table entries with **prefix lists**; **endpoint policies**; not usable from on-prem/peered VPCs/TGW; same Region only. Combine with bucket policy `aws:SourceVpce` to lock buckets to the VPC.
- **Interface VPC endpoints (Amazon Web Services [AWS] PrivateLink)**: Elastic Network Interfaces (ENIs) with private IPs per AZ subnet; **security group on the endpoint must allow 443 from clients**; **private DNS** (needs `enableDnsSupport` + `enableDnsHostnames`) so default service hostnames resolve privately; endpoint policies (most services); reachable from on-prem via DX/VPN and from peered VPCs/TGW (with Route 53 Resolver inbound endpoints or private hosted zones for DNS); hourly + per-GB cost per AZ. Common for Systems Manager (`ssm`, `ssmmessages`, `ec2messages`), CloudWatch (`monitoring`, `logs`), Elastic Container Registry [ECR] (`ecr.api`, `ecr.dkr` + S3 gateway), Security Token Service (STS), Key Management Service (KMS), Secrets Manager, Simple Queue Service (SQS), Simple Notification Service (SNS), Lambda, CloudFormation.
- **Endpoint services (PrivateLink provider)**: expose your service behind a **Network Load Balancer [NLB]** (or GWLB) to consumers in other VPCs/accounts; **acceptance required** option, **allowed principals**, private DNS name with domain verification; works with **overlapping CIDRs**; one-way (consumer initiates).
- **Resource endpoints / VPC Lattice** (service networks) for sharing resources like databases across VPCs privately.
- **VPC peering**: one-to-one, same/cross-account, same/cross-Region; **no transitive routing**, **no overlapping CIDRs**, **no edge-to-edge** (can't use peer's IGW, NAT, Virtual Private Network [VPN], Direct Connect [DX], gateway endpoint); requester creates, **accepter accepts**; add **routes in both** VPCs' route tables; update SGs (can reference peer SGs within the same Region); enable **DNS resolution** option to resolve peer's private DNS names; Maximum Transmission Unit (MTU) 1500 cross-Region.
- **Transit Gateway**: hub for many VPCs/VPN/DX; attachments, **Transit Gateway (TGW) route tables** (association + propagation), blackhole routes for isolation, inter-Region peering (static routes), **appliance mode** for stateful inspection; shared via RAM.
- **Exam triggers:** "Elastic Compute Cloud (EC2) in private subnet must reach S3 with no internet/NAT" → **S3 gateway endpoint**. "Private access to Systems Manager (SSM)/CloudWatch/KMS without NAT" → **interface endpoints**. "Share an internal Application Programming Interface (API) with 100 customer VPCs with overlapping CIDRs" → **PrivateLink endpoint service**. "Two VPCs need full private connectivity" → **peering** (+ routes both ways).

### Auditing network protection services (skill 5.1.3) `[also: SAA]`

- **AWS WAF** (web ACLs on CloudFront, Application Load Balancer [ALB], API Gateway, AppSync, Cognito, App Runner, Verified Access, Amplify):
  - **Audit**: review rules and **rule priority order**, default action, managed rule group versions, **Count mode** to test rules without blocking, **sampled requests** in console, **CloudWatch metrics** per rule (`AllowedRequests`, `BlockedRequests`, `CountedRequests`, `CaptchaRequests`), **web ACL logging** (to CloudWatch Logs `aws-waf-logs-*`, S3, or Firehose; with redacted fields and logging filters), **WAF [dashboards](00-cloudops-basics.md#dashboards)**, labels.
  - **Rate-based rules**, IP sets, geo match, Bot Control, ATP/ACFP, size constraints, scope-down statements.
  - **False positives** → move rule to Count, add scope-down or exclusion (rule action override), check log `terminatingRuleId`.
- **AWS Shield**: **Standard** (automatic L3/L4 protection, free). **Advanced** (subscription): protected resources list, **attack event history** and CloudWatch metrics (`DDoSDetected`, `DDoSAttackBitsPerSecond`), **health-based detection** (associate Route 53 health checks), **automatic application-layer Distributed Denial of Service (DDoS) mitigation** (WAF rules), **Shield Response Team** access (proactive engagement with Business/Enterprise support), cost protection, protection groups.
- **AWS Network Firewall**: firewall policy with **stateless** (ordered 5-tuple) and **stateful** rule groups (domain lists using Hypertext Transfer Protocol [HTTP] Host/TLS Server Name Indication [SNI], Suricata-compatible rules, managed threat signatures), **strict vs action order** evaluation, [Transport Layer Security [TLS]](00-cloudops-basics.md#tls-and-certificates) inspection; firewall endpoints per AZ in dedicated subnets; **routing must send traffic through the endpoints symmetrically** (IGW ingress route table + subnet routes). Audit via **alert logs** and **flow logs** (to S3/CloudWatch Logs/Firehose), CloudWatch metrics (`DroppedPackets`, `PassedPackets`), and the firewall monitoring dashboard.
- **Route 53 Resolver DNS Firewall**: rule groups (domain lists: custom or **AWS managed** lists of malware/botnet domains; actions **ALLOW, BLOCK** (NODATA/NXDOMAIN/override response), **ALERT**) associated with VPCs by priority; **fail-open vs fail-closed** setting; **advanced rules** detect DNS tunneling/Domain Generation Algorithm (DGA) domains; logs via **Resolver query logging**; CloudWatch metrics. Shareable via RAM, central via Firewall Manager.
- **Audit checklist (single account)**: protection attached to every internet-facing resource (CloudFront/ALB/API Gateway have web ACLs; Shield Advanced resources listed), logging enabled for WAF/Network Firewall/DNS Firewall/Resolver queries, rules in the right mode (not stuck in Count), managed rule groups current, Config rules (`wafv2-logging-enabled`, `alb-waf-enabled`, `netfw-policy-default-action-*`), Security Hub controls, Firewall Manager for multi-account consistency.
- **Exam triggers:** "verify which WAF rule blocked a legitimate request" → **WAF logs/sampled requests** (`terminatingRuleId`). "Test new rules safely" → **Count mode**. "Block DNS lookups to known malicious domains from the VPC" → **DNS Firewall with AWS managed domain list**. "Confirm Network Firewall drops traffic to a domain" → **alert logs** + `DroppedPackets`.

### Optimizing network costs (skill 5.1.4) `[also: SAA]`

- **Data transfer facts**: inbound free; same-AZ private IP free; **cross-AZ charged each direction**; cross-Region charged; internet egress tiered; using **public/Elastic IPs** inside AWS is billed like cross-AZ traffic.
- **Levers**:
  - **Gateway endpoints** for S3/DynamoDB (removes NAT per-GB processing and keeps traffic private).
  - **Interface endpoints** for heavy AWS API traffic from private subnets (cheaper per GB than NAT for high volume).
  - **NAT gateway per AZ** for prod (avoids cross-AZ charges to a single NAT), **single NAT** for dev; consolidate egress through a central egress VPC (TGW) only if processing fees net out.
  - Keep chatty tiers in the same AZ; enable **ALB cross-zone** (free) but note **NLB cross-zone** incurs inter-AZ charges.
  - **CloudFront** for egress to users (S3→CloudFront transfer free; cheaper egress; caching reduces [origin](00-cloudops-basics.md#cdn-origin-and-cache-key) traffic).
  - **VPC peering** has no hourly/processing charge vs **TGW** per-attachment-hour + per-GB.
  - **Direct Connect** lowers egress price for large steady volumes.
  - Release unused **Elastic IPs/public IPv4**, use IPv6 and IPAM public IP insights.
  - Find drivers with **Cost Explorer** (usage types `DataTransfer-Regional-Bytes`, `NatGateway-Bytes`), **Cost and Usage Report (CUR) + Athena**, **VPC Flow Logs**, **Network Flow Monitor**.
- **Exam triggers:** "NAT gateway bills high due to S3 downloads" → **S3 gateway endpoint**. "Cross-AZ data transfer high between app and cache" → co-locate/AZ-aware routing. "Reduce internet egress for global static content" → **CloudFront**.

---

## Task 5.2: Domains, DNS and content delivery

### Route 53 Resolver and DNS configuration (skill 5.2.1) `[also: ANS]`

- **Route 53 Resolver** (AmazonProvidedDNS at VPC CIDR base +2 and 169.254.169.253): answers for public DNS, **private hosted zones** associated with the VPC, and EC2 internal names.
- **Private hosted zones**: associate with one or more VPCs (cross-account association via Command Line Interface [CLI]/API authorization); require VPC `enableDnsSupport` and `enableDnsHostnames`; **split-horizon DNS** (same domain public + private zones; private wins inside the VPC).
- **Hybrid DNS**:
  - **Inbound endpoint** (ENIs in your VPC, ≥ 2 Availability Zones [AZs]): on-prem DNS servers **forward** queries for AWS private zones to these IPs.
  - **Outbound endpoint** + **forwarding rules** (domain → on-prem DNS IPs): VPC resources resolve on-prem names (`corp.example.com`). **System rules** override. Share rules with other accounts via **RAM** and associate with their VPCs.
  - Endpoints need **security groups** allowing DNS ([TCP](00-cloudops-basics.md#ports-and-protocols)/UDP 53) and routes/connectivity (VPN/DX).
- **Route 53 Profiles**: package DNS configuration (private hosted zones, Resolver rules, DNS Firewall rule groups) and share/apply it to many VPCs and accounts.
- **Domain Name System Security Extensions (DNSSEC)**: **signing** for public hosted zones (Key-Signing Key [KSK] backed by a KMS asymmetric key in us-east-1; establish chain of trust with Delegation Signer [DS] record at parent) and **DNSSEC validation** on the Resolver for VPCs.
- **Exam triggers:** "on-prem servers must resolve names in an AWS private hosted zone" → **inbound endpoint** + conditional forwarder on-prem. "EC2 must resolve on-prem domain" → **outbound endpoint + forwarding rule**. "Private hosted zone not resolving" → VPC not associated or DNS attributes disabled.

### Route 53 routing policies, configurations and query logging (skill 5.2.2) `[also: ANS, SAA]`

- **Hosted zones** (public/private), **NS and SOA** records created automatically; **delegation**: registrar Name Server (NS) must match the hosted zone NS (common cause of "domain doesn't resolve" after migration); **subdomain delegation** with NS records in the parent zone; domain registration and transfer (registrar lock, auth code).
- **Records**: A, AAAA, **Canonical Name [CNAME]** (not at apex), **Alias** (apex allowed; to ELB, CloudFront, S3 website, API Gateway, VPC interface endpoint, Global Accelerator, Elastic Beanstalk, another record in the zone; free queries; Time To Live [TTL] from target), Mail Exchange (MX), TXT, Sender Policy Framework (SPF) via TXT, Certification Authority Authorization (CAA), NS, PTR (reverse lookups; set via EIP reverse DNS for EC2), Service (SRV), HTTPS/Service Binding (SVCB).
- **Routing policies**:
  - **Simple**, **weighted** (0–255 per record; 0 = no traffic unless all 0), **[latency](00-cloudops-basics.md#iops-throughput-and-latency)** (per Region), **failover** (primary/secondary with health checks), **geolocation** (continent/country/US state + **default**), **geoproximity** (Traffic Flow with **bias**; or coordinates for local zones), **IP-based** (CIDR collections), **multivalue answer** (up to 8 healthy values).
  - Combine with **health checks** and **Evaluate Target Health** for Alias.
  - **Traffic Flow** policies and policy records (visual, versioned complex routing trees).
- **TTL tuning**: lower TTL before migrations/failover-sensitive records; higher TTL to reduce query cost.
- **Query logging**:
  - **Public DNS query logging**: queries Route 53 receives for a **public hosted zone** → **CloudWatch Logs** (log group must be in **us-east-1**, resource policy allowing Route 53).
  - **Resolver query logging**: queries made **from your VPCs** (to any domain, incl. private zones, forwarded domains) → CloudWatch Logs, S3 or Firehose; shareable config via RAM. Fields include query name, type, response code (NXDOMAIN), source IP/instance, DNS Firewall action.
- **Exam triggers:** "send 20% of traffic to new stack" → **weighted**. "Users in Japan must use the Tokyo endpoint" → **geolocation**. "Point apex to ALB" → **Alias**. "Log which instances query a suspicious domain" → **Resolver query logging**. "Domain stopped resolving after creating a new hosted zone" → registrar **NS mismatch**.

### Content and service distribution: CloudFront and Global Accelerator (skill 5.2.3) `[also: ANS, SAA]`

- **Amazon CloudFront configuration:**
  - **Origins**: S3 (Representational State Transfer [REST] endpoint with **Origin Access Control [OAC]**), S3 website endpoint (custom origin; no OAC), ALB/NLB/EC2 (public, or private via **VPC origins**), API Gateway, Lambda function URLs, MediaPackage, custom HTTP. **Origin groups** for failover (on configurable 4xx/5xx for GET/HEAD/OPTIONS).
  - **Cache behaviors**: path pattern → origin, viewer protocol policy (HTTP and Hypertext Transfer Protocol Secure [HTTPS] / redirect to HTTPS / HTTPS only), allowed methods, **cache policy**, **origin request policy**, **response headers policy** (Cross-Origin Resource Sharing [CORS], security headers), compression, field-level encryption, function associations.
  - **Security**: **OAC** + bucket policy granting `cloudfront.amazonaws.com` with `AWS:SourceArn` of the distribution (and KMS key policy if Server-Side Encryption with KMS keys [SSE-KMS]); custom header from CloudFront + ALB rule to block direct-to-origin access (or CloudFront managed prefix list in ALB SG); **signed URLs/cookies** (key groups); **geo restriction**; WAF web ACL; HTTPS with AWS Certificate Manager (ACM) cert in **us-east-1**, alternate domain names (CNAMEs) must match the certificate; minimum TLS/security policy; **mutual TLS**/origin mutual TLS (mTLS) options.
  - **Edge functions**: CloudFront Functions (viewer request/response, sub-ms, JavaScript) and Lambda@Edge (viewer/origin events, heavier logic, deployed from us-east-1).
  - **Other**: price classes, **Origin Shield**, continuous deployment (staging distribution with header/weight-based traffic), real-time logs, standard logs (v2 to CloudWatch Logs/S3/Firehose), **invalidations**, error pages and caching of error responses (**error caching minimum TTL**, default 10 s).
- **AWS Global Accelerator configuration:** accelerator → **two static [anycast](00-cloudops-basics.md#anycast) IPs** (dual-stack optional) → **listeners** (TCP/UDP ports, **client affinity** source IP) → **endpoint groups** (one per Region, **traffic dial** 0–100%, [health check](00-cloudops-basics.md#health-check) settings) → **endpoints** (ALB, NLB, EC2, EIP; **weights**; client IP preservation for ALB/EC2). Health-based failover within seconds; **custom routing accelerators** for deterministic mapping to EC2 ports. Flow logs available. Monitoring: `NewFlowCount`, `ProcessedBytesIn/Out`.
- **Exam triggers:** "serve private S3 content only via CloudFront" → **OAC + bucket policy**. "Users bypass CloudFront and hit the ALB directly" → **custom header check / CloudFront prefix list in ALB SG**. "[Blue/green](00-cloudops-basics.md#deployment-strategies) test of CloudFront config changes" → **continuous deployment**. "Shift 10% of global traffic to a new Region for User Datagram Protocol (UDP) game servers" → **Global Accelerator traffic dial/weights**.

---

## Task 5.3: Troubleshoot network connectivity

### Troubleshooting VPC configurations (skill 5.3.1) `[also: ANS]`

**Systematic path check (source → destination and back):**
1. **Instance/OS**: service listening on the port, Operating System (OS) firewall (iptables/Windows Firewall), correct IP.
2. **Security group** of the destination allows inbound from the source (IP/SG/prefix list) on the port; source SG allows outbound.
3. **NACLs** on both subnets allow the traffic **and the return ephemeral ports** both ways.
4. **Route tables**: source subnet has a route to the destination (IGW/NAT/peering/TGW/VGW/endpoint); destination subnet has a **return route**; most specific route wins; blackhole routes (deleted target, e.g., terminated NAT instance or deleted peering).
5. **Gateways**: IGW attached; instance has **public IP/EIP** for internet; NAT gateway in a **public** subnet with a route to IGW and **available** state; egress-only IGW for IPv6.
6. **Transit Gateway**: attachment **associated** with the right TGW route table, routes **propagated** or static, VPC route tables point to the TGW, **appliance mode** for stateful inspection across AZs, both directions configured.
7. **Peering**: active, routes both sides, no overlapping CIDRs, no transitive path.
8. **DNS**: hostname resolves (VPC DNS attributes, private zone association, Resolver rules).
9. **Endpoint policies / service policies** (VPC endpoint policy, bucket policy with `aws:SourceVpce`).

**Tools:**
- **VPC Reachability Analyzer**: static configuration analysis between a source and destination (instances, ENIs, IGW, TGW attachments, endpoints…); shows the **hop-by-hop path** or the **exact blocking component** (SG, NACL, route, missing IGW) without sending packets.
- **Network Access Analyzer**: finds **unintended network access** paths against your requirements (e.g., "no internet path to databases").
- **VPC Flow Logs** (below), **Traffic Mirroring** (full packet copies to an analysis appliance), EC2 serial console/`AWSSupport-TroubleshootSSH`/`AWSSupport-ConnectivityTroubleshooter` [runbooks](00-cloudops-basics.md#runbook-and-playbook).

| Symptom | Likely cause |
|---|---|
| Can't Secure Shell (SSH) to public instance; Flow Logs show REJECT inbound | SG or NACL inbound |
| Flow Logs: inbound ACCEPT, outbound REJECT | **NACL** blocks return **ephemeral ports** (stateless) |
| Private instance can't reach internet | No NAT route, NAT in private subnet, NAT subnet lacks IGW route, NACL blocks |
| Public instance can't reach internet | No public IP/EIP, no IGW route, IGW detached |
| Peered VPC unreachable | Missing route on one side, SG not allowing peer CIDR/SG, overlapping CIDR, trying transitive path |
| TGW-attached VPCs can't talk | Missing association/propagation, VPC route to TGW missing, blackhole route |
| Interface endpoint unreachable | Endpoint SG doesn't allow 443 from clients; private DNS disabled; DNS attributes off |
| Instance can't launch / ENI creation fails | Subnet out of IPs |
| Connection timeout vs refused | **Timeout** = packet dropped (SG/NACL/route); **refused** = reached host, nothing listening (OS/app) |

### Networking logs (skill 5.3.2) `[also: ANS, DOP]`

- **VPC Flow Logs**:
  - Scope: VPC, subnet, ENI (also TGW and TGW attachments: **Transit Gateway Flow Logs**).
  - Destinations: CloudWatch Logs, **S3** (Parquet option, Hive-compatible partitions → **Athena**), Firehose. Aggregation interval 10 min (default) or 1 min.
  - Default fields: `version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status`. **Custom format** adds `vpc-id`, `subnet-id`, `instance-id`, `tcp-flags`, `pkt-srcaddr`/`pkt-dstaddr` (original IPs behind NAT), `flow-direction`, `traffic-path`, `pkt-src-aws-service`, `az-id`, `sublocation-type`.
  - `action`: ACCEPT/REJECT (security group or NACL decision); `log-status`: OK, NODATA, SKIPDATA.
  - Not captured: Amazon DNS traffic (use Resolver query logs), instance metadata, DHCP, Time Sync, Windows license activation, mirrored traffic.
  - Analyze with **CloudWatch Logs Insights** (`filter action="REJECT" | stats count(*) by srcaddr`), **Athena**, Contributor Insights.
- **ELB access logs** (ALB/NLB/Classic Load Balancer [CLB]; **off by default**; delivered to **S3** every 5 min; bucket policy must allow ELB log delivery): client IP:port, target IP:port, `request_processing_time`, `target_processing_time`, `response_processing_time`, `elb_status_code`, `target_status_code`, request line, user agent, Secure Sockets Layer (SSL) cipher/protocol, `target_group_arn`, trace ID, `error_reason`, `actions_executed`. Interpreting: `elb_status_code` [502](00-cloudops-basics.md#http-status-codes) + no target status → target closed connection/bad response; **504** → target didn't respond within **idle timeout**; `target_processing_time = -1` → target never responded; **460** → client closed connection before response. **ALB connection logs** record TLS handshake details (mTLS). Query with Athena.
- **AWS WAF web ACL logs**: per request: `action`, **`terminatingRuleId`**, `ruleGroupList` (which managed rule matched), `httpRequest` (client IP, country, URI, headers), labels. Destinations CloudWatch Logs (log group name `aws-waf-logs-…`), S3, Firehose. Use to debug false positives/blocked traffic.
- **CloudFront logs**: **standard logs** (to S3 or, with v2, CloudWatch Logs/Firehose; delivered within an hour; fields include `x-edge-result-type` (**Hit, RefreshHit, Miss, LimitExceeded, CapacityExceeded, Error, Redirect**), `x-edge-detailed-result-type`, `sc-status`, `time-taken`, `cs-uri-stem`, `x-edge-location`, `time-to-first-byte`), **real-time logs** (to Kinesis Data Streams within seconds, sampling rate, chosen fields). CloudWatch metrics: `Requests`, `BytesDownloaded`, `4xxErrorRate`, `5xxErrorRate`, `TotalErrorRate`; additional metrics (paid): **`CacheHitRate`**, `OriginLatency`, error rates by code.
- **Container logs**: Elastic Container Service (ECS) **`awslogs`** log driver (task definition `logConfiguration` → CloudWatch log group; execution role needs `logs:CreateLogStream`/`PutLogEvents`), **FireLens** (Fluent Bit sidecar routing to CloudWatch, S3, Firehose, OpenSearch, third parties); Elastic Kubernetes Service (EKS) via **Fluent Bit** DaemonSet (CloudWatch Observability add-on), control plane logs (API server, audit, authenticator, controller manager, scheduler) enabled per cluster to CloudWatch Logs; **Container Insights** performance logs; VPC CNI/kube-proxy logs for pod networking issues.
- **Other**: **Route 53 Resolver query logs**, **Network Firewall alert/flow logs**, **Global Accelerator flow logs**, **Client VPN connection logs**, **API Gateway access logs**, **CloudTrail** (network config changes like route or SG modifications).
- **Exam triggers:** "find which IP is being rejected" → **Flow Logs REJECT**. "Determine whether 5xx come from the ALB or targets" → **ALB access logs** (`elb_status_code` vs `target_status_code`) / metrics `HTTPCode_ELB_5XX` vs `HTTPCode_Target_5XX`. "Why did WAF block this request" → **WAF logs `terminatingRuleId`**. "Low cache hit ratio investigation" → CloudFront logs `x-edge-result-type`.

### CloudFront caching issues (skill 5.3.3) `[also: ANS]`

| Problem | Cause | Fix |
|---|---|---|
| **Low cache hit ratio** | Cache key includes unnecessary headers/cookies/query strings (e.g., forwarding all headers, `User-Agent`, session cookies), short TTLs, origin `Cache-Control: no-cache/private/max-age=0` | Use a **cache policy** with only needed values; move other forwarded values to an **origin request policy**; set longer TTLs / correct origin headers; Origin Shield; normalize query strings/headers with CloudFront Functions |
| **Users see stale content after deploy** | Objects cached until TTL expires | **Versioned file names** (preferred) or **invalidation** (`/*` or specific paths; first 1,000 paths/month free); shorter TTL for HTML |
| **Content never updates despite invalidation** | Browser cache (`Cache-Control` max-age), wrong path/case in invalidation, multiple behaviors | Set browser TTL headers correctly; invalidate exact paths |
| **Dynamic/personalized content served to wrong users** | Cookies/headers that personalize not in cache key | Include required cookie/header in cache key or disable caching for that path (CachingDisabled policy) |
| **403 from CloudFront for S3 origin** | OAC/Origin Access Identity (OAI) not in bucket policy, bucket objects encrypted with SSE-KMS without key policy for CloudFront, object missing (S3 returns 403 when no `ListBucket`), default root object not set, geo restriction or WAF block, signed Uniform Resource Locator (URL) required | Fix bucket/KMS policy, default root object, check WAF/geo |
| **502 Bad Gateway** | CloudFront can't establish TLS with origin (certificate doesn't match origin domain, expired, self-signed, unsupported protocol/cipher), origin returned invalid response, Lambda@Edge error | Valid public cert matching origin hostname, origin protocol policy/ports, check Lambda@Edge logs |
| **503** | Origin capacity/throttling, Lambda@Edge throttled/limit | Scale origin, check functions |
| **504 Gateway Timeout** | Origin slow or unreachable: SG/firewall blocks CloudFront IPs, **origin response timeout** too low (default 30 s), keep-alive mismatch | Allow CloudFront prefix list, raise origin timeout, fix origin performance |
| **Errors cached** | Error caching minimum TTL | Lower error caching TTL in custom error responses |
| **Wrong CORS behavior** | `Origin` and CORS request headers not forwarded/cached; missing response headers | Origin request policy forwarding CORS headers + **response headers policy** for CORS |
| **Compression not working** | Compression off in behavior or `Accept-Encoding` not in cache key | Enable compression (cache policy includes gzip/brotli normalization) |

- **Metrics to watch**: `CacheHitRate`, `OriginLatency`, 4xx/5xx rates; logs `x-edge-result-type`.

### Hybrid and private connectivity issues (skill 5.3.4) `[also: ANS]`

- **AWS Site-to-Site VPN:**
  - **Two tunnels** per connection (different AWS endpoints); configure **both** on the customer gateway device for redundancy.
  - **Tunnel DOWN**: IKE/Internet Protocol Security (IPsec) phase 1/2 parameter mismatch (encryption, integrity, Diffie-Hellman [DH] group, Internet Key Exchange [IKE] version), wrong **pre-shared key**, customer gateway public IP mismatch, UDP 500/4500 and Encapsulating Security Payload [ESP] (protocol 50) blocked, **NAT Traversal (NAT-T)** needed behind NAT, dead peer detection timeouts.
  - **Tunnel UP but no traffic**: **route propagation** not enabled on VPC route tables (VGW), missing static routes, [Border Gateway Protocol [BGP]](00-cloudops-basics.md#bgp) not advertising prefixes, SG/NACL blocks on-prem CIDR, overlapping CIDRs, asymmetric routing, on-prem firewall; for TGW: attachment association/propagation.
  - **Idle tunnel drops**: send keepalive traffic or configure **Dead Peer Detection (DPD) timeout action** / startup action "start" so AWS initiates.
  - Logs/metrics: **Site-to-Site VPN logs** (IKE/BGP logs to CloudWatch Logs), CloudWatch **`TunnelState`** (1 up, 0 down), `TunnelDataIn/Out`; tunnel status in console; **Accelerated VPN** for performance; Equal-Cost Multi-Path (ECMP) over TGW for bandwidth.
- **AWS Client VPN:** endpoint associated with target subnets, **authorization rules** (which networks users may reach), **route table** entries (add routes for VPC CIDR/on-prem/internet `0.0.0.0/0` via subnet), **client CIDR** must not overlap the VPC, authentication (mutual certificates via ACM, Active Directory [AD], Security Assertion Markup Language [SAML]), **split-tunnel** (only VPC traffic through VPN), security groups on the endpoint, DNS servers setting, **connection logging** to CloudWatch Logs. Common issue: users connect but can't reach resources → missing **authorization rule** or route, or SG on target.
- **Direct Connect** (awareness for troubleshooting): physical connection **down** (cross-connect, optics, Letter of Authorization [LOA]), **BGP session down** (Autonomous System Number [ASN], Message Digest 5 [MD5] auth key, peer IPs, Virtual Local Area Network [VLAN]), routes not received (prefix limits: 100 per BGP session on private virtual interface [VIF]), **VGW/DX gateway association** missing, route propagation disabled; CloudWatch `ConnectionState`, `VirtualInterfaceBpsEgress`; VPN as backup with BGP path preference.
- **Private connectivity**: interface endpoint DNS not resolving from on-prem → **Resolver inbound endpoint** or conditional forwarding to `*.vpce.amazonaws.com` names; gateway endpoint can't be used from on-prem; endpoint policy denies; PrivateLink connection pending acceptance; NLB target health for endpoint services; cross-Region PrivateLink needs supported configuration.
- **Exam triggers:** "VPN tunnel up but instances can't reach on-prem" → **route propagation/static routes** + SG/NACL. "VPN drops when idle" → DPD/keepalive/startup action. "Client VPN users can't access VPC" → **authorization rules + routes**. "Alarm when VPN tunnel goes down" → **`TunnelState` [alarm](00-cloudops-basics.md#alarm)**.

### CloudWatch network monitoring services (skill 5.3.5)

- **CloudWatch Internet Monitor**: monitors **internet performance and availability between your users (by city/ASN/ISP) and your AWS applications** (CloudFront, VPCs, Network Load Balancers [NLBs], WorkSpaces) using AWS's global network telemetry; **health events** when ISP or AWS issues impact your users, with impact estimates; suggestions (e.g., which Region/CloudFront would improve latency); logs to CloudWatch Logs/S3; EventBridge events. Trigger: "know when an ISP issue affects our users in a city".
- **CloudWatch Network Synthetic Monitor** (formerly Network Monitor): **hybrid network monitoring**: creates **probes from your VPC subnets to on-prem IP addresses** over Direct Connect/VPN (Internet Control Message Protocol [ICMP] or Transmission Control Protocol [TCP]) to measure **round-trip time and packet loss**; **Network Health Indicator** shows whether degradation is in the AWS network; alarms on metrics. Trigger: "monitor latency/packet loss of our DX/VPN connection to on-prem and know if AWS is the cause".
- **CloudWatch Network Flow Monitor**: near-real-time **TCP-based performance metrics (retransmissions, round-trip time, data transferred) between workloads in AWS** (EC2, EKS) and to AWS services, using lightweight agents; **network health indicator** to tell whether AWS network issues are responsible; top contributors. Trigger: "find which service-to-service flows have high retransmissions/latency inside AWS".
- **Other monitoring**: VPC Flow Logs metrics via [metric](00-cloudops-basics.md#metric) filters, **Reachability Analyzer** (config), **Global Accelerator/CloudFront/ELB/NAT/VPN/DX** CloudWatch metrics, **Route 53 health check** metrics, **CloudWatch [Synthetics](00-cloudops-basics.md#synthetic-vs-real-user-monitoring) canaries** (application endpoint checks, including from inside a VPC), **Transit Gateway** metrics (`BytesIn`, `PacketDropCountBlackhole`, `PacketDropCountNoRoute`), NAT gateway `ErrorPortAllocation`, Elastic Network Adapter (ENA) driver metrics (`bw_in_allowance_exceeded`, `pps_allowance_exceeded`, `conntrack_allowance_exceeded`) via the CloudWatch agent.
- **Exam triggers:** "users in one country report slowness; is it their ISP?" → **Internet Monitor**. "Measure packet loss across Direct Connect to the data center" → **Network Synthetic Monitor**. "Identify retransmission hotspots between microservices" → **Network Flow Monitor**. "TGW drops packets with no route" → `PacketDropCountNoRoute` metric.

---

## Gotchas and exam traps

- **NACLs are stateless**: allow ephemeral return ports; Flow Logs inbound ACCEPT + outbound REJECT = NACL.
- **Timeout = dropped** (SG/NACL/route); **connection refused = reached host**.
- NAT gateway must be in a **public subnet**; **one per AZ** for HA; `ErrorPortAllocation` → add IPs.
- **Gateway endpoints** only for S3/DynamoDB, only from within the VPC; **interface endpoints** need SG 443 + DNS attributes for private DNS.
- **Peering**: no transitive, no overlap, routes both sides, no edge-to-edge.
- **Reachability Analyzer** = config path analysis without traffic; **Network Access Analyzer** = unintended access.
- Route 53 **public query logs** go to CloudWatch Logs in **us-east-1**; **Resolver query logs** cover VPC queries.
- **Alias** at apex; registrar **NS mismatch** breaks resolution.
- ELB access logs are **off by default** and go to **S3**; ALB 504 = target timeout; 502 = bad response; 460 = client closed.
- **WAF logs** need `aws-waf-logs-` prefix for CloudWatch log groups; debug with `terminatingRuleId`; test in **Count**.
- CloudFront 403 from S3 → OAC/bucket/KMS policy; 502 → origin TLS cert problem; 504 → origin timeout/firewall; low hit ratio → cache key too broad.
- VPN tunnel up but no traffic → **route propagation**; idle drops → DPD/startup action.
- Client VPN needs **authorization rules and routes**.
- Internet Monitor (users ↔ AWS via ISPs) vs Network Synthetic Monitor (AWS ↔ on-prem) vs Network Flow Monitor (inside AWS).

## Quick-recall summary

- **Build**: VPC/subnets/routes/IGW/EIGW/NAT/SG/NACL/EIP/IPAM, endpoints (gateway vs interface), PrivateLink, peering, TGW.
- **Protect and audit**: WAF (logs, Count, metrics), Shield (Advanced features), Network Firewall (rules, routing, logs), DNS Firewall (managed lists, query logs).
- **Cost**: gateway endpoints, NAT placement, same-AZ, CloudFront egress, peering vs TGW, public IPv4 cleanup.
- **DNS**: Resolver (inbound/outbound endpoints, rules, Profiles), private zones, routing policies, Alias, query logging (public vs Resolver), DNSSEC.
- **Edge**: CloudFront (origins, OAC, behaviors, policies, functions, origin groups, invalidations, logs), Global Accelerator (listeners, endpoint groups, dials, weights).
- **Troubleshoot**: path checklist, Reachability Analyzer, Flow Logs fields, ELB/WAF/CloudFront/container logs, CloudFront error table, VPN/Client VPN/DX issues, Internet Monitor, Network Synthetic Monitor, Network Flow Monitor.
