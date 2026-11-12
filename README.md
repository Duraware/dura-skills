# dura-skills

A small collection of AI **skills** and **agents** that work in both **Claude** (Claude Code, claude.ai) and **GitHub Copilot** (VS Code, Copilot CLI, Copilot coding agent).

| Name | Type | What it does |
|---|---|---|
| [architect](skills/architect/SKILL.md) | skill | Architecture mentor: explains and reviews architecture, weighs trade-offs, designs systems, writes ADRs, defines fitness functions, assesses risk. Edits architecture docs only, never code. |
| [architect](agents/architect.agent.md) | agent | The same mentor as a selectable agent. Uses the `architect` skill when it is installed. |

## Skills vs agents

- A **skill** is a folder with a `SKILL.md` (plus optional reference files). The AI loads it automatically when a task matches its `description`, or when you ask for it by name. Skills follow the open [Agent Skills](https://agentskills.io) format, so one folder works in Claude and Copilot.
- An **agent** is a single Markdown file that gives the AI a persona and rules. You pick it explicitly (`/agents` in Claude Code, the agent picker or `/agent` in Copilot).

## Install

### Claude Code (plugin)

```text
/plugin marketplace add Duraware/dura-skills
/plugin install architect@dura-skills
```

### Claude Code or GitHub Copilot (copy the files)

Skills go in a `skills` folder, agents in an `agents` folder. Pick the location for your tool:

| | Skills | Agents |
|---|---|---|
| Claude Code, one repo | `.claude/skills/` | `.claude/agents/` |
| Claude Code, all repos | `~/.claude/skills/` | `~/.claude/agents/` |
| Copilot, one repo | `.github/skills/` (also reads `.claude/skills/`) | `.github/agents/` |
| Copilot, all repos | `~/.copilot/skills/` | `~/.copilot/agents/` |

Example (Copilot, one repo):

```bash
git clone https://github.com/Duraware/dura-skills /tmp/dura-skills
mkdir -p .github/skills .github/agents
cp -r /tmp/dura-skills/skills/architect .github/skills/
cp /tmp/dura-skills/agents/architect.agent.md .github/agents/
```

### claude.ai

Zip the `skills/architect` folder and upload it in the Skills section of claude.ai's settings.

## Repository layout

```text
skills/
  <skill-name>/
    SKILL.md          # required: frontmatter (name, description) + instructions
    references/       # optional: extra files loaded only when needed
agents/
  <agent-name>.agent.md
.claude-plugin/
  marketplace.json    # lets Claude Code install this repo as a plugin
```

## Writing a new skill or agent

**Skill**

1. Create `skills/<skill-name>/SKILL.md`. The folder name is lowercase with hyphens and matches `name`.
2. Start with frontmatter:
   ```yaml
   ---
   name: skill-name
   description: What it does and when to use it. This is what the AI reads to decide when to load the skill, so name the triggers.
   ---
   ```
3. Keep `SKILL.md` under ~500 lines. Move long material (catalogues, templates) into `references/` and link to it from `SKILL.md`.
4. Add it to the table above and to `skills` in `.claude-plugin/marketplace.json`.

**Agent**

1. Create `agents/<agent-name>.agent.md`. The `.agent.md` extension is what Copilot needs; Claude reads it too.
2. Use frontmatter both tools understand: `name` and `description`. Leave out `tools` and `model` so each tool uses its defaults (their tool names differ).
3. Keep the agent short and let it load the matching skill, so the instructions live in one place.
4. Add it to the table above and to `agents` in `.claude-plugin/marketplace.json`.

## License

[MIT](LICENSE)
