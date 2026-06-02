---
type: project
name: AIP — Asset Intelligence Platform
status: active (early build phase)
tags: [Zscaler, AEM, Databricks, data-engineering, AWS, cybersecurity, multi-tenant, GITSIR]
started: 2025
last_updated: 2026-05
owner: Wendy Tan (platform + infra); team shared ownership on AEM study
***

# AIP — Asset Intelligence Platform

## Summary
AIP is an Asset Intelligence Platform that provides ministry-wide asset visibility for Singapore government agencies. It serves cybersecurity and operations teams (GITSIR, GIROC, agency CISOs/CIOs) with a unified view of assets across on-prem, cloud, and SaaS environments. The platform was originally built on Axonius (now fully decommissioned) and is being rebuilt on Zscaler AEM (Asset Exposure Management) as the new data source. Asset data will be piped into Databricks as the analytics and presentation layer for most tenants, as Databricks is already familiar to other departments and subsidiaries.

## Business Context
- **Primary goal**: Ministry-wide asset visibility to support faster threat response, IM8 compliance, and IT rationalisation
- **Key users**:
  - **Cybersecurity team (GITSIR, GIROC)**: Direct access to Zscaler AEM and its local dashboard
  - **All other tenants (agencies/departments)**: Receive data via Databricks only
- **Scale**: Multi-tenant — each agency/department is a separate tenant
- **Milestones**: Agency insights visible Jun 2026; GITSIR/GIROC access Jan 2026

## Architecture

### Previous Architecture (Axonius — DECOMMISSIONED)
- NLB → EC2 Axonius core nodes (one per agency/environment)
- Core nodes exported asset ZIPs to S3 every 24h
- Central-core node aggregated all core node data for unified visibility
- Access via SGVPN, NUCLEUS, ZTNA over HTTPS/TCP 443
- Data sources: GCCI Intranet, Internet, VMS, ServiceNow

### New Architecture (Zscaler AEM — IN PLANNING)
- **Zscaler AEM**: Primary asset exposure data source
  - Cybersecurity team uses AEM directly via its local dashboard
  - All other tenants consume data via Databricks
- **Data pipeline**: AEM → [ingestion method TBD with vendor] → Databricks
  - Exact ingestion mechanism (API pull / webhook / batch export) still being confirmed with Zscaler vendor
- **Databricks**: Analytics and presentation layer for tenant consumption
- **Infrastructure**: Reusing existing AWS components where possible
  - Existing firewall rules from AWS account to on-premises resources will be leveraged
  - EC2, NLB, S3 reuse being evaluated to avoid rebuilding from scratch

## Data Sources (In Discovery)
- Identifying all internal platforms to ingest as data sources
- Mapping what software/products each platform is based on (prerequisite for adapter/ingestion design)
- This discovery is Wendy's current primary focus on AIP

## Features Built (Carried Over)
- **MVP Search Method**: Temporary search feature built for GITSIR to demo data attribute searches
  - Acceptance criteria met, documented, no further meetings needed
  - Considered a stopgap — will be superseded by full Databricks-based data delivery
- **RBAC design**: Gaps identified (multi-tenant model, per-tenant asset scoping, role/permission mapping)
  - Not yet fully implemented — needs formalising before search/data surfaces are exposed

## Team Structure
- Team divided and conquered on Zscaler AEM documentation study — each member covered sections and shared learnings
- Wendy's current focus: internal platform discovery (data source identification and product mapping)

## Key Decisions
- **Zscaler AEM over Axonius**: Evaluated both products; Axonius fully decommissioned, Zscaler AEM selected as replacement [see comparison evaluation in history]
- **Databricks as presentation layer**: Chosen because target tenants (agencies/departments) are already familiar with it — minimises onboarding friction
- **Cybersecurity team gets direct AEM access**: They need the full AEM dashboard and local tooling; Databricks alone is insufficient for their use-case
- **Reuse AWS infra where possible**: Existing firewall access from AWS to on-prem is a valuable asset — avoids lengthy re-approval cycles for new network paths

## Current Status

| Area | Status |
|---|---|
| Axonius decommission | ✅ Complete |
| Zscaler AEM vendor onboarding | 🔄 In progress — working with vendor |
| AEM documentation study | 🔄 In progress — team divided and conquered |
| Internal data source discovery | 🔄 In progress — Wendy's current task |
| AEM → Databricks pipeline design | ⏳ Blocked — ingestion method TBD with vendor |
| Databricks pipeline build | ⏳ Not started — pending pipeline design |
| RBAC / multi-tenant model | ⏳ Not started — gaps identified, not formalised |
| Infrastructure reuse assessment | 🔄 In progress |

## Open Questions / Blockers
- **Ingestion method**: How exactly does data flow from Zscaler AEM to Databricks? (API pull, webhook, batch export — TBD with vendor)
- **Scope clarity**: Data engineering scope is not fully defined — Wendy's exact responsibilities vs. data engineering specialists TBD
- **Data source inventory**: Full list of internal platforms and their underlying products not yet complete
- **RBAC model**: Tenant scoping and role/permission design not yet formalised — needed before any data surface is exposed

## Wendy's Skill Gaps Being Addressed
- Data engineering concepts (Spark, DataFrames, PySpark) — studying via DataCamp
- Databricks platform — beginner, actively learning
- Zscaler AEM — beginner, studying with team and vendor
- Goal: be fluent enough to architect the ingestion pipeline and collaborate effectively with data engineering specialists

## Notes
- This is the most ambiguous project Wendy is currently on — scope, stack, and architecture are all still being defined
- The Axonius-era infrastructure and lessons learned (multi-tenant design, RBAC gaps, discovery cycle patterns) remain relevant reference points
- On-prem firewall access already established from AWS is a significant head start for data source ingestion