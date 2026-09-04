---
type: project
name: GovSight (formerly AIP) — Asset Intelligence Platform
status: active (build phase — handover to Ryan in progress)
tags: [Zscaler, AEM, GEM, Databricks, data-engineering, AWS, cybersecurity, multi-tenant, handover]
started: 2025
last_updated: 2026-09
owner: Ryan (technical build, incoming); KJ/Kai Jian (mapping, ongoing); Wendy stays POC
***

# GovSight (formerly AIP) — Asset Intelligence Platform

## Summary
GovSight — formerly named AIP — is the asset-visibility component of GEM (Government Exposure Management), providing organisation-wide asset visibility across multiple tenants on Zscaler AEM (Asset Exposure Management). It serves cybersecurity and operations teams with a unified view of assets across on-prem, cloud, and SaaS environments. The platform was originally built on Axonius (now fully decommissioned) and is being rebuilt on Zscaler AEM. Asset data is piped into Databricks as the analytics and presentation layer for most tenants.

Wendy's scope has narrowed to two workstreams: **AnySource** (the ingestion pipeline for data sources with no native Zscaler connector) and the **AEM Data Dictionary field mapping**. She is handing the technical build to Ryan, with a target of Ryan being self-sufficient by **Fri 11 Sep 2026**. KJ (Kai Jian) continues as the other main engineer on mapping; Wendy stays on as point of contact if needed.

## Business Context
- **Primary goal**: Organisation-wide asset visibility to support faster threat response, compliance, and IT rationalisation
- **Key users**:
  - **Cybersecurity team**: Direct access to Zscaler AEM and its local dashboard
  - **All other tenants (departments/agencies)**: Receive data via Databricks only
- **Scale**: Multi-tenant — each department/team is a separate tenant
- **Milestones**: Tenant insights visible mid-2026; cybersecurity team access late 2025

## Architecture

### Previous Architecture (Axonius — DECOMMISSIONED)
- NLB → EC2 Axonius core nodes (one per tenant/environment)
- Core nodes exported asset ZIPs to S3 every 24h
- Central-core node aggregated all core node data for unified visibility
- Access via VPN, internal network, ZTNA over HTTPS/TCP 443
- Data sources: internal intranet, internet, vulnerability management, ITSM

### Current Architecture (Zscaler AEM + AnySource)
- **Zscaler AEM**: Primary asset exposure data source
  - Cybersecurity team uses AEM directly via its local dashboard
  - All other tenants consume data via Databricks
- **AnySource pipeline**: Wendy's build — ingestion for data sources that have no native Zscaler connector, feeding into the same Databricks presentation layer
- **AEM Data Dictionary**: Field-mapping work aligning source data models to a consistent schema; Zoey (CSG) is leading the ZScaler engagement/approach on validating the data model and polishing mapping accuracy, with Wendy syncing and following her lead
- **Databricks**: Analytics and presentation layer for tenant consumption; being evaluated further for cross-dataset relationship discovery (Databricks markets computational/analytics tooling for finding hidden relationships across disparate datasets)
- **Infrastructure**: Reusing existing AWS components where possible (existing firewall rules from AWS to on-prem, EC2/NLB/S3 reuse)

## Data Dictionary & Connector Onboarding
- Synced with ZScaler on documentation format/expectations for data mapping deliverables
- Waiting on ZScaler to kick off the first connectors, which will be used as a template to polish the onboarding process for further data sources
- Pushing for a dedicated technical deep-dive session on connector internals + data modelling details

## Use-Case Pitching
- Researched and mapped use-cases/importance for 15 existing + roadmap data sources (e.g. CrowdStrike, Panorama, Tenable, WIZ)
- Reviewed `CNP Team.xlsx` and mapped use-cases for networking products (Aruba, Palo Alto, Check Point, F5, Cisco, Tectia) into the ingestion story
- Synced with Elmo (BA) on agency survey findings
- In progress: deeper cross-source relationships; use-cases/insights not yet identified or requested by agencies; working with KJ on discrepancies (e.g. why two sources report different numbers of assets/findings)

## Data-Ingestion Architecture (AnySource)
- Working with KJ and Venu (infra/secrets/TAF/TRA) on the ideal pipeline design to pull data from sources without a native Zscaler connector

## AI/Automation Exploration
- Generally collecting information on team workflows to identify opportunities to automate tedious tasks (e.g. data mapping/modelling) with AI
- Upskilling via the Anthropic Claude Developer course to close the gap in building AI pipelines/tools

## Handover to Ryan
- Handover pack written to `docs/handover/`: master checklist, time plan, an evidence-first handover standard, and a source-onboarding runbook
- Target: Ryan self-sufficient on the technical build by **Fri 11 Sep 2026**
- Wendy remains available as POC after handover

## Team Structure
- **KJ / Kai Jian** — mapping, continuing as the other main engineer
- **Zoey (CSG)** — leads ZScaler engagement/data-model validation approach; rolling off Oct/Nov 2026
- **Ezekiel (CSG)** — security-finding sources
- **Kelvin** — delivery/programme manager
- **Venu** — infra, secrets, TAF/TRA
- **Elmo, Shawn** — business analysts
- **Gilford, Ziv, Shahar, Asaf** — Zscaler vendor side
- **Ryan** — incoming owner of the technical build

## Key Decisions
- **Zscaler AEM over Axonius**: Evaluated both products; Axonius fully decommissioned, Zscaler AEM selected as replacement
- **Databricks as presentation layer**: Chosen because target tenants are already familiar with it — minimises onboarding friction
- **Cybersecurity team gets direct AEM access**: They need the full AEM dashboard and local tooling; Databricks alone is insufficient for their use-case
- **Reuse AWS infra where possible**: Existing firewall access from AWS to on-prem is a valuable asset — avoids lengthy re-approval cycles for new network paths
- **Handover to Ryan**: Wendy's build responsibility transitions off to focus on other workstreams (StackOps, AMP ramp-up); she remains POC

## Current Status

| Area | Status |
|---|---|
| Axonius decommission | ✅ Complete |
| Rename to GovSight (part of GEM) | ✅ Complete |
| AnySource pipeline design | 🔄 In progress — with KJ and Venu |
| AEM Data Dictionary / field mapping | 🔄 In progress — Zoey leading validation approach |
| Use-case pitching (15 sources + networking products) | 🔄 In progress — ongoing cross-source relationship work |
| Handover to Ryan | 🔄 In progress — target self-sufficient Fri 11 Sep 2026 |
| Databricks cross-dataset analytics evaluation | 🔄 In progress |
| RBAC / multi-tenant model | ⏳ Not started — gaps identified, not formalised |

## Open Questions / Blockers
- Full list of internal platforms and their underlying products not yet complete
- RBAC model — tenant scoping and role/permission design not yet formalised — needed before any data surface is exposed
- Discrepancies between sources reporting different asset/finding counts — being investigated with KJ

## Skill Gaps Being Addressed
- Data engineering concepts (Spark, DataFrames, PySpark) — studying via structured courses, during work hours
- Databricks platform — actively learning
- Zscaler AEM — studying with team and vendor
- AI pipeline/tooling — Anthropic Claude Developer course, to explore automating tedious mapping/modelling tasks

## Notes
- The Axonius-era infrastructure and lessons learned (multi-tenant design, RBAC gaps, discovery cycle patterns) remain relevant reference points
- On-prem firewall access already established from AWS is a significant head start for data source ingestion
- Handover standard written for this project is intended as a reusable framework other engineers can use to qualify handed-over work
