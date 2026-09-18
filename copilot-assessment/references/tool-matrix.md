# Copilot assessment tool matrix

Use exact tool schemas from the CIPP catalog before calling a tool. The matrix is a routing aid, not proof that a source is live or current.

| Evidence area | Hudu/CIPP source | Use | Provenance rule |
|---|---|---|---|
| Governing procedure | `hudu-article_show_tool` for ids `670`, `662`, `667`; fallback `hudu-article_index_tool` | Load `0. AI Rollout`, `3. CIPP Copilot Readiness Assessment`, and `4. AI Readiness Remediation Quoting` | Record id, slug, title, retrieval time, and whether the article was missing or mismatched. MCP content only. |
| Suite catalog | `ListTestReports` | Resolve the CIPP report containing `CopilotReadiness` | Record the selected report id/name and the exact report catalog response. |
| Suite execution | `ListTests` | Run the report for the target `tenantFilter`; use `reportId` when resolved | Store raw test name, status, timestamp, and report id as **suite status**, never as corroboration. |
| MFA registration | `ListMFAUsers` | Validate registration status with aggregate counts; use `UseReportDB` only when cache age is known | Mark live vs cached and retain only the minimum user-level detail needed for a conflict. |
| Office activation | `ListGraphReports` with `getOffice365ActivationsUserDetail` and `getOffice365ActivationsUserCount` | Cross-check enabled/activated user counts and bounded detail | Record report name, period, export timestamp, and aggregation. Do not expose raw user identifiers. |
| Intune/configuration | `ListIntunePolicy`, `ListCompliancePolicies`, `ListDBCache`, or the endpoint's `UseReportDB` option | Inspect policy settings, assignments, and cache freshness | Treat as configuration evidence only. Never call assignment or generic compliance effective Office rollout. |
| Licensing | `ListLicenses` | Check eligible/assigned/available license counts | Preserve SKU scope and retrieval time; do not alter licenses. |
| Copilot administration | `ListCopilotSettings`, `ListCopilotUsage` | Check tenant settings and usage/adoption evidence | Distinguish configured, enabled, active, and unknown; usage is not authorization. |
| Identity/access | `ListConditionalAccessPolicies`, `ListCAPolicyCoverage` when needed | Review policy intent and resolved assignment coverage | A policy definition or what-if result is not proof of every effective session outcome. |
| SharePoint | `ListSharePointSharing`, `ListSharePointPermissions`, `ListSharePointExternalUsers`, `ListSharepointSettings` | Find sharing, permission, guest, and tenant-setting evidence | These tools are cache-backed where documented; label cache source/freshness and avoid raw identities. |
| Compliance | `ListSensitivityLabel`, `ListDlpCompliancePolicy`, `ListRetentionCompliancePolicy` | Review Purview controls relevant to Copilot data governance | A listed policy is not proof of user coverage or effective enforcement unless the source reports it. |
| AI governance | `ListShadowAI` | Review cached AI application/service-principal signals | Mark cached and bounded 7-day live lookup behavior; do not infer intent from an app name alone. |
| Security posture | `ListSecureScoreReport`, `ListServiceHealth` | Add current score and active service incidents as context | These are contextual evidence, not standalone Copilot readiness gates. |
| Standards | `ListStandardsCurrentState`, `ListStandardsCompare`, `ListTenantDrift` | Show current state, comparison, and drift | Standards compliance does not prove effective Office rollout; keep it separate from suite and corroboration. |
| Auditability | `ListAuditLogCoverage` | Establish whether audit evidence is sufficiently covered | Coverage is a data-quality signal, not a substitute for a missing event or policy result. |

## Evidence handling

For every material control, capture:

`control -> source/tool -> raw result/status -> retrieved_at -> freshness -> scope -> corroboration -> confidence -> decision impact`

Use `high` confidence only when the source is current, scoped to the target tenant, and independently corroborated. Use `medium` for one reliable but uncorroborated source. Use `low` for stale/cache-only, partial, or indirect evidence. Use `unknown` rather than a guessed value.
