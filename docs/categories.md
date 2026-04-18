# Agent Taxonomy

Forty-five agents share one specification. They do not share much else: surface, vendor, license, and target audience all vary. This page sorts them into useful buckets.

> Each row links to the per-agent deep-dive in [`agents/`](./agents).

---

## By surface (form factor)

### 1. CLI / TUI

The largest category. Most modern coding agents ship as a CLI first.

| Agent | Vendor | Notes |
| --- | --- | --- |
| [Amp](./agents/amp.md) | Sourcegraph | CLI + IDE extensions, Sourcegraph context |
| [Claude Code](./agents/claude-code.md) | Anthropic | Reference implementation of skills |
| [Codex](./agents/codex.md) | OpenAI | CLI + Cloud agent |
| [Command Code](./agents/command-code.md) | Command Code | "Taste"-learning system |
| [Cortex Code](./agents/cortex.md) | Snowflake | Snowflake Cortex agent |
| [Crush](./agents/crush.md) | Charmbracelet | Polished Go TUI |
| [Droid](./agents/droid.md) | Factory AI | Async-friendly CLI |
| [Gemini CLI](./agents/gemini-cli.md) | Google | Free Gemini-backed CLI |
| [Goose](./agents/goose.md) | AAIF (Linux Foundation) | Originally Block, now Linux Foundation |
| [iFlow CLI](./agents/iflow-cli.md) | iFlow.cn | Chinese platform; sunsetting Apr 2026 → Qoder |
| [Kimi Code CLI](./agents/kimi-cli.md) | Moonshot AI | Python-based CLI by the makers of Kimi K2 |
| [Kiro CLI](./agents/kiro-cli.md) | Amazon Web Services | Companion CLI to Kiro IDE |
| [Kode](./agents/kode.md) | shareAI-lab | TypeScript CLI, AGENTS.md-native |
| [Mistral Vibe](./agents/mistral-vibe.md) | Mistral AI | Devstral-2-powered CLI |
| [Mux](./agents/mux.md) | Coder | Headless one-shot CLI |
| [OpenCode](./agents/opencode.md) | sst | Provider-agnostic terminal agent |
| [Pi](./agents/pi.md) | badlogic | Minimal, embeddable Anthropic-first CLI |
| [Qwen Code](./agents/qwen-code.md) | Alibaba | Qwen-3-Coder-tuned CLI |
| [Universal](./agents/universal.md) | (generic) | Catch-all for tools that read `.agents/skills/` |
| [Warp](./agents/warp.md) | Warp | Terminal app with built-in agent |

### 2. Native AI IDE (a fork of VS Code or its own shell)

| Agent | Vendor | Notes |
| --- | --- | --- |
| [Antigravity](./agents/antigravity.md) | Google | Gemini 3-powered, "Mission Control" UX |
| [Cursor](./agents/cursor.md) | Anysphere | The category-defining AI IDE |
| [IBM Bob](./agents/bob.md) | IBM | Enterprise SDLC partner |
| [Kiro CLI / Kiro IDE](./agents/kiro-cli.md) | AWS | Spec-driven; runs on Bedrock |
| [OpenClaw](./agents/openclaw.md) | OpenClaw | AgentSkills-compatible, "ClawHub" registry |
| [Qoder](./agents/qoder.md) | Qoder | Agentic IDE with "Quest Mode" |
| [Trae](./agents/trae.md) | ByteDance | Free AI IDE, Cursor competitor |
| [Trae CN](./agents/trae-cn.md) | ByteDance (CN) | Chinese-mainland edition |
| [Windsurf](./agents/windsurf.md) | Cognition (was Codeium) | Cascade agent in a VS Code fork |

### 3. IDE Extension (lives inside someone else's editor)

| Agent | Host editor(s) | Notes |
| --- | --- | --- |
| [Augment](./agents/augment.md) | VS Code, JetBrains, Vim, Zed | Augment Code |
| [CodeBuddy](./agents/codebuddy.md) | VS Code, JetBrains | Tencent Cloud |
| [Cline](./agents/cline.md) | VS Code | Open-source agent |
| [Continue](./agents/continue.md) | VS Code, JetBrains | Open-source, fully customisable |
| [Firebender](./agents/firebender.md) | Android Studio / IntelliJ | Android-specialist YC W24 |
| [GitHub Copilot](./agents/github-copilot.md) | VS Code, JetBrains, Visual Studio, Xcode, Neovim | Microsoft / GitHub |
| [Junie](./agents/junie.md) | JetBrains IDEs | JetBrains' own agent |
| [Kilo Code](./agents/kilo.md) | VS Code, JetBrains | OSS, multi-mode |
| [Pochi](./agents/pochi.md) | VS Code | TabbyML, OSS |
| [Roo Code](./agents/roo.md) | VS Code | Community fork of Cline |
| [Zencoder](./agents/zencoder.md) | VS Code, JetBrains | "Zen Agents" framework |

### 4. Cloud / web

| Agent | Vendor | Notes |
| --- | --- | --- |
| [AdaL](./agents/adal.md) | SylphAI | Terminal + web; "self-evolving" |
| [Deep Agents](./agents/deepagents.md) | LangChain | SDK + CLI; embed in your own app |
| [MCPJam](./agents/mcpjam.md) | MCPJam | Web/desktop MCP testing harness |
| [Neovate](./agents/neovate.md) | Neovate AI | CLI + Web + Desktop |
| [OpenHands](./agents/openhands.md) | All-Hands AI | Was OpenDevin; cloud + local |
| [Replit](./agents/replit.md) | Replit | Cloud IDE w/ Replit Agent |

---

## By vendor / origin

### Hyperscalers / Big Tech

| Agent | Vendor |
| --- | --- |
| [GitHub Copilot](./agents/github-copilot.md) | Microsoft / GitHub |
| [Antigravity](./agents/antigravity.md), [Gemini CLI](./agents/gemini-cli.md) | Google |
| [Codex](./agents/codex.md) | OpenAI |
| [Claude Code](./agents/claude-code.md) | Anthropic |
| [Kiro CLI](./agents/kiro-cli.md) | Amazon Web Services |
| [IBM Bob](./agents/bob.md) | IBM |
| [Cortex Code](./agents/cortex.md) | Snowflake |

### LLM-vendor first-party agents

| Agent | LLM behind it |
| --- | --- |
| [Mistral Vibe](./agents/mistral-vibe.md) | Devstral 2 (Mistral) |
| [Qwen Code](./agents/qwen-code.md) | Qwen 3 Coder (Alibaba) |
| [Kimi Code CLI](./agents/kimi-cli.md) | Kimi K2 (Moonshot) |
| [Gemini CLI](./agents/gemini-cli.md) | Gemini (Google) |
| [Codex](./agents/codex.md) | GPT (OpenAI) |
| [Claude Code](./agents/claude-code.md) | Claude (Anthropic) |

### Startups / VC-backed independents

| Agent | Stage |
| --- | --- |
| [Cursor](./agents/cursor.md) | Anysphere — late-stage |
| [Windsurf](./agents/windsurf.md) | Cognition (acquired Codeium) |
| [Replit](./agents/replit.md) | Public |
| [Augment](./agents/augment.md) | Series B+ |
| [Command Code](./agents/command-code.md) | Seed ($5M, led by Tom Preston-Werner) |
| [Firebender](./agents/firebender.md) | YC W24 |
| [Zencoder](./agents/zencoder.md) | Series A |
| [Neovate](./agents/neovate.md) | Independent |
| [Qoder](./agents/qoder.md) | Independent |

### Open source / community

| Agent | License |
| --- | --- |
| [Cline](./agents/cline.md) | Apache 2.0 |
| [Roo Code](./agents/roo.md) | Apache 2.0 (Cline fork) |
| [OpenHands](./agents/openhands.md) | MIT (was OpenDevin) |
| [OpenCode](./agents/opencode.md) | Apache 2.0 |
| [Goose](./agents/goose.md) | MIT (Linux Foundation) |
| [Continue](./agents/continue.md) | Apache 2.0 |
| [Pochi](./agents/pochi.md) | OSS (TabbyML) |
| [Crush](./agents/crush.md) | MIT (Charmbracelet) |
| [Pi](./agents/pi.md) | OSS, MIT |
| [Kode](./agents/kode.md) | Apache 2.0 |
| [Kilo Code](./agents/kilo.md) | OSS |
| [Deep Agents](./agents/deepagents.md) | MIT (LangChain) |

### Chinese ecosystem

| Agent | Vendor |
| --- | --- |
| [Trae](./agents/trae.md), [Trae CN](./agents/trae-cn.md) | ByteDance |
| [CodeBuddy](./agents/codebuddy.md) | Tencent Cloud |
| [Qwen Code](./agents/qwen-code.md) | Alibaba |
| [Kimi Code CLI](./agents/kimi-cli.md) | Moonshot |
| [iFlow CLI](./agents/iflow-cli.md) | iFlow.cn (sunset April 2026) |
| [Qoder](./agents/qoder.md) | Qoder (CN-led) |

---

## By business model

| Model | Agents |
| --- | --- |
| Free, OSS | Cline, Continue, OpenCode, OpenHands, Pochi, Crush, Pi, Kode, Goose, Deep Agents, Kilo Code, MCPJam (open-core) |
| Free with paid model API (BYOK) | Aider-style: Pi, Crush, OpenCode, Continue, Junie BYOK tier |
| Subscription (per-seat) | Cursor, Windsurf, Copilot, Augment, Junie, Replit, Zencoder, AdaL, Command Code |
| Free product, paid backend | Trae, Antigravity (preview), Gemini CLI (Google account quota), Kiro (50 free actions/mo), iFlow CLI |
| Enterprise / cloud-bound | IBM Bob, Cortex Code, AWS Kiro, CodeBuddy, Mistral Vibe (enterprise tier) |

---

## By skills-folder convention

How an agent decided to namespace its install path tells you something about whether it sees skills as core or as a side feature.

### Use the shared `.agents/skills/` bucket (project-level)

The "we'll just go along" group. 15 agents:

> Amp, Antigravity, Cline, Warp, Codex, Cursor, Deep Agents, Firebender, Gemini CLI, GitHub Copilot, Kimi CLI, OpenCode, Replit, Universal, plus indirectly anyone who reads from the universal bucket.

Note: Cursor stores **globally** at `~/.cursor/skills/` but uses the shared `.agents/skills/` for projects — a hybrid stance.

### Use a dedicated, namespaced folder (project-level)

The "we own this UX" group:

> `.augment/` (Augment), `.bob/` (IBM Bob), `.claude/` (Claude Code), `.codebuddy/`, `.commandcode/`, `.continue/`, `.cortex/`, `.crush/`, `.factory/` (Droid), `.goose/`, `.iflow/`, `.junie/`, `.kilocode/`, `.kiro/`, `.kode/`, `.mcpjam/`, `.vibe/` (Mistral), `.mux/`, `.openhands/`, `.pi/`, `.pochi/`, `.qoder/`, `.qwen/`, `.roo/`, `.trae/`, `.windsurf/`, `.zencoder/`, `.neovate/`, `.adal/`, plus OpenClaw which uses bare `skills/`.

### Outliers

- **OpenClaw** — uses bare `skills/` at the project root (no leading dot).
- **Kiro CLI** — even after install, requires manual JSON config to wire the skills into a custom agent.

See [`installation-paths.md`](./installation-paths.md) for the exhaustive table.
