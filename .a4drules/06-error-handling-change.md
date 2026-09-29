# Error Handling Change — Rules

When modifying error handlers, always follow these conventions:

- **Prefer shared handlers**: extend the `waters-common-error-handler` or the existing process-flow-level `<error-handler>`. Add a scoped handler only if the behavior is flow-specific.
- **No empty blocks**: every `<on-error-continue>` and `<on-error-propagate>` must contain meaningful logic (logging, status mapping, or recovery).
- **APIKit response contract**: always set `vars.httpStatus` / `vars.outboundHeaders` to preserve the response contract on the main flow.
- **Error logging**: log every error path using `waters-util:logger` with `flowStep="ERROR"` wrapped in `<async>`.
- **Continue vs propagate**: be deliberate — `on-error-continue` when the flow can recover or a fallback is acceptable; `on-error-propagate` when the error must bubble up to the caller. Always justify the choice.
- **Async model awareness**: in fire-and-forget flows, the process-flow error handler affects logs and downstream side-effects — it does **not** change the caller's HTTP response. If the caller must see failures, that is a design change that must be explicitly called out.
- **Known issues**: check `curie/knowledge/known-issues.md` for known behavior around swallowed failures before changing continue/propagate.
