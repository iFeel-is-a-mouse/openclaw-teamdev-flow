# constitution.md — Project Constitution

> Template file. When each project starts, main generates `docs/constitution.md` based on this template.
>
> The constitution defines the project's **governance principles** — the supreme basis for all subsequent decisions.
> Unlike spec.md (what to do) and style-guide.md (how to write), the constitution answers: **what is non-negotiable**.

---

## 1. Project Identity

- **Project Name:** [ProjectName]
- **One-Line Positioning:** [What problem this project solves, and for whom]
- **Creation Date:** YYYY-MM-DD

## 2. Core Principles

> 3-5 non-negotiable principles. Each principle must be verifiable — it must be possible to determine whether it has been followed.

### Principle 1: [Name]
**Statement:** [One-sentence description]
**Verification Method:** [How to verify this principle has been followed]
**Violation Example:** [What constitutes a violation]

### Principle 2: [Name]
**Statement:**
**Verification Method:**
**Violation Example:**

### Principle 3: [Name]
**Statement:**
**Verification Method:**
**Violation Example:**

## 3. Quality Standards

### Code Quality
- [ ] All public APIs have documentation comments
- [ ] Cyclomatic complexity does not exceed [X]
- [ ] Test coverage no less than [X]%
- [ ] No known security vulnerabilities (OWASP Top 10)
- [ ] [Project-specific standards]

### Documentation Quality
- [ ] README "Quick Start" instructions are executable
- [ ] All external interfaces have usage examples
- [ ] Design decisions have ADR records

### Process Quality
- [ ] Every feature point is audited by auditor
- [ ] 100% test pass rate required before final audit
- [ ] Requirement changes follow the complete delta process

## 4. Technical Constraints

> Non-optional constraints — fundamental project settings.

| Constraint | Decision | Rationale |
|--------|------|------|
| Programming Language | [Java/Python/JS/...] | [Why] |
| Build System | [Maven/Gradle/npm/...] | |
| Target Platform | [JVM/Node/Browser/...] | |
| Database | [If applicable] | |
| Deployment Method | [If applicable] | |

## 5. Non-Goals

> Explicitly state what this project does **not** do, to prevent scope creep.

1. [What this project does not do]
2. [Scenarios not optimized for]
3. [Platforms/environments not supported]

## 6. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|--------|------|---------|
| [Risk description] | High/Medium/Low | High/Medium/Low | [Mitigation strategy] |

---

## Version History

| Date | Version | Change | Author |
|------|------|------|------|
| YYYY-MM-DD | 1.0 | Initial constitution | main |

---

*"A constitution is not a wish list — it is a contract. Violating the constitution means breach of contract."*
