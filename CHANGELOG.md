# MA Team Development Framework — Changelog

## [4.4.0] — 2026-06-21

### Role Restructuring
- codereviewer renamed to reviewer
- main's five identities streamlined to three: Advisor · Coach · Project Manager
- Editor-in-chief duties transferred to publicist; README writing fully delegated to publicist
- PM renamed to Project Manager; HR role removed, duties absorbed by Project Manager

### Documentation Cleanup
- Removed process artifacts (RETROSPECTIVE.md, recurring-issues.md)
- CHANGELOG trimmed, retaining only the latest version
- Removed specific model names; replaced with qualitative descriptions of preferred/complementary models

## [4.3.0] — 2026-06-08

### Full Consistency Audit + Routing Rules + JSON Structuring

- **Consistency issue fixes** (based on three-agent joint audit):
  - reviewer AGENTS.md: added Stage 4b design review process (previously missing, P0)
  - auditor AGENTS.md: added retrospective audit duties + change quality gate (P1)
  - tester AGENTS.md: added 2 redline rules for delta regression / non-blocking tracking (P1)
  - SKILL.md metadata version sync fix (recurring P1 issue, registered in recurring-issues)
  - Created docs/recurring-issues.md to register version number desync (P1)
- **Routing rules** (agents lacked MA framework awareness):
  - SKILL.md YAML: added routing table (agents/routing/routing-redirects)
  - All 5 agent AGENTS.md files: added ROUTING markers + forwarding rules
  - Fixed: users directly messaging coder/tester about "team development" were not forwarded to main
- **Format optimization**:
  - §2 Lifecycle table → JSON + Mermaid sequence diagram (LLM-precisely parseable)
  - §4 Self-check checklist table → JSON
  - §10.4 Quality gate table → JSON
  - §11.1 Principles table → JSON
  - All ASCII decision trees / flowcharts → structured tables or JSON
- **README.md kept in pure table format** (documentation for humans)
- **VERSION unified to 4.3.0**

### Affected Files

- `projects/ma/VERSION` — 4.2.0 → 4.3.0
- `projects/ma/docs/change-management.md` — JSON embedding + Mermaid
- `projects/ma/skills/SKILL.md` — Routing table YAML
- `projects/ma/coder/AGENTS.md` — Routing rules
- `projects/ma/tester/AGENTS.md` — Routing rules + redlines
- `projects/ma/reviewer/AGENTS.md` — Routing rules + Stage 4b
- `projects/ma/auditor/AGENTS.md` — Routing rules + retrospective audit
- `projects/ma/publicist/AGENTS.md` — Routing rules
- `projects/ma/CHANGELOG.md` — This entry

(Earlier version records have been archived)
