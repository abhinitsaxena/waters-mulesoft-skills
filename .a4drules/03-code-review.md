# Code Review — Rules

When reviewing MuleSoft code, apply these standards:

- **Findings severity**: order all findings as blocker → high → medium → nit.
- **Cite and trace**: for each finding, cite file and line, state the issue, link the rule from the relevant knowledge file, and propose a concrete fix.
- **No style-only findings**: every finding must have a realistic failure path — no theoretical risks without a concrete scenario.
- **Knowledge-file tracing**: every finding must trace to a knowledge-file rule or clear convention.
- **Cross-reference known issues**: consult `curie/knowledge/known-issues.md` before flagging — do not re-litigate pre-existing known issues unless the PR claims to fix them.
- **Key checks**: verify logger in `<async>` with clean `flowName`/`flowStep`/`requestType`, `sendCorrelationId="ALWAYS"`, no hardcoded hosts/paths, trigger parity, `until-successful` on downstream calls, and whether edge cases (empty result, error records, exhausted retries) are handled.
- **Focus area awareness**: when reviewing by focus area (security, transformations, error-handling, best-practices), load the corresponding knowledge files.
