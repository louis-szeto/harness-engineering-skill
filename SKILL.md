---
name: harness-engineer
description: 'Apply harness engineering principles to transform a codebase into an agent-first development environment. Use when the user asks to: (1) set up harness engineering for a project, (2) rewrite/regenerate project markdowns into harness engineering format, (3) spawn Claude Code agents to implement features following harness patterns, (4) audit a codebase for harness readiness, (5) create structured knowledge bases (AGENTS.md, docs/ hierarchy) for AI agent consumption. Core workflow: read codebase → generate structured markdowns → spawn coding agents with clear contracts → agents recursively search, implement, test, self-review until perfect.'
---

# Harness Engineer

Transform codebases into agent-first environments where Claude Code agents can work autonomously, correctly, and coherently.

## Core Philosophy

From OpenAI's harness engineering: **"Humans steer. Agents execute."** The primary job is designing environments, specifying intent, and building feedback loops—not writing code.

**Key principles:**
- Give agents a **map**, not a 1,000-page manual
- Enforce **invariants**, not implementations
- **Parse at boundaries** (validate data shapes at entry points)
- Treat plans as **first-class artifacts** (versioned, co-located)
- Encode "golden principles" and run periodic **garbage collection**

## Workflow

### Phase 1: Analyze Codebase

Before writing anything, understand the project:

1. Read the existing project structure (`find`, `tree`, `ls -la`)
2. Read existing markdown files (README, CLAUDE.md, AGENTS.md, any docs/)
3. Identify: languages, frameworks, test infrastructure, CI config, package managers
4. Map the architectural domains and dependency directions
5. Identify gaps: missing docs, missing tests, missing constraints

**Output:** A mental (or written) map of domains, layers, and boundaries.

### Phase 2: Generate Harness Markdowns

Rewrite or create the following files in harness engineering format:

#### 2.1 AGENTS.md (Root — ~100 lines, table of contents style)

```markdown
# AGENTS.md

## Project Overview
One paragraph: what this is, tech stack, how to build/test/run.

## Quick Start
```bash
# Install, build, test, run — the minimal commands
```

## Architecture Map
Pointer to `docs/architecture.md`. Domain → layer → package mapping.

## Domain Guide
Pointer to `docs/domains.md`. What lives where, dependency rules.

## Codebase Conventions
- Language-specific style rules
- Naming conventions
- File organization patterns

## Quality & Invariants
- What custom linters enforce
- Structural test requirements
- Boundary validation rules ("parse, don't validate")

## Knowledge Base
- `docs/architecture.md` — system of record for design decisions
- `docs/domains.md` — domain boundaries and dependency rules
- `docs/quality.md` — quality grades per domain, tracked gaps
- `docs/golden-principles.md` — encoded human taste, enforced by linters
- `docs/plans/` — active plans, completed plans, known tech debt

## How to Work Here
1. Read this file first
2. Read `docs/architecture.md` for context
3. Check `docs/plans/` for any active execution plan
4. Follow the invariant rules below
5. Write tests before or alongside code
6. Run tests and fix failures before opening PRs
```

#### 2.2 docs/architecture.md

```markdown
# Architecture

## Domains & Layers
| Domain | Layers (dependency direction →) |
|--------|-------------------------------|
| Auth | Types → Config → Repo → Service → Runtime → UI |
| ... | ... |

## Cross-Cutting Concerns
Auth, telemetry, connectors, feature flags — enter through explicit Provider interfaces.

## Design Decisions
Numbered ADRs: what, why, alternatives considered, date.

## Tech Stack & Rationale
Why each technology was chosen (agents reason better with "why").
```

#### 2.3 docs/domains.md

```markdown
# Domains

## Dependency Rules
- Within each domain, code flows forward through layers only
- Cross-domain dependencies go through explicit interfaces
- No circular dependencies (enforced by linter)

## Domain Map
### {Domain Name}
- Purpose: ...
- Packages: ...
- Depends on: ...
- Tests location: ...
```

#### 2.4 docs/quality.md

```markdown
# Quality Grades

## Grade Scale: A (production-ready) → F (no tests, no docs)

| Domain | Tests | Docs | Linting | Grade | Notes |
|--------|-------|------|---------|-------|-------|
| Auth | 95% | ✅ | ✅ | A | |
| ... | ... | ... | ... | ... | |

## Known Gaps
- [ ] Domain X needs integration tests
- [ ] Module Y has no boundary validation

Updated: YYYY-MM-DD
```

#### 2.5 docs/golden-principles.md

```markdown
# Golden Principles

Encoded human taste. Enforced by linters and periodic cleanup agents.

1. **Prefer shared utility packages** over hand-rolled helpers
2. **Parse, don't validate** — validate data shapes at boundaries
3. **No YOLO data access** — use typed SDKs, never guess shapes
4. **Structured logging only** — no print statements, no f-strings in logs
5. **File size limits** — files over N lines trigger refactoring
6. **Schema naming convention** — {entity}_{use}.ts/jo/py
```

#### 2.6 docs/plans/ (directory)

Active plans, completed plans, tech debt — all versioned.

Each plan:
```markdown
# Plan: {title}

## Status: active | completed | tech-debt
## Created: YYYY-MM-DD
## Updated: YYYY-MM-DD

## Objective
...

## Acceptance Criteria
- [ ] ...

## Progress Log
- YYYY-MM-DD: Started sprint 1, completed X
- YYYY-MM-DD: Hit issue with Y, pivoted to Z
```

### Phase 3: Spawn Coding Agents

After generating the harness markdowns, spawn Claude Code agents to do the actual work.

#### Agent Assignment Strategy

1. **Decompose** the task into independent work units (one per domain, module, or feature)
2. **Write a sprint contract** for each agent: what to build, acceptance criteria, how to verify
3. **Spawn agents** — one per work unit, each in its own worktree or directory

See [references/agent-prompts.md](references/agent-prompts.md) for detailed prompt templates.

#### Spawning Commands

```bash
# Claude Code — single task
claude --permission-mode bypassPermissions --print "Your task. When done, run: openclaw system event --text 'Done: summary' --mode now"

# Background for long tasks
claude --permission-mode bypassPermissions --print "Your task..." &
# Or via exec tool with background:true
```

```bash
# Codex — single task (needs PTY)
codex exec --full-auto "Your task"

# Background
codex exec --full-auto "Your task. When done, run: openclaw system event --text 'Done: summary' --mode now" &
```

#### The Recursive Loop (Per Agent)

Each spawned agent follows this cycle until convergence:

```
┌─────────────────────────────────────────────┐
│ 1. READ sprint contract + harness markdowns  │
│ 2. WEB SEARCH for best practices & standards  │
│ 3. IMPLEMENT code following patterns          │
│ 4. WRITE comprehensive tests                  │
│ 5. RUN tests                                 │
│ 6. READ test logs + failures                 │
│ 7. SELF-ANALYZE: what went wrong, why        │
│ 8. REVIEW own code against golden principles  │
│ 9. FIX issues found in step 7-8              │
│ 10. GOTO step 5 until all tests pass         │
│ 11. REPORT completion + quality metrics       │
└─────────────────────────────────────────────┘
```

**Convergence criteria:** All tests pass + no lint errors + code follows golden principles + sprint contract acceptance criteria met.

#### Agent Prompt Template

Include in each agent's task:

```
You are working on {project} as part of a harness engineering workflow.

## Your Sprint Contract
{specific task, acceptance criteria, verification steps}

## Instructions
1. Read AGENTS.md and docs/architecture.md for context
2. Web search for best practices for {specific technology/pattern}
3. Implement the required changes
4. Write comprehensive tests (unit + integration where applicable)
5. Run the tests. If any fail:
   a. Read the test output carefully
   b. Analyze the root cause
   c. Fix the code or the test (whichever is wrong)
   d. Re-run until all pass
6. Self-review your code against the golden principles in docs/golden-principles.md
7. If you find violations, fix them and re-run tests
8. Repeat steps 5-7 until everything is clean
9. Commit with a descriptive message
10. When completely finished, run: openclaw system event --text "Done: {brief summary}" --mode now
```

### Phase 4: Monitor & Collect Results

After spawning agents:

1. Track sessions via `process action:list` or `subagents action:list`
2. Check logs periodically: `process action:log sessionId:XXX`
3. When an agent finishes, verify:
   - Tests pass
   - No lint errors
   - Code follows harness conventions
4. If quality is insufficient, respawn with additional guidance

### Phase 5: Garbage Collection (Periodic)

After agent work completes, run cleanup:

1. Check for drift from golden principles
2. Verify documentation matches code reality
3. Update quality grades in `docs/quality.md`
4. Flag new tech debt in `docs/plans/`
5. Run linters across the full codebase

## File Structure Summary

```
{project-root}/
├── AGENTS.md                    # Entry point (~100 lines)
├── docs/
│   ├── architecture.md          # System of record for design
│   ├── domains.md               # Domain boundaries & deps
│   ├── quality.md               # Quality grades & gaps
│   ├── golden-principles.md     # Enforced human taste
│   └── plans/                   # Active + completed plans
│       ├── active-plan.md
│       └── completed/
├── src/                         # Source code (agent-generated)
├── tests/                       # Tests (agent-generated)
└── .github/
    └── workflows/               # CI (agent-generated)
```

## References

- [Agent prompt templates](references/agent-prompts.md) — detailed prompts for different task types
- [Golden principles catalog](references/golden-principles.md) — common principles to enforce
- [Quality grading rubric](references/quality-rubric.md) — how to grade domain quality
