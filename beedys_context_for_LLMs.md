---
type: context
purpose: quick-inject AI context for any platform
last_updated: 2026-05
---

# Wendy Tan — AI Context File

## Who I Am
- Cloud Infrastructure Engineer at GovTech Singapore (6 months, first hands-on infra role)
- Prior: 6 months AWS Specialist SA, 1 year Ansible Specialist SA at Red Hat
- Based in Singapore
- RHCSA + RHCE certified, AWS Solutions Architect Associate certified

## My Team & Role
- IC under a Tech Lead, largely autonomous, bi-weekly syncs
- Shared internal platform team — we build capabilities consumed by all group subsidiaries
- Formally appointed Automation SME: consult teams on automation opportunities, upskill others,
  provide examples and hands-on guidance when teams are stuck

## My Core Stack
- Cloud: AWS (daily), Azure (beginner, rarely used)
- IaC: Terraform (competent), Terraformer, Terracognita
- Automation: Ansible / AAP (expert), GitLab CI/CD (competent)
- Containers: Docker
- Languages: Bash/Shell (competent), Python (competent — AI-assisted), HCL, YAML
- Tools: AWS CLI, GitLab, VS Code

## Current Projects

### AMP — Automation Management Platform (complete, L3 support only)
Built on Red Hat AAP. Core features delivered: GitOps inventory management pipeline,
EE creation pipeline (governance-enforced), PAH monthly sync, GitLab repo structure
enforcement. All features live. PCSA security remediation ongoing by teammate.

### AIP — Asset Intelligence Platform (active, early build phase)
Built on Zscaler AEM (replaced Axonius). Asset exposure data piped to Databricks for
tenant consumption. Cybersecurity team (GITSIR/GIROC) uses AEM directly. Currently:
studying AEM docs, identifying internal data sources to ingest, working with vendor on
ingestion design. Databricks is the analytics/presentation layer for all other tenants.

### IaC Self-Service Extraction Tool (active, ongoing refinement)
Toolchain for subsidiaries to scan cloud resources with read-only credentials and extract
Terraform HCL. AWS uses Terraformer; Azure uses Terracognita with iterative exclusion loop.
Post-extraction cleanup scripts handle known HCL quirks. Drift check validates zero-change.

## How I Work With AI
- Explain from first principles on new topics; skip basics on Ansible, AWS, Bash, YAML
- No repetition — do not restate the question or summarise what you are about to say
- Point form over paragraphs always
- Start every response/doc/script with a high-level overview
- Use Docker (not Podman) for containers
- Use AWS CLI with full flags; Terraform HCL for IaC; Bash for scripting
- Always show full commands, never partial snippets
- For scripts: include full inline explanation per section + a short 1-line summary comment
  I will delete the full explanation after reviewing and keep only the summary
- Lay out options with trade-offs so I can decide — don't just give one answer
- No GUI solutions unless I ask
- No unsolicited "be careful in production" warnings

## Current Learning Priorities
1. Data engineering — Databricks, Spark, PySpark (work hours, for AIP)
2. Zscaler AEM — working with vendor and team
3. CCNA + Cisco DevNet — personal time, targeting Cisco Live November 2026
