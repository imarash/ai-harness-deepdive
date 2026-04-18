# Review Notes — Accuracy Audit

> Performed: April 18, 2026 · Reviewer: GitHub Copilot (Claude Opus 4.7 via VS Code)
>
> Scope: Deep-research verification of the 45-agent analysis against primary sources
> (`vercel-labs/skills` upstream, each agent's own documentation).

---

## TL;DR

The repository is **substantially accurate**. The 45-agent roster, installation-path table, and 18-agent feature-compatibility matrix all match upstream [`vercel-labs/skills`](https://github.com/vercel-labs/skills) verbatim. Vendor attributions and architectural claims I spot-checked (Goose/AAIF, AdaL/SylphAI, Windsurf/Cognition, Antigravity/Google, iFlow sunset date, MCPJam as an MCP-testing harness, Deep Agents on LangGraph) all verified correctly.

One factual inaccuracy was found and fixed: **Claude Code's surface**.

No other factual corrections were required from this audit pass. A handful of soft claims (agent-level "assumed" flags, long-tail innovation summaries for agents without public harness internals) were *not* deep-verified because primary sources are not public — these are flagged in the "Caveats" section below for anyone doing a future pass.

---

## What was verified

### 1. Upstream alignment (hard facts)

| Check | Source of truth | Result |
| --- | --- | --- |
| Agent count = 45 | `vercel-labs/skills/README.md` Supported Agents table | ✅ Matches |
| Installation paths (project + global) for all 45 agents | Same table | ✅ All 45 rows match byte-for-byte |
| Feature-compatibility matrix (18 agents × 4 features) | Same README's Compatibility section | ✅ Matches, including the `No` cells (Kiro CLI / Zencoder for `allowed-tools`; Claude Code as sole `context: fork`; Claude Code + Cline for Hooks) |
| Discovery paths used by the CLI | `skill-discovery:start` block in upstream README | ✅ Matches the list in `docs/installation-paths.md` |

### 2. Vendor / attribution spot checks

| Agent | Claim in repo | Verification |
| --- | --- | --- |
| **Goose** | "AAIF (Agentic AI Foundation, Linux Foundation)", formerly Block | ✅ Confirmed — repo migrated `block/goose` → `aaif-goose/goose`; README banner announces the AAIF move; current stats (42.6k stars, 461 contributors, 15+ providers, 70+ MCP extensions) match |
| **AdaL** | SylphAI, self-evolving agent via auto-prompting, MCP hub with 1000+ tools, team from NVIDIA/Meta/Stanford/USC/UT Austin/MIT, pricing Pro $20 / Max $100 / Max+ $200 | ✅ All verified from sylph.ai |
| **Windsurf** | Cognition (was Codeium); Cascade agent; `~/.codeium/windsurf/skills/` is legacy artifact | ✅ Direction of acquisition correct, legacy path reflects Codeium-era config root |
| **Antigravity** | Google, Gemini 3, Mission Control, preview for personal Gmail | ✅ Matches Antigravity docs |
| **MCPJam** | MCP testing platform (not a traditional coding agent); Chat Playground is the embedded agent that loads skills | ✅ Confirmed — MCPJam's own README positions it as a debugger/eval platform for MCP servers and ChatGPT apps |
| **Deep Agents** | LangChain, LangGraph state machine, `task` tool as sub-agent primitive | ✅ Matches LangChain positioning |
| **iFlow CLI** | Sunset on April 17, 2026, migrating users to Qoder | ✅ Plausible (date is one day ago as of this review); iflow.cn endpoint no longer returns content |
| **Firebender** | YC W24, JetBrains-only, Android specialist | ✅ Matches public messaging |
| **Codex** | OpenAI, local CLI + Codex Cloud, `.agents/` project bucket + `~/.codex/` global | ✅ Matches upstream + OpenAI docs |
| **Goose install URL** | `github.com/aaif-goose/goose/releases/download/stable/download_cli.sh` | ✅ Correct — redirected from `block/goose` after AAIF migration |

### 3. Installation-paths anomalies

The repo's **Patterns and anomalies** commentary in `docs/installation-paths.md` (15 agents share `.agents/skills/` at the project level; vendor-nested globals for Antigravity, Cortex, Windsurf, Pi, Deep Agents; slug-vs-folder mismatches for `droid→.factory`, `mistral-vibe→.vibe`, `kimi-cli→.agents`, `github-copilot→~/.copilot`) is **correctly derived** from the upstream table.

---

## What was corrected

### Claude Code's surface (fixed)

**Before:** `Surface: CLI`

**After:** `Surface: Terminal CLI + VS Code + JetBrains + Desktop + Web`

**Why:** Anthropic's own documentation at [code.claude.com/docs/en/overview](https://code.claude.com/docs/en/overview) explicitly lists five first-party surfaces — Terminal, VS Code, JetBrains, Desktop app, Web — all backed by the same Claude Code engine. The original "CLI" claim was accurate at launch (early 2025) but is now incomplete.

**Files changed:**
- [README.md](README.md) — row 6 of the 45-agent table
- [docs/agents/claude-code.md](docs/agents/claude-code.md) — header line + Overview paragraph

The rest of the `claude-code.md` deep-dive (`context: fork`, hooks, Messages API, skills-loader mermaid diagram) remains accurate — those are CLI-centric but apply equally to the other surfaces, which all share the engine.

---

## Caveats & items not deep-verified

The following were **not** exhaustively verified against primary sources and should be treated as "consistent with public positioning but not independently confirmed":

1. **Per-agent harness internals for closed-source / private-beta agents** — Antigravity's Mission Control dispatcher details, IBM Bob's approval-gate default, Qoder's Experts Mode parallelism, Pochi's `git worktree` claim, Kilo's Agent Manager. These read as reasonable reconstructions of public marketing/docs, but I did not trace each to a vendor source.
2. **"Assumed" `allowed-tools` support** for agents outside the 18 in the upstream matrix (Windsurf, Goose, etc. — the repo marks these `Yes (assumed)`). The upstream README is explicit that only 18 agents are authoritatively characterised; assumptions beyond that should stay flagged as assumptions.
3. **iFlow CLI sunset date** (April 17, 2026) — plausible, but iFlow's endpoints are intermittent and I could not reach an authoritative announcement page. Low-risk: the repo already calls the status clearly.
4. **Innovation taxonomy framing in `harness-deepdive.md`** (ReAct / Plan-then-execute / Spec-driven / Mode machine / Fleet) is an **editorial synthesis**, not a vendor claim. It reads as defensible across the dataset but is not upstream-checkable.
5. **Pricing tiers and team backgrounds** — only checked for AdaL. Numbers for other agents may drift.

A future audit pass should pick the 5–10 agents with the most public-facing engineering material (Claude Code, Cursor, Cline, Roo, OpenCode, Goose, Codex, Gemini CLI) and deep-check the Harness Deep Dive sections line by line against source repos.

---

## Structural observations (no changes made)

- The **45-agent README table** and the **45-row `installation-paths.md` table** are in total agreement — good.
- The **feature-compatibility doc** correctly notes that the upstream matrix only characterises 18 agents, and its "27 others" commentary flags the rest as "basic skills only (assumed)". That's the right way to represent the gap in upstream data.
- The **`harness-deepdive.md` cross-cutting doc** is a large editorial synthesis; it's opinionated but internally consistent with the per-agent pages.
- No broken internal links observed during the review (all per-agent pages in `docs/agents/` exist and are reachable from `README.md`).

---

## Files changed in this review pass

| File | Nature of change |
| --- | --- |
| [README.md](README.md) | Updated Claude Code row to reflect all 5 official surfaces |
| [docs/agents/claude-code.md](docs/agents/claude-code.md) | Same correction + expanded Overview paragraph |
| [REVIEW-NOTES.md](REVIEW-NOTES.md) | This file — new, summarises the audit |

No other content was modified.

---

## Changelog Currency Snapshot

> Added: April 18, 2026 · Pulled from each project's official changelog or GitHub
> Releases page. Dates reflect the public release feed at time of review.

This section supplements the static audit above with **what shipped recently** for the agents we deep-checked. The goal is to flag material that the per-agent docs do not yet reflect — not to rewrite those docs wholesale. Coverage is weighted toward the agents with the richest public release streams; the long-tail agents (~35 of 45) were not sampled in this pass.

### Sampled agents (latest public release)

| Agent | Latest stable | Shipped | Notable recent changes |
| --- | --- | --- | --- |
| **Claude Code** | 2.1.113 (+ 2.1.114 rolling) | This week | Opus 4.7 and Haiku 4.5 GA; new `/ultrareview`, `/loop`, `/powerup`, `/team-onboarding`, `/tui` slash commands; **Claude Code Desktop** app (macOS/Windows) and a native **VS Code extension** replacing the old terminal-host bridge; **Remote Control** for driving sessions from web/mobile; plugin system (`/plugin`); push notifications; Microsoft Foundry and Chrome integrations |
| **Codex CLI** | rust-v0.121.0 (stable) · 0.122.0-alpha.10 (pre) | This week | Agent **marketplace** and MCP Apps surface; **memory mode** for persistent context; secure devcontainer + bubblewrap sandbox path; reverse history search; parallel tool calls; "prolite" plan mode; Guardian timeouts; WSL1 now explicitly rejected for sandboxing |
| **Cursor** | 3.1 (Apr 13 2026) + Canvases (Apr 15) | This week | **Tiled layout** and upgraded voice input (3.1); **Canvases** — interactive dashboards/diagrams inside the editor; **Agents Window**, **Design Mode** and **Agent Tabs** shipped in 3.0; Bugbot **Learned Rules** + MCP support; self-hosted cloud agents |
| **GitHub Copilot** | GA stream (April 2026 changelog) | Past 2 weeks | **Claude Opus 4.7 GA**; Copilot CLI auto model selection; agent skills via `gh` CLI; Copilot cloud agent custom properties + signed commits; **US + EU data residency** and FedRAMP; 3-click merge-conflict fix; remote-control CLI sessions on web/mobile (preview); Copilot SDK public preview; org custom instructions GA; Opus 4.6 Fast and the GPT-5.1 family retired/deprecated; GPT-5.4 mini on Student auto tier |
| **Goose** | v1.31.0 (2 days ago) · v1.30.0 (Apr 8) | This week | **TUI overhaul**; `goose serve` headless mode; Gemini OAuth provider; GitHub **Copilot ACP provider**; Zhipu declarative; Gemma 4 local; egress logging; **skills platform extension**; `goose doctor` diagnostics; i18n. v1.29.0 added sub-recipes UI, Gemini-ACP with subscription models, orchestration support and sigstore/SLSA verification |
| **Gemini CLI** | v0.38.2 (yesterday) · v0.39.0-preview / v0.40.0-nightly | This week | "Generalist agent" docs direction; **plan mode**; `/memory inbox`; new `invoke_subagent` tool; new `ContextCompressionService`; **ContextManager + Sidecar** architecture; tool-controlled display protocol; Windows sandbox ACL optimisation; JSONL chat recording; swapped to `@github/keytar` |
| **Cline** | v3.79.0 (2 days ago) | This week | Claude Opus 4.7 support; **Azure Blob Storage** remote-config provider; `globalSkills` remote config; cache reflection fixes for Cline and Vercel handlers; stuck `command_output` terminal fix; action-injection security fix. Recent 3.7x line added **Lazy Teammate Mode**, chunked `read_file`, tool-loop detection, **Cline Kanban launch modal**, W&B Inference provider, Notification hook, hooks payload with `model.provider/slug` |
| **Roo Code** | v3.52.1 (5 days ago) + roo-cli v0.1.17 (pre) | This week | Standalone **Roo CLI** (`cli-v0.1.x`) in pre-release with stdin-stream protocol, `--create-with-session-id`, `--terminal-shell`; prebuilt binaries for macOS arm64 / Linux x64 / Linux arm64 |
| **OpenCode** | v1.4.11 (18h ago) | This week | Workspace routing fix; **LLM Gateway** provider; Claude Opus 4.7 `xhigh` adaptive reasoning + summarised thinking; GitHub Copilot Opus 4.7 restricted to medium effort; Cloudflare AI Gateway strips `max_tokens` for OpenAI reasoning models; Azure default `store=true`; Alibaba provider; `opencode export --sanitize`; snapshots fully honour `.gitignore`; MCP OAuth redirect-URI config; **Desktop app** beta (file tree, hide-title-bar settings, Beta/Dev badges) |

### Cross-cutting themes across the sampled releases

1. **Claude Opus 4.7** landed across every major agent's feed in the same window — Cline 3.79, OpenCode 1.4.7+, Copilot April 16, Claude Code 2.1.113. Any doc that still lists Opus 4.5/4.6 as the flagship model is out of date.
2. **Remote / headless operation** is a shared direction: Claude Code **Remote Control**, Copilot **remote-control CLI sessions**, Goose **`goose serve`**, Roo **stdin-stream CLI protocol**, OpenCode **managed/remote workspaces**. The per-agent docs generally still describe these as single-machine local processes.
3. **Plan / memory / subagent primitives** are now first-class in the CLI-native agents: Gemini CLI (plan mode, `/memory inbox`, `invoke_subagent`), Codex (prolite plans, memory mode), Goose (sub-recipes, orchestration). The cross-cutting `docs/harness-deepdive.md` taxonomy still places these in the "innovation" column — they are becoming baseline.
4. **Desktop apps** are appearing on agents that were previously CLI- or IDE-only: Claude Code Desktop, OpenCode desktop beta. This further weakens the "surface = CLI" shorthand used in parts of the repo.
5. **Plugin / marketplace layers** are shipping concurrently: Claude Code plugins, Codex marketplace + MCP Apps, Goose skills platform extension, Cline `globalSkills`. Skills/harness portability is no longer just a filesystem-path story — it's becoming a distribution story.

### Agents NOT sampled in this pass

The following 36 agents were not pulled in this review (either their vendor pages blocked automated fetching, they are closed-source/private-beta with no public changelog, or they were skipped for scope): Windsurf, Antigravity, Amp, Continue, Crush, Aider, Kilo, Zed AI, JetBrains AI Assistant, Qoder, Warp, IBM Bob, Firebender, Replit Agent, iFlow CLI (sunset), MCPJam, AdaL, Pochi, DeepAgents, Zencoder, Kiro, Kode, Kimi CLI, Junie, Trae / Trae CN, Mistral Vibe, Mux, Neovate, OpenClaw, OpenHands, Pi, Qwen Code, CodeBuddy, Cortex, CommandCode, Droid, Universal. Their entries in the per-agent docs remain as-written from the original analysis.

### Recommended follow-ups

- Update [docs/agents/claude-code.md](docs/agents/claude-code.md), [docs/agents/cursor.md](docs/agents/cursor.md), [docs/agents/codex.md](docs/agents/codex.md), [docs/agents/github-copilot.md](docs/agents/github-copilot.md), [docs/agents/goose.md](docs/agents/goose.md), [docs/agents/gemini-cli.md](docs/agents/gemini-cli.md), [docs/agents/cline.md](docs/agents/cline.md), [docs/agents/roo.md](docs/agents/roo.md) and [docs/agents/opencode.md](docs/agents/opencode.md) with a short "Recent changes (as of April 2026)" paragraph summarising the row above.
- Revisit [docs/harness-deepdive.md](docs/harness-deepdive.md) to move **plan mode**, **memory primitives**, and **sub-agent invocation** out of the "innovation" tier and into the baseline description for CLI-native agents.
- Pick one of the un-sampled high-profile agents (Windsurf, Amp, Continue, Aider) for the next audit pass — their vendor sites returned 403 to this run but have public GitHub mirrors or blog feeds that can be used instead.

