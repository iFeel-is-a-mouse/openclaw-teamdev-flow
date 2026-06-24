# Loop Engineering Patterns (Verified in Real-World Iteration)

> Version: v1.0 | Effective Date: 2026-06-24
> Source: Verified through 45 rounds of LLM-driven coverage testing project iteration (0% → 100% branch coverage)
> Reference: Addy Osmani Loop Engineering Framework (Goal / Generate / Execute / Verify / Decide)

---

Loop Engineering is an automation loop framework that "introduces the right human judgment at the right time." This document records reusable building blocks, patterns, and cost models verified in real-world iteration.

---

## I. Five Building Blocks

| Building Block | General Practice | Verified Outcome |
|----------------|------------------|------------------|
| **Goal** | Quantify as auto-detectable metrics; set stop-loss conditions (round limit, stall threshold, budget cap) | 100% branch coverage |
| **Generate** | LLM generates candidate solutions; inject precise context (API signatures, complete condition blocks, return pattern detection) | Pass rate 0% → 100% |
| **Execute** | Deterministic tool automation (coverage tool + test framework + isolated sandbox). Verify before writing to disk. | Stable and reliable |
| **Verify** | Use metric deltas instead of True/False (coverage delta rather than returncode) | Eliminates false positives |
| **Decide** | Multi-dimensional thresholds (stall beyond N rounds, consecutive zero output, priority ranking) | 8 critical strategy switches |

### Building Block Details

#### Goal — Measurable Targets with Stop-Loss Conditions

- Goals must be machine-verifiable (e.g., "100% branch coverage," not "good test quality")
- Must set hard limits: round cap, budget cap, consecutive stall threshold
- Layered goals outperform single goals: first break through the ceiling, then clear module by module

#### Generate — Context Determines Generation Quality

- LLM generation quality is determined by context quality, not model capability
- Must provide: API signature inventory, import paths, complete condition blocks, return pattern detection
- No need for LLM to guess return values — invoking successfully = coverage achieved. If correctness verification is needed post-coverage, add precise assertions then
- Few-Shot injection may be redundant when coverage tools already precisely report what's missing

#### Execute — Deterministic Tools, Verify Before Writing to Disk

- The execution chain must be reliable, repeatable, and isolated
- **All file write operations must go through isolated verification first; only write to disk after passing** — this order is non-negotiable
- Use industry-standard tools (Coverage.py / pytest); do not build custom execution engines

#### Verify — Use Metric Deltas Instead of True/False

- Do not trust returncode — trust coverage change (coverage after - coverage before > 0)
- Stall detection: N consecutive rounds with no coverage change → trigger root cause analysis instead of continuing the loop
- Regression protection: accumulated tests guarantee coverage only increases, never decreases

#### Decide — Multi-Dimensional Signal-Driven

- Priority ranking: sort files by coverage from low to high; defer 0-branch files
- Stall termination: N consecutive rounds with no improvement → terminate this loop
- Strategy switch: same approach produces no results for N rounds → force strategy switch
- Filtering over fixing: uncovariable modules filtered at Generate phase; never retried at Execute phase

---

## II. Reusable Patterns

### Pattern 1: Filter Over Fix

**Scenario**: A module cannot be test-covered due to environment dependencies or architecture constraints.

**Approach**: Remove the module from context at the Generate phase — don't let the LLM see it, don't let the test framework attempt to execute it. Eliminate all associated token consumption and time waste.

**Why It Works**: Repeated attempts on uncovariable modules are the biggest iteration-round black hole. A 100-line uncovariable module can consume 10+ rounds with zero output. Complete filtering reduces cost to zero.

**Applicable Conditions**: Module is confirmed uncovariable in the current environment, and filtering does not affect core functionality verification.

---

### Pattern 2: Real Files Over Mocks

**Scenario**: The function under test involves filesystem operations (file read/write, directory creation, path checks).

**Approach**: Use `tempfile.mkdtemp()` to create real temporary directories and files, rather than `unittest.mock.patch()` to simulate the filesystem.

**Why It Works**:
- Real environments are closer to production; test conclusions are more trustworthy
- LLM success rate for generating real-file operation code is far higher than generating multi-layer mock code
- Standard library operations; LLM needs no additional understanding of mock framework semantics

**Exception**: Databases, network APIs, message queues, and other external services — must mock, but mock layers ≤ 1.

---

### Pattern 3: Human Finds Patterns, LLM Implements

**Scenario**: The automated loop gradually loses effectiveness in deep coverage zones (60%+) — remaining uncovered branches are extremely complex or extremely granular.

**Approach**:
```
Auditor analyzes testability of uncovered code
    ↓
Reviewer identifies the most testable functions + provides precise test plans (fixture construction, trigger method, assertion pattern)
    ↓
LLM generates test code following the plan
    ↓
Clear file by file
```

**Why It Works**:
- LLMs lack the domain judgment to "select targets and choose strategies"
- Human judgment compensates for LLM strategy blind spots
- LLM execution capability compensates for human efficiency bottlenecks
- The combination outperforms either approach alone

**Applicable Conditions**: Remaining uncovered branches have high complexity; LLM auto-loop stalls for 3+ consecutive rounds.

---

### Pattern 4: Accumulation-Based

**Scenario**: Multi-round automated test generation.

**Approach**:
```
Passing tests permanently retained → clear coverage and rerun all each round → LLM only fills new gaps → coverage only rises, never falls
```

**Why It Works**:
- Coverage source shifts from "pure LLM this round's output" to "accumulated test library"
- LLM burden shifts from "start from scratch each round" to "only fill new gaps"
- Convergence shifts from "uncertain (may regress)" to "natural convergence (only rises)"
- The codebase itself serves as the state carrier — more robust than JSON state files

**Comparison with Clear-Slate Approach**: In the clear-slate approach, all tests are regenerated each round, and coverage can regress due to LLM output randomness.

---

### Pattern 5: Pre-Audit

**Scenario**: After design is complete, before coding begins.

**Approach**: Auditor reviews at the design phase:
1. Requirement completeness and lack of ambiguity
2. Whether the design approach aligns with industry consensus (if not, whether the justification for divergence is sufficient)
3. Constitution consistency check
4. Security risk identification
5. Test feasibility assessment

**Why It Works**: Issues caught at the design phase cost 1/5 of coding-phase fixes. Design review intercepts methodology-level errors; once coding begins, fix costs grow exponentially.

**Applicable Conditions**: M-level and above projects must execute. S-level projects may use main self-check as substitute.

---

## III. Cost Experience

The following are general cost references, based on LLM-driven coverage testing real-world data:

| Project Type | Estimated Cost (Reference) | Estimated Time | Recommended Budget |
|-------------|---------------------------|----------------|-------------------|
| Small (< 10 files) | Low | 1-2 hours | 5× actual consumption |
| Medium (10-50 files) | Medium | 3-5 hours | 5× actual consumption |
| Large (50+ files) | High | 5-10+ hours | 10× actual consumption |

### Cost Control Principles

1. **Budget at 10× actual consumption guarantees no interruption.** A budget that's too tight will stall near completion due to insufficient balance — the most wasteful kind of loss.
2. **Check balance after every round.** Pause the loop when balance drops below 20%; evaluate whether to continue.
3. **Pricing model should use actual model pricing, not outdated references.** Pricing can differ by 10× between models.
4. **Token efficiency evaluation.** Every prompt enhancement must evaluate token increment vs. expected coverage increment. Cut content with no gain.

---

## IV. Anti-Patterns (Must Avoid)

| # | Anti-Pattern | Consequence | Alternative |
|---|-------------|-------------|-------------|
| 1 | Designing interfaces with >2 abstraction layers for LLMs | LLM cannot use correctly; 0% pass rate across multiple rounds | Prefer real environments; mock layers ≤ 1 |
| 2 | Writing files before verification | Orphan file pollution; coverage reset to zero | Isolate-verify syntax and compatibility first; write to disk only after pass |
| 3 | Persisting with the same approach beyond 3 rounds | Wastes tokens and rounds with zero output | 3 consecutive ineffective rounds → force strategy switch |
| 4 | Judging effectiveness by returncode alone | False positives — crashes reported as passes | Use coverage change instead of returncode |
| 5 | Pure LLM auto-loop struggling in deep coverage zones | Repeated stalls; unable to break through | Switch to human-in-the-loop at 60%+ |

---

## V. Human-in-the-Loop Trigger Conditions

Introduce human (Auditor / Reviewer) judgment when the following signals appear:

| Signal | Action |
|--------|--------|
| 3 consecutive rounds with no coverage improvement | Pause auto-loop; Auditor analyzes uncovered branch testability |
| Same module: 2 consecutive rounds with no new coverage | Evaluate whether to filter the module |
| Coverage growth significantly slows after 60%+ | Switch to "human finds pattern, LLM implements" mode |
| Exception paths (try/except) largely uncovered | Reviewer provides exception-triggering plans per function |
| Test pass rate below 50% | Check whether prompt is missing critical context (API signatures, import paths, return patterns) |

---

## VI. Alignment with the Loop Engineering Framework

| Dimension | Loop Engineering Philosophy | MA Practice |
|-----------|---------------------------|-------------|
| **Cost** | Assess cost per loop; set hard caps | Budget cap + per-round balance check + actual model pricing |
| **State** | State persists and is traceable across loops | Accumulation-based test system + codebase as state carrier |
| **Runaway** | Prevent infinite loops, directional drift, quality degradation | Consecutive stall termination + hard round cap + strategy switch thresholds |

### Unique Contributions of the MA Framework

1. **Accumulation-based approach is an exemplary implementation of "State."** The test codebase as state carrier is more robust than JSON files — git provides complete history traceability and rollback capability.
2. **"Filter over fix" embodies "Decide" intelligence.** Deciding "don't let the LLM see it" is more efficient than "repeatedly attempting to fix" — this is judgment at the decision layer.
3. **"Human finds pattern, LLM implements" is best practice for human-in-the-loop.** A loop does not mean fully automated. In deep coverage zones, human judgment determines "what to do next," while LLM generates "the actions" — each plays to its strength.
4. **"Don't modify source code" is the safety baseline.** The biggest risk of an automated loop is irreversible damage to the system. This baseline must be established before the automated loop begins.

---

## Update Record

- **2026-06-24: v1.0** — Extracted from 45 rounds of LLM-driven coverage testing iteration, covering 5 building blocks, 5 reusable patterns, cost model, anti-patterns, and human-in-the-loop trigger conditions
