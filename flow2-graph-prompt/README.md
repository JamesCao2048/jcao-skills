# flow2-graph-prompt

> Turn any pipeline or workflow into a ready-to-paste Gemini image generation prompt — NotebookLM visual style.

## Why this exists

I kept converting pipeline descriptions to flowchart prompts manually until I got tired of doing it. Friends started asking me for the prompt template, so I automated it.

## What it does

Takes any pipeline or workflow you describe, clarifies the structure with you, then generates a complete image generation prompt you can paste directly into Gemini (or similar models).

Output style is fixed to **NotebookLM Style** — clean whiteboard aesthetic, rounded rectangles, soft blues and oranges. Suitable for PPT slides and design review docs.

Two granularity levels:
- **Report granularity** (default) — abstract step names, no code details. Good for presentations.
- **Design granularity** — file paths, function names, call relationships. Good for architecture reviews.

## Trigger phrases

```
/flow2-graph-prompt
生成流程图prompt
画流程图
根据流程生成图片
流程图PPT
pipeline图片
Gemini图片生成
flowchart prompt
```

## Install

```bash
cp SKILL.md ~/.claude/skills/flow2-graph-prompt/SKILL.md
```
