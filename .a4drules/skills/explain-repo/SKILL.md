---
name: explain-repo
description: >
  Explain and onboard engineers to this MuleSoft repository.
  Use when the user asks "what does this repo do", "give me a tour",
  "onboard me", or asks to understand the repository architecture,
  integrations, systems, conventions, or business rules.
---

# explain-repo1

Generate a concise explanation of the repository on what it does.
The following files are located in the MuleSoft repository and should be
read when executing this skill:

- `curie/knowledge/architecture.md`
- `curie/knowledge/external-systems.md`
- `curie/knowledge/conventions.md`
- `curie/knowledge/employee-file-business-rules.md`

If a file is missing or cannot be read, continue with the available files and explicitly identify the missing source.


## Usage

Run this skill when the user asks any of the following:

* "What does this repo do?"
* "Give me a tour"
* "Onboard me"

Also trigger when the user expresses an equivalent intent, such as "Explain this project" or "Help me understand this repository."

## Steps

1. **Explain the purpose in one paragraph.**
   Read `curie/knowledge/architecture.md` and describe the app's purpose, domain, and the
   high-level integration it performs.

2. **Explain the trigger surface.**
   From `curie/knowledge/architecture.md`, describe how the app is triggered (HTTP endpoints,
   schedulers) and key architectural constraints (async model, runtime modules present or absent).

3. **Explain the systems involved.**
   From `curie/knowledge/external-systems.md`, list all downstream and upstream systems and what
   role each plays.

4. **Highlight day-one conventions.**
   From `curie/knowledge/conventions.md`, summarize the 4–5 most important conventions a new
   engineer must know immediately.

5. **Point to the next steps.**
   Direct the engineer to `CURIE.md` and the knowledge files under `curie/knowledge/` for deeper
   exploration of architecture, business rules, and conventions.

## Output Format

Return **one short Markdown document, under one page**.

Use this structure:

```markdown
# Repository Tour

## What this repo does
<One concise paragraph from curie/knowledge/architecture.md.>

## How it runs
- <Trigger surface and scheduler from architecture.md>
- <Key architectural constraints from architecture.md>

## Systems
- <Upstream and downstream systems from external-systems.md>

## Day-one conventions
- <Key conventions from conventions.md>

## Where to look next
- <Pointers to curie/knowledge/ files and CURIE.md>
```

## Success Criteria

A new engineer should be able to read the response in approximately **3 minutes** and understand:

* What the repo does.
* How the integration is triggered.
* Which systems it connects to.
* Where the important business rules and conventions live.
* What to read next.
* How to find follow-up onboarding guidance.
