# TOOL ROUTER

## PURPOSE
Centralized control point for all tool usage. Every tool call passes through here.

---

## FLOW
```
Agent → Tool Request → Tool Router → Validate → Execute → Normalize Output → Return → Agent Validates
```

---

## RESPONSIBILITIES
1. **Validate inputs** — reject malformed or dangerous requests before execution.
2. **Route** to the correct tool implementation.
3. **Normalize outputs** — all results returned in standard format (see below).
4. **Enforce safety constraints** — block destructive commands without approval.
5. **Log every call** — write to `docs/generated/tool-logs/`.

---

## SAFETY RULES
- Block any destructive command (`write_file` on a non-draft path, `git_commit` without tests passing, server restarts in production) unless explicitly approved.
- Prevent infinite loops: if the same tool is called with the same input 3+ times in a row, halt and log.
- Enforce rate limits: no more than 10 tool calls per minute per agent.
- Never execute shell commands that were not pre-registered in `TOOL_REGISTRY.md`.

---

## STANDARD OUTPUT FORMAT

```json
{
  "status": "success | failure | blocked",
  "tool": "<tool name>",
  "input": { "<key>": "<value>" },
  "data": "<result payload>",
  "errors": "<error message or null>",
  "timestamp": "<ISO 8601>",
  "log_path": "docs/generated/tool-logs/<id>.json"
}
```

---

## BLOCKED ACTIONS (require explicit human approval)
- Deleting files or directories
- Dropping databases or migrations
- Merging to main/trunk directly (PR required)
- Disabling security scans
- Modifying `CONFIG.yaml` runtime limits
