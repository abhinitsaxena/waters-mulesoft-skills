# Refresh CURIE.md — Rules

When refreshing CURIE.md and knowledge files, follow these conventions:

- **Volatile vs durable sections**: only update volatile sections. Never edit durable sections unless a real architectural decision changed.
- **Volatile sections include**: runtime-and-build versions, external-systems property namespaces, architecture flow/global-config inventory, transformation list, employee-file business rules, resiliency state, integration-test-cases, and testing file lists.
- **Source of truth**: re-read `pom.xml`, `mule-artifact.json`, `src/main/mule/**`, `src/main/resources/env/*.yaml`, and `src/main/resources/dwl/**` before updating.
- **Known issues maintenance**: update `curie/knowledge/known-issues.md` if an issue was fixed or a new one discovered.
- **CURIE.md re-rendering**: only re-render if section 2, 4, or 5 need new rows.
- **New extras tagging**: new extra files must be tagged `(extra)` in `curie/CURIE.md`, listed in `curie/README.md`, and added to the maintenance list — without modifying the body of existing knowledge/prompt files.
- **Integration-test layer**: keep `integration-test-cases.md` and `curie/testData/` in sync with runnable envs and test case catalog.
- **Changelog**: add a short changelog line (`Last refreshed: <date>`) at the end of each updated file.
