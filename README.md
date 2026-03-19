# jcao-skills

English | [简体中文](./README.zh-CN.md)

Practical AI agent skills, battle-tested in real workflows.

> Skills I built because people kept asking me for them.

---

## Skills

| Skill | What it does | Status |
|-------|-------------|--------|
| [flow-viz-prompt](./flow-viz-prompt/) | Turn any pipeline description into a ready-to-paste Gemini image generation prompt — NotebookLM style | 🔥 Battle-tested |
| [skill-publisher](./skill-publisher/) | Scan an internal skill for privacy leaks, i18n issues, and structure problems — outputs a sanitized publication-ready copy | 🌱 New |

---

## Examples

### flow-viz-prompt

Two diagrams generated with this skill, both using Gemini's image generation model.

---

**Codex Subagent Spawn: Key Design Choices**

Prompt: *"Code-level design diagram for the Codex agent — how does the parent model spawn a subagent, what safety gates run, and how does async completion notification work? Source: `codex-rs/` codebase."*

→ Triggers **code granularity** (keyword: "code-level"). The skill reads the repo, confirms the control flow (AgentControl → safety gates → fork vs. new thread decision → completion watcher), then generates the prompt.

![Codex Subagent Spawn](flow-viz-prompt/examples/codex-subagent-spawn.png)

---

**OpenClaw Memory Architecture — What Gets Remembered vs. Discarded**

Prompt: *"Flowchart of OpenClaw's memory architecture — I want to show what gets remembered vs. discarded across sessions. Top-to-bottom layout."*

→ Triggers **reporting granularity** (default). The skill asks for input/output layers and core steps, confirms the control flow (channel history buffer → session transcript → persistence paths → long-term memory flush), then generates the prompt.

![OpenClaw Memory Architecture](flow-viz-prompt/examples/openclaw-memory-arch.png)

---

## Coming soon

| Skill | What it does |
|-------|-------------|
| multi-fallback-websearch | Fetch any URL that blocks bots — six-layer fallback: curl → Jina Reader → Scrapling → Tavily Extract → Wayback Machine → Tavily Search |
| verify-deep-research | Verify every URL citation in an AI-generated research report, cross-check claims, assess source authority, and output a corrected document |
| npu-env-setup | Set up NPU execution environments interactively — local, SSH remote, or inside Docker containers |
| notebook-llm-pipeline | Automate NotebookLM workflows: add sources, query notebooks, and create audio/study guides via MCP |

---

## How to install

Each skill is a single `SKILL.md` file. Copy it into your Claude skills directory:

```bash
# macOS / Linux
cp flow-viz-prompt/SKILL.md ~/.claude/skills/flow-viz-prompt/SKILL.md
cp skill-publisher/SKILL.md ~/.claude/skills/skill-publisher/SKILL.md
```

Then invoke it in Claude Code:

```
/flow-viz-prompt
/skill-publisher
```

---

## Status labels

| Label | Meaning |
|-------|---------|
| 🔥 Battle-tested | Used repeatedly, shared with others, holds up in real workflows |
| 🌱 New | Newly published — feedback welcome |

---

## Contributing

Found a bug or want to suggest an improvement? Open an issue or PR.

---

## Skills I use daily

- [obra/superpowers](https://github.com/obra/superpowers) — Development workflow skills: TDD, systematic debugging, git worktrees, code review. The reason I started writing my own.
- [mattpocock/skills](https://github.com/mattpocock/skills) — Personal skills shared straight from Matt's `.claude` directory. Good reference for what a real, unpolished skill collection looks like.
