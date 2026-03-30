# SELF-IMPROVEMENT ENGINE

Every failure is an opportunity to make the system better. This is not optional.

---

## THE RULE
Every failure must produce **at least one** of:
- A new **constraint** in `references/constraints.md`
- A new **test** in `tests/`
- A new **documentation** entry in `docs/`

A failure that produces none of these is an incomplete cycle.

---

## IMPROVEMENT PROCESS

```
failure
  → root cause analysis (debugger_agent)
  → identify system gap (missing constraint, test, or doc)
  → create fix (implementer_agent / doc_writer_agent)
  → verify fix eliminates failure
  → update MEMORY.md with Prevention Rule
  → re-deploy
```

---

## IMPROVEMENT TRIGGERS

| Signal | Action |
|--------|--------|
| Test failure | New regression test + constraint |
| Security scan finding | New security rule in `references/constraints.md` |
| Performance regression | New benchmark baseline + optimization note |
| Doc gap discovered | Immediate doc update before next cycle |
| Same failure twice | Mandatory Prevention Rule |

---

## RESULT
Over time, agents accumulate constraints and tests that prevent classes of errors entirely.
The system's regression rate asymptotically approaches zero.
Quality improves continuously. "Done" is not a state — it is a direction.
