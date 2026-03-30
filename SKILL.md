---
name: harness-engineer
description: >
  A persistent autonomous engineering harness runtime that transforms any repository into a
  self-improving software system. Use this skill whenever the user wants to: build or run an
  autonomous coding agent, set up a self-healing engineering loop, orchestrate multi-agent
  software development, implement harness engineering principles, create a doc-driven
  development workflow, or run long-horizon autonomous software tasks. Trigger on phrases like
  "autonomous agent", "self-healing codebase", "harness engineering", "multi-agent pipeline",
  "continuous improvement loop", "OpenClaw skill", or any request for an agentic engineering
  system that runs without constant human input.
---

# HARNESS AUTONOMOUS RUNTIME

A complete skill package for turning Claude Code into a **self-evolving software engineering
system**. It combines persistent memory, multi-agent specialization, tool-driven execution, and
a recursive improvement loop.

---

## NON-NEGOTIABLE RULES

1. **EVERYTHING MUST BE IN-REPO** — if it is not in `docs/`, it does not exist.
2. **NO IMPLEMENTATION WITHOUT** a spec, a plan, and validation criteria.
3. **FAILURE = HARNESS GAP** — never patch output alone; fix the system cause.
4. **ALWAYS** test → validate → document → score.
5. **OPTIMIZATION PRIORITY (STRICT ORDER):**
   1. Security
   2. Correctness
   3. Reliability
   4. Performance
   5. Memory efficiency
   6. Maintainability
   7. Cost

---

## EXECUTION MODEL

The system runs a continuous loop:

```
UNDERSTAND → DOCUMENT → PLAN → BUILD → VERIFY → REFLECT → IMPROVE → LOOP
```

The system never truly stops — it transitions into **maintenance**, **optimization**, or
**garbage-collection** mode.

---

## HOW TO USE THIS SKILL

When activated, Claude should:

1. **Read `CONFIG.yaml`** to load runtime settings.
2. **Read `runtime/loop.md`** to understand the execution loop.
3. **Read `agents/dispatcher.md`** to understand how to decompose tasks.
4. **Read `tools/TOOL_REGISTRY.md`** to understand available tool calls.
5. **Load `MEMORY.md`** to restore context from prior cycles.

Then begin the loop. Use the reference files below as needed during execution.

---

## REFERENCE FILES

| File | When to read |
|------|-------------|
| `CONFIG.yaml` | At startup — sets all runtime parameters |
| `MEMORY.md` | At startup and after every failure |
| `runtime/loop.md` | To execute each loop cycle |
| `runtime/memory-system.md` | When writing or querying memory |
| `runtime/self-improvement.md` | After any failure |
| `runtime/prioritization.md` | When selecting the next task |
| `runtime/autonomy-rules.md` | When blocked or uncertain |
| `agents/dispatcher.md` | When decomposing a task into sub-agents |
| `agents/*.md` | Load the specific agent file when spawning that agent |
| `tools/TOOL_REGISTRY.md` | Before any tool call |
| `tools/tool-router.md` | To validate and route tool requests |
| `tools/execution-protocol.md` | For the full tool call lifecycle |
| `references/harness-rules.md` | For core harness engineering constraints |
| `references/testing-standards.md` | Before writing or running tests |
| `references/security-performance.md` | Before any implementation |
| `references/git-workflow.md` | Before any commit or PR |
| `templates/` | When creating plans, ADRs, or agent manifests |
