---
name: copilot-assessment
description: "Technician-facing Microsoft Copilot readiness assessment for a named managed tenant. Invoke for `/copilot-assessment` followed by a tenant name, Copilot readiness or rollout requests, or CIPP/Hudu evidence reviews. Requires Hudu MCP and CIPP MCP; retrieves governing Hudu SOPs, runs the CIPP CopilotReadiness suite, cross-validates with read-only CIPP/Microsoft evidence, and returns a redacted verdict plus quote-ready work packages without changing tenant state."
compatibility: "Requires Hudu MCP and CIPP MCP with read-only access to the target tenant; Microsoft Learn MCP is preferred for official fallback references."
---

# Copilot assessment

Use this skill as a minimal router, not as a fixed remediation itinerary. Keep the assessment read-only, preserve uncertainty, and disclose provenance so a technician can defend every decision.

## Preconditions and safety

- Require a target tenant name or tenant identifier. Resolve it to the CIPP tenant filter before querying.
- Require both **Hudu MCP** and **CIPP MCP**. If either is unavailable, stop with `Insufficient evidence`; do not substitute public pages, guessed data, or an unapproved Graph session.
- Retrieve Hudu content through MCP only. Do not fetch a Hudu public URL or rely on inaccessible/public article content.
- Do not change licenses, policies, Purview, SharePoint, users, standards, or other tenant state. Do not execute remediation, create a quote, or imply authorization. Produce evidence and a quote-ready mapping only.
- Redact or aggregate user/device identifiers by default. Use counts, status buckets, and masked examples only when needed to explain a conflict.
- Read `references/tool-matrix.md` when selecting tools and `references/output-schema.md` when assembling the report. Read `references/microsoft-evidence.md` only when a first-party evidence fallback is needed.

## Route

1. **Load the governing SOPs from Hudu.** Prefer `hudu-article_show_tool` for:
   - `0. AI Rollout`: article id `670`, slug `48d62f68a80d`
   - `3. CIPP Copilot Readiness Assessment`: article id `662`, slug `2db254937855`
   - `4. AI Readiness Remediation Quoting`: article id `667`, slug `b983f98b916e`

   Verify the returned title and slug. If an id is unavailable or mismatched, use `hudu-article_index_tool` with an exact title/slug search, then show the matching article. Record which SOPs were found, missing, stale, or conflicting. Never treat a missing SOP as permission to invent a threshold.

2. **Run the CIPP readiness suite.**
   - Call `ListTestReports` and select the report whose name/key identifies `CopilotReadiness`.
   - Call `ListTests` with the resolved `tenantFilter` and that `reportId`. If the report cannot be resolved, call `ListTests` for the tenant without `reportId`, preserve the raw limitation, and mark the suite unassessed.
   - Record each test as suite evidence only: pass, fail, skipped, unknown, stale, or conflicting. A suite result is not independent corroboration.

3. **Cross-validate core controls with dedicated read-only sources.**
   - MFA registration: `ListMFAUsers` (use the reporting cache only when its freshness is recorded; prefer aggregate status counts).
   - Office activation: `ListGraphReports` for `getOffice365ActivationsUserDetail` and `getOffice365ActivationsUserCount`; use counts and bounded aggregates by default.
   - Configuration evidence: inspect `ListIntunePolicy`, `ListCompliancePolicies`, and the applicable CIPP cache endpoint (`ListDBCache` or the endpoint's `UseReportDB` option) before drawing conclusions.
   - Do **not** infer effective Intune rollout from policy assignment, CIPP standard compliance, or generic device compliance. These are configuration or compliance signals, not proof that the required Office build/channel is effective on devices.
   - The effective Office update-channel status is not retrievable through the current CIPP GET/Graph surface. Never count that datapoint as a readiness failure or remediation item. If the suite reports it, place it only in an explicit `excluded/unvalidated` note.

4. **Collect supplemental evidence when available and relevant.** Use the exact schemas returned by CIPP search/description tools for: `ListLicenses`, `ListCopilotSettings`, `ListCopilotUsage`, `ListConditionalAccessPolicies`, `ListSharePointSharing`, `ListSharePointPermissions`, `ListSharePointExternalUsers`, `ListSharepointSettings`, `ListSensitivityLabel`, `ListDlpCompliancePolicy`, `ListRetentionCompliancePolicy`, `ListShadowAI`, `ListSecureScoreReport`, `ListStandardsCurrentState`, `ListStandardsCompare`, `ListTenantDrift`, `ListAuditLogCoverage`, and `ListServiceHealth`. A cache-backed result must be labeled cached and dated; a missing, stale, or conflicting source remains unknown.

5. **Build the decision record and quote mapping.** Apply the SOP's acceptance criteria when present; otherwise state that no threshold was supplied. Keep three separate evidence tracks:
   - **Suite status:** what CIPP CopilotReadiness reported.
   - **Independent corroboration:** what a dedicated CIPP/Graph/configuration source showed.
   - **Unknown/stale/conflicting/excluded:** what could not be validated and why.

   Map material gaps to quote-ready work packages with an owner, dependency, acceptance evidence, and an authorization state of `not authorized`. Assign roles across the assigned technical lead, L1-L3 technicians, Sales, Project Manager, Service Manager, CTO, and Client Sponsor.

## Default report

Return one concise technician report. Follow the schema in `references/output-schema.md`:

1. Assessment metadata and scope.
2. Verdict: `Ready for controlled pilot`, `Conditionally ready`, `Not ready`, or `Insufficient evidence`.
3. Evidence table with status, source, freshness, scope, confidence, and decision impact.
4. Confidence/provenance ledger separating suite, corroboration, and unknown/stale/conflicting evidence.
5. Excluded/unvalidated and skipped items, including the Office update-channel exclusion when applicable.
6. **G2 evidence/decision record** and **G3 quote-ready control-to-work-package mapping** with acceptance evidence.
7. Ownership and next decision owner; never claim client approval or remediation authorization.

Use the lowest defensible verdict:

- **Ready for controlled pilot:** all material gates have current, non-conflicting evidence at the scope required by the SOP, with independent corroboration for critical controls.
- **Conditionally ready:** no corroborated hard blocker, but bounded gaps, stale evidence, or scoped work packages remain before or during a controlled pilot.
- **Not ready:** a current, corroborated blocker or unresolved material conflict fails an SOP acceptance criterion.
- **Insufficient evidence:** mandatory MCP/SOP/suite evidence is unavailable, or unknowns prevent a defensible readiness decision.

Do not manufacture numeric pass thresholds, freshness windows, or remediation estimates. If the SOP does not define them, say so.

## Microsoft Learn CLI fallback

Use Microsoft Learn MCP first. When it is unavailable, use the equivalent CLI command and cite the returned official Learn URL:

| Need | Preferred MCP | `mslearn` fallback |
|---|---|---|
| Search concepts/guidance | `microsoft-learn-microsoft_docs_search` | `mslearn search "query"` |
| Search official code | `microsoft-learn-microsoft_code_sample_search` | `mslearn code-search "query" --language powershell` |
| Fetch one official page | `microsoft-learn-microsoft_docs_fetch` | `mslearn fetch "https://learn.microsoft.com/..."` |

The fallback is research-only. Do not use it to replace Hudu or CIPP tenant evidence.
