# MCPJam

> **Slug**: `mcpjam` · **Surface**: Web + CLI + Desktop · **Vendor**: MCPJam · **License**: Open core

A development platform for testing, debugging, and evaluating MCP (Model Context Protocol) servers and applications. The most "specialist" agent in the matrix.

## Overview

MCPJam is not a traditional coding agent — it's an MCP testing harness. It helps developers inspect, test, and evaluate how MCP servers behave across modern MCP clients (ChatGPT, Claude, Cursor, etc.). Asana uses it daily for testing MCP integrations.

The reason it's in the skills matrix at all is that the MCPJam platform ships with an embedded Chat Playground that itself runs as a coding-style agent — and that agent loads skills via the standard convention.

## Skills support

| Item | Value |
| --- | --- |
| Project path | `.mcpjam/skills/` |
| Global path | `~/.mcpjam/skills/` |
| `--agent` slug | `mcpjam` |
| `allowed-tools` | Yes |
| `context: fork` | No |
| Hooks | No |

## Installation

```bash
npx skills add vercel-labs/agent-skills -a mcpjam
```

## Notable behavior

- **App Builder**: debug MCP servers against models with tool calls or in-panel chat.
- **Chat Playground**: use frontier models to interact with MCP apps the way real users would in clients like ChatGPT, Claude, Cursor.
- **OAuth Debugger**: for visualizing MCP authorization flows.
- **Evaluations**: run test cases across multiple LLMs with metrics for accuracy.
- **Workspaces**: share server groups with team members in real time.
- Available as web app (no install), terminal tool (`npx @mcpjam/sdk`), or desktop app (Mac/Windows).
- The `@mcpjam/sdk` is a TypeScript SDK for unit/end-to-end testing of MCP primitives.

## Internals & Architecture

MCPJam is the only entry in the matrix that's an **MCP testing harness, not a coding agent**. The thing that earns it a slot is the embedded **Chat Playground**: a coding-style agent that exists to drive MCP servers under test, and that loads skills via the standard convention. So MCPJam is, architecturally, a debugger UI wrapped around an agent runtime — the inverse of every other entry, where the agent is the product and debugging tools are an afterthought.

```mermaid
flowchart TD
    User([MCP developer]) --> Surface{Surface}
    Surface -->|web| WebApp[mcpjam.com<br/>no install]
    Surface -->|terminal| SDK[@mcpjam/sdk<br/>npx invocation]
    Surface -->|desktop| Desktop[MCPJam desktop<br/>Mac / Windows]
    WebApp --> Inspector[MCP Inspector core]
    SDK --> Inspector
    Desktop --> Inspector
    Inspector --> AppBuilder[App Builder<br/>debug MCP servers]
    Inspector --> ChatPlayground[Chat Playground<br/>embedded agent]
    Inspector --> OAuthDbg[OAuth Debugger<br/>auth flow viz]
    Inspector --> Evals[Evaluations<br/>multi-LLM test cases]
    Inspector --> Workspaces[Workspaces<br/>share server groups]
    ChatPlayground --> SkillsLoad[Skills loader<br/>.mcpjam/skills/<br/>~/.mcpjam/skills/]
    SkillsLoad --> Models[(Frontier models<br/>via OpenAI / Anthropic / etc.)]
    Models --> ToolBus[Tool bus<br/>MCP-only]
    ToolBus --> MCPServers[(MCP servers under test)]
    MCPServers --> AppBuilder
    MCPServers --> Models
    Models -->|response| ChatPlayground
    Evals --> Metrics[(Accuracy / latency<br/>per LLM)]
```

The architectural pattern is **agent-as-driver, not agent-as-product**. MCPJam's Chat Playground exists to exercise MCP servers the way real chat clients (Claude, ChatGPT, Cursor) would. Skills give Playground users a way to script repeatable test flows — "test this MCP server using these recipes" — without writing custom code, which is uniquely valuable in the MCP ecosystem where end-to-end testing is otherwise hand-rolled.

## Harness Deep Dive

### Agent loop

- **Shape**: ReAct, but the agent's *purpose* is to drive MCP servers under test, not to author code.
- **Tool-call style**: Native function calling on whichever frontier model the user picks.
- **Halting**: Standard end-turn or end-of-test-case.
- **Streaming**: Token + tool-call event streams (visible in the Inspector UI).

### Context & memory

- **Context strategy**: Skills as repeatable test scripts. Workspaces (shared server groups) provide team-level state.
- **Persistent files**: `.mcpjam/skills/`, `~/.mcpjam/skills/`, plus per-workspace state.
- **Compaction**: Standard.
- **Sub-context**: Multi-LLM Evaluations spawn parallel test runs across providers.
- **Cross-session memory**: Skill files + Workspace-shared server groups.

### Tool runtime

- **Built-ins**: **MCP-only** by design — the entire tool surface is the MCP servers under test.
- **Parallelism**: Multi-LLM Evaluations run cases in parallel.
- **Approval / safety**: Tooling is for testing, so destructive actions are scoped to test fixtures.
- **Sandbox**: Browser sandbox in web mode; OS sandbox in desktop.
- **MCP**: **MCP is the entire product** — first-class everywhere.
- **OAuth Debugger**: Visualizes MCP authorization flows.

### Model integration

- **Provider model**: Frontier models via OpenAI / Anthropic / etc.
- **Caching**: Provider-level.
- **Multi-model**: Evaluations run per-LLM and report metrics (accuracy / latency).

### Innovation summary

**Inverted product — MCP testing harness with an embedded coding-style agent.** MCPJam is the dataset's only "agent-as-driver, not agent-as-product" entry. Skills become repeatable MCP test scripts; Evaluations turn into multi-LLM benchmarks. Uniquely valuable for the MCP ecosystem where end-to-end testing was otherwise hand-rolled.

## Documentation

- [MCPJam Inspector docs](https://docs.mcpjam.com/)
- [MCPJam Getting Started](https://docs.mcpjam.com/getting-started)
- [MCPJam SDK](https://docs.mcpjam.com/sdk)
