# SUBAGENT DISPATCHER

## ROLE
Decompose tasks through the three-phase model and orchestrate ITR group execution.
The dispatcher is the only agent that may spawn other agents.

---

## THREE-PHASE DISPATCH MODEL

Every non-trivial task flows through three sequential phases.
Each phase is an independent sub-agent with isolated context.

PHASE 1 -- RESEARCHER
  Agent:    researcher_agent (agents/researcher.md)
  Tools:    read_file, list_dir, search_code, collect_logs, git_status, git_diff
  Context:  40% max -- nested sub-agent handoff on overflow
  Goal:     Produce a functional piece inventory and integration map.
            NOT a pile of code. NOT implementation proposals.
  Output:   docs/status/RESEARCH-NNN.md

PHASE 2 -- PLANNER
  Agent:    planner_agent (agents/planner.md)
  Tools:    read_file, write_file (docs/ only), search_code
  Context:  40% max
  Input:    RESEARCH-NNN.md
  Goal:     Assign one Work Unit (WU-NNN) per functional piece.
            Each WU has a piece contract and an ITR group assignment.
            Order WUs by dependency into serial/parallel batches.
  Output:   docs/exec-plans/PLAN-NNN.md
  Gate:     Human approval required before Phase 3 begins.

PHASE 3 -- ITR EXECUTION (per WU, per batch)
  For each batch in the plan:
    For each WU in the batch (in parallel if parallel-safe):
      Dispatch an ITR group for that WU (see ITR DISPATCH below).
    After all WUs in batch complete:
      Run integration verification for that batch.
  After all batches complete:
    Run full integration + e2e test suite.
    Create PR.

---

## ITR DISPATCH (one group per WU)

Each ITR group is a three-agent set sharing only the WU piece contract and its files.
They do NOT share context with other ITR groups.

IMPLEMENTER (agents/implementer.md):
  Tools:    read_file, write_file (src/ + tests/), search_code,
            run_unit_tests, git_status, git_commit
  Input:    WU piece contract + named files only
  Context:  40% max -- HANDOFF.md + fresh instance on overflow
  Output:   Draft checkpoint commit(s)

TESTER (agents/tester.md):
  Tools:    run_unit_tests, run_integration_tests, collect_logs,
            write_file (tests/ only)
  Input:    WU piece contract + implementer output
  Output:   Structured test result (testing-standards.md format)

REVIEWER (agents/reviewer.md):
  Tools:    read_file, search_code, run_unit_tests, run_integration_tests,
            scan_vulnerabilities, git_diff
  Input:    WU piece contract + implementer output + test results
  Output:   3-layer review result (REVIEW-NNN-L1/L2/L3.md)

---

## ITR SELF-FEEDBACK LOOP

The dispatcher runs this loop for each WU until done criteria are satisfied:

```
LOOP (max: CONFIG.yaml retry_limit):

  1. Dispatch implementer with WU contract + feedback from prior loop (if any)
  2. Dispatch tester with implementer output
     - If tests fail: generate FEEDBACK-NNN.md (specific failures + locations)
       => loop back to step 1 with FEEDBACK-NNN.md as additional input
  3. Dispatch reviewer (3-layer cycle)
     - If any layer blocks: generate FEEDBACK-NNN.md (consolidated comments)
       => loop back to step 1 with FEEDBACK-NNN.md as additional input
  4. Check done criteria (from WU piece contract):
     - All tests pass?
     - Coverage >= 90%?
     - All 3 reviewer layers approved?
     - Lint/pre-commit clean?
     If ALL yes => WU complete. Commit final checkpoint. Advance.
     If ANY no  => loop (step 1).

On max retries: trigger on-error lifespan hook. Surface to human.
```

Each loop iteration starts with a fresh implementer instance.
The implementer receives only: the WU piece contract + FEEDBACK-NNN.md + named files.
It does NOT receive the full conversation history of prior iterations.
This prevents context pollution between loop iterations.

---

## FEEDBACK GENERATION

When tester or reviewer blocks, the dispatcher spawns a comment_generator agent:

  Input:  All REVIEW-NNN-L*.md and test failure reports for this WU
  Output: FEEDBACK-NNN.md

  FEEDBACK-NNN.md format:
  ```
  # FEEDBACK -- WU-NNN -- Iteration N
  
  ## Blocking Issues (must fix before done criteria can pass)
  
  ISSUE-01:
    Type:     test-failure | reviewer-layer-1 | reviewer-layer-2 | reviewer-layer-3
    Location: <file:line>
    Problem:  <what is wrong -- specific>
    Required: <exact change needed>
  
  ISSUE-02:
    ...
  
  ## Piece Contract Reminder
  <paste the done criteria from the WU piece contract>
  ```

---

## INTEGRATION VERIFICATION DISPATCH

After each batch of WUs completes, dispatcher runs:

  Agent:  tester_agent (integration scope)
  Tools:  run_integration_tests, collect_logs
  Input:  Integration verification steps from PLAN-NNN.md for this batch
  Output: Integration test result
  If fail: trigger debugger_agent + surface to human before next batch

---

## ADDITIONAL AGENTS

| Agent                   | When dispatched                          |
|-------------------------|------------------------------------------|
| debugger_agent          | On any Tier 1 failure or abnormality     |
| optimizer_agent         | In optimization mode                     |
| doc_writer_agent        | Phase 2 doc gaps, post-plan completion   |
| garbage_collector_agent | On gc_interval                           |

---

## DISPATCH FORMAT (all agent spawns)

TASK:            <one-line description>
PHASE:           research | plan | implement | test | review | feedback | integrate
AGENT:           <agent name>
WU:              <WU-NNN or "n/a">
TOOLS ALLOWED:   <explicit list -- never "all">
CONTEXT:         <only what this agent needs -- WU contract + named files>
EXPECTED OUTPUT: <exact artifact the agent must produce>
VALIDATION:      <done criteria for this agent's output>
DEPENDENCIES:    <WUs or phases that must complete first>
CONTEXT LIMIT:   40% -- write HANDOFF.md and spawn fresh instance if exceeded

---

## PARALLELISM RULES

- WUs with no dependencies in a batch: dispatch ITR groups in parallel
- WUs with dependencies: wait for dependency WUs to reach "done" first
- Maximum parallel ITR groups: CONFIG.yaml runtime.max_parallel_agents (default: 3)
- Each ITR group has fully isolated context -- no shared context windows between groups
