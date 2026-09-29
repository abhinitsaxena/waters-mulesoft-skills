# Change Impact Analysis — Rules

When assessing the impact of a proposed change, always check these dimensions:

- **Trigger parity**: all trigger entry points (HTTP endpoints, schedulers) funnel into the process flow — a change to process/flag handling usually touches all entries.
- **Transform parity**: related transform scripts (create, filter, error) must stay consistent with each other.
- **Pagination interaction**: the paged pull flow recurses on itself — page-size or skip changes affect the whole pull and retry behavior.
- **Downstream contract**: file-shape or column changes affect downstream system recipients — check `external-systems.md` and `related-repos.md` for who consumes the output.
- **Env-YAML fan-out**: any property namespace change must be applied across **all** env YAML files (local, dev, qa, stage, prod).
- **Repo-wide grep**: trace dependencies explicitly — grep the property/flow/field name across the entire repo. This is one of the few contexts where repo-wide search is appropriate.
- **Blast-radius ordering**: list affected items by certainty: definitely affected → possibly affected.
- **Cross-repo coordination**: identify when downstream system owners need to be notified of contract changes.
