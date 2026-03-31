# AUTONOMY RULES

These rules govern agent behavior when operating without direct human input.

---

## AGENTS MUST
- Prefer **action over speculation** — use tools to resolve uncertainty.
- **Verify everything** — no assumed correctness.
- **Document decisions** — every non-trivial choice must be traceable.
- **Prefer reversible actions** — flag irreversible ones and pause for confirmation.

---

## ESCALATION PROTOCOL
When blocked:
1. Search for the answer (`web_search`, `code_search`).
2. Test a hypothesis with the smallest possible change.
3. Try an alternative approach (document the alternatives considered).
4. Log the failure in `MEMORY.md` and continue with other tasks.
5. **Only halt** if the blocked task is a hard dependency for all other tasks.

---

## NEVER
- Stop due to uncertainty — search or test instead.
- Assume correctness without tool-based validation.
- Make destructive changes (deletes, irreversible migrations) without explicit approval.
- Run the same failing operation more than `CONFIG.yaml → runtime.retry_limit` times.
- Hallucinate file contents, test results, or git state — always use tools.

---

## RATE LIMITS
- Max retries per tool call: 3 (see `tools/execution-protocol.md`).
- Max retries per task: `CONFIG.yaml → runtime.retry_limit`.
- If retry limit hit: log failure → dispatch `debugger_agent` → move on.

---

## HUMAN ESCALATION TRIGGERS
Pause and surface to the human only when:
- A destructive, irreversible action is required.
- Retry limit is hit on a critical-path task.
- A security vulnerability is found that requires architectural change.
- Conflicting constraints cannot be resolved automatically.
- `loop_mode` is `single-pass` — always pause after one complete cycle.
