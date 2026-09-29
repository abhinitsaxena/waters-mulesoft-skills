# Tech Spec from Functional Spec — Rules

When generating a technical specification from a functional spec, follow these conventions:

- **No invented values**: never invent versions, hostnames, paths, or property names. All claims must be verified against the actual repository.
- **Open questions over assumptions**: when the functional spec is ambiguous, list items as **Open Questions** rather than silently resolving them.
- **All 13 sections present**: every tech spec must include: Context, Scope, High-level design, API/interface contract changes, External system interactions, Data transformations, Business rules, Error handling, Logging, Security, Test strategy, Deployment & config, and Open questions — even if a section is "N/A".
- **Knowledge-file sourcing**: derive technical context from the relevant `curie/knowledge/` files (architecture, external-systems, conventions, error-handling, logging, security, transformations, etc.).
- **Cross-check against repo**: verify every runtime, dependency, path, and namespace claim against the actual repo before including in the spec.
- **Convention references**: explicitly reference team conventions — logger in `<async>`, correlation-id, secure properties, trigger parity, `until-successful` posture.
- **Paste-ready**: output must be paste-ready for Confluence.
- **No tests in scope**: the tech spec describes test strategy at a high level but does not write actual tests.
