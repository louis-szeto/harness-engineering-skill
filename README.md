# Harness Engineer

An OpenClaw skill that transforms Claude Code into a **self-evolving software engineering system** through persistent memory, multi-agent specialization, tool-driven execution, and a recursive improvement loop.

## How It Works

The runtime executes a continuous 7-step cycle:

```
UNDERSTAND → DOCUMENT → PLAN → BUILD → VERIFY → REFLECT → IMPROVE → LOOP
```

It never truly stops — it transitions into **maintenance**, **optimization**, or **garbage-collection** mode when active work completes.

## Architecture

### Agents (role-specialized subprocesses)

| Agent | Role |
|-------|------|
| **dispatcher** | Task decomposition and agent spawning — the only agent that can create others |
| **architect** | System design, architecture docs, ADRs |
| **implementer** | Production code — requires specs and plans before writing |
| **tester** | Tests (unit/integration/e2e) — enforces 90% coverage floor |
| **reviewer** | PR enforcement and constraint checking |
| **debugger** | Root cause analysis — never patches symptoms |
| **optimizer** | Performance improvements (after security/correctness) |
| **doc-writer** | All documentation using templates |
| **garbage-collector** | Dead code removal, drift detection, entropy reduction |

### Runtime (`runtime/`)

- **loop.md** — The core execution engine and cycle definition
- **memory-system.md** — How MEMORY.md stores episodic, semantic, and procedural knowledge
- **prioritization.md** — Task scoring formula: security > failing tests > correctness > ...
- **autonomy-rules.md** — Behavior when blocked or uncertain
- **self-improvement.md** — Every failure produces a constraint, test, or doc

### Tools (`tools/`)

- **TOOL_REGISTRY.md** — Catalog of all available tools across 9 categories
- **tool-router.md** — Centralized routing, rate limiting, destructive-action blocking
- **execution-protocol.md** — 5-step lifecycle: Request → Route → Execute → Validate → Log

### References (`references/`)

Non-negotiable constraints: harness rules, testing standards, security-performance requirements, git workflow (trunk-based), phases of operation, and self-generated constraints.

### Templates (`templates/`)

Scaffolds for plans, ADRs, architecture docs, test plans, quality reports, and agent manifests.

## Startup Sequence

1. Read `CONFIG.yaml` for runtime settings
2. Read `runtime/loop.md` for the execution loop
3. Read `agents/dispatcher.md` for task decomposition rules
4. Read `tools/TOOL_REGISTRY.md` for available tools
5. Load `MEMORY.md` to restore context from prior cycles
6. Begin the loop

## Configuration

All settings live in `CONFIG.yaml`:

- **runtime**: loop mode (continuous/single-pass/maintenance), parallelism, retry limits
- **priorities**: fixed order — security, correctness, reliability, performance, memory, maintainability, cost
- **testing**: 95% coverage minimum, unit + integration + e2e required
- **git**: trunk-based, PR required, 2-day branch lifetime
- **memory**: persistent, unlimited history, pattern detection after 2 failures
- **self_improvement**: enabled — failures generate rules, tests, or docs

## Core Rules

1. **Everything must be in-repo** — if it's not in `docs/`, it doesn't exist
2. **No implementation without** a spec, a plan, and validation criteria
3. **Failure = harness gap** — fix the system cause, not just the output
4. **Always** test → validate → document → score
5. **Optimization priority** (strict): security → correctness → reliability → performance → memory → maintainability → cost
