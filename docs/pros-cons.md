# Per-Agent Pros & Cons

A condensed strengths/weaknesses card for each of the 45 supported agents. These are *opinionated* takes derived from each agent's architecture, ecosystem position, and community signals as of April 2026 — they are not meant to be vendor-neutral.

> Use this page when you're shortlisting agents and want a tactical "what does this thing do well, where does it fall down?" summary. Pair with [`use-cases.md`](./use-cases.md) for scenario-driven recommendations.

---

## How to read this page

Each card has four sections:

- **Pros** — concrete strengths backed by the agent's architecture or ecosystem position.
- **Cons** — concrete weaknesses, friction points, or concerning trade-offs.
- **Best for** — the audience or workflow it's optimized for.
- **Avoid if** — when the agent is genuinely the wrong choice.

Cards are ordered alphabetically by display name. Where pros/cons reference unique architectural features, see the per-agent `Internals & Architecture` section in [`agents/`](./agents).

---

## AdaL

- **Pros**: Self-evolving auto-prompting system reduces the need to hand-author rules; multi-model routing including local; strong privacy story (code stays local); 1000+ MCP tools.
- **Cons**: Auto-prompt-rewrites make debugging "why did it do that?" harder; SaaS subscription pricing for a research-flavoured workflow that suits a narrow segment; relatively young product (limited community signals).
- **Best for**: Solo developers or small teams who want an agent that adapts itself without writing many explicit rules.
- **Avoid if**: You need reproducible/auditable agent behavior or you're allergic to "the prompt changes itself."

## Amp

- **Pros**: Sourcegraph code-graph integration is the strongest cross-repo navigation in the dataset; same skills work across CLI + VS Code + JetBrains; sits in the shared XDG bucket so skills overlap with Kimi CLI / Replit / Universal "for free".
- **Cons**: Sourcegraph dependency means Amp is only as good as the index — large monorepos or dynamic codegen confuse it; pricing only makes sense if you already pay for Sourcegraph.
- **Best for**: Teams already using Sourcegraph who want an agent that can reason about a multi-million-line monorepo.
- **Avoid if**: You're working on a small repo where grep + a vanilla agent is enough.

## Antigravity

- **Pros**: Mission Control fleet UX is unique in the dataset; Gemini 3 model quality is genuinely competitive; per-task verification surfaces issues earlier than per-diff review.
- **Cons**: Preview-only, personal Gmail accounts only; the fleet model is overkill for one-developer-one-task workflows; vendor-locked to Google's gateway.
- **Best for**: Developers who want to supervise multiple parallel agents on independent tasks ("write the migration, fix the test suite, draft the docs — all at once").
- **Avoid if**: You don't want to supervise a fleet — sometimes one agent is plenty.

## Augment

- **Pros**: The Context Engine is the strongest large-monorepo answer in the dataset; multi-IDE support means skills installed once apply across VS Code / JetBrains / Vim / Zed; "Memories" complements skills nicely.
- **Cons**: Cloud-hosted Context Engine adds a "your code visits the vendor" question; expensive at scale; index freshness depends on background sync working.
- **Best for**: Engineers in 1M+ LOC monorepos who need cross-file reasoning that grep can't deliver.
- **Avoid if**: You can't ship code to a third-party indexing service.

## IBM Bob

- **Pros**: Approval-by-default makes it the safest agent for regulated environments; rich folder semantics for skills (templates + checklists + style guides); strong enterprise governance and audit story.
- **Cons**: Approval friction slows down day-to-day use; tied to IBM's identity and pricing; limited model selection compared to BYOK agents.
- **Best for**: Enterprise teams with compliance requirements where every agent action needs an audit trail.
- **Avoid if**: You want a fast, low-friction "just do the thing" agent.

## Claude Code

- **Pros**: Reference implementation of skills — every spec feature works here, including `context: fork` and hooks; excellent docs; plugin marketplace doubles as a skill registry; quality of the Claude family is consistently top-tier.
- **Cons**: Anthropic-only; no IDE integration of its own (depends on host terminal); subscription / API pricing locks you to Anthropic billing.
- **Best for**: Anyone who wants the canonical skills experience, especially advanced users who'll use forks and hooks.
- **Avoid if**: You need provider flexibility or strong IDE integration out of the box.

## Cline

- **Pros**: One of only two agents with hook support; mature MCP integration; per-action approval gates make long autonomous runs safe; very active community + frequent releases.
- **Cons**: Lives inside VS Code, so non-VS-Code users are out; can be slow with many approval cards; UX still matures release-by-release.
- **Best for**: VS Code users who want a configurable, OSS, hookable agent with strong MCP support.
- **Avoid if**: You're not on VS Code or you find approval cards too tedious.

## CodeBuddy

- **Pros**: Three product surfaces (IDE / plugin / CLI) reading the same skill folder; built-in Figma → code; deep Tencent Cloud integration.
- **Cons**: Tencent Cloud is the natural deployment target — outside that ecosystem the integrations don't pay off; primarily aimed at the China market with international polish trailing.
- **Best for**: Teams shipping to Tencent Cloud who want a unified IDE + CLI + plugin experience.
- **Avoid if**: You're not on Tencent Cloud and don't need Figma integration.

## Codex

- **Pros**: Two surfaces (local CLI + Codex Cloud) using the shared `.agents/skills/` bucket; cloud workers parallelize work cleanly; strong AGENTS.md ecosystem alignment.
- **Cons**: OpenAI-only models; cloud workers add billing complexity; the local CLI lacks some polish that Anthropic/Google CLIs ship.
- **Best for**: OpenAI customers who want both local interactive use and cloud-based async fanout.
- **Avoid if**: You want provider choice or you don't have an OpenAI org.

## Command Code

- **Pros**: The Taste system — implicit-preference learning — is genuinely novel; combines well with skills (skills for explicit, Taste for implicit); plan/headless/interactive modes cover most workflows.
- **Cons**: Smaller community than the bigger names; Anthropic-only; "Taste profiles" are an extra concept to learn and maintain.
- **Best for**: Power users who care about coding *style* matching their team's idioms beyond what rules can capture.
- **Avoid if**: You don't have time to nurture a Taste profile or you don't see the value in implicit-preference modeling.

## Continue

- **Pros**: Most "Lego-like" of the IDE extensions — every part is a swappable block; provider-agnostic with strong local-model support (Ollama, llama.cpp); Continue Hub for sharing assistants and skills; Apache 2.0.
- **Cons**: Configuration ceremony is real — `config.yaml` can grow; default UX is less polished than Cursor/Cline; smaller chat UX innovation than newer entrants.
- **Best for**: Teams that want maximum control over which models, tools, and prompts they're shipping; local-LLM enthusiasts.
- **Avoid if**: You want zero-config, always-cloud experience.

## Cortex Code

- **Pros**: Stays inside the Snowflake account boundary — uniquely strong for regulated workloads; Snowflake-aware tools and SQL/Snowpark patterns out of the box; tight integration with the rest of the Snowflake CLI.
- **Cons**: Only useful if you're a Snowflake customer; limited to Snowflake-hosted models; sparse community signal outside Snowflake's own.
- **Best for**: Snowflake-shop developers building Snowpark / Cortex / Snowsight applications.
- **Avoid if**: You aren't a Snowflake customer.

## Crush

- **Pros**: Best-looking terminal UX in the dataset (Charmbracelet polish); LSP-augmented context gives editor-grade symbol navigation; cross-platform including BSD/Android; provider-agnostic with mid-session switching; persistent sessions.
- **Cons**: TUI only — no IDE integration; smaller community than OpenCode; MCP-first means skills are layered on top rather than core.
- **Best for**: Terminal-loving developers who want a polished, cross-platform, LSP-aware OSS agent.
- **Avoid if**: You want IDE integration or hate TUIs.

## Cursor

- **Pros**: Category-defining IDE-agent integration; deep editor APIs (Read/Edit/Search across the open workspace); Background Agents stand in for `context: fork`; hybrid skills layout (project shared + global namespaced); broad model support; mature MCP.
- **Cons**: VS Code fork lock-in; subscription pricing; you're in Anysphere's gateway path for routing.
- **Best for**: Most working developers in 2026 — especially those who want serious agent capabilities without leaving an IDE that already feels like VS Code.
- **Avoid if**: You're committed to JetBrains, vim, or terminal-only workflows.

## Deep Agents

- **Pros**: LangGraph state-machine architecture is the most introspectable / pause-resume-able runtime in the dataset; built-in subagent (`task`), todo, memory, web tools; SDK + CLI; MIT licensed.
- **Cons**: LangChain dependency tree is heavy; the SDK is stronger than the CLI; you're somewhat tied to LangChain's evolution.
- **Best for**: Developers building their own agent products who want a batteries-included starting point.
- **Avoid if**: You want a polished CLI as the primary surface.

## Droid

- **Pros**: Async-friendly invocation (interactive / scripted / cloud); first-class PR generation; clean separation of company/product naming convention.
- **Cons**: Smaller community footprint vs Codex/Claude Code; PR-first orientation means it's overkill for inline edits.
- **Best for**: Engineering teams that run agents from CI to open PRs against their repos.
- **Avoid if**: You want chat-style interactive coding.

## Firebender

- **Pros**: The only agent in the dataset with native Logcat / emulator integration; Android-specific signals (Compose, Gradle, Lint) baked in; strong fit for mobile workflows; YC-backed and well-funded.
- **Cons**: Android Studio / IntelliJ only; over-specialized for non-Android work; uses the shared `.agents/` bucket so cross-pollination of skills is easy *and* mixed.
- **Best for**: Android engineers who want an agent that understands their actual platform.
- **Avoid if**: You're not building Android apps.

## Gemini CLI

- **Pros**: Free with a generous Google account quota; native function-calling protocol means high tool-use reliability; open-source (Apache 2.0); shared `.agents/` bucket.
- **Cons**: Google-only models; CLI UX trails Claude Code/Crush in polish; relies on `gemini.toml` for config which is yet another file.
- **Best for**: Cost-conscious solo developers who want a free, open-source CLI from a major model vendor.
- **Avoid if**: You need provider choice or polished IDE integration.

## GitHub Copilot

- **Pros**: Federated across seven IDE families + CLI + web; benefits from Microsoft / GitHub's distribution; Copilot Workspace is uniquely good at issue → PR flows; service-side state means conversations survive IDE restarts.
- **Cons**: Service-side state means more "your code visits the vendor"; tied to GitHub auth + billing; the historical Copilot UX is uneven across hosts.
- **Best for**: GitHub-centric organizations that need consistent agent UX across many IDEs and the web.
- **Avoid if**: You don't use GitHub or you want everything to stay local.

## Goose

- **Pros**: Linux Foundation governance reduces vendor risk; Rust runtime is fast; 70+ MCP extensions in the registry; multi-provider including Ollama; desktop + CLI shared global folder.
- **Cons**: General-purpose orientation means coding-specific UX trails specialists; MCP-first means skills feel layered-on; fewer "wow" features than younger products.
- **Best for**: Long-horizon enterprise adoption where governance / OSS-stability matter.
- **Avoid if**: You want bleeding-edge UX innovation.

## iFlow CLI

- **Pros** (historical): Bundled multi-model access (Kimi K2 / Qwen 3 / DeepSeek / GLM 4.5); multi-agent orchestration baked in; multimodal.
- **Cons**: **Sunsetting on April 17, 2026 — migrate to Qoder.** Don't start new projects on it.
- **Best for**: Nothing new. Existing users should migrate.
- **Avoid if**: You're starting fresh; pick Qoder or another active CLI.

## Junie

- **Pros**: Deep IntelliJ-platform integration (PSI, refactoring, debugger); LLM-agnostic (Claude / GPT / Gemini / Grok / BYOK); planning mode + live prompting; Custom Subagents stand in for `context: fork`.
- **Cons**: JetBrains-only; subscription tiers ($10–$60/mo) compete with cheaper alternatives; CLI is still beta.
- **Best for**: JetBrains users who want a first-party agent with semantic-refactor strength.
- **Avoid if**: You're not on JetBrains or you don't want yet another subscription.

## Kilo Code

- **Pros**: Portable Core HTTP server lets state survive across surfaces (VS Code → CLI on remote box); Agent Manager runs multiple sessions in parallel; inline code review before apply; 500+ models; OSS.
- **Cons**: Architectural complexity (HTTP server, Agent Manager, 500 providers) means more knobs; younger than Cline/Roo despite ambitious feature set.
- **Best for**: Power users who want multiple parallel agents and cross-surface session continuity.
- **Avoid if**: You prefer a simple, single-loop, single-session agent.

## Kimi Code CLI

- **Pros**: Best fit for Kimi K2 family (long-context MoE); shared `.agents/` and XDG buckets give cross-pollination with Amp / Replit / Universal; clean skill / plugin separation; OSS.
- **Cons**: Tuned for Kimi models — runs other providers but loses the model-loop tightness; Python + TS mixed codebase is a non-trivial install on bare-bones systems.
- **Best for**: Developers who want to get the most out of Kimi K2 specifically.
- **Avoid if**: You prefer a pure single-language CLI or a different model family.

## Kiro CLI

- **Pros**: Spec-driven workflow produces unusually clean implementations on green-field tasks; AWS Bedrock integration; free-tier (50 actions/mo); Project Steering captures longer-lived preferences cleanly.
- **Cons**: Manual JSON wiring of skills (no auto-discovery from install path); AWS-orientation only fits AWS shops; spec-driven flow is overkill for small edits; doesn't enforce `allowed-tools`.
- **Best for**: AWS-shop teams green-field building applications who like requirements → design → plan → code workflows.
- **Avoid if**: You make small edits all day or you're not on AWS.

## Kode

- **Pros**: AGENTS.md-native; plugin marketplace pattern; slash-commands-as-skills makes specific recipes fast to invoke; cross-platform native binaries; 20+ providers.
- **Cons**: YOLO-mode-by-default is unsafe for sensitive projects; smaller community than Crush/OpenCode; less polished UX.
- **Best for**: Power users who want fast slash-command access to many recipes and don't mind YOLO defaults.
- **Avoid if**: You need safety defaults out of the box.

## MCPJam

- **Pros**: Only MCP-testing harness in the dataset — uniquely valuable if you ship MCP servers; OAuth debugger and evaluations are first-class; team workspaces.
- **Cons**: Not really a coding agent in the conventional sense — it's a debugger that ships an embedded agent; if you don't ship MCP servers, you don't need it.
- **Best for**: Teams building MCP servers/integrations.
- **Avoid if**: You don't build MCP servers.

## Mistral Vibe

- **Pros**: 256k context window; plan-first workflow makes long runs reviewable up-front; Devstral Small 2 (24B) runs on a workstation for air-gapped use; OSS CLI; IDE extensions inherit the same skills.
- **Cons**: Devstral 2 model family is competitive but not always frontier; plan-first adds friction for small edits; Vibe's permission model is parallel to but not aligned with `allowed-tools`.
- **Best for**: Privacy-sensitive shops that want a strong local-LLM option from a major vendor.
- **Avoid if**: You want absolute frontier model quality without a plan gate.

## Mux

- **Pros**: Headless one-shot design is uniquely well-suited to CI/automation; runtime selection (local / worktree / SSH / Docker) is the cleanest in the dataset; hard budget + thinking caps.
- **Cons**: Not interactive — wrong choice for chat-style coding; smaller ecosystem than mainstream CLIs; the name collides with Mux Inc. (video streaming).
- **Best for**: CI/CD pipelines, scheduled jobs, scripts that need bounded-cost agent invocations.
- **Avoid if**: You want an interactive chat experience.

## Neovate

- **Pros**: Batteries-included tool set (12 built-ins, 21 slash commands); `skill` is itself a tool the model can call mid-conversation; spec-driven brainstorm → plan → execute; 10+ providers; multi-surface (CLI / Web / Desktop).
- **Cons**: Younger product; the abundance of features is harder to onboard onto than Crush/OpenCode minimalism; pace of change means docs lag.
- **Best for**: Power users who like opinionated, full-featured agents and won't be overwhelmed by lots of knobs.
- **Avoid if**: You prefer minimal surfaces.

## OpenClaw

- **Pros**: Layered skill loader with explicit precedence (rare) makes "which skill wins?" obvious; ClawHub registry; per-agent skill allowlists; reads the shared `.agents/skills/` bucket too.
- **Cons**: Bare `skills/` project path is a relic that conflicts with publisher discovery; smaller community than the mainstream IDEs; proprietary (with OSS pieces, unclear boundary).
- **Best for**: Teams that need fine-grained skill scoping per agent persona.
- **Avoid if**: You want a mainstream IDE with a big community.

## OpenCode

- **Pros**: Polished provider-agnostic CLI; shared `.agents/skills/` bucket; `/skill:name` slash command lets users force-load skills; clean Bun + TypeScript implementation; Apache 2.0; sst-team build quality.
- **Cons**: TUI only — no IDE; younger than Crush in polish; XDG-path users sometimes find Mac UX surprising.
- **Best for**: Terminal users who want a clean OSS CLI with provider flexibility.
- **Avoid if**: You want IDE integration.

## OpenHands

- **Pros**: Sandboxed Docker by default makes long autonomous runs safe; multi-provider via litellm; mature web + CLI + cloud surfaces; the de-facto reference for academic SWE-bench-style benchmarks; MIT licensed.
- **Cons**: Container cold-start latency; the "container = workspace" model creates filesystem distance from your editor; rapid release cadence means breaking changes happen.
- **Best for**: Teams that want a sandboxed agent for long autonomous runs without trusting it on the host.
- **Avoid if**: You need tight editor integration or low-latency interactive editing.

## Pi

- **Pros**: Smallest, easiest-to-embed agent in the dataset; clean SDK for embedding in your own apps; minimal default toolset (`read`/`write`/`edit`/`bash`); subscription auth via `/login`; 34k+ stars.
- **Cons**: Minimal-by-design means most "modern" features are user-installed extensions; smaller community than Crush/OpenCode; Anthropic-first.
- **Best for**: Developers building their own agent products who want a clean base to extend; minimalists.
- **Avoid if**: You want batteries-included — Pi ships almost no batteries.

## Pochi

- **Pros**: Parallel Agents via Git worktrees give a *durable* fork primitive (better than chat-context fork in many ways); BYOK with strong privacy posture; deep GitHub integration; Auto Compact for long conversations.
- **Cons**: VS Code only; smaller community than Cline; the worktree pattern is unusual and takes getting used to.
- **Best for**: VS Code users who want true parallel agents with a real safety net (worktrees > forks).
- **Avoid if**: You're not on VS Code or you find git worktrees confusing.

## Qoder

- **Pros**: 100k-file context engine + 26-hour task budget is the largest in the dataset; Quest Mode + Experts Mode are unique; multi-platform (desktop / JetBrains / CLI); the recommended migration target for iFlow CLI users.
- **Cons**: Multi-mode design is more concept-load than competitors; younger product with smaller ecosystem; CN-led may concern some Western enterprises.
- **Best for**: Long-horizon complex tasks (refactors, migrations) where most agents would have run out of context.
- **Avoid if**: You want a small, focused agent for short edits.

## Qwen Code

- **Pros**: Tightly tuned to Qwen 3 Coder — best out-of-box reliability for that model; AGENTS.md-native; OSS; Alibaba Cloud routing built-in.
- **Cons**: Best with Qwen models specifically; smaller English-language community than Anthropic/OpenAI ecosystems; sparse non-Chinese docs in places.
- **Best for**: Developers committed to Qwen 3 Coder, especially in the China region.
- **Avoid if**: You want frontier-Anthropic/OpenAI quality through a CLI tuned for that family.

## Replit

- **Pros**: Cloud-native and zero-install; skills committed to the repo travel with forks; ideal for shareable working environments; tight deploy integration.
- **Cons**: Cloud-only — no offline; locked into the Replit platform; "global" skills don't really apply because the workstation isn't yours.
- **Best for**: Education, prototyping, and shareable demo environments.
- **Avoid if**: You need offline / local-development workflows.

## Roo Code

- **Pros**: Custom modes (Architect / Coder / Debugger / user-defined); inherits Cline's strengths plus more aggressive UI; mode-as-tool lets a single skill orchestrate persona changes mid-task; OSS.
- **Cons**: No hooks (Cline's headline feature is dropped); fork dynamics mean some Cline improvements take time to land; can feel UI-busy.
- **Best for**: VS Code users who want explicit modes and don't need hooks.
- **Avoid if**: You need hooks or prefer Cline's straighter UX.

## Trae

- **Pros**: Free tier with strong models; multimodal screenshot → code is a real differentiator; sub-200ms autocomplete latency on Apple Silicon; per-step approval gate in Builder mode is safe-by-default.
- **Cons**: VS Code fork lock-in; ByteDance origin raises questions for some Western enterprises; per-step approval slows long runs.
- **Best for**: Designers / front-end developers who want screenshot-to-code workflows and don't mind ByteDance.
- **Avoid if**: ByteDance-origin is a non-starter or you want zero approval friction.

## Trae CN

- **Pros**: Same runtime as Trae, optimized for the China market; project skills shared with Trae for cross-region collaboration.
- **Cons**: All Trae cons, plus regional model pool may not be everyone's preference.
- **Best for**: China-region developers, or teams that need regional infrastructure compliance.
- **Avoid if**: You're not in China and don't have a regional reason to pick CN edition.

## Universal

- **Pros**: A guaranteed install location for spec-conformant tools; ideal for environment provisioners; bets on the spec rather than any one runtime.
- **Cons**: Not actually an agent — relies on side-effect discovery from real agents; doesn't auto-detect.
- **Best for**: Devcontainer / Brewfile / Ansible scripts that want to drop skills "somewhere usable" without enumerating agents.
- **Avoid if**: You're choosing an agent (it's not one).

## Warp

- **Pros**: Terminal-native — the agent inherits your full shell environment, aliases, PATH; shared `~/.agents/skills/` with Cline gives unified terminal + IDE skill story; GPU-accelerated terminal UX.
- **Cons**: macOS / Linux / Windows polish varies; agent runs *as* you, no sandboxing; subscription required for full feature set.
- **Best for**: Terminal-heavy developers who want their agent to live in the same environment as their day-to-day commands.
- **Avoid if**: You want sandboxing or you don't spend much time in the terminal.

## Windsurf

- **Pros**: Cascade's plan-first style is well-suited to multi-step refactors; deep VS Code-fork integration; Cognition's resources backing the product; mature feature set.
- **Cons**: VS Code fork lock-in; legacy `~/.codeium/windsurf/skills/` global path is awkward; Cascade's "plan everything first" is overkill for small edits.
- **Best for**: Teams doing larger refactors who appreciate Cascade's planning surface.
- **Avoid if**: You want quick, reactive edits without a planning gate.

## Zencoder

- **Pros**: Zen Agents framework composes specialized agents into workflows; great fit for enterprise gates (generate → security → format → submit); IDE-side agent picker.
- **Cons**: Doesn't enforce `allowed-tools`; younger product; the workflow concept is unfamiliar to teams used to single-agent chat.
- **Best for**: Enterprise teams with multi-step approval / security / formatting gates.
- **Avoid if**: You want a simple, fast, single-agent chat experience.

---

## Summary axis cards (for shortlisting)

A few cross-cutting comparisons that show up over and over.

### Safest defaults (most conservative)

1. **IBM Bob** — approval-by-default for skills.
2. **Cline / Roo** — per-action approval cards, hookable.
3. **OpenHands** — sandboxed in Docker.
4. **Pochi** — parallel agents in Git worktrees.
5. **Trae Builder mode** — per-step approval gate.

### Fastest / least friction

1. **Kode** — YOLO mode by default.
2. **Crush / OpenCode** — clean TUI, no approval friction.
3. **Cursor** — auto-approve toggle, polished editor integration.
4. **Warp** — agent-in-terminal, no separate panel.
5. **Pi** — minimal default toolset, fast to start.

### Best for cross-repo / monorepo work

1. **Augment** — Context Engine.
2. **Amp** — Sourcegraph code graph.
3. **Qoder** — 100k-file context.
4. **Cursor** — semantic-search across workspace.

### Best for long autonomous runs

1. **Qoder** — Quest Mode, 26-hour budget.
2. **OpenHands** — sandboxed Docker.
3. **Pochi** — worktrees survive failures.
4. **Mistral Vibe** — plan-first workflow.
5. **Antigravity** — fleet with verification.

### Best for OSS / privacy

1. **Continue / OpenCode / Crush / Pi / Cline / Roo / Goose / Pochi / Kode / Kilo / Deep Agents / Mistral Vibe (CLI)** — all OSS, BYOK.
2. **Cortex Code** — stays inside Snowflake account boundary.
3. **OpenHands** — self-host Docker.

### Best for CI / async / automation

1. **Mux** — designed for it.
2. **Droid** — PR generation built-in.
3. **Codex Cloud** — fan-out parallel workers.
4. **Mistral Vibe** — async + programmatic mode.

### Strongest spec implementations

1. **Claude Code** — only one with `context: fork` and full hooks.
2. **Cline** — hooks + `allowed-tools`.
3. **Cursor** — `allowed-tools`, hybrid project/global path, fully spec-aligned.
4. **Codex** — shared bucket + `allowed-tools`.

### Weakest spec implementations

1. **Kiro CLI** — manual JSON wiring required, no `allowed-tools`.
2. **Zencoder** — ignores `allowed-tools`.
3. **OpenClaw** — bare `skills/` path conflicts with publisher discovery.

---

## Where to look next

- [`use-cases.md`](./use-cases.md) — scenario-based "which agent for which problem" guidance.
- [`strengths-comparison.md`](./strengths-comparison.md) — side-by-side capability scoring.
- [`internals-overview.md`](./internals-overview.md) — architecture patterns the agents share.
- The per-agent cards in [`agents/`](./agents) for the full deep-dive.
