# AUTONOMOUS LOOP

Each full pass is one cycle. The loop runs phases sequentially.
Context budget is monitored at every phase boundary.

---

## PHASE 0: SESSION INIT (every session start)

1. Read CLAUDE.md / AGENTS.md (base knowledge)
2. Read docs/status/PROGRESS.md (is this a resumption?)
3. Read docs/status/HANDOFF.md (load checkpoint if resuming)
4. Read MEMORY.md (failure patterns)
5. Read references/constraints.md (apply prevention rules)
6. Check context: if already above 20% from init reads, compact now

LIFESPAN HOOK: on-start
  Surface to human: "Resuming cycle NNN from checkpoint T-NNN" or "Starting new cycle"
  Wait for human acknowledgment before proceeding.

---

## PHASE 1: UNDERSTAND

- Scan repo with list_dir and read_file (codebase is truth)
- Update or verify architecture map in docs/architecture/
- Identify current system state: healthy / degraded / unknown
- Context check: if above 40%, compact before Phase 2

---

## PHASE 2: DOCUMENT

- Verify docs completeness
- Identify code that lacks a spec or plan
- Dispatch doc_writer_agent for gaps before proceeding
- Context check: if above 40%, sub-agent the scan

---

## PHASE 3: PLAN

- Dispatch researcher sub-agent (Phase 1 of 3-phase model)
  - Input: task description and relevant file list
  - Output: research summary (docs/status/RESEARCH-NNN.md)
  - researcher uses read-only tools only

- Dispatch planner sub-agent (Phase 2 of 3-phase model)
  - Input: research summary
  - Output: PLAN-NNN.md with exact steps, filenames, line numbers, test steps

HUMAN GATE (P6): Surface plan to human for approval.
  Do not proceed to Phase 4 without explicit human approval.
  Record approval in PLAN-NNN.md with timestamp.

---

## PHASE 4: BUILD

- Dispatch implementer sub-agent (Phase 3 of 3-phase model)
  - Input: approved plan
  - Context limit: 40% per implementer instance
  - Creates CHECKLIST-NNN.md before writing any code
  - Commits a checkpoint after every atomic task (T-NNN)
  - Updates PROGRESS.md after each checkpoint

- If implementer hits 40% context mid-task:
  - Write HANDOFF.md
  - Spawn a fresh implementer with HANDOFF + relevant files only
  - Continue from "Next step"

---

## PHASE 5: VERIFY (tool-driven, deterministic first)

1. Run pre-commit hooks (linter, formatter) -- must pass before tests
2. run_unit_tests() => run_integration_tests() => run_e2e_tests()
3. collect_logs() for any failure
4. scan_vulnerabilities() + dependency_audit()
5. performance_profile() (application-level only)
6. Run 3-layer recursive review cycle (agents/reviewer.md)

Categorize all output per runtime/observability.md (Tier 1-4).

IF Tier 1 failure:
  Stop. Dispatch debugger_agent. Write MEMORY.md entry.
  Identify harness gap (see runtime/observability.md).
  Do NOT advance to Phase 6 until clean.

---

## PHASE 6: REFLECT

- Write CYCLE-NNN.md summary (docs/status/)
- Write MEMORY.md entries for every failure (EPISODIC type)
- Identify gap categories for recurring patterns
- Update references/constraints.md if Prevention Rule is needed

LIFESPAN HOOK: on-cycle-complete
  Surface summary to human: what was built, what failed, what was learned.

---

## PHASE 7: IMPROVE

- Apply self-improvement rules (runtime/self-improvement.md)
- Run garbage_collector_agent if gc_interval has elapsed
- Run harness review if 5 cycles have completed (runtime/observability.md)

---

## LOOP CONDITION

Continue unless:
  - No tasks remain (transition to maintenance mode)
  - Human explicitly halts

In single-pass mode: stop after Phase 7, surface summary, wait for human go-ahead.

---

## SINGLE-PASS MODE

When CONFIG.yaml loop_mode is single-pass:
1. Complete one full cycle (Phases 0-7)
2. Produce quality report (templates/quality.md)
3. Surface to human: all files changed, PRs created, test results, failures
4. Wait for explicit human approval before running another cycle
