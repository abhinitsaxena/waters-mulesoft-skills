# Debug Issue — Rules

When debugging a failing flow or production issue, follow these conventions:

- **Hypotheses first**: form 2–3 hypotheses *before* reading code. Do not jump straight into the codebase.
- **Known issues first**: match the error signature against `curie/knowledge/known-issues.md` and `curie/knowledge/error-handling.md` before investigating novel causes.
- **Common categories to check**:
  - Encryption/decryption errors → env key configuration
  - Job triggered but nothing delivered → fire-and-forget + swallowed downstream failure
  - Paged pull hangs / partial data → recursive pagination + retry interaction
  - File delivery fails → per-env credential/key/host mismatch
  - Records unexpectedly dropped → mandatory-field or filter logic
  - Observability grouping odd → logger convention issues
  - Connectivity fails in one env only → host/port differs per env YAML
- **Correlation ID tracing**: use the correlation ID to trace the request across flows and systems.
- **Targeted reading**: read only the files the hypotheses point at — do not scan the entire repo.
- **Three-strike rule**: if 3 fix attempts have already failed, stop iterating — summarize what was tried, propose a different angle or manual verification.
- **Concrete fixes**: every fix proposal must point at a real file and line, not a guess.
