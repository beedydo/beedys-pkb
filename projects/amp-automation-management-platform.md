---
type: project
name: AMP — Automation Management Platform
status: complete (maintenance/L3 support only)
tags: [AAP, Ansible, GitLab, CI/CD, IaC, Red Hat, AWS, on-prem, EE, PAH]
started: 2025
last_updated: 2026-05
owner: platform engineer (built); another team member (ongoing security remediation)
***

# AMP — Automation Management Platform

## Summary
AMP is an internal Automation Management Platform built on Red Hat Ansible Automation Platform (AAP) 2.6, self-hosted on AWS EC2. It serves as the centralised automation platform for all tenants (departments/subsidiaries), providing a governed, standardised environment for teams to run their own automation use-cases. The platform engineer designed and built all core platform features. The platform is now in steady-state — active development has concluded. Remaining work (security gap remediation from a security posture scan) is handled by another team member. Original builder provides L3 support for incidents involving features they built.

## Infrastructure
- **AAP**: Self-hosted on AWS EC2
- **Execution Nodes (EN)**: Multiple on-prem execution nodes connected to AAP for running jobs in the intranet/on-prem environment
- **Private Automation Hub (PAH)**: Fully set up and running on the platform
- **AAP API path**: `/api/controller/v2`
- **AAP URL**: Internal intranet only

## Features Built

### 1. GitOps Inventory Management Pipeline
- Tenants commit inventory YAML files to a central GitLab repo under `<track>/<environment>/<inventory_file_name>.yml`
- GitLab CI pipeline detects changes (new/updated/deleted files) via `git diff`
- Pipeline generates `latest_config.yml` dynamically and triggers AAP Job Template via API
- AAP job template uses `infra.aap_configuration` collection to create/update/delete inventories and sources in AAP
- Inventories are routed to the correct AAP organisation: `prod` → **Production**, `non-prod` → **Non-Production**
- All sensitive values stored as GitLab CI/CD variables (`AAP_TOKEN`, `AAP_TEMPLATE_ID`, `AAP_URL`) — nothing hardcoded
- Generated config files are pipeline artifacts only — never committed back to repo
- Onboarding guide written for tenants
- ADR (Architecture Decision Record) written documenting design decisions

### 2. Execution Environment (EE) Creation Pipeline
- Tenants commit an `ee-requirements.yml` file to trigger EE creation
- Pipeline enforces strict governance:
  - Content collections sourced **only** from approved registry (Red Hat Hybrid Cloud Console)
  - Base images pulled **only** from approved container registry
- Security and compliance scans run before any build proceeds
- On pass: pipeline triggers the EE creation job template, parses relevant variables, and builds the EE dynamically with the correct credentials attached
- All EEs are standardised across the platform
- Fully documented

### 3. PAH Collection Sync Policy
- Monthly scheduled AAP job calls the PAH API to sync with Red Hat's public Automation Hub
- Retention policy: keep latest version + up to 2 minor versions below (e.g. latest 2.5.0 → retain 2.4.x and 2.3.x → deprecate 2.2.x)
- Python script identifies versions for removal; notification service sends deprecation alerts
- Pre-sync snapshots committed to Git for change tracking
- Fully implemented and running on schedule

### 4. GitLab Repo Structure Enforcement
- CI pipeline checks enforce correct folder/file organisation for all tenant repos
- Required repo root: `collections/`, `environments/`, `execution_environments/`, `playbooks/`, `roles/`, `.ansible-lint.yml`, `.gitlab-ci.yml`, `ansible.cfg`, `README.md`
- Motivation: ensures all tenant repos are consistent and easy for the platform team to investigate when support tickets are raised

## Key Architecture Decisions
- **`infra.aap_configuration` collection over raw API calls**: Chosen for idempotency, declarative style, and alignment with Ansible-native patterns. Raw API would require more custom error handling.
- **GitOps over AAP-native inventory management**: Putting inventory files in Git gives version history, merge request review gates, and a single source of truth — tenants cannot bypass review.
- **Tenant responsibility model**: Job templates and use-cases are the tenant's responsibility. AMP provides the platform, governance, and tooling — not the automation content itself. This gives scale without the platform team becoming a bottleneck.
- **EE governance via pipeline**: Restricting base images and collection sources at pipeline level (not just policy) makes it structurally impossible for tenants to pull from unapproved sources.
- **Execution Nodes on-prem**: Allows AAP (hosted in AWS) to reach on-prem/intranet systems via dedicated ENs, bridging cloud and intranet without opening broad network access.

## Current Status
**Feature development: COMPLETE**
All core platform features are built, documented, and operational.

| Feature | Status |
|---|---|
| Inventory management pipeline | ✅ Live |
| EE creation pipeline | ✅ Live |
| PAH sync (monthly scheduled) | ✅ Live |
| GitLab repo structure enforcement | ✅ Live |
| Tenant onboarding documentation | ✅ Written |
| ADR documentation | ✅ Written |
| Security gap remediation | 🔄 In progress |

## Current Involvement
- **No active development**
- **L3 support only** — called in if an incident involves features originally built
- Fully transitioned to AIP as primary focus

## Tenant Model
- Tenants: departments and subsidiaries
- Tenants are free to build any automation use-cases they wish using AAP
- Platform team provides: the platform, governance pipelines, EE tooling, PAH, and support
- Tenants are responsible for: their own job templates, playbooks, and automation content
