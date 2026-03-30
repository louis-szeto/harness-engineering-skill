# TOOL REGISTRY

All tool calls flow through the Tool Router (see `tools/tool-router.md`).
Agents NEVER call tools directly — they submit a Tool Request.

---

## RULES
- Tools must be deterministic where possible.
- All tool outputs must be machine-readable (JSON preferred).
- Every tool call must be logged to `docs/generated/tool-logs/`.
- NEVER trust tool output blindly — always validate.
- Retry up to 3 times on failure before escalating.

---

## AVAILABLE TOOLS

### FILESYSTEM
| Tool | Signature | Notes |
|------|-----------|-------|
| Read file | `read_file(path)` | Returns file contents |
| Write file | `write_file(path, content)` | Creates or overwrites |
| List directory | `list_dir(path)` | Returns file tree |
| Search code | `search_code(query)` | Regex/semantic search across repo |

### GIT
| Tool | Signature | Notes |
|------|-----------|-------|
| Status | `git_status()` | Staged, unstaged, untracked |
| Diff | `git_diff()` | Current changes vs HEAD |
| Checkout | `git_checkout(branch)` | Switch or create branch |
| Commit | `git_commit(message)` | Must include plan reference in message |
| Create PR | `git_create_pr()` | Blocked without tests + docs |

### TESTING
| Tool | Signature | Notes |
|------|-----------|-------|
| Unit tests | `run_unit_tests()` | Returns structured JSON result |
| Integration tests | `run_integration_tests()` | |
| E2E tests | `run_e2e_tests()` | |
| Fuzz tests | `run_fuzz_tests()` | Recommended, not required |

### CI/CD
| Tool | Signature | Notes |
|------|-----------|-------|
| Trigger pipeline | `trigger_pipeline()` | Starts CI run |
| Pipeline status | `get_pipeline_status()` | Polls until complete |

### SEARCH
| Tool | Signature | Notes |
|------|-----------|-------|
| Web search | `web_search(query)` | External knowledge acquisition |
| Code search | `code_search(query)` | Cross-repo symbol search |
| Dependency lookup | `dependency_lookup(package)` | Versions, vulnerabilities |

### RUNTIME
| Tool | Signature | Notes |
|------|-----------|-------|
| Start server | `start_server()` | |
| Stop server | `stop_server()` | |
| Call API | `call_api(endpoint, method, body)` | |

### SECURITY
| Tool | Signature | Notes |
|------|-----------|-------|
| Vulnerability scan | `scan_vulnerabilities()` | SAST + dependency scan |
| Dependency audit | `dependency_audit()` | Check for known CVEs |

### METRICS
| Tool | Signature | Notes |
|------|-----------|-------|
| Collect logs | `collect_logs()` | Aggregates runtime logs |
| Performance profile | `performance_profile()` | CPU, memory, latency |
