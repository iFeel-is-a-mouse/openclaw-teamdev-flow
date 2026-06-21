# auditor/audit-report-template.md — Audit Template Set

> auditor uses this template for issue management and audit reports. When using, copy corresponding sections to `docs/audit-issues.md` and `docs/audit-report.md`.

---

# Part 1: Issue Registration · Tracking · Verification · Closure

> Copy to `docs/audit-issues.md`, register issues one by one during the audit.

## Audit Info

| Project | Audit Type (Pre-Audit/Final/Delta) | Date | Baseline |
|------|---------------------------|------|------|
| `<Project Name>` | | `YYYY-MM-DD` | constitution + spec + design |

## Issue Registry

| ID | Severity | Category | Issue Description | Baseline Reference | Status | Resolution |
|------|------|------|---------|---------|------|------|
| AUDIT-001 | | | | | ⬜ | |

## Issue Details

### AUDIT-001

**Severity:** P0/P1/P2/P3 | **Category:** Completeness/Security/Testability/Constitution Compliance/Ambiguity/Process Compliance

**Issue:** [Description] | **Baseline Reference:** [Cite spec/constitution item]

**Fix:** Person: [ ] | Plan: [ ] | File: [ ]

**Verification:** Person: [ ] | Result: ✅/❌ | Notes: [ ]

**Closure Verdict:** Person: main | Result: ✅ Pass/🔁 Continue/📝 Technical Debt | Reason: [ ]

## Severity Levels

| P0 Blocker | P1 Critical | P2 Moderate | P3 Suggestion |
|---------|---------|---------|---------|

## Closed-Loop Process

`Registration → Report to main → main decides → coder fixes → tester verifies → auditor reviews → main closes`

---

# Part 2: Audit Report

> Copy to `docs/audit-report.md`, fill in after audit is complete.

## Audit Info

| Project | Type | Date | Auditor | Baseline |
|------|------|------|--------|------|
| | | | auditor | |

## Pre-Audit (including clarify)

| # | Check Item | Result |
|---|--------|------|
| 1 | Ambiguous word check (subjective words like "appropriate", "sufficient") | ✅/⚠️ |
| 2 | Boundary clarity (input boundaries, exception inputs, extreme values) | |
| 3 | Undefined behavior | |
| 4 | Requirement contradictions | |
| 5 | Missing scenarios (error paths, concurrency, resource exhaustion) | |
| 6 | Acceptance criteria verifiability | |

**Completeness:** [Spec requirement points list with implementation/acceptance status]
**Constitution Compliance:** [Principles list with satisfaction status]

## Final Audit

**Requirement Traceability:** [Spec requirement point → implementation status → verification method]

**Process Compliance:**
| # | Check Item | Result |
|---|--------|------|
| 1 | Coder escalation path | ✅/❌ |
| 2 | Coder-tester interaction tracked | |
| 3 | Main confirmation receipt | |
| 4 | Build artifact delivery prerequisite | |

**Security Audit:** Injection/Leakage/Authentication/Configuration — item by item ✅/⚠️/❌
**Checklist:** [Total]/[Passed]/[Failed]/[N/A] — 🔴 Not released until 100% passed

## Conclusion

- Pass / Not Pass
- Key Findings: [ ]
- 🔴 Checklist Status: [ ]
