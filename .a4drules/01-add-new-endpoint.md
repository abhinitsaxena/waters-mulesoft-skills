# Add New Endpoint — Rules

When adding a new HTTP/APIKit endpoint, always follow these conventions:

- **Spec first**: add the operation to the RAML asset and regenerate APIKit bindings — never hand-edit the `post:\…`/`get:\…` APIKit flows.
- **Mirror the existing pattern**: use async START + END `waters-util:logger` with `requestType`, clean distinct `flowName`, and `responseTime="true"` on END. Match the existing trigger's response-before-async-work model or deliberately deviate and document why.
- **No hardcoded hosts or paths**: build all downstream request URLs from env-YAML namespace properties using `${…}` or `${secure::…}`.
- **Correlation ID**: set `sendCorrelationId="ALWAYS"` on every new HTTP request configuration.
- **HTTP status and headers**: set `vars.httpStatus` / `vars.outboundHeaders` for non-200 / custom headers, preserving the APIKit response contract on the main flow.
- **Retries**: if the endpoint calls a downstream that needs resiliency, wrap idempotent calls in `until-successful` driven by env properties.
- **Env-YAML additions**: list all new env-YAML keys needed per environment — cleartext shape only (encryption is a separate step).
- **No MUnit in this scope**: endpoint creation does not include writing tests — that is a separate task.
