# STATUS MANAGEMENT

State lives in the repo, not the context window. This enables recovery at any point.

---

## EXTERNAL STATE FILES

docs/status/PROGRESS.md     -- structured checklist of all tasks (current cycle)
docs/status/HANDOFF.md      -- checkpoint state for context resets and session handoffs
docs/status/CYCLE-NNN.md    -- completed cycle summaries (one per cycle, append-only)

These files are the single source of truth for "where are we now."
Read them at session start before doing anything else.

---

## PROGRESS.md FORMAT

```
# PROGRESS -- Cycle NNN
Last updated: YYYY-MM-DD HH:MM
Current phase: research | plan | implement | verify | reflect
Current task: <T-NNN description>

## Backlog
- [ ] T-001: <description> [priority: critical]
- [ ] T-002: <description> [priority: high]

## In Progress
- [~] T-003: <description> -- started YYYY-MM-DD

## Completed This Cycle
- [x] T-004: <description> -- committed abc1234

## Blocked
- [!] T-005: <description> -- blocked by: <reason> -- needs: human | debugger | input
```

---

## HANDOFF.md FORMAT

Written before any context reset, session end, or sub-agent handoff.

```
# HANDOFF -- <timestamp>
Reason: context-reset | session-end | sub-agent | compact

## System State
Phase: <current harness phase>
Cycle: NNN
Last checkpoint commit: <git SHA>

## Current Task
Task: <T-NNN description>
Status: <what was done, what remains>
Next step: <exact next action for resuming agent>

## Active Constraints
<list any prevention rules or constraints activated this session>

## Open Questions
<list any unresolved decisions or blockers>

## Files Modified This Session
<list of file paths -- no contents>
```

---

## CHECKPOINT PROTOCOL

A checkpoint is a git commit after every atomic task completion. Rules:

- Commit immediately after each T-NNN task is verified (tests pass)
- Commit message format: "checkpoint(T-NNN): <description> [PLAN-NNN]"
- Update PROGRESS.md before committing (move task from In Progress to Completed)
- Never bundle multiple unverified tasks in one commit

Checkpoint frequency ensures that a context reset loses at most one task of work.

---

## RECOVERY PROTOCOL

When starting a new session or spawning a recovery agent:

1. Read docs/status/HANDOFF.md -- load system state
2. Read docs/status/PROGRESS.md -- identify current task
3. Run git log --oneline -10 -- verify last checkpoint
4. Read references/constraints.md -- apply prevention rules
5. Read MEMORY.md -- reload failure patterns
6. Resume from "Next step" in HANDOFF.md

Do not re-plan from scratch. Trust the checkpoint. Re-plan only if the codebase
has diverged from what HANDOFF.md describes (use codebase, not docs, as truth).

---

## FUNCTION CHECKLIST MARKDOWNS

For complex tasks, create a checklist markdown in docs/status/CHECKLIST-NNN.md:

```
# CHECKLIST -- <feature name>

## Pre-conditions
- [ ] Research complete (researcher output in docs/status/RESEARCH-NNN.md)
- [ ] Plan approved by human (PLAN-NNN.md)
- [ ] Test plan written (templates/test-plan.md)

## Implementation Steps
- [ ] Step 1: <exact description with filename:line>
- [ ] Step 2: ...

## Verification
- [ ] Unit tests pass (coverage >= 90%)
- [ ] Integration tests pass
- [ ] Reviewer cycle complete (all reviewers approved)
- [ ] Checkpoint committed
```

Checklists are not created by the planner -- they are created by the implementer
before writing any code, as a commitment device and progress tracker.
