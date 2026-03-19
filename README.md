# jcao-skills

English | [简体中文](./README.zh-CN.md)

Practical AI agent skills, battle-tested in real workflows.

> Skills I published because people kept asking me for them.

---

## Skills

| Skill | What it does | Status |
|-------|-------------|--------|
| [flow-viz-prompt](./flow-viz-prompt/) | Turn any pipeline description or code into a ready-to-paste NotebookLM style image generation prompt (supports reporting and code granularity) | 🔥 Battle-tested |
| [skill-publisher](./skill-publisher/) | Scan a personal or proprietary skill for privacy leaks, i18n issues, and structure problems — outputs a sanitized publication-ready copy | 🌱 New |

---

## Examples

### flow-viz-prompt

Two diagrams generated with this skill using Gemini Pro (Nano-Banana 2).

---

**Codex Subagent Spawn: Key Design Choices**

Prompt: *"Code-level diagram prompt for the Codex subagent mechanism, highlighting key design choices and reasoning. Source: `xxx/codex/` codebase."*

→ Triggers **code granularity** (keyword: "code-level"). The skill reads the repo and confirms the control flow (AgentControl → safety gates → fork vs. new thread decision → completion watcher) with the user. The user can review and refine the control flow before giving final approval, then the agent generates the prompt.

![Codex Subagent Spawn](examples/flow-viz-prompt/codex-subagent-spawn.png)

---

**OpenClaw Memory Architecture — What Gets Remembered vs. Discarded**

Prompt: *"Flowchart prompt of the memory architecture. I want to show what gets remembered vs. discarded with Discord or Slack channels. Code: `xxx/openclaw`"*

→ Triggers **reporting granularity** (default). The skill asks for input/output layers and core steps, confirms the control flow (channel history buffer → session transcript → persistence paths → long-term memory flush), then generates the prompt.

![OpenClaw Memory Architecture](examples/flow-viz-prompt/openclaw-memory-arch.png)

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

**Option 1 — Ask Claude Code (easiest, works on all platforms)**

Open Claude Code in any directory and paste:

```
Install this skill: https://github.com/JamesCao2048/jcao-skills/tree/main/flow-viz-prompt
```

Claude will fetch the files and copy them into your skills directory automatically.

**Option 2 — Manual copy**

Clone the repo and copy the skill folder:

```bash
git clone https://github.com/JamesCao2048/jcao-skills.git

# macOS / Linux
cp -r jcao-skills/flow-viz-prompt ~/.claude/skills/

# Windows (PowerShell)
Copy-Item -Recurse jcao-skills\flow-viz-prompt $HOME\.claude\skills\
```

> Copy the whole folder, not just `SKILL.md`, some skills include reference files alongside.

**Invoking a skill**

Use the slash command, or just describe what you want in plain language:

```
/flow-viz-prompt

# or naturally:
"Give me a flowchart prompt for my data pipeline"
"Code-level architecture diagram for this codebase"
"diagram prompt for my agent workflow"
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

- [obra/superpowers](https://github.com/obra/superpowers) — Development workflow skills: TDD, systematic debugging, git worktrees, code review. The reason Claude Code becomes a genuinely different tool for me.
- [mattpocock/skills](https://github.com/mattpocock/skills) — Personal skills shared straight from Matt's `.claude` directory. Good reference for what a real, unpolished skill collection looks like.
