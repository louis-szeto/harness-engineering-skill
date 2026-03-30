# Golden Principles Catalog

Common principles to enforce in agent-generated codebases. Select and adapt for each project.

## Universal Principles

### 1. Parse, Don't Validate
Validate data shapes at system boundaries (APIs, file I/O, DB reads). Internal code trusts typed data.

### 2. Shared Utilities Over Hand-Rolled
Prefer centralized, well-tested utility packages over inline helper functions scattered across modules.

### 3. No YOLO Data Access
Never guess data shapes. Use typed SDKs, schemas, or validation at boundaries.

### 4. Structured Logging Only
- No `print()` statements
- No f-string logging
- Use structured loggers with context (request ID, user ID, trace ID)
- Enforced by linter

### 5. Explicit Error Handling
- No bare `except:` or empty catch blocks
- Errors should carry context about what failed and why
- Use domain-specific error types, not generic exceptions

### 6. File Size Limits
Files exceeding {N} lines should be split. Default: 300 lines for logic files, 500 for test files.

### 7. Naming Conventions
- Schemas/types: `{entity}_{use}` (e.g., `user_create`, `order_response`)
- Tests: `test_{unit}_{scenario}_{expected}` (e.g., `test_auth_invalid_token_returns_401`)
- Constants: UPPER_SNAKE_CASE
- No abbreviations except well-known ones (URL, ID, API)

### 8. Dependency Direction
Within each domain, code flows in one direction through layers:
```
Types → Config → Repo → Service → Runtime → UI
```
Reverse dependencies are forbidden. Enforced by structural tests.

### 9. No Circular Dependencies
Detected and blocked by linter. Use dependency injection or event systems to break cycles.

### 10. Tests as Contracts
Tests define behavior. If a test needs updating after a refactor, question whether the refactor changed behavior.

## Domain-Specific Principles (Add Per Project)

### Frontend
- Component files: one component per file
- No inline styles (use design tokens)
- Accessible by default (ARIA labels, keyboard nav)

### Backend/API
- All endpoints have input validation
- Response shapes are typed and documented
- Idempotency for mutating endpoints

### Data/ML
- All model inputs are validated before inference
- Training data lineage is tracked
- Model versions are immutable once deployed

### Infrastructure
- Infrastructure as code (no manual changes)
- Secrets from vault, never in code
- All resources tagged for cost tracking
