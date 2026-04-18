# Feature Compatibility Matrix

The Agent Skills spec defines a small core (a folder + `SKILL.md` + YAML frontmatter) and a few **optional features** that not every agent implements. The upstream `vercel-labs/skills` README documents the support state for **18 agents** — the most popular ones. This page reproduces that matrix and adds an interpretive layer.

---

## The matrix (verbatim from upstream)

| Feature | OpenCode | OpenHands | Claude Code | Cline | CodeBuddy | Codex | Command Code | Kiro CLI | Cursor | Antigravity | Roo Code | Github Copilot | Amp | OpenClaw | Neovate | Pi | Qoder | Zencoder |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Basic skills | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes |
| `allowed-tools` | Yes | Yes | Yes | Yes | Yes | Yes | Yes | **No** | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | **No** |
| `context: fork` | No | No | **Yes** | No | No | No | No | No | No | No | No | No | No | No | No | No | No | No |
| Hooks | No | No | **Yes** | **Yes** | No | No | No | No | No | No | No | No | No | No | No | No | No | No |

---

## Glossary

### Basic skills

Every listed agent supports the core spec: discover `SKILL.md`, parse the YAML frontmatter (`name`, `description`), inject the description into the system prompt, fetch the body when the model decides the skill is relevant. **Universally Yes**, including for the 27 agents *not* in the matrix above.

### `allowed-tools`

An optional frontmatter field that scopes which tools the skill is allowed to invoke. Example:

```yaml
---
name: pr-creator
description: Create a pull request following our team's conventions
allowed-tools: ["Shell", "Read", "Write", "GitHubMCP::create_pr"]
---
```

When the agent invokes the skill, only the listed tools are made available to the model in that turn. This is a sandboxing primitive — useful for skills you'd otherwise want to mark as auto-approve.

**Doesn't support it (in the matrix):** Kiro CLI, Zencoder. For the others, behavior is consistent.

### `context: fork`

A Claude-Code-only feature today. Setting `context: fork` in the frontmatter tells Claude Code to spawn a **subordinate context** for the skill: the parent conversation pauses, the skill runs in a fresh context window, and the result (a summary) returns to the parent.

This is functionally identical to a sub-agent invocation, just declared in the skill itself. It's powerful because:

- Long skills (e.g., a multi-step refactor) don't pollute the parent context.
- The fork inherits the project but not the chat history.
- The skill can use a much larger `max_tokens` budget without affecting the parent.

No other agent ships this today. The closest equivalents in other harnesses:

- **Cursor**: Background agents and the Task tool with `subagent_type`.
- **Cline / Roo**: "New Task" tool, similar effect manually.
- **Codex Cloud**: One-shot tasks operate in fresh contexts by design.
- **Deep Agents (LangChain)**: `task` tool spawns sub-agents with isolated contexts.

### Hooks

Pre- and post-skill activation lifecycle hooks. Today only **Claude Code** and **Cline** implement these in the skills format.

In Claude Code, you can declare hooks at the project or global level (in `settings.json`) and they fire when:
- A skill is about to be activated (`PreSkill`)
- A skill has finished (`PostSkill`)
- A tool is about to run inside a skill
- After a skill writes to a file

This is what makes Claude Code the de-facto reference implementation: the spec is *intentionally* small, but Claude Code is willing to extend it with the most lifecycle surface area.

In Cline, hooks are limited to pre/post-tool execution but can be triggered from inside a skill.

---

## What about the other 27 agents?

The matrix in `vercel-labs/skills` only lists 18 agents. The other 27 (AdaL, Augment, Bob, Continue, Cortex, Crush, Cursor's edge cases, Deep Agents, Droid, Firebender, Gemini CLI, Goose, iFlow, Junie, Kilo, Kimi CLI, Kode, MCPJam, Mistral Vibe, Mux, OpenHands edge cases, Pochi, Qwen, Replit, Trae, Trae CN, Universal, Warp, Windsurf) are presumed to support **basic skills only**, with `allowed-tools` parsed-but-not-enforced behavior varying.

Some that we know specifically:

| Agent | Notes |
| --- | --- |
| **Continue** | Supports skills via the `.continue/` config; `allowed-tools` honored. |
| **Cursor** | Supports basic skills + `allowed-tools`. No `context: fork`. Cursor's own equivalent is Background Agents. |
| **Crush** | Basic skills. Tool-call constraints are configurable separately via Crush's TOML config. |
| **Goose** | Basic skills. Goose's "extensions" (MCP) are the primary extension mechanism; skills are layered on top. |
| **Gemini CLI** | Basic skills + `allowed-tools`. Gemini CLI also supports its own `gemini.toml` for tool config. |
| **Codex** | Basic skills + `allowed-tools`. The OpenAI-hosted Codex Cloud variant has additional sandboxing. |
| **Junie** | Basic skills + `allowed-tools`. Junie's "subagents" are the equivalent of `context: fork`. |
| **Mistral Vibe** | Basic skills. Tool restriction is via Vibe's own permission model rather than `allowed-tools`. |

---

## Implications for skill authors

If you're writing skills meant to be portable across the whole ecosystem:

1. **Always assume only basic skills.** The frontmatter `name` + `description` + body markdown is the only universal contract.
2. **Treat `allowed-tools` as advisory.** Even Kiro/Zencoder users can install and run your skill; they'll just see all available tools.
3. **Don't depend on `context: fork`.** If your skill is long enough to need a forked context, ship it as a *subagent / task* with the activation pattern delegated to the host agent. Mention in the description: "this is a long-running skill; consider running it via a sub-agent if your harness supports it".
4. **Hooks are Claude-Code/Cline-only.** Authoritative pre/post behavior should be inlined in the skill body as instructions, not as hooks.

---

## A note about scope creep

The Agent Skills spec is deliberately spartan. The reason `context: fork` and hooks aren't in the core spec yet is that they're solved at very different layers in different harnesses (some via tools, some via lifecycle events, some via process boundaries). Standardizing prematurely would have made the spec harder to adopt for the 33 agents that don't have a forking model.

The pragmatic story: the "shared specification" is mostly the **install path + file format**. The runtime behavior is, and probably should remain, agent-specific.
