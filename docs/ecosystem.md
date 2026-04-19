# The Agent Skills Ecosystem

## TL;DR

**Agent Skills** are a portable format for packaging instructions, conventions, and small tool wrappers that any compatible coding agent can install and use. A skill is just a folder containing a `SKILL.md` file with YAML frontmatter — minimum two fields: `name` and `description`.

The [`vercel-labs/skills`](https://github.com/vercel-labs/skills) CLI (`npx skills`) is the *package manager* for this ecosystem. It clones a git repo, finds every `SKILL.md` in it, and copies (or symlinks) the parent folder into the right place for whichever coding agent is installed on your machine. As of v1.5.1 (April 2026) it knows about **45 distinct agents**.

## The problem skills solve

Pre-skills, every agent had its own way of giving the model "team conventions":

| Agent | Pre-skills mechanism |
| --- | --- |
| Cursor | `.cursor/rules/*.mdc` |
| Claude Code | `CLAUDE.md` at project root |
| GitHub Copilot | `.github/copilot-instructions.md`, path-scoped `.github/instructions/*.instructions.md`, and `AGENTS.md` (plus other agent-instruction filenames on some surfaces) |
| Continue | `.continue/config.yaml` |
| Cline | `.clinerules/*.md` |
| Windsurf | `.windsurfrules` (later `.windsurf/rules/`) |
| Codex | `AGENTS.md` |

The result: if you maintained a team standard ("always use `pnpm`", "PRs follow this template", "we use Tailwind v4"), you had to maintain *N* copies — one per tool. Worse, each tool implemented activation differently. Some always loaded the file; some only loaded it on a tool call; some only when a slash command matched.

Skills standardize three things:

1. **The file format** — A folder with `SKILL.md` plus YAML frontmatter (`name`, `description`).
2. **The activation contract** — The `description` field is what the model reads to decide whether to load the rest of the file. This is *progressive disclosure*: only the description is in the system prompt; the body is fetched only when the model decides the skill is relevant.
3. **A discovery convention** — Every agent looks in a predictable directory (e.g., `.claude/skills/`, `.cursor/skills/`, `.windsurf/skills/`, etc.).

## How `npx skills` actually works

```text
$ npx skills add vercel-labs/agent-skills
       │
       ▼
1. source-parser.ts  →  resolves "vercel-labs/agent-skills" to a git URL
2. git.ts            →  shallow-clones into a temp dir
3. skills.ts         →  walks the repo for SKILL.md files (multiple search paths)
4. agents.ts         →  detects which of the 45 agents you have installed
5. installer.ts      →  for each (skill × agent) pair, symlinks (or copies) the
                        skill folder into the agent's project or global path
6. local-lock.ts     →  writes skills-lock.json so `skills update` knows the
                        original source + folder hash
```

A few important consequences of this design:

- **Symlink-by-default**. The CLI prefers a symlink so that one canonical copy of a skill exists per machine. This means an `npx skills update` is just "re-pull and the symlinks already point to the new content".
- **Multi-agent install in one command**. If you have Cursor and Claude Code and Codex installed, `npx skills add ...` will install to all three.
- **Project vs Global**. Without `-g`, skills land inside the current project (committable). With `-g`, they land in your home directory (machine-wide).

## The skills lock file

`skills update` works by:

1. Reading `~/.agents/.skill-lock.json` (global) or `skills-lock.json` (project) for installed skills.
2. Calling the GitHub Trees API for each skill's source folder.
3. Comparing the latest tree SHA with the recorded `skillFolderHash`.
4. Reinstalling whichever skills have changed.

This is why the CLI strongly prefers GitHub-hosted skills — only GitHub-backed skills can be checked for updates. (GitLab and arbitrary git URLs work for `add`, but won't be diffed by `update`.)

## The "agents bucket" convention

Look at the install paths and you'll see something subtle:

```
.agents/skills/        ← Amp, Antigravity, Cline, Warp, Codex, Cursor,
                         Deep Agents, Firebender, Gemini CLI,
                         GitHub Copilot, Kimi CLI, OpenCode, Replit, Universal
.claude/skills/        ← Claude Code only
.cursor/skills/        ← (only used as the global path, not project)
.continue/skills/      ← Continue only
.codex/skills/         ← (only used as the global path)
...
```

About **15 of the 45 agents share the same project path: `.agents/skills/`**. That's the de-facto "we don't have an opinion, just use the universal directory" convention. Agents that picked their own folder (Continue, CodeBuddy, Cortex, Crush, Goose, Junie, etc.) tend to be ones that shipped skills support before the convention solidified, or that have a strong product-identity reason to namespace.

## Plugin marketplace compatibility

The CLI also discovers skills declared in `.claude-plugin/marketplace.json` or `.claude-plugin/plugin.json`:

```json
{
  "metadata": { "pluginRoot": "./plugins" },
  "plugins": [
    { "name": "my-plugin", "source": "my-plugin", "skills": ["./skills/review"] }
  ]
}
```

This makes any Claude Code plugin a valid skills source for *all 45 agents*, not just Claude Code. It's a clever bit of leverage — Anthropic's plugin marketplace becomes the cross-agent default.

## What's *not* standardized

The frontmatter standardizes `name` and `description`. Everything else is a soft convention or vendor-specific:

- **`allowed-tools`** — Most agents respect this, but Kiro CLI and Zencoder ignore it.
- **`context: fork`** — Only Claude Code implements this (sub-context for the skill).
- **Hooks** — Only Claude Code and Cline support pre/post-skill hooks.
- **Skill activation strategy** — Some agents auto-activate, some require approval, some only run on slash commands.

See [`feature-compatibility.md`](./feature-compatibility.md) for the full annotated matrix.

## Where the spec lives

- **Specification**: <https://agentskills.io>
- **Skills directory**: <https://skills.sh>
- **CLI source**: <https://github.com/vercel-labs/skills>
- **Reference skill collection**: <https://github.com/vercel-labs/agent-skills>

The `agentskills.io` spec is intentionally minimal — that's why such a wide range of vendors could adopt it without breaking their existing UX.
