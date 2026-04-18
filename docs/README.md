# Documentation Hub

This folder contains the deep-dive analysis of the [vercel-labs/skills](https://github.com/vercel-labs/skills) supported-agents matrix — **focused on the harnesses themselves**, not just the skills they share.

## Suggested reading order

### If you want to *understand the harnesses*

1. **[`harness-deepdive.md`](./harness-deepdive.md)** — Start here. The end-to-end view: agent loop shapes, memory strategies, tool runtimes, model integration, sub-context primitives, and an innovation taxonomy across all 45 agents. **Read this if you're trying to understand "how do these agents actually work?"**
2. **[`internals-overview.md`](./internals-overview.md)** — Cross-cutting *architectural patterns* (the six harness shapes). Lighter than `harness-deepdive.md`, useful as a tour.
3. **[`agents/`](./agents)** — One markdown file per agent. Each page now includes both an **Internals & Architecture** section (mermaid diagram of the agent loop) *and* a **Harness Deep Dive** (loop / context / tools / model / innovation). Use this folder as a reference, not a read-through.

### If you want to *understand the skills ecosystem*

1. **[`ecosystem.md`](./ecosystem.md)** — What Agent Skills are, why a shared format exists, and how the `npx skills` CLI fits in.
2. **[`categories.md`](./categories.md)** — A taxonomy of all 45 agents (CLI vs IDE vs cloud, vendor, geography, business model).
3. **[`installation-paths.md`](./installation-paths.md)** — The full path reference. This is also the canonical answer to "where does the CLI actually drop my `SKILL.md`?"
4. **[`feature-compatibility.md`](./feature-compatibility.md)** — Annotated matrix of `allowed-tools`, `context: fork`, and hooks.
5. **[`skill-format.md`](./skill-format.md)** — How to author a skill. Frontmatter, discovery search paths, plugin manifests.
6. **[`analysis.md`](./analysis.md)** — Cross-cutting findings and patterns: path conventions, the "agents bucket" trick, vendor strategy.

### If you want to *pick* an agent

1. **[`use-cases.md`](./use-cases.md)** — Scenario-driven recommendations ("which agent for which job?").
2. **[`pros-cons.md`](./pros-cons.md)** — Per-agent strengths & weaknesses cards, plus shortlist axes.
3. **[`strengths-comparison.md`](./strengths-comparison.md)** — Quantitative side-by-side scoring across six capability axes.

## Per-agent index

See [`agents/README.md`](./agents/README.md) for the alphabetical index with one-line summaries.

## Conventions used in this documentation

- **Project path** — where the CLI installs a skill when run inside a project (no `-g`).
- **Global path** — where the CLI installs a skill with `-g` (the user's home directory).
- **`--agent` slug** — the value you pass to `--agent`/`-a` when targeting a specific agent.
- **Discovery path** — when *publishing* a skill, where the CLI looks for `SKILL.md` files inside *your* repository (separate from the install path).
- **Surface** — the form factor: CLI, native IDE, IDE extension, cloud/web, terminal, etc.

## How this repo was built

The data in these files was reconciled from:

- The official `README.md` and `AGENTS.md` files in `vercel-labs/skills`.
- The compatibility matrix, which is partial — only 18 agents are listed, even though 45 are supported.
- Each vendor's own skills documentation (linked at the bottom of every agent page).

When you spot an inconsistency, the upstream `vercel-labs/skills` repository is the source of truth for installation paths and slugs; the vendor's own docs are authoritative for behavior.
