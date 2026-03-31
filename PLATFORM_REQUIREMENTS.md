# PLATFORM REQUIREMENTS

This skill is instruction-only. It assumes the host environment (Claude Code, OpenClaw,
or equivalent) provides and enforces the following capabilities. Verify each item before
running the harness on a real repository.

---

## REQUIRED PLATFORM CAPABILITIES

### 1. MCP Tool Router
The platform must route all tool calls through a central router that:
  - Blocks tools not in the agent's assigned subset (see references/mcp-tools.md)
  - Blocks writes to protected paths (SKILL.md, CONFIG.yaml, agents/**, runtime/**, tools/**)
  - Redacts credential-shaped values from tool outputs before they reach agent context
  - Logs tool call metadata (not payloads) to docs/generated/tool-logs/
  - Enforces retry limits (CONFIG.yaml tools.max_retries)

Verify: Can you confirm that write_file to runtime/ is blocked for agent calls?
If not: restrict file-system access at the OS or container level before use.

### 2. Sandboxed Test Execution
Generated code (tests, linters, pre-commit hooks) must run in an isolated sandbox that:
  - Has NO access to the harness environment variables or credentials
  - Has NO network path to the harness security context
  - Is ephemeral: each execution starts clean
  - Returns results only through structured output (not environment mutation)

Verify: Does your sandbox enforce separate compute contexts for harness vs. generated code?
If not: run all test execution in a container with no access to host env vars.
Reference: Vercel security boundary model (harness compute separate from sandbox compute).

### 3. Git Permission Scoping
The agent's git credentials must be scoped to:
  - Create branches (yes)
  - Create PRs (yes)
  - Commit to feature branches (yes)
  - Push directly to main/trunk (NO -- branch protection must block this)
  - Access repositories outside the current project (NO)

Verify: Is main/trunk protected with required human reviewers in your git host?
If not: configure branch protection before enabling the harness.

### 4. Web Search Staging
If the platform provides web_search, it must:
  - Write results only to docs/generated/search-staging/ (not docs/references/ directly)
  - Require a human to promote staged content to docs/references/
  
Verify: Is your web_search output routed through the staging directory?
If not: disable web_search in the agent's tool subset until staging is enforced.

### 5. Human Approval Infrastructure
The platform must support surfacing output to a human and waiting for acknowledgment at:
  - on-start (session acknowledgment)
  - on-plan-complete (plan approval gate)
  - on-cycle-complete / single-pass halt
  - on-error retry exhaustion

Verify: Does your environment support human-in-the-loop gates in the agent loop?
If not: run in single-pass mode only and check outputs manually between cycles.

---

## RECOMMENDED MONITORING

Once the harness is running, monitor these over time:

| What to watch              | Where to look                         | Why                                    |
|----------------------------|---------------------------------------|----------------------------------------|
| Appended prevention rules  | references/constraints.md             | Rules accumulate -- review periodically|
| Created docs and tests     | docs/, tests/                         | Harness writes here autonomously       |
| Search staging directory   | docs/generated/search-staging/        | Promote or discard staged findings     |
| Cycle summaries            | docs/status/CYCLE-NNN.md             | Track harness health over time         |
| Harness improvements       | docs/harness-improvements/            | Review before each skill update        |

---

## CONFIDENCE NOTE

The harness is internally coherent and all security rules are explicit in the skill files.
Safety in practice depends on this platform checklist being satisfied.
Run the SAFE START sequence (SKILL.md) regardless of platform confidence level.
