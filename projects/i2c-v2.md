---
type: project
name: I2C-v2 — AI-Assisted Terraform Codification Tool
status: active (refactor: prototype → production 3-tier web app)
tags: [Terraform, AI, Claude API, AWS, AAP, Node.js, refactor, IaC, drift-management]
started: 2026 (exact month unconfirmed)
last_updated: 2026-09
owner: Wendy (self-built)
***

# I2C-v2 — AI-Assisted Terraform Codification Tool

## Summary
I2C-v2 is a self-built tool that uses the Claude API together with `terraform plan` as a correctness oracle, running an **iterate-until-clean loop** to generate Terraform for unmanaged AWS resources. It has been validated at production scale — **300+ resources across 3 environments**. It was originally orchestrated through Red Hat AAP (called via `ansible.builtin.uri`, using a custom AAP credential type to hold the Anthropic API key) as part of a drift-management workflow. It is currently being refactored from a Node.js prototype into a standalone, production-grade 3-tier web application.

## Current Architecture (Prototype)
- **Entry point**: Node.js
- **AI pipeline**: pre-processor + agent loop (statefulness of the loop not yet confirmed — needs review of `agent.js`)
- **Terraform workspace lifecycle**: managed per-workspace
- **AWS discovery**: `awsDiscovery.js` — read-only credentials
- **Persistence**: SQLite, **ephemeral** — no persistent volume; workspace state is lost on restart (flagged as the actual blocker to resolve first, not a nice-to-have)
- **API endpoints**: `POST /api/workspace` (SSE stream for auto-refine), `GET /api/workspace` (poll), `POST /api/workspace/commit`, `DELETE /api/workspace`, `POST /api/discovery/runs`

## AAP Integration (Original Use Case)
- Called via `ansible.builtin.uri` with an Anthropic API key held in an AAP custom credential type
- AAP handled auth on I2C-v2's behalf in this mode — this disappears once I2C-v2 becomes a standalone product
- Intended combination: AAP's RBAC/audit/EDA/self-service layer + I2C-v2's Terraform-plan-as-oracle codification loop for drift management at multi-account scale

## Refactor: Standalone 3-Tier Web App
Decision: standalone product, independent of AAP and of GCC-owned infrastructure (Airbase, GitLab Dedicated, PAH) — open to any stack/hosting.

Key architecture decisions, in resolution order:
1. **Tenancy model** (upstream decision — resolve first): single-tenant-per-deployment vs. multi-tenant SaaS with per-tenant data isolation. This determines everything else below.
2. **Auth**: recommended auth-as-a-service (Clerk/Auth0/Supabase Auth) for user auth, rather than building password security from scratch. For AWS access: **AssumeRole-only**, never store static AWS keys — customer provides a role ARN + external ID.
3. **Database**: recommended **PostgreSQL with JSONB** columns (relational structure for users/workspaces/runs/audit log; JSONB for semi-structured Terraform plan diffs/resource maps) — over ephemeral SQLite or MongoDB.
4. **API design**: recommended **job queue** (e.g. BullMQ + Redis, or pg-boss) over raw SSE — workspace creation returns a job ID immediately, client polls/subscribes for updates. More resilient to dropped connections, supports retry/resume, decouples the AI agent loop from the HTTP request lifecycle. REST retained over GraphQL.
5. **Agent loop statelessness**: open gap — needs review of `agent.js` to confirm whether the pre-processor + agent loop is stateful in-memory (would need redesign to be resumable/stateless under the job-queue model).

## Known Gap
- A manual/human-in-the-loop step remains in the iterate-until-clean loop. This has been described inconsistently as "complete" vs. "incomplete" across different conversations/interview answers — needs reconciling into one consistent description. One framing considered: reframe it deliberately as a HITL governance checkpoint (industry data cited: only 34% of practitioners trust AI agents to make autonomous production changes; 42% cite lack of guardrails as the top blocker).

## Competitive Positioning (Build vs. Buy)
- **Wiz**: not a real comparator — Wiz is a CNAPP (security posture scanning: CSPM/CWPP/CIEM/IaC-scan-for-misconfig), not a codification tool. It would flag a misconfigured resource, not generate Terraform to bring it under management. Possible complementary use: scanning I2C-v2-generated Terraform for security misconfigs before MR merge (plan validates state-convergence, not security posture).
- **Firefly**: the real comparator — has a "Codification Agent" doing the same core job (unmanaged resource → generated Terraform/OpenTofu), plus continuous drift detection, dependency-aware DR, a 600+ policy governance engine, and multi-cloud/K8s/SaaS coverage I2C-v2 doesn't have. I2C-v2's differentiator: using `terraform plan` diff-to-zero as an automated correctness oracle in a closed iterate-until-clean loop — more rigorous than Firefly's "Plan Implications" (which explains plan output in plain English rather than using it as a pass/fail gate).
- **Build case (for GCC-specific deployment)**: rests on constraints commercial SaaS likely can't meet natively — air-gapped/no direct internet egress, on-prem GitLab Dedicated as commit target (vs. GitLab SaaS), SEED API Gateway / TechPass (Singapore government-specific identity and API gateway infrastructure). Also a cost asymmetry: I2C-v2's net-new cost is ~$0–60/month (Anthropic API tokens only); commercial platforms (Firefly, env0, Spacelift, Scalr) are typically five-figure-plus annual commitments at 100+ account scale.
- Gap not yet closed: no researched data on whether env0, Spacelift, Scalr, Digger, or Terramate support air-gapped/GCC-specific constraints.

## Portfolio / Interview Use
- Used as a technical talking point in interview processes (AlphaSense, Ollion); HashiCorp/IBM process dropped out (stopped hiring)
- Framed as replicating HashiCorp's Terraform MCP server pattern (GA June 2026) ahead of it being productized

## Current Status

| Area | Status |
|---|---|
| Prototype (AAP-orchestrated) | ✅ Built, validated at 300+ resources / 3 environments |
| Tenancy model decision | ⏳ Not yet decided — blocking downstream decisions |
| Auth approach | 🔄 Recommendation made (auth-as-a-service + AssumeRole-only), not yet implemented |
| Database migration (Postgres+JSONB) | 🔄 Recommendation made, not yet implemented |
| API redesign (job queue) | 🔄 Recommendation made, not yet implemented |
| Agent loop statelessness review | ⏳ Blocked — `agent.js` not yet reviewed |
| Manual/HITL step reconciliation | ⏳ Open — inconsistent framing across past answers |

## Notes
- Only the `SUMMARY.md` and AAP-integration YAML/docs have been used for architecture discussions so far — the actual `i2cv2/` source folder (`server.js`, `agent.js`, `workspace.js`, `awsDiscovery.js`) has not yet been reviewed in full
