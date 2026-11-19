# AWS Certified CloudOps Engineer – Associate (SOA-C03) Study Notes

Self-contained study notes for SOA-C03 (formerly SysOps Administrator – Associate), built from the official exam guide (v1.2, October 2026) and its in-scope service list. Every term is defined where it's used.

## How to study with this repo

1. **Skim [00-cloudops-basics.md](00-cloudops-basics.md)** for the operations vocabulary: metrics vs logs vs traces, alarms, runbooks, IaC, deployment strategies, burst credits, HTTP status codes, stateful vs stateless filtering, and more. Domain files link back to it.
2. **Work through the five domain files.** Each starts with the official tasks/skills, then covers every service and feature with: what it is and key terms, how to configure it, how to troubleshoot it, and **exam triggers** (scenario words → answer). Each ends with gotchas and a quick-recall summary.
3. **Review [06-cheat-sheet.md](06-cheat-sheet.md)** before the exam: decision tables per domain, top traps, numbers.
4. **Look up abbreviations in [00-abbreviations-glossary.md](00-abbreviations-glossary.md).** Each is also spelled out on first use in every file; inside brackets the abbreviation uses square brackets, e.g. *(Virtual Private Cloud [VPC])*.

## Files

| File | Domain | Weight |
|---|---|---|
| [00-cloudops-basics.md](00-cloudops-basics.md) | The 101 layer for operations | — |
| [01-monitoring-logging-remediation-performance.md](01-monitoring-logging-remediation-performance.md) | Domain 1: Monitoring, Logging, Analysis, Remediation, and Performance Optimization | 22% |
| [02-reliability-business-continuity.md](02-reliability-business-continuity.md) | Domain 2: Reliability and Business Continuity | 22% |
| [03-deployment-provisioning-automation.md](03-deployment-provisioning-automation.md) | Domain 3: Deployment, Provisioning, and Automation | 22% |
| [04-security-compliance.md](04-security-compliance.md) | Domain 4: Security and Compliance | 16% |
| [05-networking-content-delivery.md](05-networking-content-delivery.md) | Domain 5: Networking and Content Delivery | 18% |
| [06-cheat-sheet.md](06-cheat-sheet.md) | Decision tables, traps, numbers | — |
| [00-abbreviations-glossary.md](00-abbreviations-glossary.md) | A–Z abbreviations | — |

## Overlap tags

`[also: DOP]` (DevOps Engineer Professional), `[also: SAA]` (Solutions Architect Associate), `[also: ANS]` (Advanced Networking) mark material shared with those exams. Everything is still covered in full here.

## Exam at a glance

- 65 questions (50 scored, 15 unscored), 130 minutes, passing score 720 / 1000; multiple choice and multiple response. No exam labs are listed in the current guide.
- New in SOA-C03 vs C02: CloudWatch agent for ECS/EKS, CDK, compliance enforcement (Regions/services), CloudWatch network monitoring services, AI-related services (Bedrock monitoring, AWS DevOps Agent, Kiro, AWS Security Agent), Amazon S3 Files. The old cost/performance domain was folded into Domain 1.
- Official guide: https://docs.aws.amazon.com/aws-certification/latest/sysops-administrator-associate-03/sysops-administrator-associate-03.html

> Numbers and quotas change over time. Where AWS raised a limit recently, both the newer value and the value older questions use are given. Newer AI services (DevOps Agent, Security Agent, Kiro, S3 Files) are described only as far as AWS's official pages describe them.
