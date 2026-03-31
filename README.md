# Harness Engineer

A production-grade OpenClaw skill that transforms Claude Code into a **self-improving software engineering system**. It applies six harness engineering principles — context engineering, tool isolation, verification, status management, observability, and human supervision — to run autonomous, long-horizon development loops safely.

Designed for Claude Code and OpenClaw environments.

---

## Six Core Principles

| # | Principle | Summary |
|---|-----------|---------|
| P1 | **Context Engineering** | Context is finite. Compact or outsource to sub-agents at 40% of window. Ground truth: codebase > CLAUDE.md > logs > docs > memory. |
| P2 | **Tool Usage** | Each agent gets only the tools its role requires. All tools routed through MCP. Generated code runs in an isolated sandbox. |
| P3 | **Verification** | Generation and review are always separate. Deterministic checks first (linters, tests). 3-layer recursive review until all reviewers approve. |
| P4 | **Status Management** | State lives in the repo (PROGRESS.md, HANDOFF.md), not the context window. Git checkpoint after every atomic task. Full recovery from any context reset. |
| P5 | **Observability** | Track execution, categorize quality (4 tiers), detect abnormalities, and feed failures back as harness improvements — not code patches. |
| P6 | **Human Supervision** | Plans require approval before implementation. Architecture changes need sign-off. Destructive actions are blocked without human confirmation. |

---

## Execution Model

```
UNDERSTAND → DOCUMENT → PLAN → BUILD → VERIFY → REFLECT → IMPROVE → LOOP
```

The system never truly stops — it transitions into **maintenance**, **optimization**, or **garbage-collection** mode.

Tasks flow through a **three-phase sub-agent model**:

1. **Researcher** — compress codebase information into truth (read-only, no opinions)
2. **Planner** — convert research into a specific plan with filenames, line numbers, test steps
3. **Implementer + Reviewer cycle** — execute the plan, verify each output against it

---

## Architecture

### Agents (`agents/`)

| Agent | Role | Tools |
|-------|------|-------|
| **dispatcher** | Task decomposition — the only agent that can spawn others | All (orchestration only) |
| **researcher** | Read-only codebase analysis, log collection | read-only set |
| **planner** | Produce PLAN-NNN.md with exact implementation steps | read + write to `docs/` |
| **implementer** | Write production code from the plan | read + write to `src/`, `tests/` |
| **reviewer** | Verify output against plan (not just correctness) | read + test run + security scan |
| **tester** | Unit/integration/e2e/fuzz tests (90% coverage floor) | test execution + write to `tests/` |
| **debugger** | Root cause analysis — triggered on abnormality or regression | read + search + logs |
| **optimizer** | Performance improvements (after security/correctness pass) | read + profile + audit |
| **doc-writer** | All documentation using templates | read + write to `docs/` |
| **garbage-collector** | Dead code removal, drift detection, entropy reduction | read + search + diff |

### Runtime (`runtime/`)

| File | Purpose |
|------|---------|
| `loop.md` | Core execution engine and 7-step cycle definition |
| `context-engineering.md` | 40% rule, ground truth hierarchy, compaction rules, session start protocol |
| `status-management.md` | PROGRESS.md, HANDOFF.md, checkpoint protocol, recovery protocol |
| `observability.md` | Execution tracking, quality tiers, abnormality detection, harness gap analysis |
| `memory-system.md` | Episodic/semantic/procedural memory format and rules |
| `prioritization.md` | Task scoring: security > failing tests > correctness > ... |
| `self-improvement.md` | Every failure produces a constraint, test, or doc |
| `autonomy-rules.md` | Behavior when blocked, uncertain, or at a human gate |

### Tools (`tools/`)

All tool calls go through MCP. The tool-router enforces per-agent subsets, blocks destructive actions without approval, redacts credentials, and logs metadata.

- **TOOL_REGISTRY.md** — Catalog of all tools across 9 categories
- **tool-router.md** — Centralized routing, rate limiting, safety rules
- **execution-protocol.md** — 5-step lifecycle: Request → Route → Execute → Validate → Log

### References (`references/`)

Non-negotiable constraints governing all harness behavior: harness rules, testing standards, security/performance requirements, git workflow (trunk-based), phases of operation, MCP tool subsets, and self-generated prevention rules.

### Templates (`templates/`)

Scaffolds for plans, ADRs, architecture docs, test plans, quality reports, handoffs, and agent manifests.

---

## Startup Sequence

1. Read `CLAUDE.md` or `AGENTS.md` (base knowledge)
2. Read `CONFIG.yaml` (runtime settings)
3. Read `runtime/loop.md` (execution model)
4. Read `runtime/context-engineering.md` (context budget rules)
5. Read `runtime/status-management.md` (restore checkpoint if resuming)
6. Read `MEMORY.md` (prior failure context)
7. Read `agents/dispatcher.md` (task decomposition model)
8. Begin the loop

---

## Configuration (`CONFIG.yaml`)

| Section | Key settings |
|---------|-------------|
| **runtime** | `loop_mode`: single-pass (safe default) / continuous / maintenance. `max_parallel_agents`: 3 (increase after validation). `retry_limit`: 5 |
| **priorities** | Fixed strict order: security, correctness, reliability, performance, memory, maintainability, cost |
| **testing** | 90% coverage minimum. Unit + integration + e2e required. Fuzz recommended |
| **git** | Trunk-based. PR required with human approval (no auto-merge). 2-day branch lifetime |
| **memory** | Persistent, unlimited history. Pattern detection after 2 failures creates prevention rules |
| **self_improvement** | Enabled — every failure generates a rule, test, or doc |
| **tools** | 3 retries max. All calls logged. Destructive actions blocked without approval (never disable) |

---

## Safe Start

Before running on a real repository:

1. **Verify platform requirements** (`PLATFORM_REQUIREMENTS.md`) — MCP tool router, sandboxed execution, git scoping, web search staging, human approval infrastructure
2. **Sandbox first** — run on a throwaway branch in single-pass mode before enabling continuous
3. **Review CONFIG.yaml** — confirm `loop_mode: single-pass`, `max_parallel_agents: 3`, `block_destructive_without_approval: true`
4. **Protect main branch** — require human reviewers in your git host
5. **Graduate gradually** — single-pass → maintenance → continuous

---

## Non-Negotiable Rules

1. CLAUDE.md / AGENTS.md is ground truth — read it first, every session
2. Codebase over docs — when they conflict, trust the code
3. 40% context rule — compact or sub-agent before crossing 40% of context window
4. No implementation without research, a plan, and validation criteria
5. Generation and review are always separate — never the same agent
6. Failure = harness gap — fix the harness, not just the symptom
7. Optimization priority (strict): Security → Correctness → Reliability → Performance → Memory → Maintainability → Cost
