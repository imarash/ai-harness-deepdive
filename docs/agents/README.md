# Per-Agent Reference Index

One markdown file per supported agent. Use the table below as the alphabetical index; each file is structured the same way:

```text
# <Agent name>

> Slug · Surface · Vendor · License (one-liner)

## Overview
## Skills support
- Project path
- Global path
- Discovery path
- allowed-tools
- context: fork
- Hooks
## Installation
## Notable behavior
## Internals & Architecture           <-- prose + mermaid diagram of the agent loop
## Harness Deep Dive                  <-- agent loop, memory, tools, model, innovation
## Documentation links
```

> Want a cross-cutting view? See [`../harness-deepdive.md`](../harness-deepdive.md) for the end-to-end harness analysis, [`../internals-overview.md`](../internals-overview.md) for shared architectural patterns, [`../strengths-comparison.md`](../strengths-comparison.md) for capability scoring, [`../pros-cons.md`](../pros-cons.md) for per-agent strengths/weaknesses cards, and [`../use-cases.md`](../use-cases.md) for scenario-driven recommendations.

---

## Alphabetical index

| Agent | Slug | Vendor | One-liner |
| --- | --- | --- | --- |
| [AdaL](./adal.md) | `adal` | SylphAI | Self-evolving terminal/web agent that learns team patterns. |
| [Amp](./amp.md) | `amp` | Sourcegraph | Sourcegraph's agentic CLI/IDE pair. |
| [Antigravity](./antigravity.md) | `antigravity` | Google | Gemini-3 agent-first IDE with "Mission Control". |
| [Augment](./augment.md) | `augment` | Augment Code | Multi-IDE extension with deep-context engine. |
| [IBM Bob](./bob.md) | `bob` | IBM | Enterprise SDLC partner with Skills as first-class workflows. |
| [Claude Code](./claude-code.md) | `claude-code` | Anthropic | The reference implementation of the Skills spec. |
| [Cline](./cline.md) | `cline` | Cline (OSS) | Open-source autonomous coding agent for VS Code. |
| [CodeBuddy](./codebuddy.md) | `codebuddy` | Tencent Cloud | Tencent's IDE + plugin + CLI trio. |
| [Codex](./codex.md) | `codex` | OpenAI | OpenAI's CLI + cloud agent. |
| [Command Code](./command-code.md) | `command-code` | Command Code | "Taste"-learning CLI on Claude Sonnet/Opus. |
| [Continue](./continue.md) | `continue` | Continue.dev | Open-source extension for VS Code & JetBrains. |
| [Cortex Code](./cortex.md) | `cortex` | Snowflake | Snowflake Cortex's coding agent. |
| [Crush](./crush.md) | `crush` | Charmbracelet | Glamorous Go TUI for agentic coding. |
| [Cursor](./cursor.md) | `cursor` | Anysphere | The category-defining AI IDE. |
| [Deep Agents](./deepagents.md) | `deepagents` | LangChain | SDK + CLI for multi-step agentic workflows. |
| [Droid](./droid.md) | `droid` | Factory AI | Factory's CLI agent. |
| [Firebender](./firebender.md) | `firebender` | Firebender (YC W24) | Android Studio AI specialist. |
| [Gemini CLI](./gemini-cli.md) | `gemini-cli` | Google | Free Gemini-backed terminal agent. |
| [GitHub Copilot](./github-copilot.md) | `github-copilot` | GitHub / Microsoft | Multi-IDE agent + CLI. |
| [Goose](./goose.md) | `goose` | AAIF (Linux Foundation) | Originally Block; now LF; desktop + CLI. |
| [iFlow CLI](./iflow-cli.md) | `iflow-cli` | iFlow.cn | Chinese terminal agent (sunsetting April 2026). |
| [Junie](./junie.md) | `junie` | JetBrains | JetBrains' LLM-agnostic agent. |
| [Kilo Code](./kilo.md) | `kilo` | Kilo Code Inc. | OSS multi-mode VS Code/JetBrains/CLI agent. |
| [Kimi Code CLI](./kimi-cli.md) | `kimi-cli` | Moonshot AI | Kimi-K2-tuned terminal agent. |
| [Kiro CLI](./kiro-cli.md) | `kiro-cli` | Amazon Web Services | Spec-driven agentic IDE + CLI on Bedrock. |
| [Kode](./kode.md) | `kode` | shareAI-lab | TypeScript CLI, AGENTS.md-native. |
| [MCPJam](./mcpjam.md) | `mcpjam` | MCPJam | Web/CLI/desktop MCP testing harness. |
| [Mistral Vibe](./mistral-vibe.md) | `mistral-vibe` | Mistral AI | Devstral-2-powered enterprise coding agent. |
| [Mux](./mux.md) | `mux` | Coder | Headless one-shot coding agent. |
| [Neovate](./neovate.md) | `neovate` | Neovate AI | CLI/Web/Desktop agent with rich plugin/skills system. |
| [OpenClaw](./openclaw.md) | `openclaw` | OpenClaw | AgentSkills-compatible IDE with ClawHub registry. |
| [OpenCode](./opencode.md) | `opencode` | sst | Provider-agnostic open-source terminal agent. |
| [OpenHands](./openhands.md) | `openhands` | All-Hands AI | Was OpenDevin; cloud + local agent platform. |
| [Pi](./pi.md) | `pi` | badlogic | Minimal embeddable terminal agent. |
| [Pochi](./pochi.md) | `pochi` | TabbyML | Open-source VS Code agent with parallel-agent support. |
| [Qoder](./qoder.md) | `qoder` | Qoder | Agentic IDE with "Experts" and "Quest" modes. |
| [Qwen Code](./qwen-code.md) | `qwen-code` | Alibaba | Qwen-3-Coder-tuned terminal agent. |
| [Replit](./replit.md) | `replit` | Replit | Cloud IDE with Replit Agent. |
| [Roo Code](./roo.md) | `roo` | Roo (community fork of Cline) | Cline derivative with extended modes. |
| [Trae](./trae.md) | `trae` | ByteDance | Free AI IDE rivalling Cursor. |
| [Trae CN](./trae-cn.md) | `trae-cn` | ByteDance (CN) | Mainland-China edition of Trae. |
| [Universal](./universal.md) | `universal` | (generic) | Catch-all slug for tools that read `.agents/skills/`. |
| [Warp](./warp.md) | `warp` | Warp | Terminal app with built-in agent. |
| [Windsurf](./windsurf.md) | `windsurf` | Cognition (was Codeium) | VS Code fork with Cascade agent. |
| [Zencoder](./zencoder.md) | `zencoder` | Zencoder | Multi-IDE agent with Zen Agents framework. |
