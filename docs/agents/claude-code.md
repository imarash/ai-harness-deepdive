# Claude Code

> **Slug**: `claude-code` · **Surface**: Terminal CLI + VS Code + JetBrains + Desktop app + Web · **Vendor**: Anthropic · **License**: Proprietary

The reference implementation of the Agent Skills spec. The skill format originated as a Claude Code feature and was later promoted into the cross-agent standard.

## Overview

Claude Code is Anthropic's official coding agent. It launched as a developer preview in early 2025 starting with the Terminal CLI and has since expanded to VS Code, JetBrains, a standalone Desktop app, and a hosted Web experience — all backed by the same underlying Claude Code engine, so `CLAUDE.md`, settings, and MCP servers work across every surface. It remains the most-feature-complete implementation of skills — every part of the spec works here, including the parts no other agent ships.

## Skills support

| Item | Value |
| --- | --- |
| Project path | `.claude/skills/` |
| Global path | `~/.claude/skills/` |
| `--agent` slug | `claude-code` |
| `allowed-tools` | Yes |
| `context: fork` | **Yes (only agent)** |
| Hooks | **Yes** |

### `context: fork`

Claude Code's signature feature. Add `context: fork` to your skill frontmatter and the skill runs in a sub-context — a fresh window with a fresh token budget — and returns a summary to the parent. Use it for long, mechanical refactors that would otherwise blow the parent context.

### Hooks

Pre/post-skill, pre/post-tool, and PostFile hooks declared in `~/.claude/settings.json` or per-project. Useful for automated formatting, audit logs, and policy enforcement.

## Installation

Skills install into `.claude/skills/` (project) or `~/.claude/skills/` (global). They are immediately discoverable by the agent on the next session — no restart required.

```bash
npx skills add vercel-labs/agent-skills -a claude-code
```

## Notable behavior

- Anthropic's plugin marketplace (`.claude-plugin/marketplace.json`) is *natively* understood by `npx skills`, so any Claude Code plugin's declared skills become installable into all 45 agents.
- Skill activation is auto + descriptive: the `description` field is what Claude reads to decide whether to load.
- The CLI honors `allowed-tools` strictly — listed tools are made available, others are not.

## Internals & Architecture

Claude Code is a single agent process — multiple surfaces (Terminal CLI, headless `claude -p`, Agent SDK, IDE / Desktop / Browser) all converge on **one** `queryLoop()` async generator (`query.ts`, ≈68 KB). Its design is unusually transparent because Anthropic publishes the product-level engineering logic and a community has extracted the v2.1.88 TypeScript source (≈1,884 files, ≈512K LOC); a recent source-level study ([Liu et al. 2026, arXiv:2604.14228](../papers/dive-into-claude-code.md)) estimates **≈1.6% of the codebase is decision logic and ≈98.4% is operational harness** — a ratio that captures the design philosophy precisely. The agent loop is a tight read–evaluate–tool cycle on top of the Anthropic Messages API, with a deterministic skills loader injected into the system prompt and a `Task`/`fork` primitive that spawns nested agent processes when a skill or the model requests it. Hooks and MCP are bolted in as orthogonal extension points.

```mermaid
flowchart TD
    User([User prompt]) --> CLI[claude CLI process]
    CLI --> SkillsLoader[Skills loader<br/>scan .claude/skills/<br/>+ ~/.claude/skills/]
    SkillsLoader --> Frontmatter[Parse YAML frontmatter<br/>name, description, allowed-tools, context]
    Frontmatter --> SystemPrompt[System prompt builder<br/>injects only name+description<br/>progressive disclosure]
    SystemPrompt --> AnthropicAPI[Anthropic Messages API<br/>Claude Sonnet/Opus]
    AnthropicAPI --> Decision{Model decides}
    Decision -->|load skill| FetchBody[Read SKILL.md body<br/>+ allowed-tools restriction]
    FetchBody --> AnthropicAPI
    Decision -->|tool call| Hooks1[PreTool hook]
    Hooks1 --> ToolExec[Tool executor<br/>Read/Write/Edit/Bash/Task/MCP]
    ToolExec --> Hooks2[PostTool hook]
    Hooks2 --> AnthropicAPI
    Decision -->|context: fork| ForkAgent[Spawn subordinate context<br/>fresh window, fresh budget]
    ForkAgent --> Summary[Returns summary only]
    Summary --> AnthropicAPI
    Decision -->|done| Output([Result + PostSkill hook])
    MCP[(MCP servers)] -.->|tools| ToolExec
    Marketplace[(.claude-plugin<br/>marketplace.json)] -.->|skills| SkillsLoader
```

The arrows that matter most: `context: fork` is the only true sub-context primitive in the entire dataset, and the hook sequence (`PreSkill → PreTool → PostTool → PostFile → PostSkill`) is what no other agent reproduces. Together they make Claude Code the spec's reference implementation in *behavior*, not just file format.

## Harness Deep Dive

> Counts and source-file references in this section are taken from Liu et al. 2026's source-level analysis of v2.1.88 ([paper note](../papers/dive-into-claude-code.md)), which traces each subsystem to specific files in the extracted TypeScript codebase (`query.ts`, `permissions.ts`, `tools.ts`, `compact.ts`, `AgentTool.tsx`, `runAgent.ts`, `sessionStorage.ts`, `services/mcp/client.ts`, `claudemd.ts`, etc.). Feature-flagged subsystems are noted explicitly because different builds may ship them off; the per-flag list lives in the paper note.

### Agent loop

- **Shape**: ReAct (think → tool → observe), implemented as a single `queryLoop()` async generator (`query.ts`) with **seven continue points**, each overwriting one `State` object via whole-object assignment rather than field-level mutation.
- **One loop, every surface**: Interactive CLI, headless `claude -p` (wrapped by `QueryEngine`), Agent SDK, and IDE/Desktop/Browser all call the same `query()` path; only the rendering layer varies. `QueryEngine` is a *conversation wrapper* for non-interactive surfaces, not a separate engine.
- **Tool-call style**: Native function calling on the Anthropic Messages API. No XML/JSON parsing fallbacks — Anthropic-only by design.
- **Tool dispatch**: Primary path is `StreamingToolExecutor` — tools begin executing as they stream from the model. Read-only tools run concurrently; state-modifying tools (e.g. `Bash`) are serialized. A *sibling abort controller* terminates in-flight subprocesses if any `Bash` tool errors. Results are buffered and emitted in tool-use order regardless of finish order. Fallback path: `runTools()` in `toolOrchestration.ts`.
- **Halting**: Five stop conditions — no tool use (text-only response), `maxTurns` reached, `prompt_too_long` from the API, a `PostToolUse` hook setting `hook_stopped_continuation`, or `abortController` firing.
- **Recovery**: Up to three max-output-token escalations per turn (`MAX_OUTPUT_TOKENS_RECOVERY_LIMIT = 3`); reactive compaction at most once per turn (gated by `REACTIVE_COMPACT`); `prompt_too_long` triggers context-collapse overflow + reactive compaction before terminating; configurable `fallbackModel` if the primary fails.
- **Streaming**: Generator yields `StreamEvent`, `RequestStartEvent`, `Message`, `TombstoneMessage`, and `ToolUseSummaryMessage` events.

### Context & memory

- **Context strategy**: Aggressive **Anthropic prompt-cache** use on the system prompt + skills + `CLAUDE.md`. The stable prefix is cached across turns so only the delta gets billed. Memoized context loaders (`getSystemContext`, `getUserContext` in `context.ts`, both via lodash `memoize`) cache git status and CLAUDE.md content per session.
- **`CLAUDE.md` hierarchy** (`claudemd.ts`): four tiers, loaded in *reverse priority* so closer-to-CWD files get more model attention.
  1. **Managed** (`/etc/claude-code/CLAUDE.md` on Linux) — OS-level policy.
  2. **User** (`~/.claude/CLAUDE.md`) — private global.
  3. **Project** (`CLAUDE.md`, `.claude/CLAUDE.md`, `.claude/rules/*.md`) — checked-in.
  4. **Local** (`CLAUDE.local.md`) — gitignored, private project-specific.
  Files below CWD lazy-load when the agent reads files in matching directories. `@include` directive (`@path`, `@./relative`, `@~/home`, `@/absolute`) supports modular instruction sets, leaf-text-only, with cycle prevention. CLAUDE.md content is delivered as a **user-context message**, not as system prompt — making compliance probabilistic; permission rules provide the deterministic enforcement layer.
- **Auto memory**: separate from CLAUDE.md. LLM-based scan of memory-file headers selects up to **5** relevant files per turn (no embeddings, no vector index — file-level granularity).
- **Compaction — five-layer pipeline** (`query.ts:365–453`, `compact.ts`), in execution order with feature-flag gates:
  1. **Budget reduction** (always on) — per-tool-result size limits; oversized outputs replaced by content references and persisted for resume reconstruction.
  2. **Snip** (`HISTORY_SNIP`) — lightweight older-history trim; explicitly plumbs `tokensFreed` to auto-compact because the main token counter reads `usage` from the surviving assistant message.
  3. **Microcompact** (`CACHED_MICROCOMPACT`) — fine-grained cache-aware compression; defers boundary messages until after the API response so they can use real `cache_deleted_input_tokens`.
  4. **Context collapse** (`CONTEXT_COLLAPSE`) — *read-time virtual projection* over the full REPL history; the full history is preserved on disk, the model just sees a collapsed view. **No user-visible artifact.**
  5. **Auto-compact** (default-on, user-disableable) — full model-generated summary via `compactConversation()`; emits a `compact_boundary` annotated with `headUuid / anchorUuid / tailUuid` for read-time chain patching.
- **Sub-context primitives**: **`context: fork`** is the only frontmatter-declarative fork primitive in the entire dataset. The **`Agent`** tool (`AgentTool.tsx`, `Task` retained as legacy alias) spawns subordinate contexts on demand with three isolation modes: `worktree` (temporary git worktree), `remote` (internal-only), and in-process default. Both return summary text only.
- **Sidechain transcripts**: each subagent writes its own `.jsonl + .meta.json` pair (`sessionStorage.ts:247`) so subagent history never inflates the parent's context window. This is *the* mechanism behind summary-only return.
- **Cross-session memory**: None beyond `CLAUDE.md`, skills, and the append-only transcript on disk. Resume / fork explicitly **do not** restore session-scoped permissions (`conversationRecovery.ts`) — sessions are isolated trust domains by design.

### Tool runtime

- **Tool pool size**: up to **54** built-in tools — **19 always included** (`AgentTool`, `BashTool`, `FileReadTool`, `FileEditTool`, `FileWriteTool`, `SkillTool`, `WebFetchTool`, `WebSearchTool`, etc.) and **35 conditional** on feature flags / user type / build. Simple mode (`CLAUDE_CODE_SIMPLE`) collapses to **3** (`Bash`, `Read`, `Edit`).
- **Assembly** (`assembleToolPool` in `tools.ts`): five-step pipeline — base enumeration → mode filter → deny-rule pre-filter → MCP merge → name-based dedup (built-ins win). MCP server-prefix denies (`mcp__server`) strip whole servers from the model's view at assembly time.
- **Subagent types**: up to **6** built-in (`Explore`, `Plan`, `general-purpose`, `Claude Code Guide`, `Verification`, `Statusline-setup`) plus user-defined `.claude/agents/*.md` and plugin-contributed agents. `runAgent()` accepts **21 parameters** spanning prompts, tools, model, isolation, hooks, memory scope.
- **Parallelism**: Concurrent for read-only tools, serial for state-modifying ones. Subagents are the cross-process parallelism story; for multi-instance agent teams, coordination is **file-lock-based** (no message broker, no distributed coordinator), trading throughput for zero-dependency deployment.
- **Permission system — 7 modes** (`types/permissions.ts`): `plan`, `default`, `acceptEdits`, `auto` (gated by `TRANSCRIPT_CLASSIFIER`), `dontAsk`, `bypassPermissions`, plus internal-only `bubble` for subagent-to-parent escalation. Evaluation is **deny-first**: deny rules always beat allow rules even when the allow is more specific.
- **Permission pipeline — 7 independent layers**: tool pre-filter → deny-first rule eval → mode constraints → ML auto-mode classifier (`yoloClassifier.ts`) → shell sandbox (`shouldUseSandbox.ts`) → no-restoration-on-resume → hook interception. `--dangerously-skip-permissions` (legacy) maps onto `bypassPermissions` mode and still respects safety-critical and bypass-immune checks.
- **Hooks**: **27 event types** (`coreTypes.ts`) — **15** with rich Zod-validated output schemas, **5** participate in the permission flow (`PreToolUse`, `PostToolUse`, `PostToolUseFailure`, `PermissionDenied`, `PermissionRequest`). Persisted hook command types: `command` (shell), `prompt` (LLM), `http`, `agent` (agentic verifier). Runtime-only `callback` for SDK / internal instrumentation. Hook sources: `settings.json`, plugins, managed policy, plus dynamic registration from skills.
- **Sandbox**: Optional shell sandbox (`shouldUseSandbox.ts`) provides filesystem + network isolation, *independent* of the application-level permission model. A command can be permission-approved but still sandboxed. Anthropic reports sandboxing reduced permission prompts by **≈84%** (cited in paper §11.3 from `anthropic2025sandboxing`).
- **MCP**: First-class. Multi-transport — **stdio, SSE, HTTP, WebSocket, SDK, sse-ide, ws-ide, claudeai-proxy** (`services/mcp/client.ts`). Servers configured at project / user / local / enterprise scopes plus runtime-merged plugin and `claude.ai` servers. Dedicated `ListMcpResourcesTool` and `ReadMcpResourceTool` for non-tool resources.
- **Plugin manifest**: **10 component types** (`commands`, `agents`, `skills`, `hooks`, `mcpServers`, `lspServers`, `outputStyles`, `channels`, `settings`, `userConfig`) — a single plugin can extend Claude Code across all three loop injection points (`assemble` / `model` / `execute`).

### Model integration

- **Provider model**: Anthropic-only (Claude Sonnet / Opus). No router, no BYOK, no local-model story.
- **Caching**: Heavy use of Anthropic prompt caching — typically 5–10× cost savings on long sessions. Cache-aware compaction (microcompact) actively avoids invalidating prefix caches.
- **Multi-model**: No mid-session swap on the primary path, but `fallbackModel` lets the recovery layer switch on failure.

### Session persistence

- **Storage**: Mostly append-only **JSONL** at `<projectDir>/<sessionId>.jsonl` (`sessionStorage.ts`), with explicit cleanup rewrites as the only exception. Three independent channels: session transcripts (project-scoped), global prompt history (`history.jsonl` for Up-arrow / Ctrl-R, reverse-iterated via `readLinesReverse`), and subagent sidechains.
- **Session identity**: `sessionId` paired with `sessionProjectDir`, set together during resume / branch — the transcript path must use the same project directory active when messages were written, or hooks look in the wrong directory.
- **Resume vs. fork**: `--resume` replays the transcript (`conversationRecovery.ts`); fork creates a new session from an existing one (`commands/branch/branch.ts`). Neither restores session-scoped permissions — re-grant required.
- **Checkpoints**: `~/.claude/file-history/...` exists for `--rewind-files` only — **file-level snapshots**, not a generic checkpoint store.

### Innovation summary

The reference implementation. **`context: fork` + the full hook lifecycle + the plugin marketplace** are the three things no other harness ships at the same depth. The deeper architectural story — captured by Liu et al. 2026's source-level study — is the **≈1.6% / 98.4% ratio**: almost the entire codebase is the operational harness around a thin agent loop. The graduated stacks (5-layer compaction, 7-layer permission pipeline, 4-mechanism extensibility ordered by context cost, 3-mode subagent isolation) are what no other agent in this dataset reproduces simultaneously. Skills as a file format originated here; everything else in this dataset is, in some sense, in conversation with what Anthropic shipped first.

## Documentation

- [Claude Code Skills](https://code.claude.com/docs/en/skills)
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Anthropic engineering blog](https://www.anthropic.com/engineering)
- [How Claude Code Works](https://code.claude.com/docs/en/how-claude-code-works) — Anthropic's product-level account of the agentic loop
- Liu, Zhao, Shang & Shen, *Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems* — [arXiv:2604.14228](https://arxiv.org/abs/2604.14228) · source-level analysis of v2.1.88, OpenClaw structural contrast, six open design directions. **In-repo notes**: [`docs/papers/dive-into-claude-code.md`](../papers/dive-into-claude-code.md).
