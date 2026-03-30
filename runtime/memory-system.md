# MEMORY SYSTEM (RUNTIME)

See `MEMORY.md` for the live memory store. This file describes how to use it.

---

## READ PROTOCOL
At the start of every cycle, load `MEMORY.md` and scan for:
- Entries matching the current task domain (search by `Context` keyword).
- Prevention Rules that apply to the current work.
- Recurrence patterns (same failure appearing 2+ times).

## WRITE PROTOCOL
After every failure, write an entry to `MEMORY.md` using the format defined there.
Prepend — newest entries first.

---

## TOOL-AWARE MEMORY
Every tool interaction that produces a non-trivial result must be recorded:

```
Tool:    <tool name>
Input:   <what was passed>
Output:  <what was returned (summary)>
Result:  success | failure
Lesson:  <what this taught the system>
```

Store tool interaction records in `docs/generated/tool-logs/`.

---

## PATTERN DETECTION
If the same failure appears in 2 or more EPISODIC entries:
1. Create a Prevention Rule in `references/constraints.md`.
2. Create a new test in `tests/` that would have caught it.
3. Update `MEMORY.md` entry with `Prevention Rule:` filled in.

---

## MEMORY QUERY PATTERN
Before starting any non-trivial task, ask:
> "Has this failed before? Search MEMORY.md for keywords from this task's `Context`."

If a match is found, apply the Prevention Rule before proceeding.
