# Agent Prompt Templates

Templates for spawning Claude Code / Codex agents in harness engineering workflows.

## Template 1: Feature Implementation

```
You are working on {project} following harness engineering principles.

## Context
Read these files first for project context:
- AGENTS.md — project overview, conventions, invariants
- docs/architecture.md — domain structure, design decisions
- docs/domains.md — dependency rules for your domain
- docs/golden-principles.md — code quality rules

## Your Sprint Contract
**Feature:** {feature name}
**Domain:** {domain}
**Acceptance Criteria:**
- [ ] {criterion 1}
- [ ] {criterion 2}
- [ ] All existing tests still pass
- [ ] New tests cover the feature at ≥90% line coverage
- [ ] No lint errors
- [ ] Code follows golden principles

## Implementation Instructions
1. Read the context files listed above
2. Web search for best practices on {specific technology/pattern}
3. Implement the feature following the architectural constraints
4. Write tests FIRST (TDD preferred) or alongside the code
5. Run tests: {test command}
6. If tests fail: read output → analyze root cause → fix → re-run
7. Self-review against golden principles
8. Fix any violations
9. Repeat steps 5-8 until convergence
10. Run linter: {lint command}
11. Commit with message: "feat({domain}): {description}"

## Verification
After all tests pass, verify manually if applicable:
- {manual verification steps}

When completely finished, run: openclaw system event --text "Done: {summary}" --mode now
```

## Template 2: Test Writing

```
You are working on {project} following harness engineering principles.

## Context
Read AGENTS.md and docs/architecture.md for project context.

## Your Sprint Contract
**Task:** Write comprehensive tests for {module/package}
**Target:** {file or directory}
**Coverage Goal:** ≥{X}%

## Instructions
1. Read the source code in {target}
2. Web search for testing best practices for {language/framework}
3. Identify all public APIs, edge cases, error paths
4. Write tests covering:
   - Happy paths
   - Edge cases (empty input, null, boundary values)
   - Error paths (exceptions, invalid input)
   - Integration with dependencies (mocked)
5. Run tests: {test command}
6. For each failure:
   a. Is the test wrong or the code wrong?
   b. If code is wrong, note it but don't fix it (separate task)
   c. If test is wrong, fix the test
   d. Re-run
7. Check coverage: {coverage command}
8. If below goal, identify untested paths and add tests
9. Repeat until coverage goal met
10. Run linter
11. Commit: "test({module}): comprehensive test coverage"

When completely finished, run: openclaw system event --text "Done: wrote N tests for {module}, coverage {X}%" --mode now
```

## Template 3: Documentation Generation

```
You are working on {project} following harness engineering principles.

## Context
This project needs its documentation transformed into harness engineering format.

## Your Sprint Contract
**Task:** Generate {specific doc} for {project}
**Output:** {path}

## Instructions
1. Read the existing codebase to understand:
   - Project structure and domains
   - Dependencies and architecture
   - Existing conventions and patterns
2. Web search for best practices on {doc type} for {tech stack}
3. Analyze the code to ensure documentation matches reality
4. Write the documentation following harness engineering format:
   - Concise, actionable, agent-consumable
   - Tables for structured info (not prose)
   - Pointers to deeper sources (not walls of text)
   - Include verification status where applicable
5. Cross-reference against actual code (check that file paths, package names, etc. are correct)
6. Run any validation if available

When completely finished, run: openclaw system event --text "Done: generated {doc} for {project}" --mode now
```

## Template 4: Refactoring / Golden Principles Compliance

```
You are working on {project} following harness engineering principles.

## Context
Read AGENTS.md and docs/golden-principles.md for the rules.

## Your Sprint Contract
**Task:** Refactor {target} to comply with golden principles
**Violations Found:**
- {violation 1}
- {violation 2}

## Instructions
1. Read the golden principles
2. Read the target code
3. For each violation:
   a. Understand what the principle requires
   b. Web search for best practice implementation
   c. Refactor to comply
   d. Write/update tests
4. Run tests: {test command}
5. If any test breaks from refactoring:
   a. Analyze if the test needs updating (behavior preserved) or code is wrong
   b. Fix and re-run
6. Run linter
7. Commit: "refactor({domain}): comply with golden principle {N}"

When completely finished, run: openclaw system event --text "Done: refactored {target}, resolved N violations" --mode now
```

## Template 5: Bug Fix with Recursive Verification

```
You are working on {project} following harness engineering principles.

## Context
Read AGENTS.md for project context.

## Your Sprint Contract
**Bug:** {description}
**Reproduction:** {steps to reproduce}
**Expected:** {expected behavior}
**Actual:** {actual behavior}

## Instructions
1. Read AGENTS.md and relevant docs
2. Reproduce the bug: {reproduction steps}
3. Web search for known issues / solutions related to {bug type}
4. Identify root cause (read code, logs, stack traces)
5. Write a failing test that captures the bug
6. Fix the bug
7. Run tests — verify the new test passes AND all existing tests pass
8. If any existing test breaks:
   a. Analyze: did the fix change behavior unintentionally?
   b. If yes, adjust the fix
   c. If no, update the test to match correct behavior
   d. Re-run all tests
9. Self-review: does the fix follow golden principles?
10. If not, refactor and re-run tests
11. Commit: "fix({domain}): {description}"

When completely finished, run: openclaw system event --text "Done: fixed {bug} in {module}" --mode now
```
