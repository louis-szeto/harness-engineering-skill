# Harness Engineer

A production-grade OpenClaw skill that transforms Claude Code into a **self-improving software engineering system**. It uses persistent memory, role-specialized agents, tool-driven execution, and a recursive improvement loop to deliver continuously improving code quality.

## Six Core Principles

| # | Principle | What it means |
|---|-----------|---------------|
| P1 | **Context Engineering** | Treat context as finite and precious — 40% rule, sub-agents to isolate concerns, codebase over docs |
| P2 | **Tool Usage** | Each agent gets only the tools it needs via MCP; generated code runs sandboxed |
| P3 | **Verification** | Every output verified by someone other than who produced it; deterministic checks before LLM judgment |
| P4 | **Status Management** | State lives in the repo (PROGRESS.md, HANDOFF.md), not in the context window |
| P5 | **Observability** | Track what happens; feed failures back into the harness, not the code |
| P6 | **Human Supervision** | Humans approve plans, architecture changes, retry decisions, and lifespan hooks |

## Execution Model

The runtime runs a continuous 8-phase loop:

```
Session Init → Understand → Document → Plan → Build → Verify → Reflect → Improve → LOOP
```

Three loop modes available in `CONFIG.yaml`:
- **single-pass** (safe default) — runs one cycle then stops
- **maintenance** — reduced loop, bug fixes only
- **continuous** — full autonomous loop (graduate to this after sandbox validation)

When active work completes, the system transitions into **optimization** or **garbage-collection** mode rather than stopping.

## Agent Architecture

The dispatcher orchestrates a **three-phase model**: Research → Plan → ITR (Implementer-Tester-Reviewer) Execution.

| Agent | Role |
|-------|------|
| **dispatcher** | Task decomposition, agent spawning, context budgeting — only agent that creates others |
| **researcher** | Decomposes codebase into functional pieces, maps integrations, produces factual inventory only |
| **planner** | Converts research into modular execution plans with work units and piece contracts |
| **implementer** | Writes production code against approved plans; atomic commits with checkpoint discipline |
| **tester** | Writes/runs tests; 90% coverage floor; structured JSON output; dispatches debugger on failure |
| **reviewer** | 3-layer recursive review (plan alignment → correctness → architecture); generation-review isolation |
| **debugger** | Root cause analysis — never patches symptoms; escalates to Prevention Rules after 2+ occurrences |
| **optimizer** | Performance, memory, cost improvements (after security/correctness); requires before/after profiling |
| **garbage-collector** | Dead code, outdated docs, drift detection; entropy must trend downward |

## Project Structure

```
CONFIG.yaml              Runtime settings (loop mode, priorities, coverage, git rules)
SKILL.md                 Skill manifest — principles, startup sequence, file index
MEMORY.md                Live memory store (episodic, semantic, procedural entries)
PLATFORM_REQUIREMENTS.md Platform capability checks — read before first use

agents/                  11 role-specialized agent definitions
references/              Non-negotiable constraints and standards
runtime/                 Execution engine, context rules, status management, observability
templates/               Scaffolds for plans, ADRs, architecture docs, test plans, quality reports
tools/                   Tool registry, routing, execution protocol
```

## Startup Sequence

1. Read `CLAUDE.md` or `AGENTS.md` if present (base context)
2. Read `CONFIG.yaml` (runtime settings)
3. Read `runtime/loop.md` (execution model)
4. Read `runtime/context-engineering.md` (context budget rules)
5. Read `runtime/status-management.md` (restore checkpoint if resuming)
6. Read `MEMORY.md` (prior failure context)
7. Read `agents/dispatcher.md` (task decomposition model)
8. Begin the loop

## Safe Start

1. Read `PLATFORM_REQUIREMENTS.md` and verify every item
2. Run on a throwaway branch in **single-pass** mode first
3. Keep `max_parallel_agents: 3` until behavior is validated
4. Require human reviewers on main/trunk in your git host
5. Graduate: single-pass → maintenance → continuous

## Configuration (`CONFIG.yaml`)

- **runtime**: `loop_mode` (single-pass/maintenance/continuous), `max_parallel_agents` (default 3), `retry_limit` (5), `gc_interval` (every 10 cycles)
- **priorities**: security, correctness (critical) → reliability, performance (high) → memory, maintainability (medium) → cost (low)
- **testing**: 90% coverage minimum; unit + integration + e2e required
- **git**: trunk-based, PR required with human approval, 2-day branch lifetime, no auto-merge
- **memory**: persistent, unlimited history, pattern detection after 2 failures
- **self_improvement**: failures generate constraints, tests, or documentation
- **tools**: max 3 retries, all calls logged, destructive actions blocked without approval

## Non-Negotiable Rules

1. CLAUDE.md / AGENTS.md is ground truth — read it first, every session
2. Codebase over docs — when they conflict, trust the code
3. 40% context rule — compact or delegate to sub-agent before crossing 40% of context window
4. No implementation without research output, a plan, and validation criteria
5. Generation and review are always separate — never the same agent
6. Failure = harness gap — fix the system cause, not the symptom
7. Optimization priority (strict): security → correctness → reliability → performance → memory → maintainability → cost
