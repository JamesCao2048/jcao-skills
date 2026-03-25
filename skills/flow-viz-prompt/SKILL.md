---
name: flow-viz-prompt
description: Use when user wants to generate an image-generation prompt for a pipeline, workflow, or codebase architecture diagram. Supports NotebookLM (clean/professional) and Cartoon & Sketch (hand-drawn/playful) styles. Triggers on: "flowchart prompt", "diagram prompt", "figure prompt", "visualization prompt", "cartoon flowchart", "sketch diagram", and Chinese: "流程图提示词", "架构图提示词", "画图提示词", "pipeline可视化提示词", "给我一个流程图的prompt", "卡通流程图", "手绘架构图". Do NOT trigger on requests to just draw or show a diagram without "prompt" — e.g., "draw graph", "生成流程图" alone should NOT trigger this skill.
---

# Flow Viz Prompt

Converts any pipeline or workflow into a professional image-generation prompt, ready to paste into Gemini or any image-generation model.

Supports two visual styles:
- **NotebookLM Style** (default): Clean digital whiteboard aesthetic, professional and modern
- **Cartoon & Sketch Style**: Hand-drawn, playful illustration aesthetic inspired by [angie.temaly.com](https://angie.temaly.com/) — ideal for blog posts, social media, and making technical content more approachable

Supports two granularity levels:
- **Reporting granularity** (default): Suitable for PPT/tech presentations — nodes are abstract step names, no code details
- **Code granularity**: Suitable for design reviews — nodes show file paths, function names, and call relationships at code level

## Input

- **Text description**: Natural language description of the pipeline or workflow.
  - Supports: reporting granularity only
  - Exception: if the description includes specific file paths, function names, or class names, code granularity also becomes available
- **Project / script / agent path**: A local path to a codebase, script, or agent directory.
  - Supports: both reporting granularity and code granularity

## Output

A complete image-generation prompt in the confirmed output language, ready to paste into Gemini or any image-generation model.

---

## Step 0: Style & Granularity Detection (automatic, no prompting)

### Style Detection

Infer visual style from the user's trigger phrase:

| Style | Trigger keywords (any match) |
|-------|------------------------------|
| **Cartoon & Sketch** | "cartoon", "sketch", "hand-drawn", "doodle", "playful", "fun", "cute", "illustrated", "卡通", "手绘", "涂鸦", "可爱风", "sketch style" |
| **NotebookLM** | All other cases (default) |

Announce the detected style: `[Style: NotebookLM]` or `[Style: Cartoon & Sketch]`.

### Granularity Detection

Infer granularity from the user's trigger phrase:

| Granularity | Trigger keywords (any match) |
|-------------|------------------------------|
| **Code granularity** | "detailed design", "design diagram", "code-level", "file-level", "function-level", "implementation details", "detailed", "code-level", "详细设计图", "设计图", "代码级", "文件级", "函数级", "实现细节" |
| **Reporting granularity** | All other cases (default) |

If code granularity is detected but the input is a text description without any file paths or function names, inform the user:
> "Code granularity requires implementation details (file paths, function names, or a project path). Based on your input, I'll proceed with reporting granularity — or share a project path to enable code granularity."

Announce the detected granularity at the start of the reply: `[Granularity: Reporting]` or `[Granularity: Code]`.

---

## Step 0.5: Output Language Detection (automatic, confirm before generating)

Detect the default output language from the user's message language. Announce and ask for confirmation before Step 2:

> "I'll generate the prompt in **[detected language]**. Confirm, or let me know your preferred language."

Wait for confirmation before proceeding to Step 2.

---

## Workflow (Steps 1–2, cannot be skipped)

### Step 1: Clarify the Pipeline with the User

Before generating the prompt, **confirm the following**. If the user has already provided a control-flow description, extract from it directly and ask only for missing pieces.

#### Reporting granularity — confirm 5 points (ask all at once)

1. **Topic name**: What is the title of this diagram? (e.g., "Codex Agent Request Pipeline")
2. **Input layer**: What are the input sources? What key sub-items does each contain?
3. **Core steps**: What are the main processing stages? Which steps need their own node, and which can be merged?
4. **Output layer**: What are the final artifacts? What types or categories?
5. **Omissions**: What preparation, merging, or cleanup steps should **not** appear in the diagram?

#### Code granularity — confirm 7 points (ask all at once)

Same as reporting granularity, plus:

6. **Key files and functions**: Which code files correspond to each core step? What are the key entry functions/classes? (e.g., `codex-rs/tui/src/app.rs → App::handle_event()` calls `core/src/agent/control.rs → AgentControl::run()`)
7. **Call relationships**: What is the call chain between steps? Which are direct function calls, and which are indirect (subprocess, tool call, HTTP, IPC)?

After receiving the user's reply, show a structured control-flow summary and ask for confirmation before proceeding to Step 2.

**Control-flow confirmation template (reporting granularity):**
```
Input layer: [source 1] + [source 2] → merge
Core steps:
  Step A: [name] — [input] → [output]
  Step B: [name] (sub-region: dashed box wrapping B1→B2→B3)
  ...
Output layer: [artifact 1] | [artifact 2] | ...
Omitted: [list of steps not shown]
```

**Control-flow confirmation template (code granularity):**
```
Input layer: [source 1] + [source 2] → merge
Core steps:
  Step A: [name]
    File: [path/to/file.rs]
    Entry: [ClassName::method()] or [function_name()]
    Calls: [path/to/other.rs → target_func()]
    Input → Output
  Step B: [name] (sub-region: dashed box wrapping B1→B2→B3)
    B1 File: [...] Entry: [...]
    B2 File: [...] Entry: [...]
    ...
Output layer: [artifact 1] | [artifact 2] | ...
Omitted: [list of steps not shown]
```

### Step 2: Generate the Gemini Image-Generation Prompt

After the user confirms the control flow:

1. Read the references file matching the detected style:
   - **NotebookLM** → read `references/notebooklm.md`
   - **Cartoon & Sketch** → read `references/cartoon-sketch.md`

2. Select the template matching the detected granularity:
   - **Reporting granularity** → use Template A (NotebookLM) or Template C (Cartoon & Sketch)
   - **Code granularity** → use Template B (NotebookLM) or Template D (Cartoon & Sketch)

3. Fill in all `[...]` placeholders with the user-confirmed content from Step 1.

4. Output the complete prompt directly so the user can copy-paste it into Gemini. Generate in the language confirmed in Step 0.5.

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Skipping Step 1 and generating directly | Must confirm control flow first — node structure will be wrong otherwise |
| Including omitted steps in the diagram | Steps the user marked as "omit" must not appear in the prompt |
| Output layer node is too simple | Output layer must show types / categories / dimensions, not just a single box |
| Missing dashed boxes | Any sub-flow or parallel path must be wrapped with a dashed border |
| Inconsistent color scheme | Strictly follow the style spec color zones — do not improvise |
| Code granularity missing code annotations | In code granularity, every core node **must** have a file path + function name in the bottom zone |
| Code granularity arrows not differentiated | Direct call = solid, indirect call = dashed + call method label — do not mix |
| Code granularity missing language labels | Different language modules must use background tint + top label to distinguish |
| Reporting granularity contains code details | No file paths, function names, or class names in reporting granularity |
| Code granularity requested but input has no implementation details | Inform user, fall back to reporting granularity, or ask for a project path |
| Generated prompt contains absolute paths | All file paths in the generated prompt must be relative (e.g., `src/agent/control.rs`). Never include machine-local absolute paths like `/Users/xxx/...` |
| Cartoon style used with wrong template | Cartoon & Sketch style must use Template C/D, not Template A/B — the visual language is completely different |
| Cartoon style too cluttered with doodles | Decorative elements should enhance, not overwhelm — max 1 small icon per node, sparse stars/squiggles |
| Mixing styles in a single prompt | Never mix NotebookLM and Cartoon & Sketch elements in one prompt — pick one and stay consistent |
