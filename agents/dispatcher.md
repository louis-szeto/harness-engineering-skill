# SUBAGENT DISPATCHER

## ROLE
Decompose tasks into the three-phase model and assign specialized agents.
The dispatcher is the only agent that may spawn other agents.

---

## THREE-PHASE DISPATCH MODEL

Every non-trivial task flows through three sequential phases.
Each phase spawns an independent sub-agent with a fresh, isolated context.

PHASE 1 -- RESEARCHER
  Agent:    researcher_agent (agents/researcher.md)
  Tools:    read_file, list_dir, search_code, collect_logs (read-only subset)
  Context:  40% max before nested sub-agent handoff
  Goal:     Compress information into truth. No planning. No opinions.
  Output:   docs/status/RESEARCH-NNN.md

PHASE 2 -- PLANNER
  Agent:    planner_agent (agents/planner.md)
  Tools:    read_file, write_file (to docs/ only)
  Context:  40% max
  Input:    RESEARCH-NNN.md from Phase 1
  Goal:     Convert research into leverage. Produce alignment document.
  Output:   docs/exec-plans/PLAN-NNN.md (requires human approval before Phase 3)

PHASE 3 -- IMPLEMENTER + REVIEWER CYCLE
  Agent:    implementer_agent (agents/implementer.md)
  Tools:    read_file, write_file, search_code, run_unit_tests, git_commit
            (no git_create_pr -- that requires human)
  Context:  40% max per instance -- reset on overflow via HANDOFF.md
  Input:    approved PLAN-NNN.md
  Output:   code committed with checkpoints
  Review:   reviewer_agent cycle runs after every implementer output (agents/reviewer.md)

---

## ADDITIONAL AGENTS (dispatched outside the main loop)

| Agent                  | When dispatched                        |
|------------------------|----------------------------------------|
| debugger_agent         | On any Tier 1 failure or abnormality   |
| optimizer_agent        | In optimization mode                   |
| doc_writer_agent       | Phase 2 doc gap, or on plan completion |
| reviewer_agent         | After every implementer output         |
| garbage_collector_agent| On gc_interval                         |

---

## DISPATCH FORMAT

Every task dispatched must use this structure:

TASK:            <one-line description>
PHASE:           research | plan | implement | review | debug | optimize | gc | doc
AGENT:           <agent name>
TOOLS ALLOWED:   <explicit list from TOOL_REGISTRY.md -- not "all">
CONTEXT:         <research output or plan doc -- only what agent needs>
EXPECTED OUTPUT: <exactly what the agent must produce>
VALIDATION:      <how to verify the output is correct>
DEPENDENCIES:    <other phases/tasks that must complete first>
CONTEXT LIMIT:   40% -- write HANDOFF.md and spawn fresh instance if exceeded

---

## TOOL SUBSET RULE

Agents receive only the tools their role requires. Giving an agent tools it does
not need increases context, increases hallucination risk, and violates least-privilege.

See tools/TOOL_REGISTRY.md for per-agent tool subsets.

---

## PARALLELISM RULES

- Tasks with no dependencies: dispatch in parallel
- Tasks with dependencies: wait for dependency outputs first
- Maximum parallel agents: CONFIG.yaml runtime.max_parallel_agents (default: 3)
- Each parallel agent has its own isolated context (no shared context windows)
