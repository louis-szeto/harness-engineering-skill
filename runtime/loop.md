# AUTONOMOUS LOOP

This is the core execution engine. Run this loop continuously. Each full pass is one **cycle**.

---

## STEP 1: UNDERSTAND
- Scan the repo with `list_dir` and `read_file`.
- Update or create the architecture map (`docs/architecture/`).
- Reload `MEMORY.md` to restore context.
- Identify current system state: healthy / degraded / unknown.

## STEP 2: DOCUMENT
- Verify docs completeness (`list_dir docs/`).
- Identify any code that lacks a corresponding spec or plan.
- Dispatch `doc_writer_agent` to fill gaps before proceeding.

## STEP 3: PLAN
- Create `docs/exec-plans/PLAN-NNN.md` using `templates/plan.md`.
- Break work into atomic tasks — each task must be completable by a single agent.
- Score every task using `runtime/prioritization.md`.
- Select the highest-scoring tasks for this cycle.

## STEP 4: BUILD
- Dispatch agents via `agents/dispatcher.md`.
- Implement in parallel where dependencies allow.
- All implementations reference their PLAN-NNN.md.

## STEP 5: VERIFY (TOOL-DRIVEN)
1. Run tests: `run_unit_tests()` → `run_integration_tests()` → `run_e2e_tests()`
2. Collect logs: `collect_logs()`
3. Security scan: `scan_vulnerabilities()` + `dependency_audit()`
4. Performance profile: `performance_profile()`

**IF ANY STEP FAILS:**
→ dispatch `debugger_agent`
→ write MEMORY.md entry
→ re-loop from STEP 5 (do not proceed to STEP 6 until clean)

## STEP 6: REFLECT
- Analyze all failures from this cycle.
- Write MEMORY.md entries for every failure (EPISODIC type).
- Identify patterns (2+ same failure = Prevention Rule).

## STEP 7: IMPROVE
- Apply self-improvement rules from `runtime/self-improvement.md`.
- Update `references/constraints.md` with new Prevention Rules.
- Update `references/harness-rules.md` if a new enforcement rule is needed.
- Dispatch `garbage_collector_agent` if `gc_interval` has elapsed.

## LOOP CONDITION
Continue unless:
- No tasks remain AND no improvements are possible (transition to maintenance mode).
- A human explicitly halts execution.

**The system does not stop. It transitions.**
