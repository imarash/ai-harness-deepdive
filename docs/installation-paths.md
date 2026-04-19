# Installation-Path Reference

This is the canonical reference for **where the `npx skills` CLI installs a skill** for each of the 45 supported agents — both the `--project` (default) and `-g` (global) locations.

> Source: [`vercel-labs/skills/README.md`](https://github.com/vercel-labs/skills#supported-agents) (verified against `src/agents.ts` in the same repo at v1.5.1).

---

## The full table

| # | Agent | `--agent` slug | Project path | Global path |
| --- | --- | --- | --- | --- |
| 1 | Amp | `amp` | `.agents/skills/` | `~/.config/agents/skills/` |
| 2 | Kimi Code CLI | `kimi-cli` | `.agents/skills/` | `~/.config/agents/skills/` |
| 3 | Replit | `replit` | `.agents/skills/` | `~/.config/agents/skills/` |
| 4 | Universal | `universal` | `.agents/skills/` | `~/.config/agents/skills/` |
| 5 | Antigravity | `antigravity` | `.agents/skills/` | `~/.gemini/antigravity/skills/` |
| 6 | Augment | `augment` | `.augment/skills/` | `~/.augment/skills/` |
| 7 | IBM Bob | `bob` | `.bob/skills/` | `~/.bob/skills/` |
| 8 | Claude Code | `claude-code` | `.claude/skills/` | `~/.claude/skills/` |
| 9 | OpenClaw | `openclaw` | `skills/` | `~/.openclaw/skills/` |
| 10 | Cline | `cline` | `.agents/skills/` | `~/.agents/skills/` |
| 11 | Warp | `warp` | `.agents/skills/` | `~/.agents/skills/` |
| 12 | CodeBuddy | `codebuddy` | `.codebuddy/skills/` | `~/.codebuddy/skills/` |
| 13 | Codex | `codex` | `.agents/skills/` | `~/.codex/skills/` |
| 14 | Command Code | `command-code` | `.commandcode/skills/` | `~/.commandcode/skills/` |
| 15 | Continue | `continue` | `.continue/skills/` | `~/.continue/skills/` |
| 16 | Cortex Code | `cortex` | `.cortex/skills/` | `~/.snowflake/cortex/skills/` |
| 17 | Crush | `crush` | `.crush/skills/` | `~/.config/crush/skills/` |
| 18 | Cursor | `cursor` | `.agents/skills/` | `~/.cursor/skills/` |
| 19 | Deep Agents | `deepagents` | `.agents/skills/` | `~/.deepagents/agent/skills/` |
| 20 | Droid | `droid` | `.factory/skills/` | `~/.factory/skills/` |
| 21 | Firebender | `firebender` | `.agents/skills/` | `~/.firebender/skills/` |
| 22 | Gemini CLI | `gemini-cli` | `.agents/skills/` | `~/.gemini/skills/` |
| 23 | GitHub Copilot | `github-copilot` | `.agents/skills/` | `~/.copilot/skills/` |
| 24 | Goose | `goose` | `.goose/skills/` | `~/.config/goose/skills/` |
| 25 | Junie | `junie` | `.junie/skills/` | `~/.junie/skills/` |
| 26 | iFlow CLI | `iflow-cli` | `.iflow/skills/` | `~/.iflow/skills/` |
| 27 | Kilo Code | `kilo` | `.kilocode/skills/` | `~/.kilocode/skills/` |
| 28 | Kiro CLI | `kiro-cli` | `.kiro/skills/` | `~/.kiro/skills/` |
| 29 | Kode | `kode` | `.kode/skills/` | `~/.kode/skills/` |
| 30 | MCPJam | `mcpjam` | `.mcpjam/skills/` | `~/.mcpjam/skills/` |
| 31 | Mistral Vibe | `mistral-vibe` | `.vibe/skills/` | `~/.vibe/skills/` |
| 32 | Mux | `mux` | `.mux/skills/` | `~/.mux/skills/` |
| 33 | OpenCode | `opencode` | `.agents/skills/` | `~/.config/opencode/skills/` |
| 34 | OpenHands | `openhands` | `.openhands/skills/` | `~/.openhands/skills/` |
| 35 | Pi | `pi` | `.pi/skills/` | `~/.pi/agent/skills/` |
| 36 | Qoder | `qoder` | `.qoder/skills/` | `~/.qoder/skills/` |
| 37 | Qwen Code | `qwen-code` | `.qwen/skills/` | `~/.qwen/skills/` |
| 38 | Roo Code | `roo` | `.roo/skills/` | `~/.roo/skills/` |
| 39 | Trae | `trae` | `.trae/skills/` | `~/.trae/skills/` |
| 40 | Trae CN | `trae-cn` | `.trae/skills/` | `~/.trae-cn/skills/` |
| 41 | Windsurf | `windsurf` | `.windsurf/skills/` | `~/.codeium/windsurf/skills/` |
| 42 | Zencoder | `zencoder` | `.zencoder/skills/` | `~/.zencoder/skills/` |
| 43 | Neovate | `neovate` | `.neovate/skills/` | `~/.neovate/skills/` |
| 44 | Pochi | `pochi` | `.pochi/skills/` | `~/.pochi/skills/` |
| 45 | AdaL | `adal` | `.adal/skills/` | `~/.adal/skills/` |

---

## Patterns and anomalies

### Project paths

- **`.agents/skills/`** is shared by 15 agents. This is the de-facto "shared bucket" path. If you only target this one path, you cover Amp, Antigravity, Cline, Codex, Cursor, Deep Agents, Firebender, Gemini CLI, GitHub Copilot, Kimi CLI, OpenCode, Replit, Universal, and Warp.
- **GitHub Copilot** — the table above lists where `npx skills` *installs* for `--agent github-copilot` (still `.agents/skills/`). GitHub's own documentation additionally lists **`.github/skills/`** and **`.claude/skills/`** as project discovery paths Copilot will load — see [About agent skills](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills). Same idea for globals: Copilot reads `~/.copilot/skills/` but also `~/.agents/skills/` and `~/.claude/skills/`.
- **`skills/`** (no leading dot) is unique to OpenClaw. This will be picked up by *any* tool that does a recursive search of the project.
- **`.factory/skills/`** is Droid's path — note that the slug is `droid` but the folder is `.factory`. Factory AI is the company; Droid is the product.
- **`.vibe/skills/`** is Mistral Vibe — short folder name, not `.mistral-vibe`.
- **`.trae/skills/`** is shared by both Trae and Trae CN at the project level — they only diverge in the global path.

### Global paths — three families

The 45 global paths fall into three groups:

1. **`~/.<slug>/skills/`** (the simple convention)
   Used by Augment, Bob, Claude Code, CodeBuddy, Codex, Command Code, Continue, Firebender, GitHub Copilot (note: `~/.copilot/skills/`), iFlow, Junie, Kilo, Kiro, Kode, MCPJam, Mistral Vibe (`~/.vibe/`), Mux, Neovate, OpenHands, Pochi, Qoder, Qwen, Roo, Trae, Zencoder, AdaL.

2. **`~/.config/<slug>/skills/`** (XDG-style)
   Crush, Goose, OpenCode use this. Amp / Kimi CLI / Replit / Universal share `~/.config/agents/skills/`.

3. **Vendor-prefixed nested paths** (the long ones)
   - Antigravity → `~/.gemini/antigravity/skills/` (Google nests under `.gemini`)
   - Cortex Code → `~/.snowflake/cortex/skills/` (Snowflake nests under `.snowflake`)
   - Windsurf → `~/.codeium/windsurf/skills/` (history: Windsurf was Codeium's IDE)
   - Pi → `~/.pi/agent/skills/` (extra `agent/` segment)
   - Deep Agents → `~/.deepagents/agent/skills/` (same pattern as Pi)
   - Trae CN → `~/.trae-cn/skills/` (avoids stomping on Trae)

### "Universal" project path, agent-specific global path

A few agents share `.agents/skills/` for *projects* but have their own *global* path:

| Agent | Project | Global |
| --- | --- | --- |
| Cursor | `.agents/skills/` | `~/.cursor/skills/` |
| Codex | `.agents/skills/` | `~/.codex/skills/` |
| Antigravity | `.agents/skills/` | `~/.gemini/antigravity/skills/` |
| Deep Agents | `.agents/skills/` | `~/.deepagents/agent/skills/` |
| Firebender | `.agents/skills/` | `~/.firebender/skills/` |
| Gemini CLI | `.agents/skills/` | `~/.gemini/skills/` |
| GitHub Copilot | `.agents/skills/` | `~/.copilot/skills/` |
| OpenCode | `.agents/skills/` | `~/.config/opencode/skills/` |

This is a smart design: the *project* path is shared so a single skill folder serves multiple agents on the same checkout, while the *global* path stays vendor-namespaced so per-agent skills don't collide.

### The "agents bucket" winners

If you only ever read from one place per agent, the most-supported single path is:

```
.agents/skills/   →   reaches 15 of 45 agents
```

Followed by:

```
~/.agents/skills/  →   shared global path for Cline + Warp
~/.config/agents/skills/  →  shared global path for Amp, Kimi CLI, Replit, Universal
```

### Path-naming inconsistencies to be aware of

These are the cases where the slug doesn't match the folder name:

| Slug (`--agent`) | Project folder | Notes |
| --- | --- | --- |
| `kimi-cli` | `.agents/` | Uses the shared bucket, not `.kimi/` (the global path is also shared) |
| `mistral-vibe` | `.vibe/` | The product is "Mistral Vibe", but the folder is just `.vibe` |
| `droid` | `.factory/` | The product is "Droid"; the company is "Factory AI" |
| `trae-cn` | `.trae/` (project) | Shared with Trae at the project level |
| `kilo` | `.kilocode/` | The slug is short, the folder is long |
| `cortex` | `.cortex/` (project) and `~/.snowflake/cortex/skills/` (global) | Snowflake namespacing |
| `github-copilot` | `~/.copilot/` (global) | Just `.copilot`, not `.github-copilot` |

---

## Where the *publisher* puts skills (discovery paths)

When you author a skills repo, the CLI searches these locations in your repo for `SKILL.md` files (separate from the install paths above):

```
SKILL.md                    ← root (single-skill repo)
skills/
skills/.curated/
skills/.experimental/
skills/.system/
.agents/skills/
.augment/skills/
.bob/skills/
.claude/skills/
./skills/
.codebuddy/skills/
.commandcode/skills/
.continue/skills/
.cortex/skills/
.crush/skills/
.factory/skills/
.goose/skills/
.junie/skills/
.iflow/skills/
.kilocode/skills/
.kiro/skills/
.kode/skills/
.mcpjam/skills/
.vibe/skills/
.mux/skills/
.openhands/skills/
.pi/skills/
.qoder/skills/
.qwen/skills/
.roo/skills/
.trae/skills/
.windsurf/skills/
.zencoder/skills/
.neovate/skills/
.pochi/skills/
.adal/skills/
```

If none of these locations match, the CLI falls back to a **recursive scan** for any `SKILL.md` file in the repo. So in practice, you can put skills almost anywhere — but if you stick to `skills/` you'll get the cleanest, fastest discovery.
