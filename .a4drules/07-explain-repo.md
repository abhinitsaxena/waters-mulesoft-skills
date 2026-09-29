# Explain Repo — Rules

When explaining or onboarding an engineer to a MuleSoft repository, follow these conventions:

- **Brevity**: output must be under one page — a new engineer should read it in approximately 3 minutes.
- **Structure**: always cover these five sections: what the repo does, how it runs (trigger surface and architectural constraints), which systems it connects to, day-one conventions, and where to look next.
- **Source from knowledge files**: derive the explanation from `curie/knowledge/architecture.md`, `curie/knowledge/external-systems.md`, `curie/knowledge/conventions.md`, and `curie/knowledge/employee-file-business-rules.md`.
- **Graceful degradation**: if a knowledge file is missing or cannot be read, continue with the available files and explicitly identify the missing source.
- **Next steps**: always point the engineer to `CURIE.md` and the knowledge files under `curie/knowledge/` for deeper exploration.
