# Logging Change — Rules

When adding or modifying logging, always follow these conventions:

- **Logger component**: always use `waters-util:logger` with `config-ref="Waters_Utility_Connector_Config"`. Never use the standard Mule `<logger>` component.
- **Async wrapping**: every `waters-util:logger` call must be wrapped in `<async>`. No exceptions.
- **Required attributes**:
  - `requestType` — `"HTTP"` for HTTP-triggered flows, `#[vars.requestType]` for schedulers/polls (never a literal `"POLL"` or `"vars.requestType"` string)
  - `flowName` — clean, distinct name uniquely identifying the flow
  - `flowStep` — one of: `START`, `MIDDLE`, `END`, `ERROR`
  - `eventName` — short descriptive label for the event
  - `message` — human-readable log message
- **END loggers**: must set `responseTime="true"` to capture elapsed time.
- **Additional parameters**: populate `additionalParams` with relevant business identifiers (order IDs, customer IDs, etc.).
- **No secrets**: never log secrets, decrypted `secure::` values, passwords, API keys, or PGP keys.
- **Known typos to fix**: `flowStep="STAR"` must be `"START"`; literal `requestType="POLL"` or `requestType="vars.requestType"` must be the DataWeave expression `#[vars.requestType]`.
- **MUnit stability**: if a MUnit `verify-call` references a logger by `doc:id`, keep the `doc:id` stable or update the corresponding test.
