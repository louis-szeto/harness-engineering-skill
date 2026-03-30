# Harness Engineer

Transform codebases into agent-first environments where AI agents can work autonomously, correctly, and coherently.

Built on the principle: **Humans steer. Agents execute.**

## What It Does

Harness Engineer is a skill that sets up projects for autonomous AI agent development. Instead of writing code yourself, you design the environment — structured markdowns, enforced invariants, and clear contracts — then spawn agents to do the implementation.

## The Workflow

1. **Analyze** the codebase (structure, domains, gaps)
2. **Generate** harness markdowns (AGENTS.md, docs/ hierarchy)
3. **Spawn** coding agents with sprint contracts
4. **Monitor** results and verify quality
5. **Garbage collect** — periodic drift checks and quality refreshes

## What Gets Generated

In your target project:

```
your-project/
├── AGENTS.md                    # Entry point (~100 lines)
├── docs/
│   ├── architecture.md          # Design decisions, domain structure
│   ├── domains.md               # Dependency rules per domain
│   ├── quality.md               # A–F quality grades with gaps
│   ├── golden-principles.md     # Enforced code quality rules
│   └── plans/                   # Active and completed plans
```

## Spawning an Agent

```bash
claude --permission-mode bypassPermissions --print "Your task. When done, run: openclaw system event --text 'Done: summary' --mode now"
```

Each agent reads the harness markdowns, implements its sprint contract, and loops through test → self-review → fix until all tests pass and no lint errors remain.

## Contents

- `SKILL.md` — Full methodology (the main reference)
- `references/agent-prompts.md` — Prompt templates for feature work, testing, docs, refactoring, and bug fixes
- `references/golden-principles.md` — Catalog of enforceable quality rules (parse don't validate, structured logging, file size limits, etc.)
- `references/quality-rubric.md` — A–F grading scale and assessment process

## Core Rules

- **Dependency direction** within any domain: `Types → Config → Repo → Service → Runtime → UI` — never backwards
- **Convergence criteria**: all tests pass + zero lint errors + golden principles compliant + acceptance criteria met
- **Parse at boundaries**: validate data shapes at entry points, trust typed data internally
- **Plans are artifacts**: versioned, co-located, tracked alongside code
