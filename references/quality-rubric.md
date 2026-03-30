# Quality Grading Rubric

## Grade Scale

| Grade | Tests | Docs | Linting | Golden Principles | Description |
|-------|-------|------|---------|-------------------|-------------|
| **A** | ≥90% coverage, all pass | Architecture + domain docs current | Zero errors, zero warnings | Full compliance | Production-ready |
| **B** | ≥75% coverage, all pass | Architecture doc current | Zero errors | Minor violations (1-2) | Good, minor cleanup needed |
| **C** | ≥50% coverage | Partial docs | Errors exist | Several violations | Needs investment |
| **D** | <50% coverage | Outdated or missing | Many errors | Widespread violations | Technical debt |
| **F** | No tests | No docs | Not configured | Not enforced | Not harness-ready |

## Assessment Criteria

### Tests
- **Coverage:** Line + branch coverage percentage
- **Quality:** Do tests actually verify behavior or just assert types?
- **Maintenance:** Are tests flaky? Do they break on refactors?
- **Scope:** Unit + integration + (for APIs) contract tests

### Documentation
- **Architecture doc:** Up to date? Reflects actual code?
- **Domain doc:** Boundaries correct? Dependencies accurate?
- **AGENTS.md:** Concise? Points to deeper docs? Under 150 lines?
- **Plans:** Active plans tracked? Completed plans archived?

### Linting
- **Errors:** Zero tolerance
- **Warnings:** Should be addressed within sprint
- **Custom linters:** Domain-specific rules enforced?
- **Structural tests:** Architecture constraints verified?

### Golden Principles
- Count violations (custom linter output)
- Severity: critical (data safety) vs cosmetic (naming)
- Trend: improving, stable, or deteriorating?

## How to Grade a Domain

1. Run test suite, capture coverage
2. Check if docs exist and match code (spot-check file paths, package names)
3. Run linter, count errors/warnings
4. Run structural tests if configured
5. Check golden principle compliance
6. Assign grade based on rubric above
7. Note specific gaps for the quality.md tracking

## Quality Report Format

```markdown
## {Domain Name}
- **Grade:** B
- **Test Coverage:** 78% (unit), 45% (integration)
- **Docs:** Architecture current, domain doc missing edge cases
- **Linting:** 0 errors, 3 warnings
- **Golden Principles:** 2 violations (file size in service.py, print in util.py)
- **Gaps:**
  - [ ] Add integration tests for auth flow
  - [ ] Update domain doc with new Provider interface
  - [ ] Split service.py (420 lines)
- **Last Assessed:** YYYY-MM-DD
```
