# ai-harness-deepdive

> Deep research into the AI coding-agent **harness** ecosystem — the agent loop, memory subsystem, tool runtime, and model integration of every agent that ships with the **Agent Skills** standard.

This repository is a documentation-first analysis of every agent listed in the [vercel-labs/skills](https://github.com/vercel-labs/skills) project's *Supported Agents* table. It catalogues **45 coding agents**, dissects each one's harness (loop / memory / tools / model integration / design innovations), compares their architectures and skill support, and produces a per-agent reference card for each one.

---

## What is this repo?

The `vercel-labs/skills` repository was the seed: it lists which coding agents share a common file-based instruction format. But the agents themselves are far more interesting than the file format they happen to agree on. This repo answers two questions:

> **(1) How is each of these 45 agents actually built?** — agent loop, memory, tools, model layer, sandbox, sub-context primitive, design innovations.
>
> **(2) Where do they agree or diverge on the Agent Skills spec?** — install paths, feature support, the cross-agent `.agents/skills/` bucket, and the optional features (`allowed-tools`, `context: fork`, hooks, plugins).

The answer requires walking through every entry in the matrix. That's what the [`docs/`](./docs) folder does, with a per-agent page that includes a full **Harness Deep Dive** section (loop, context, tools, model, innovation) alongside the skills support table.

---

## Documentation map

### Harness deep-dive (the main attraction)

| Document | What it covers |
| --- | --- |
| [`docs/harness-deepdive.md`](./docs/harness-deepdive.md) | **The harness, end-to-end.** Agent loop shapes, memory strategies, tool runtimes, model integration, sub-context primitives, and an innovation taxonomy across all 45 agents. |
| [`docs/internals-overview.md`](./docs/internals-overview.md) | Cross-cutting **architecture patterns** observed across agents (with mermaid). The high-level companion to harness-deepdive. |
| [`docs/agents/`](./docs/agents) | One markdown file per agent. Each page has an **Internals & Architecture** diagram *and* a per-agent **Harness Deep Dive** (loop, memory, tools, model, innovation). |

### Skills ecosystem reference

| Document | What it covers |
| --- | --- |
| [`docs/README.md`](./docs/README.md) | Documentation hub & reading order |
| [`docs/ecosystem.md`](./docs/ecosystem.md) | What Agent Skills are, why they exist, history |
| [`docs/categories.md`](./docs/categories.md) | All 45 agents grouped by type, vendor, business model |
| [`docs/installation-paths.md`](./docs/installation-paths.md) | Project & global path reference for every agent |
| [`docs/feature-compatibility.md`](./docs/feature-compatibility.md) | Annotated feature matrix (`allowed-tools`, `context: fork`, hooks) |
| [`docs/skill-format.md`](./docs/skill-format.md) | Authoring guide — `SKILL.md` frontmatter, discovery, plugin manifests |
| [`docs/analysis.md`](./docs/analysis.md) | Cross-cutting findings, path conventions, vendor strategy patterns |

### Decision support

| Document | What it covers |
| --- | --- |
| [`docs/pros-cons.md`](./docs/pros-cons.md) | Per-agent **strengths & weaknesses** card, plus shortlist axes |
| [`docs/use-cases.md`](./docs/use-cases.md) | **Scenario-driven** picks: which agent for which job |
| [`docs/strengths-comparison.md`](./docs/strengths-comparison.md) | Quantitative **comparison matrices** across all 45 agents |

---

## The 45 supported agents at a glance

> Sorted alphabetically. Click any agent for a deep-dive page.

| # | Agent | Vendor / Origin | Surface | Doc |
| --- | --- | --- | --- | --- |
| 1 | AdaL | SylphAI | CLI + Web | [adal.md](./docs/agents/adal.md) |
| 2 | Amp | Sourcegraph | CLI + IDE ext. | [amp.md](./docs/agents/amp.md) |
| 3 | Antigravity | Google (Gemini 3) | Native IDE | [antigravity.md](./docs/agents/antigravity.md) |
| 4 | Augment | Augment Code | IDE ext. | [augment.md](./docs/agents/augment.md) |
| 5 | IBM Bob | IBM | Native IDE | [bob.md](./docs/agents/bob.md) |
| 6 | Claude Code | Anthropic | CLI + VS Code + JetBrains + Desktop + Web | [claude-code.md](./docs/agents/claude-code.md) |
| 7 | Cline | Cline (community) | VS Code ext. | [cline.md](./docs/agents/cline.md) |
| 8 | CodeBuddy | Tencent Cloud | IDE + Plugin + CLI | [codebuddy.md](./docs/agents/codebuddy.md) |
| 9 | Codex | OpenAI | CLI + Cloud | [codex.md](./docs/agents/codex.md) |
| 10 | Command Code | Command Code | CLI | [command-code.md](./docs/agents/command-code.md) |
| 11 | Continue | Continue.dev | IDE ext. (VS Code, JetBrains) | [continue.md](./docs/agents/continue.md) |
| 12 | Cortex Code | Snowflake | CLI | [cortex.md](./docs/agents/cortex.md) |
| 13 | Crush | Charmbracelet | TUI / CLI | [crush.md](./docs/agents/crush.md) |
| 14 | Cursor | Anysphere | Native IDE | [cursor.md](./docs/agents/cursor.md) |
| 15 | Deep Agents | LangChain | SDK + CLI | [deepagents.md](./docs/agents/deepagents.md) |
| 16 | Droid | Factory AI | CLI | [droid.md](./docs/agents/droid.md) |
| 17 | Firebender | Firebender (YC W24) | JetBrains plugin | [firebender.md](./docs/agents/firebender.md) |
| 18 | Gemini CLI | Google | CLI | [gemini-cli.md](./docs/agents/gemini-cli.md) |
| 19 | GitHub Copilot | GitHub / Microsoft | IDE ext. + CLI | [github-copilot.md](./docs/agents/github-copilot.md) |
| 20 | Goose | AAIF (was Block) | Desktop + CLI | [goose.md](./docs/agents/goose.md) |
| 21 | iFlow CLI | iFlow.cn | CLI | [iflow-cli.md](./docs/agents/iflow-cli.md) |
| 22 | Junie | JetBrains | IDE + CLI | [junie.md](./docs/agents/junie.md) |
| 23 | Kilo Code | Kilo Code Inc. | VS Code + JetBrains + CLI | [kilo.md](./docs/agents/kilo.md) |
| 24 | Kimi Code CLI | Moonshot AI | CLI | [kimi-cli.md](./docs/agents/kimi-cli.md) |
| 25 | Kiro CLI | Amazon Web Services | CLI + Native IDE | [kiro-cli.md](./docs/agents/kiro-cli.md) |
| 26 | Kode | shareAI-lab | CLI | [kode.md](./docs/agents/kode.md) |
| 27 | MCPJam | MCPJam | Web + CLI + Desktop | [mcpjam.md](./docs/agents/mcpjam.md) |
| 28 | Mistral Vibe | Mistral AI | CLI + IDE ext. | [mistral-vibe.md](./docs/agents/mistral-vibe.md) |
| 29 | Mux | Coder | CLI | [mux.md](./docs/agents/mux.md) |
| 30 | Neovate | Neovate AI | CLI + Web + Desktop | [neovate.md](./docs/agents/neovate.md) |
| 31 | OpenClaw | OpenClaw | Native IDE | [openclaw.md](./docs/agents/openclaw.md) |
| 32 | OpenCode | sst | CLI | [opencode.md](./docs/agents/opencode.md) |
| 33 | OpenHands | All-Hands AI | Web + CLI | [openhands.md](./docs/agents/openhands.md) |
| 34 | Pi | badlogic | CLI | [pi.md](./docs/agents/pi.md) |
| 35 | Pochi | TabbyML | VS Code ext. | [pochi.md](./docs/agents/pochi.md) |
| 36 | Qoder | Qoder | Native IDE + CLI | [qoder.md](./docs/agents/qoder.md) |
| 37 | Qwen Code | Alibaba (Qwen) | CLI | [qwen-code.md](./docs/agents/qwen-code.md) |
| 38 | Replit | Replit | Cloud IDE | [replit.md](./docs/agents/replit.md) |
| 39 | Roo Code | Roo (community fork) | VS Code ext. | [roo.md](./docs/agents/roo.md) |
| 40 | Trae | ByteDance | Native IDE | [trae.md](./docs/agents/trae.md) |
| 41 | Trae CN | ByteDance (CN) | Native IDE | [trae-cn.md](./docs/agents/trae-cn.md) |
| 42 | Universal | (generic) | Any | [universal.md](./docs/agents/universal.md) |
| 43 | Warp | Warp | Terminal | [warp.md](./docs/agents/warp.md) |
| 44 | Windsurf | Cognition (was Codeium) | Native IDE | [windsurf.md](./docs/agents/windsurf.md) |
| 45 | Zencoder | Zencoder | IDE ext. | [zencoder.md](./docs/agents/zencoder.md) |

---

## Source data

All facts in this repository were derived from:

- The [`vercel-labs/skills`](https://github.com/vercel-labs/skills) repository (`README.md`, `AGENTS.md`, `src/agents.ts`).
- Each agent's own official documentation (linked from each per-agent page).
- The shared [Agent Skills specification](https://agentskills.io) and the [skills.sh](https://skills.sh) directory.

If a vendor changes a path or feature flag upstream, the corresponding agent page should be updated.

## License

The content in this repository is for analysis & documentation purposes. Linked third-party documentation, names, and trademarks belong to their respective owners.
