# jcao-skills

English | [简体中文](./README.zh-CN.md)

Practical AI agent skills, battle-tested in real workflows.

> Skills I built because people kept asking me for them.

---

## Skills

| Skill | What it does | Status |
|-------|-------------|--------|
| [flow2-graph-prompt](./flow2-graph-prompt/) | Turn any pipeline description into a ready-to-paste Gemini image generation prompt — NotebookLM style | 🔥 Battle-tested |

---

## Coming soon

| Skill | What it does |
|-------|-------------|
| multi-fallback-websearch | Fetch any URL that blocks bots — six-layer fallback: curl → Jina Reader → Scrapling → Tavily Extract → Wayback Machine → Tavily Search |
| verify-deep-research | Verify every URL citation in an AI-generated research report, cross-check claims, assess source authority, and output a corrected document |
| npu-env-setup | Set up NPU execution environments interactively — local, SSH remote, or inside Docker containers |
| notebook-llm-pipeline | Automate NotebookLM workflows: add sources, query notebooks, and create audio/study guides via MCP |
| industry-pipeline | Research a target company end-to-end: industry mapping, competitive landscape, and role-fit analysis |

---

## How to install

Each skill is a single `SKILL.md` file. Copy it into your Claude skills directory:

```bash
# macOS / Linux
cp flow2-graph-prompt/SKILL.md ~/.claude/skills/flow2-graph-prompt/SKILL.md
```

Then invoke it in Claude Code:

```
/flow2-graph-prompt
```

---

## Status labels

| Label | Meaning |
|-------|---------|
| 🔥 Battle-tested | Used repeatedly, shared with others, holds up in real workflows |
| 🌱 New | Newly published — feedback welcome |

---

## Roadmap

- [ ] Add worked examples with anonymized data for each skill
- [ ] Add screenshots / output samples

## Contributing

Found a bug or want to suggest an improvement? Open an issue or PR.
