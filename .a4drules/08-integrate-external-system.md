# Integrate External System — Rules

When integrating with a new external system, enforce these standards:

- **Property namespace**: define a namespace following the existing pattern (e.g., `http.<system>.*` with `host`, `port`, `basePath`, `<verb>.path`; or `<system>.sftp.*` for SFTP). Never hardcode hosts, ports, or paths.
- **Request configuration**: add an HTTP request config or SFTP config in the global config file mirroring an existing one — include timeout from a property, default auth headers or SSH identity+passphrase, and `sendCorrelationId="ALWAYS"` for HTTP.
- **TLS posture**: check whether strict trust is required vs. the current `insecure="true"` trust-store posture. Flag if the existing posture does not meet security requirements for the new system.
- **Resiliency**: wrap idempotent calls in `until-successful` driven by env properties. Consider whether the latent circuit breaker (`cb.*`) should be wired for this system.
- **Error policy**: decide how a downstream failure maps given the app's async model. Document the intended behavior.
- **Env-YAML additions**: list all new property keys needed for **every** environment (local, dev, qa, stage, prod). Provide cleartext shape — encryption is a separate step.
- **Credentials**: all auth credentials must use `${secure::…}` with encrypted values in env-YAML.
