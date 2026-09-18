# Copilot assessment output schema

Use this compact shape for the technician-facing report. Markdown is the default rendering; preserve the same fields if a structured record is requested.

```yaml
assessment:
  tenant: "<tenant name or masked identifier>"
  assessed_at: "<ISO-8601>"
  assigned_technical_lead: "<name or role>"
  scope: ["CopilotReadiness", "MFA", "Office activation", "data/compliance"]
  hudu_sops:
    - title: "<title>"
      id: "<numeric id>"
      slug: "<slug>"
      status: "found|missing|mismatched|stale"
  verdict: "Ready for controlled pilot|Conditionally ready|Not ready|Insufficient evidence"
  verdict_basis: "<one concise, evidence-linked statement>"

evidence:
  - control: "<control>"
    status: "suite_pass|suite_fail|corroborated|corroboration_conflict|unknown|stale|skipped|excluded_unvalidated"
    source: "<Hudu/CIPP tool or official Microsoft path>"
    scope: "<tenant/count/period; no unnecessary identifiers>"
    observed: "<redacted aggregate or exact status>"
    retrieved_at: "<ISO-8601>"
    freshness: "current|stale|unknown"
    confidence: "high|medium|low"
    decision_impact: "gate|context|not counted"

ledger:
  suite_status: ["<CIPP CopilotReadiness results>"]
  independent_corroboration: ["<dedicated CIPP/Graph results>"]
  unknown_stale_conflicting: ["<why and owner for follow-up>"]
  excluded_unvalidated: ["<including effective Office update-channel status when only suite-reported>"]

g2_evidence_decision_record:
  evidence_refs: ["<stable source/result references>"]
  decision: "<verdict>"
  assumptions: ["<only explicit, non-invented assumptions>"]
  open_questions: ["<unknowns that affect the decision>"]
  approval_state: "assessment only; no remediation authorization"

g3_work_packages:
  - control_gap: "<gap or validation task>"
    work_package: "<quote-ready name>"
    owner: "L1|L2|L3|Technical Lead|Sales|Project Manager|Service Manager|CTO|Client Sponsor"
    dependency: "<license/access/client decision or none>"
    acceptance_evidence: "<observable evidence that closes the package>"
    estimate: "not estimated by this assessment"
    authorization_state: "not authorized"
```

## Decision rules

1. Apply the acceptance criteria in the retrieved Hudu SOPs. If a criterion or freshness window is not stated, record `threshold not supplied`; do not invent one.
2. A CIPP suite result is a **suite status**. Only a dedicated source that independently observes the control can be **corroboration**.
3. A policy definition, assignment, CIPP standard result, or generic device-compliance result cannot establish effective Office rollout or update channel.
4. The effective Office update-channel status is currently unavailable through the CIPP GET/Graph surface. It is `excluded_unvalidated`, never a failure or remediation item, even when a suite row mentions it.
5. Conflicting sources remain `corroboration_conflict` until resolved. Do not average or silently choose the favorable value.
6. `skipped` means the check was intentionally not run or not applicable; `unknown` means it was expected but not proven. They are not interchangeable.
7. A quote-ready work package describes the control, owner, dependency, and acceptance evidence. It does not execute work or assert that anyone approved it.

## Role ownership

| Role | Assessment responsibility |
|---|---|
| Assigned technical lead | Scope, evidence quality, verdict, and G2 record |
| L1-L3 technicians | Read-only collection, source timestamps, and evidence normalization |
| Sales | Quote packaging after technical mapping; no technical authorization implied |
| Project Manager | Plan, dependency, and acceptance coordination |
| Service Manager | Operational ownership and service-risk decision |
| CTO | Technical escalation and exception decision |
| Client Sponsor | Client-side decision/approval, recorded only when explicitly provided |
