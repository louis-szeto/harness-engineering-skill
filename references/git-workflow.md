# GIT WORKFLOW

## STRATEGY: TRUNK-BASED DEVELOPMENT
- One main branch (`main` or `trunk`).
- Short-lived feature branches only — max lifetime: `CONFIG.yaml → git.branch_max_lifetime`.
- No long-running feature branches. Decompose large features into shippable increments.

---

## PR REQUIREMENTS (every PR must include)
- [ ] Reference to PLAN-NNN.md in the PR description
- [ ] All tests passing (`run_unit_tests`, `run_integration_tests`, `run_e2e_tests`)
- [ ] Docs updated (spec, README, API docs as applicable)
- [ ] Security scan clean (`scan_vulnerabilities`)
- [ ] No coverage regression (≥90% on changed files)
- [ ] Reviewer agent approval

---

## COMMIT MESSAGE FORMAT
```
<type>(<scope>): <short description>

Plan: PLAN-NNN.md
Tests: pass | <count> failing
Coverage: <percent>%
```

Types: `feat` | `fix` | `docs` | `refactor` | `test` | `chore` | `security`

---

## AUTO-MERGE RULES
PRs merge automatically when (`CONFIG.yaml → git.auto_merge_on_green`):
1. All CI checks pass.
2. Reviewer agent has approved.
3. No blocking comments.

---

## FORBIDDEN ACTIONS
- Direct commits to `main`/`trunk` (use PRs).
- Merging with failing tests.
- Merging without a docs update.
- Force-pushing to shared branches.
- Bypassing the reviewer agent.
