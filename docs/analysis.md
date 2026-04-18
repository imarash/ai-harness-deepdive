# Cross-Cutting Analysis

After mapping all 45 agents into the same documentation shape, several patterns emerge that aren't visible from any one agent's docs alone. This page summarizes what the dataset reveals.

---

## 1. The skill ecosystem is now bigger than any single agent

Forty-five agents share one install convention. That includes:

- **All four major LLM vendors** (Anthropic / Claude Code, OpenAI / Codex, Google / Gemini CLI + Antigravity, Mistral / Vibe).
- **Three of the four hyperscalers** (AWS / Kiro, Microsoft / GitHub Copilot, Google / multiple). IBM is in via Bob; Snowflake is in via Cortex.
- **The big Chinese platforms** (ByteDance / Trae, Tencent / CodeBuddy, Alibaba / Qwen Code, Moonshot / Kimi CLI).
- **Most of the prominent indie AI IDEs** (Cursor, Windsurf, Replit, Augment, Zencoder, Qoder).
- **The mainstream OSS coding agents** (Cline, Roo, Continue, Aider-likes via OpenCode/Crush/Pi, OpenHands, Goose).

This is unusually high adoption for a 1-year-old standard. The reason it took: skills are *additive* (they don't conflict with existing rules systems), they're *file-based* (no SDK to depend on), and the install path is *namespace-segregated by default* (each agent picks its own folder, so you can install for many at once).

---

## 2. The "agents bucket" pattern (`.agents/skills/`)

15 of 45 agents read project-level skills from `.agents/skills/`. That is now the de-facto standard cross-agent project bucket. The members:

> Amp, Antigravity, Cline, Codex, Cursor, Deep Agents, Firebender, Gemini CLI, GitHub Copilot, Kimi CLI, OpenCode, Replit, Universal, Warp, plus Cursor at the project level.

The pragmatic implication: **if you ship one folder of team skills, putting it at `.agents/skills/` reaches one-third of the ecosystem on day one**. The remaining two-thirds use namespaced folders (`.cursor/`, `.windsurf/`, `.continue/`, etc.), but the CLI handles symlinking those automatically.

The presence of a `Universal` agent slug whose path *is* `.agents/skills/` confirms that this is the intended fallback bucket for any agent that wants to opt-in to the convention without changing its own filesystem layout.

---

## 3. Three families of global path layouts

Looking only at global paths (`-g`), the 45 agents cluster into three groups:

| Family | Count | Pattern | Examples |
| --- | --- | --- | --- |
| Simple (`~/.<slug>/skills/`) | ~30 | The "we own a directory" approach | `~/.claude/skills/`, `~/.junie/skills/`, `~/.cursor/skills/` |
| XDG-style (`~/.config/<slug>/skills/`) | 7 | Linux-canonical, less common on macOS | `~/.config/crush/skills/`, `~/.config/goose/skills/`, `~/.config/opencode/skills/`, `~/.config/agents/skills/` (4 sharing) |
| Vendor-nested | 5 | Path reflects company hierarchy | `~/.gemini/antigravity/skills/`, `~/.snowflake/cortex/skills/`, `~/.codeium/windsurf/skills/`, `~/.pi/agent/skills/`, `~/.deepagents/agent/skills/` |

The XDG-style group is interesting: it's a deliberate Linux-friendly choice from polished, OSS-leaning agents (Crush, Goose, OpenCode). The vendor-nested group reflects organizational acquisition (Windsurf was Codeium; Antigravity is Google's, hence under `.gemini/`).

---

## 4. China vs the rest

Six of the 45 agents are from Chinese vendors:

| Agent | Vendor | Notable |
| --- | --- | --- |
| Trae & Trae CN | ByteDance | Mainstream global + CN edition |
| CodeBuddy | Tencent Cloud | Three product variants (IDE / Plugin / CLI) |
| Qwen Code | Alibaba | Tuned for Qwen-3-Coder |
| Kimi Code CLI | Moonshot | Python-based; ships with skills baked in |
| iFlow CLI | iFlow.cn | **Sunsetting on April 17, 2026** — migration to Qoder |
| (Qoder) | Qoder | International, but CN-led; iFlow's successor |

The fact that all six implemented the same skills convention as Anthropic/OpenAI/Google is the clearest sign that the spec hit on something that transcends ecosystem politics. The skill format is small enough that there's no IP fight to have over it.

---

## 5. Surfaces are blurring

Only a few years ago, "AI coding tool" cleanly meant either an IDE extension (Copilot) or a chat box (ChatGPT). The 2026 landscape is much messier:

| Surface | Examples |
| --- | --- |
| Pure CLI / TUI | Crush, OpenCode, Pi, Mux, Kimi CLI, Cortex Code |
| CLI + IDE extension | Amp, Junie, Mistral Vibe, GitHub Copilot, Codex |
| Native AI IDE | Cursor, Windsurf, Trae, Antigravity, Qoder, IBM Bob |
| Native AI IDE + CLI | Kiro, CodeBuddy, Junie |
| IDE extension + Cloud | Augment, Continue, Cline |
| Web-first / Cloud-native | OpenHands, Replit, Deep Agents (SDK), AdaL |
| Specialized harness | Firebender (Android Studio), MCPJam (MCP testing), Mux (CI/CD) |

A useful single skill needs to work across all of these. Skills are designed to handle this by being *just instructions* — the runtime is whichever harness happens to be loading the file.

---

## 6. The "subagent" gap

The spec doesn't include a forking primitive (only Claude Code's `context: fork` exists today). But almost every harness has invented its own:

| Agent | Forking mechanism |
| --- | --- |
| Claude Code | `context: fork` in the skill, plus the Task tool. |
| Cursor | Background Agents and the Task tool with `subagent_type`. |
| Codex | Codex Cloud one-shot tasks. |
| Junie | "Custom subagents" in the IDE. |
| Deep Agents | `task` tool with isolated context windows. |
| Pi | TypeScript Extensions can spawn nested agents. |
| Goose | Extension-based, MCP-orchestrated parallel calls. |
| Kilo Code | Agent Manager with side-by-side sessions. |
| Pochi | Parallel Agents using Git worktrees. |
| Qoder | Quest Mode + Experts Mode (parallel agent team). |

A v2 of the skills spec could plausibly standardize this. Today, a portable skill that needs sub-context behavior should *describe* the work in plain English ("Spawn a sub-agent to do X if your harness supports it; otherwise do X inline") rather than declare it.

---

## 7. Vendor strategy patterns

A few business patterns are visible in the data:

### Pattern A — "Native first, skills as adoption hook"
Cursor, Windsurf, Trae, Junie, Antigravity. These vendors have polished proprietary IDEs and treat skills as a way to get rules-as-code from competitors' ecosystems running inside their IDE.

### Pattern B — "Open-source agent that needs portable rules"
Cline, Roo, Continue, Crush, OpenCode, Pi, Goose. These shipped without a strong opinion on rules format, so adopting Skills was a pure win — their users immediately got access to the `vercel-labs/agent-skills` collection and skills.sh.

### Pattern C — "Big-vendor CLI staking out the standard"
Claude Code, Codex, Gemini CLI, Mistral Vibe, Kimi CLI, Qwen Code. The model vendors all want the standard to exist *and* to be friendly to their CLI. Anthropic is most invested (Claude Code is the de-facto reference; the plugin marketplace doubles as a skill registry).

### Pattern D — "Enterprise IDE / cloud product"
IBM Bob, Cortex Code, Kiro, CodeBuddy. These need to fit into existing enterprise stacks (Bedrock, Snowflake, Tencent Cloud, IBM). Skills are perfect for them: a safe, file-based, audit-friendly extension mechanism that doesn't require code execution.

### Pattern E — "Specialist agent"
Firebender (Android), Mux (CI/CD), MCPJam (MCP testing), Pi (minimal TUI). Skills let these agents share team conventions without having to invent their own format.

---

## 8. What would v2 of the spec realistically add?

If you read between the lines of all 45 agent docs, the obvious candidates for v2 are:

1. **A standardized fork/sub-agent declaration** (`context: fork` generalized).
2. **Hooks at the spec level** (currently only Claude Code & Cline).
3. **A `tools` schema** so an agent can declare new tools the skill needs (already partial in OpenClaw and Kode).
4. **An `inputs:` block** for parametrized skills (currently this is done in the body).
5. **Cross-skill references** (one skill that pulls another in).

But — and this is the lesson of the current spec — *anything added makes adoption harder*. The reason 45 agents could converge is that the format is small. The current state is probably stable for a year+ of growth before any v2 happens.

---

## 9. Things this dataset suggests, but doesn't prove

These are inferences worth treating as hypotheses, not facts:

- **The shared `.agents/skills/` bucket will keep growing.** Once an agent adopts it, there's no real reason to migrate away. Expect the next-added agents to default to it.
- **Snake-case slugs (`gemini-cli`, `kiro-cli`, `kimi-cli`, `mistral-vibe`) are now the convention.** Newer agents pick this; older ones (`bob`, `cursor`, `goose`) used single words.
- **The Trae / Trae CN split shows that geographically split products will share project-level paths but split global ones.** Expect the same pattern for any future regional variants.
- **OpenClaw's bare `skills/` (no leading dot) is a relic and will probably be migrated.** It conflicts with the publisher discovery convention and is an outlier.

---

## 10. Where to look for further depth

- The per-agent files in [`agents/`](./agents) for vendor-specific behavior.
- [`feature-compatibility.md`](./feature-compatibility.md) for what's portable vs not.
- [`skill-format.md`](./skill-format.md) for authoring concerns.
- The upstream [`vercel-labs/skills`](https://github.com/vercel-labs/skills) for the source of truth on paths and slugs.
- [`agentskills.io`](https://agentskills.io) for the spec itself.
- [`skills.sh`](https://skills.sh) for an evolving directory of public skills.
