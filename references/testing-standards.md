# TESTING STANDARDS

## NON-NEGOTIABLE
Every feature ships with:
- **Unit tests** — ≥90% coverage of changed code
- **Integration tests** — at least one per major code path
- **E2E tests** — at least one per user-facing behavior

## RECOMMENDED (but required for critical paths)
- **Fuzz tests** — for all input parsing, serialization, and external data ingestion
- **Property-based tests** — for algorithms with invariants
- **Chaos tests** — for distributed components and retry logic

---

## TEST QUALITY RULES
- Tests must be deterministic — no random failures, no time-dependent assertions.
- Tests must be isolated — no shared mutable state between tests.
- Tests must be fast — unit tests < 100ms each; slow tests belong in integration suite.
- Tests must be meaningful — a test that always passes regardless of code is worse than no test.

---

## COVERAGE RULE
Minimum: **90% line coverage** on all changed files (set in `CONFIG.yaml`).
Coverage alone is not sufficient — tests must assert correct behavior, not just execute lines.

---

## FAILURE PROTOCOL
When tests fail:
1. `collect_logs()` immediately.
2. Identify the smallest failing case.
3. Dispatch `debugger_agent` with the failure log and test output.
4. Do NOT merge. Do NOT proceed to the next cycle task.

---

## OUTPUT FORMAT (required from tester_agent)
```json
{
  "passed": 0,
  "failed": 0,
  "skipped": 0,
  "coverage": "0%",
  "failures": [
    {
      "test": "test_name",
      "file": "tests/path.test.ts",
      "reason": "AssertionError: expected X, got Y",
      "log_path": "docs/generated/tool-logs/run-NNN.json"
    }
  ]
}
```
