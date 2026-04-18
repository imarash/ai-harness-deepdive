# The `SKILL.md` Format — Authoring Guide

A "skill" is a folder. The folder must contain a single file named `SKILL.md`. That file must have YAML frontmatter with two fields. That's it.

This page documents what's required, what's optional, what's portable across the 45 supported agents, and what the `npx skills` CLI actually inspects.

---

## Minimal valid skill

```text
my-skill/
└── SKILL.md
```

```markdown
---
name: my-skill
description: Generate release notes from the last week of commits.
---

# Release Notes

Look at git commits from the last 7 days, group by area, and write a
release-notes.md file at the repo root.
```

That's a complete, installable, portable skill. The CLI will pick it up, every agent in the matrix will run it.

---

## Frontmatter reference

### Required fields

| Field | Type | Notes |
| --- | --- | --- |
| `name` | string | Lowercase identifier; hyphens allowed. Must be unique within the repo. The CLI uses this as the folder/symlink name on install. |
| `description` | string | One sentence; this is what the agent reads to decide whether to load the skill. **Make it imperative**: "Use this when…", "When the user asks for X…". |

### Optional, well-supported

| Field | Type | Notes |
| --- | --- | --- |
| `allowed-tools` | string[] | Restrict which tools the agent can call while the skill is active. Honored by 16 of 18 documented agents (not Kiro CLI, not Zencoder). |
| `metadata.internal` | bool | If `true`, the skill is hidden from normal discovery and only installable when `INSTALL_INTERNAL_SKILLS=1`. Useful for WIP skills. |

### Optional, agent-specific

| Field | Supported by | Effect |
| --- | --- | --- |
| `context: fork` | Claude Code only | Run the skill in a forked sub-context. |
| `model` | Some (Junie, Cursor, etc.) | Override the model for this skill (e.g., `gpt-5-codex` vs `claude-sonnet-4.5`). |
| `version` | Most | Self-documentation; not enforced. |
| `tags` | Some | Used by `skills find` and `skills.sh` to surface the skill in search. |

### Reserved for the spec

The spec deliberately keeps the surface small. Authors are encouraged *not* to invent new top-level fields; instead, put extension data under `metadata`. For example:

```yaml
---
name: my-skill
description: ...
metadata:
  team: web
  owner: alice@example.com
  internal: false
---
```

---

## The `description` field is the most important thing

Because of progressive disclosure, the model only sees the skill's `name` and `description` in its system prompt — it does *not* see the body of `SKILL.md` until it decides to load the skill.

That decision is made on the description alone. Consequences:

1. **Be specific.** "Generate release notes" is OK. "When the user asks to summarize recent changes, generate release notes from the last 7 days of git commits" is much better.
2. **Use trigger words.** If your skill should fire on the word "deploy", make sure "deploy" appears in the description.
3. **Mention scope.** "Project-wide" vs "single file" vs "git repository" all change when the model thinks it should fire.

A common pattern is to write the description in the form: *"Use this skill when [trigger condition]. It will [outcome]."*

---

## A skill folder can have other files

```text
my-skill/
├── SKILL.md          ← required
├── template.md       ← optional reference template
├── checklist.md      ← optional process checklist
└── examples/
    ├── good.md
    └── bad.md
```

The body of `SKILL.md` is free to reference these files (e.g., "Read `template.md` then…"). The agent will fetch them on demand using its standard read tools.

This is what IBM Bob's docs call "checklists, templates, reference documentation, and style guides" — the skill folder is a small filesystem dedicated to one task.

---

## Where to put skills in your repo

When you're publishing a skills repo, the CLI searches these paths first (in order):

```text
SKILL.md                        ← single-skill repo at the root
skills/                         ← the canonical multi-skill convention
skills/.curated/
skills/.experimental/
skills/.system/
.agents/skills/
.augment/skills/
.bob/skills/
.claude/skills/
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

The most idiomatic choice for a brand-new skills repo: `skills/<skill-name>/SKILL.md`.

If none of the above match, the CLI does a recursive scan looking for `SKILL.md`. So you can technically put skills anywhere — the search paths above are an optimization.

---

## Plugin manifest discovery

If your repo has a `.claude-plugin/marketplace.json` or `.claude-plugin/plugin.json`, the CLI will additionally pick up skills declared there:

```json
{
  "metadata": { "pluginRoot": "./plugins" },
  "plugins": [
    {
      "name": "my-plugin",
      "source": "my-plugin",
      "skills": ["./skills/review", "./skills/test"]
    }
  ]
}
```

This makes Claude Code plugins automatically valid sources for the entire `vercel-labs/skills` ecosystem.

---

## Authoring checklist

Before you publish:

- [ ] `name` is lowercase, hyphenated, unique within the repo.
- [ ] `description` is one sentence and contains the *trigger conditions* in plain English.
- [ ] No required field beyond `name` and `description`.
- [ ] Body uses standard Markdown only (no agent-specific syntax).
- [ ] If you set `allowed-tools`, you've tested with at least one agent that enforces it (Claude Code is the safest bet).
- [ ] The skill is short enough to be useful as one unit, but if it's long, you've considered breaking it into multiple skills.
- [ ] You've added the repo to `skills.sh` (optional but helpful for discovery).

---

## Testing your skill

Three options:

```bash
# 1. Install from a local path
npx skills add ./my-skills-repo

# 2. List what would be installed
npx skills add ./my-skills-repo --list

# 3. Install only one skill from your repo to one agent
npx skills add ./my-skills-repo --skill my-skill -a claude-code -y
```

For agent-specific testing, see the per-agent pages in [`agents/`](./agents). Each lists the agent's own skill-loading behavior and any `allowed-tools` enforcement nuances.

---

## A note on prompt-injection / sandboxing

A skill is a markdown file that the agent reads as instructions. A malicious skill is, in effect, a prompt-injection vector. Be careful when installing third-party skills, especially with `--global`:

- Read the skill's `SKILL.md` before running.
- Prefer the official `vercel-labs/agent-skills` collection or `skills.sh`-listed sources.
- Use `INSTALL_INTERNAL_SKILLS=0` (the default) to avoid pulling in skills the author marked internal.
- For team-shared skills, pin the skill via the `skills-lock.json` and review changes in PRs.
