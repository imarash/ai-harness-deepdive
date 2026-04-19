# Paper note — *Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems*

> **Citation**: Liu, J., Zhao, X., Shang, X., & Shen, Z. (2026). *Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems*. arXiv preprint [arXiv:2604.14228](https://arxiv.org/abs/2604.14228). VILA Lab, Mohamed bin Zayed University of Artificial Intelligence + University College London. Submitted April 14, 2026.
>
> **Companion code & methodology**: <https://github.com/VILA-Lab/Dive-into-Claude-Code>

This page records what the paper adds to this repo's analysis and points to where each finding has been incorporated. Two of the agents in this repo's 45-agent dataset — **Claude Code** and **OpenClaw** — are the paper's central case study and structural counterpoint, so its findings update both per-agent pages and the cross-cutting [`harness-deepdive.md`](../harness-deepdive.md).

The paper is a **source-level reverse-engineering study** of Claude Code v2.1.88 (≈1,884 TypeScript files, ≈512K LOC, extracted from the publicly distributed npm package). It uses OpenClaw — an independent open-source multi-channel agent gateway — as a structural foil. All claims marked "Tier B" in the paper cite specific source files and functions, which makes its architectural inventory the most authoritative public reference for Claude Code's harness internals as of April 2026.

---

## What the paper actually claims

The authors organize the analysis around five **human values** the architecture is built to serve, traced through **thirteen design principles** and into specific source files:

| Value | What it means in the architecture |
| --- | --- |
| **Human Decision Authority** | Principal hierarchy (Anthropic → operators → users); deny-first permissions; observable, interruptible, auditable actions |
| **Safety, Security, Privacy** | Defense in depth even when the human is inattentive; threat model targets overeager behavior, honest mistakes, prompt injection, and model misalignment |
| **Reliable Execution** | Coherent across context boundaries, sessions, and subagent delegation; "ground truth from the environment" each step; separation of generation from evaluation |
| **Capability Amplification** | "Unix-utility" composition; ~27% of internal-use tasks would not have been attempted without the tool |
| **Contextual Adaptability** | Trust trajectories rather than fixed trust states (auto-approve rises ~20% → ~40% from <50 to 750 sessions); multi-tier extension architecture |

A sixth concern — **long-term human capability preservation** — is treated as an *evaluative lens*, not a co-equal value, on the basis that the architecture provides limited mechanisms that explicitly preserve long-term human understanding (see paper §2.4 and §11.2 for the empirical literature: Becker et al. 2025; Shen 2026; He 2026; Kosmyna 2025; Liu 2026).

### The concrete inventory

The paper's inventory is what's most useful for this repo, because it lets us replace plausible reconstruction with source-grounded counts:

| Item | Count / Detail | Source files cited |
| --- | --- | --- |
| Decision-logic share of codebase | **≈1.6%** of LOC; **≈98.4%** is operational harness | community analysis, summary in §11.1 |
| Permission modes | **7** (`plan`, `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, internal `bubble`); 5 always external, `auto` gated by `TRANSCRIPT_CLASSIFIER`, `bubble` is subagent-only | `permissions.ts`, `types/permissions.ts` |
| Independent safety layers | **7** stages (tool pre-filter → deny-first rules → mode constraints → auto-mode classifier → shell sandbox → no-permission-restoration on resume → hooks) | `tools.ts`, `permissions.ts`, `shouldUseSandbox.ts`, `conversationRecovery.ts`, `types/hooks.ts` |
| Pre-model context shapers | **5** (`budget reduction → snip → microcompact → context collapse → auto-compact`) — gated by `HISTORY_SNIP`, `CACHED_MICROCOMPACT`, `CONTEXT_COLLAPSE`, user-config respectively | `query.ts:365–453`, `compact.ts` |
| Hook events | **27** total, **15** with rich Zod-validated output schemas; **5** participate in the permission flow (`PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionDenied`, `PermissionRequest`); persisted hook command types: `command` / `prompt` / `http` / `agent`; runtime-only `callback` for SDK | `coreTypes.ts`, `coreSchemas.ts`, `types/hooks.ts`, `schemas/hooks.ts`, `utils/hooks.ts` |
| Built-in tools | up to **54** (19 always included, 35 conditional on flags / user type) | `tools.ts` (`getAllBaseTools`, `assembleToolPool`, `filterToolsByDenyRules`) |
| Plugin component types | **10** (`commands`, `agents`, `skills`, `hooks`, `mcpServers`, `lspServers`, `outputStyles`, `channels`, `settings`, `userConfig`) | `utils/plugins/schemas.ts`, `utils/plugins/pluginLoader.ts` |
| MCP transport types | stdio, SSE, HTTP, WebSocket, SDK, plus IDE-specific `sse-ide` / `ws-ide` and an internal `claudeai-proxy` | `services/mcp/client.ts` |
| Built-in subagent types | up to **6** (`Explore`, `Plan`, `general-purpose`, `Claude Code Guide`, `Verification`, `Statusline-setup`) | `AgentTool.tsx`, `runAgent.ts` (21 parameters), `loadAgentsDir.ts` |
| Subagent isolation modes | **3** (`worktree` / `remote` (internal-only) / in-process default) | `AgentTool.tsx`, `runAgent.ts` |
| CLAUDE.md tiers | **4** (managed / user / project / local), with `@include` directive and lazy-load for path-scoped rules below CWD | `claudemd.ts`, `context.ts` |
| Session storage | mostly append-only **JSONL** at `<projectDir>/<sessionId>.jsonl`, with sidechain `.jsonl + .meta.json` per subagent and global `history.jsonl` | `sessionStorage.ts`, `history.ts`, `conversationRecovery.ts` |
| File-history snapshots | At `~/.claude/file-history/...` for `--rewind-files`; **not** a generic checkpoint store | `sessionStorage.ts` |
| Surfaces, all sharing one `query()` path | Interactive CLI, headless `claude -p` (via `QueryEngine`), Agent SDK, IDE / Desktop / Browser | `src/entrypoints/`, `query.ts`, `QueryEngine.ts` |

The core agent loop is one `queryLoop()` async generator in `query.ts` (~68 KB) with **seven continue points**, each overwriting a single `State` object in a whole-object assignment. Every interface — interactive CLI, headless, SDK, IDE — converges on the same loop; only the rendering layer varies. `QueryEngine` is a *conversation wrapper* for non-interactive surfaces, not the engine itself.

### Where this differs from prior public descriptions

Two specifics from the paper update or extend the picture this repo previously had:

1. **The compaction pipeline is graduated, not single-shot.** Auto-compact (the user-visible "summarize and continue") is the *fifth* layer, not the only one. Earlier shapers — per-tool-result budget reduction (always on), `snip` (lightweight history trim), `microcompact` (cache-aware fine-grained), `context collapse` (read-time virtual projection over the full history) — fire first and do most of the work invisibly. Context collapse in particular leaves no user-visible artifact: the source comments call it "a read-time projection over the REPL's full history."
2. **Sidechain transcripts are how subagent isolation is implemented in practice.** Each subagent writes its own `.jsonl + .meta.json` pair; only the final summary text returns to the parent's context. This is the mechanism behind the "summary-only return" that costs ≈7× the tokens of a standard session in plan mode (per Anthropic's own *agent teams* report cited in the paper).

### The OpenClaw counterpoint

The paper's most useful contribution to *this* repo is the structural comparison with OpenClaw — because OpenClaw is in our 45-agent dataset and the paper characterizes it very differently from this repo's prior framing.

The paper describes OpenClaw as a **persistent local-first WebSocket gateway** (default loopback port 18789) that connects roughly two dozen messaging surfaces — WhatsApp, Telegram, Slack, Discord, Signal, etc. — to an embedded **Pi-agent runner**, with companion macOS/iOS/Android apps. Where Claude Code is a CLI coding harness bound to a single repo session, OpenClaw is "a persistent control plane for multi-channel personal assistance." Six dimensions of the contrast:

| Dimension | Claude Code | OpenClaw |
| --- | --- | --- |
| **System scope** | Ephemeral CLI/IDE process per session | Persistent WS gateway daemon, multi-channel control plane |
| **Trust model** | Deny-first per-action evaluation + 7 modes + ML classifier | Single trusted operator per gateway; perimeter access control (DM pairing, sender allowlists, gateway auth); opt-in sandboxing (Docker / SSH / OpenShell), configurable per-agent / per-session / shared |
| **Agent runtime** | `queryLoop()` is the system center | Pi-agent runner is *embedded inside* a gateway RPC dispatch layer; per-session queue serialization with optional global lane |
| **Extension** | 4 mechanisms × graduated context cost (MCP, plugins, skills, hooks) | Manifest-first plugin system with **12 capability types**, central registry; separate skills layer; built-in `openclaw mcp` for inbound + outbound MCP |
| **Memory** | 4-tier `CLAUDE.md` + 5-layer compaction + LLM scan of file headers | Workspace bootstrap files (`AGENTS.md`, `SOUL.md`, `TOOLS.md`, `IDENTITY.md`, `USER.md`, conditionally `BOOTSTRAP.md`, `HEARTBEAT.md`, `MEMORY.md`); separate memory system (`MEMORY.md` + `memory/YYYY-MM-DD.md` daily notes + optional `DREAMS.md`); hybrid vector + keyword search when an embedding provider is configured; **experimental "dreaming"** background promotion from short-term recall to long-term `MEMORY.md` |
| **Multi-agent** | Subagents = subordinate workers (worktree isolation, summary-only return) | Two distinct concerns split apart: (a) **routing** between fully isolated agents bound to channels, (b) **delegation** with configurable nesting depth (max 5, default 1, recommended 2), thread-bound on supported channels |

**The most consequential cross-finding for this repo**: OpenClaw can host Claude Code, OpenAI Codex, and Gemini CLI as external coding harnesses through its **ACP (Agent Client Protocol)** integration. The two systems are *composable*, not exclusive alternatives. This re-frames what we mean by "the harness" — gateway-level systems and task-level harnesses can stack.

### Six open design directions the paper names

For anyone using this repo to evaluate where the field is heading, the paper closes with six concrete open questions, each tied to a literature anchor:

1. **Observability vs. evaluation gap** — 89% adoption of observability vs. 52% for offline evaluation (LangChain *State of Agent Engineering* 2026); the paper argues the gap closes at the harness layer (more hook events for generator-evaluator separation) rather than at the model layer.
2. **Cross-session persistence** — between the per-session transcript and the static `CLAUDE.md`, what should an *experiential* tier look like? (Hu 2025 memory survey; Zhang 2026 ACE; Packer 2024 MemGPT; Chhikara 2025 mem0.)
3. **Harness boundary evolution** — Anthropic's own *Managed Agents* essay reads as a virtualization trajectory analogous to OS abstractions over hardware; the paper expects "where / when / what / with-whom" to fragment into specialized stacks.
4. **Horizon scaling** — from session-scoped Reliable Execution to multi-week scientific programs (Lu 2024 AI Scientist; Gottweis 2025 Co-scientist; Kwa 2025 METR time-horizon).
5. **Governance** — EU AI Act fully applies August 2026; only 13.3% of agentic systems publish safety cards (MIT AI Agent Index); session transcripts are *internally* auditable but not yet *externally* auditable.
6. **Long-term human capability** — the evaluative lens, made explicit: should agent harnesses surface per-session signals for comprehension drift / convention violation, and is the harness even the right locus for that intervention?

---

## Where this paper lands in the report

| Doc | Update |
| --- | --- |
| [`docs/agents/claude-code.md`](../agents/claude-code.md) | Internals, harness deep dive, and innovation summary upgraded with paper-grounded counts (7 modes, 5 shapers, 27 hooks, 54 tools, sidechains, 1.6/98.4 ratio, `queryLoop` vs `QueryEngine`). |
| [`docs/agents/openclaw.md`](../agents/openclaw.md) | Surface re-characterized as **multi-channel WS gateway**, not "Native AI IDE." Adds Pi-agent runner, 12 plugin capability types, dreaming subsystem, ACP coding-harness hosting, gateway perimeter trust model. |
| [`docs/harness-deepdive.md`](../harness-deepdive.md) | Cross-references the paper's 5-value / 13-principle frame and the OpenClaw structural contrast. |
| [`README.md`](../../README.md) | OpenClaw row updated; paper added to *Source data*. |
| [`REVIEW-NOTES.md`](../../REVIEW-NOTES.md) | Audit pass logged, with the OpenClaw correction flagged. |

---

## Methodology caveat

The paper is explicit about its own evidence tiers (§16):

- **Tier A** — Anthropic's product docs and engineering essays. Establish intent, not implementation.
- **Tier B** — Specific files / functions in the extracted v2.1.88 source. Strongest tier.
- **Tier C** — Reconstruction (community analysis, OpenClaw structural comparison, code-pattern inference). Hedged in the prose.

Findings imported into this repo from the paper are Tier B unless otherwise noted, but two systemic limits apply: (i) the analysis is a **static snapshot** of v2.1.88 with feature flags mid-rollout (`TRANSCRIPT_CLASSIFIER`, `CONTEXT_COLLAPSE`, `CACHED_MICROCOMPACT`, `BASH_CLASSIFIER`, `KAIROS`, etc.) — different builds may have functionally different applications; and (ii) reverse-engineered source reveals control flow but not design intent or production-flag prevalence. Any claim derived from the paper that this repo restates is therefore valid for v2.1.88 with the cited feature flags active, not for *every* shipping Claude Code build. The reader of this repo should treat the per-agent page as the operational summary and this paper note as the source-grounded reference.
