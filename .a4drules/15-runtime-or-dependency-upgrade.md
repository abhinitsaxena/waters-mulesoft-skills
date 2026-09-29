# Runtime or Dependency Upgrade — Rules

When upgrading Mule runtime, Java, Maven, parent POM, plugins, or connectors, follow these conventions:

- **Source of truth**: `pom.xml` and `mule-artifact.json` are the authoritative sources for current versions. Do not rely on versions stated anywhere else.
- **No drive-by bumps**: bump only the requested component. Do not upgrade other dependencies unless explicitly asked.
- **Compatibility cross-check**:
  - `app.runtime` must be compatible with `minMuleVersion` and the Java line (confirm CI JDK)
  - `mule.maven.plugin.version` must be compatible with the runtime
  - Parent POM must be compatible with managed connector dependencies
  - RAML asset version must be compatible with the APIKit version
- **`minMuleVersion` consistency**: keep `minMuleVersion` consistent with the new runtime line.
- **`javaSpecificationVersions` pin**: check `mule-artifact.json` for the Java specification version pin.
- **MUnit validation**: re-run MUnit after the bump — mocks must still bind, and tests need the real `mule.env` + decryption key.
- **Output**: provide a diff of `pom.xml` and (if changed) `mule-artifact.json`, plus a short compatibility note explaining why the chosen versions work together.
