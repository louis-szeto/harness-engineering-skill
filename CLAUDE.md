# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

This is the **harness-engineer** skill — a reference implementation for "harness engineering," a methodology that transforms codebases into agent-first environments. It is not a runnable application; it is a knowledge base and template library used by AI agents (Claude Code, Codex) to autonomously implement features in target projects.

## Architecture

```
harness-engineer/
├── SKILL.md                      # Main entry point — full methodology (5 phases)
├── references/
│   ├── agent-prompts.md          # Prompt templates for 5 task types (feature, test, docs, refactor, bugfix)
│   ├── golden-principles.md      # Catalog of enforceable code quality rules
│   └── quality-rubric.md         # A–F grading scale and assessment process
└── scripts/                      # Empty — reserved for automation tooling
```

## The 5-Phase Workflow

1. **Analyze Codebase** — Read structure, identify domains/layers/dependencies/gaps
2. **Generate Harness Markdowns** — Create AGENTS.md + docs/ hierarchy (architecture, domains, quality, golden-principles, plans)
3. **Spawn Coding Agents** — Decompose into sprint contracts, spawn one agent per work unit, each follows a read→implement→test→self-review→fix loop until convergence
4. **Monitor & Collect** — Track sessions, verify test/lint/pass results
5. **Garbage Collection** — Periodic drift checks, doc updates, quality grade refreshes

## Key Concepts

- **Sprint contracts** are the unit of agent work: feature description + acceptance criteria + verification steps. See `references/agent-prompts.md` for templates.
- **Golden principles** are encoded human taste enforced by linters. The 10 universal principles are in `references/golden-principles.md`.
- **Quality grading** uses an A–F rubric across 4 axes (tests, docs, linting, golden principles). See `references/quality-rubric.md`.
- **Convergence** = all tests pass + zero lint errors + golden principles compliant + sprint acceptance criteria met.

## Agent Spawning Pattern

```bash
claude --permission-mode bypassPermissions --print "Your task. When done, run: openclaw system event --text 'Done: summary' --mode now"
```

Each agent receives a prompt structured as: context files to read → sprint contract → implementation instructions (with recursive test-fix loop) → verification → completion signal.

## Harness File Structure (Generated in Target Projects)

The methodology generates these files in the target project:
- `AGENTS.md` — ~100-line entry point (overview, quick start, architecture map, conventions, invariants)
- `docs/architecture.md` — domains, layers, cross-cutting concerns, ADRs
- `docs/domains.md` — dependency rules per domain
- `docs/quality.md` — graded quality table with known gaps
- `docs/golden-principles.md` — project-specific subset of principles
- `docs/plans/` — active plans, completed plans, tech debt

## Dependency Direction Rule

Within any domain, code flows in one direction: `Types → Config → Repo → Service → Runtime → UI`. Reverse dependencies are forbidden.
