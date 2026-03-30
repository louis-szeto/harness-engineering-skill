# MEMORY SYSTEM

This file is the live memory store for the harness runtime. It is updated after every cycle.
New entries are prepended so the most recent information appears first.

---

## MEMORY TYPES

### 1. EPISODIC MEMORY
Past failures, bug patterns, and regressions encountered during execution.

### 2. SEMANTIC MEMORY
Architecture decisions, design patterns, and constraints derived from experience.

### 3. PROCEDURAL MEMORY
Successful execution strategies and optimization techniques worth repeating.

---

## WRITE FORMAT

Every entry must use this exact structure:

```
---
[TYPE: EPISODIC | SEMANTIC | PROCEDURAL]
Timestamp: YYYY-MM-DD HH:MM
Context:   <what was being attempted>
Failure:   <what went wrong, or "n/a" for procedural wins>
Root Cause: <why it happened>
Fix:       <what was done>
Prevention Rule: <constraint or test added to prevent recurrence>
Tools Used: <list of tools involved>
---
```

---

## RULES

- Every failure MUST produce an entry.
- Every fix MUST reference a memory entry ID.
- Every entry where `Failure` appears 2+ times MUST produce a `Prevention Rule`.
- Memory must be searchable (use consistent keywords in `Context` and `Failure` fields).

---

## ENTRIES

<!-- New entries go here, prepended above older ones -->
