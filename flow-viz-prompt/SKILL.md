---
name: flow-viz-prompt
description: Use when user wants to generate an image-generation prompt for a pipeline, workflow, or codebase architecture diagram. Triggers on: "flowchart prompt", "diagram prompt", "figure prompt", "visualization prompt", and Chinese: "流程图提示词", "架构图提示词", "画图提示词", "pipeline可视化提示词", "给我一个流程图的prompt". Do NOT trigger on requests to just draw or show a diagram without "prompt" — e.g., "draw graph", "生成流程图" alone should NOT trigger this skill.
---

# Flow Viz Prompt

Converts any pipeline or workflow into a professional image-generation prompt, ready to paste into Gemini or any image-generation model.

Supports two visual styles:
- **NotebookLM Style** (default): Clean digital whiteboard aesthetic — professional, modern, presentation-ready
- **Cartoon & Sketch Style**: Hand-drawn, playful illustration aesthetic — warm, approachable, storytelling-friendly (inspired by [Angie Temaly's portfolio](https://angie.temaly.com/))

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

## Step 0: Style Detection (automatic, no prompting)

Infer the visual style from the user's trigger phrase:

| Style | Trigger keywords (any match) |
|-------|------------------------------|
| **Cartoon & Sketch** | "cartoon", "sketch", "hand-drawn", "playful", "cute", "fun", "illustration", "doodle", "卡通", "手绘", "涂鸦", "可爱风", "插画风", "趣味" |
| **NotebookLM** | All other cases (default) |

Announce the detected style at the start of the reply: `[Style: NotebookLM]` or `[Style: Cartoon & Sketch]`.

---

## Step 0: Granularity Detection (automatic, no prompting)

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

After the user confirms the control flow, select the template matching both the **style** and **granularity**, then generate the prompt:

| Style | Granularity | Template |
|-------|-------------|----------|
| NotebookLM | Reporting | Template A |
| NotebookLM | Code | Template B |
| Cartoon & Sketch | Reporting | Template C |
| Cartoon & Sketch | Code | Template D |

Output the complete text directly so the user can copy-paste it into Gemini. Generate the prompt in the language confirmed in Step 0.5.

---

## NotebookLM Visual Style Spec (fixed, do not modify)

```
Visual aesthetic: clean, modern, digital whiteboard feel, inspired by Google NotebookLM UI
Background: off-white or very light gray
Node shape: rounded rectangle
Lines: soft, with clear directional arrows
Font: sans-serif, clean and modern, generous whitespace

Color scheme (by layer):
  - Input layer: warm gray
  - Core processing nodes: soft blue tones
  - Sub-regions / parallel paths: teal-blue (primary/main path) / deep blue-purple (secondary path)
  - External references / databases: side-attached nodes with small icons
  - Output layer: soft orange
  - Dashed containers (sub-flows / parallel regions): dashed border

Layout direction: left-to-right (default) or top-to-bottom (when content is dense)
All text: [confirmed language]
```

## Cartoon & Sketch Visual Style Spec (alternative)

```
Visual aesthetic: hand-drawn, playful illustration style, inspired by cartoon storyboards
  and sketch-note aesthetics (reference: angie.temaly.com portfolio)
Background: warm cream / light parchment texture, or soft pastel gradient
Node shape: wobbly hand-drawn rounded rectangles with visible pencil/ink stroke edges
Lines: sketchy, slightly imperfect hand-drawn arrows with personality
  — can use curved/wavy paths instead of straight lines
Font: handwriting-style or comic sans-like font, friendly and readable

Color scheme (by layer):
  - Input layer: soft warm yellow or peach
  - Core processing nodes: playful pastel palette — mint green, sky blue, lavender, coral
  - Sub-regions / parallel paths: distinguished by different pastel fills
  - External references / databases: small doodle icons (notebook, cloud, database sketch)
  - Output layer: warm sunset orange or golden
  - Containers (sub-flows / parallel regions): hand-drawn dashed border with rounded corners

Special elements:
  - Small doodle decorations around key nodes (stars, sparkles, tiny arrows, speech bubbles)
  - Connector arrows can have small annotations as hand-written labels
  - Important nodes can have slight drop shadow or highlight glow (sketch style)
  - Use emoji or small icon sketches inside nodes to represent concepts visually
  - Overall feel: like a talented designer's whiteboard sketch or illustrated explainer

Layout direction: left-to-right (default) or top-to-bottom (when content is dense)
All text: [confirmed language]
```

### Cartoon & Sketch — code granularity additions

Applied on top of the Cartoon & Sketch base spec (does not replace it):

```
Node internal structure (code granularity only):
  - Each node is split into two zones, still hand-drawn style:
    - Top zone (title): step name in bold handwriting font
    - Bottom zone (code annotation): file path + function name in a
      monospace-handwriting hybrid font, slightly smaller, in a
      "sticky note" or "code snippet" mini-box inside the node
  - Code annotation format: 📄 path/to/file.rs → func_name()

Call relationship arrows (code granularity only):
  - Direct function call: hand-drawn solid arrow with slight wobble
  - Cross-process / CLI / tool call: hand-drawn dashed arrow with
    a small tag/label banner (speech-bubble style)
  - Arrow labels in handwriting font

Module boundaries:
  - Different languages use different pastel background fills:
    - TypeScript: light sky blue
    - Rust: light peach/coral
    - Python: light mint green
    - Shell / CLI: light lavender
  - Boundaries drawn as hand-sketched rounded rectangles with
    a small flag/banner label (e.g., "🦀 Rust", "🐍 Python")
```

---

### Code granularity — additional style rules (NotebookLM)

Applied on top of the NotebookLM base spec above (does not replace it):

```
Node internal structure (code granularity only):
  - Each node is split into two zones:
    - Top zone (title): step name, bold, normal font size
    - Bottom zone (code annotation): file path + function name, monospace font,
      one size smaller, slightly lighter color
  - Code annotation format: 📄 path/to/file.rs → func_name()
  - If multiple files participate in the same step, list them vertically, one per line

Call relationship arrows (code granularity only):
  - Direct function call: solid arrow
  - Cross-process / CLI / tool call: dashed arrow, labeled with call method
    (e.g., "subprocess", "tool call", "HTTP")
  - Arrow labels use monospace font, annotating the called function signature

Module boundaries:
  - Different languages / runtimes use different background tints:
    - TypeScript module: very light blue background
    - Rust module: very light orange background
    - Python module: very light green background
    - Shell / CLI: very light gray background
  - Boundaries separated by thin solid lines, with a small language label at the top
    (e.g., "TS", "Rust", "Python")
```

---

## Prompt Generation Templates

### Template A: Reporting Granularity (default)

Generate the prompt using the structure below, replacing `[...]` with the user-confirmed content:

```
Please generate a professional flowchart for use in a technical presentation slide deck.

**Topic**: "[topic name]"

### Visual Style Requirements (NotebookLM Style)

- Visual aesthetic: clean, modern, digital whiteboard feel, inspired by Google NotebookLM UI
- Element style: rounded rectangles as primary node shape, soft lines, clear directional arrows
- Background: clean off-white or very light gray
- Color scheme: input layer in warm gray; core processing area in blue tones;
  [if sub-paths: primary path teal-blue, secondary path deep blue/purple]; output layer soft orange
- Typography: sans-serif font, clean and modern, generous whitespace, balanced overall layout

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One warm-gray node per input source, listing key sub-items inside,
with special labels if applicable]
[All input nodes converge via arrows to a small circular merge node, then a single arrow continues right]

**[Column 2: Core Processing Area (main body, largest area)]**
[If large container: solid rounded-rect container labeled "[Step name]", containing sub-regions]
[Sub-regions wrapped in dashed borders, with region labels]
[Each sub-node: bold level/name + indented list of input sources and output ratios]
[External reference nodes: side-attached with database icon, arrow pointing into corresponding node,
labeled "[label]"]

**[Last Column: Output Layer]**
Arrow from core area pointing to a large orange output node labeled: "[output name]"
Inside the node, organized by dimension:
- [Dimension 1]: [value 1] | [value 2] | ...
- [Dimension 2]: [value A] | [value B] | ...
- [Dimension 3]: [value X] → [value Y] ([group label])

### Final Check
Ensure all text is clear and in [confirmed language]; dashed boxes correctly wrap sub-flows;
overall flow direction is clear; style is clean and professional, ready for PPT use.
```

**Example (reporting granularity):**
Topic: "Codex Agent Request Pipeline"
Input layer: User message (TUI chat input)
Core steps: TUI event handler → Agent control loop → API bridge (HTTP to OpenAI) → Response parser
Output layer: Applied code patches | Shell command output
Omitted: Auth initialization, telemetry setup

### Template B: Code Granularity

```
Please generate a professional technical design flowchart for use in a design review or architecture document.
Show code file-level and function-level call relationships.

**Topic**: "[topic name]"

### Visual Style Requirements (NotebookLM Style · Code Granularity)

- Visual aesthetic: clean, modern, digital whiteboard feel, inspired by Google NotebookLM UI
- Element style: rounded rectangles as primary node shape, soft lines, clear directional arrows
- Background: clean off-white or very light gray
- Color scheme: input layer in warm gray; core processing area in blue tones;
  [if sub-paths: primary path teal-blue, secondary path deep blue/purple]; output layer soft orange
- Typography: sans-serif for step titles, monospace for file paths and function names,
  generous whitespace

#### Code Granularity Style Rules
- **Node internal structure**: each node has two zones — top zone: step name (bold);
  bottom zone: code annotation (monospace, one size smaller, lighter color),
  format: `📄 file/path → function_name()`
- **Call relationship arrows**: direct function call = solid arrow;
  cross-process/CLI/tool call = dashed arrow labeled with call method
  (e.g., "subprocess", "tool call")
- **Arrow labels**: monospace font annotating the called function signature
- **Module boundaries**: different languages use different light background tints —
  TypeScript: very light blue; Rust: very light orange; Python: very light green;
  Shell/CLI: very light gray; boundary top has a small language label

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One warm-gray node per input source, listing key sub-items inside]
[All input nodes converge via arrows to a small circular merge node, then a single arrow continues right]

**[Column 2+: Core Processing Area (main body, largest area)]**
[If large container: solid rounded-rect container labeled "[Step name]", containing sub-regions]
[Sub-regions wrapped in dashed borders, with region labels]
[Each sub-node top zone: step name (bold)]
[Each sub-node bottom zone (monospace, gray):
  📄 [file path] → [entry function()]
  📄 [file path 2] → [helper function()] (if multiple files)]
[Arrows between nodes:
  - Solid arrow = direct call, labeled [called function signature]
  - Dashed arrow = indirect call, labeled "[call method]: [target function]"]
[Different language modules wrapped in corresponding tint, top label "TS" / "Rust" / "Python" / "Shell"]
[External reference nodes: side-attached with database/file icon, arrow pointing into corresponding node]

**[Last Column: Output Layer]**
Arrow from core area pointing to a large orange output node labeled: "[output name]"
Inside the node, organized by dimension:
- [Dimension 1]: [value 1] | [value 2] | ...
- [Dimension 2]: [value A] | [value B] | ...

### Final Check
Ensure all text is clear and in [confirmed language]; code paths and function names use monospace
and are legible; dashed boxes correctly wrap sub-flows; solid/dashed arrows are correctly
distinguished; language background tints are correctly applied; overall flow direction is clear;
style is clean and professional.
```

**Example (code granularity):**
Topic: "Codex Agent Request Pipeline"
Input layer: User message (TUI chat input)
Core steps:
  - `codex-rs/tui/src/app.rs → App::handle_event()` (Rust) — receives user input
  - `codex-rs/core/src/agent/control.rs → AgentControl::run()` (Rust) — agent loop
  - `codex-rs/core/src/api_bridge.rs → send_request()` (Rust) — HTTP call to OpenAI API
  - `codex-rs/core/src/apply_patch.rs → apply_patch()` (Rust) — applies file changes
Call relationships: `App` → solid arrow → `AgentControl`; `AgentControl` → dashed arrow (HTTP) → OpenAI API; `AgentControl` → solid arrow → `apply_patch`
Output layer: Applied code patches | Shell command output

### Template C: Cartoon & Sketch — Reporting Granularity

Generate the prompt using the structure below when Cartoon & Sketch style is detected:

```
Please generate a hand-drawn, cartoon-style flowchart illustration that feels like a talented
designer's whiteboard sketch or an illustrated explainer diagram.

**Topic**: "[topic name]"

### Visual Style Requirements (Cartoon & Sketch Style)

- Visual aesthetic: hand-drawn, playful illustration style, inspired by cartoon storyboards
  and sketch-note aesthetics
- Element style: wobbly hand-drawn rounded rectangles with visible pencil/ink stroke edges,
  sketchy imperfect arrows with personality, curved/wavy paths
- Background: warm cream or light parchment texture
- Color scheme: input layer in soft warm yellow/peach; core processing in playful pastels
  (mint green, sky blue, lavender, coral); output layer in warm sunset orange/golden
- Typography: handwriting-style font, friendly and readable, generous whitespace
- Decorations: small doodle elements (stars, sparkles, tiny icons) around key nodes;
  speech-bubble style labels on connectors; emoji or sketch icons inside nodes

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One warm-yellow/peach hand-drawn node per input source, with small doodle icons,
listing key sub-items inside]
[All input nodes converge via sketchy curved arrows to a merge point, then continue right]

**[Column 2: Core Processing Area (main body, largest area)]**
[If large container: hand-drawn container with sketchy border, labeled "[Step name]"]
[Sub-regions wrapped in hand-drawn dashed borders with rounded corners]
[Each sub-node: handwriting-bold step name + small doodle icon + indented list]
[External reference nodes: small notebook/cloud doodle icons, with sketchy arrow and label]

**[Last Column: Output Layer]**
Sketchy arrow from core area pointing to a warm orange/golden hand-drawn output node
labeled: "[output name]"
Inside the node, organized by dimension:
- [Dimension 1]: [value 1] | [value 2] | ...
- [Dimension 2]: [value A] | [value B] | ...

### Final Check
Ensure all text is clear and in [confirmed language]; hand-drawn style is consistent throughout;
dashed boxes correctly wrap sub-flows; overall flow is clear despite the playful aesthetic;
the diagram feels warm, approachable, and storytelling-friendly.
```

### Template D: Cartoon & Sketch — Code Granularity

```
Please generate a hand-drawn, cartoon-style technical design flowchart that shows code-level
details in a playful, illustrated style — like a talented designer explaining architecture
on a whiteboard with colored markers.

**Topic**: "[topic name]"

### Visual Style Requirements (Cartoon & Sketch Style · Code Granularity)

- Visual aesthetic: hand-drawn, playful illustration style with code-level detail
- Element style: wobbly hand-drawn rounded rectangles, sketchy arrows, curved paths
- Background: warm cream or light parchment texture
- Color scheme: input layer in soft warm yellow/peach; core processing in playful pastels;
  output layer in warm sunset orange/golden
- Typography: handwriting font for step titles; monospace-handwriting hybrid for code paths;
  generous whitespace

#### Code Granularity Style Rules (Sketch Edition)
- **Node internal structure**: each node has two zones — top zone: step name in bold handwriting;
  bottom zone: code annotation in a "sticky note" mini-box inside the node,
  format: `📄 file/path → function_name()`
- **Call relationship arrows**: direct call = sketchy solid arrow with wobble;
  cross-process/tool call = sketchy dashed arrow with speech-bubble label
- **Module boundaries**: different languages use pastel fills —
  TypeScript: light sky blue 🟦; Rust: light peach 🦀; Python: light mint 🐍;
  Shell: light lavender; boundary has a small flag/banner label

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One warm-yellow hand-drawn node per input source with doodle icons]
[Converge via sketchy arrows to merge point]

**[Column 2+: Core Processing Area]**
[Hand-drawn containers with sketchy borders]
[Each sub-node top zone: step name in bold handwriting]
[Each sub-node bottom zone (sticky-note style):
  📄 [file path] → [entry function()]
  📄 [file path 2] → [helper function()]]
[Arrows: solid sketchy = direct call; dashed sketchy with speech-bubble = indirect call]
[Language modules in corresponding pastel fill with flag labels]
[External references as doodle icons with sketchy arrows]

**[Last Column: Output Layer]**
Sketchy arrow to warm orange hand-drawn output node: "[output name]"
Inside the node:
- [Dimension 1]: [value 1] | [value 2] | ...

### Final Check
Ensure hand-drawn style is consistent; code paths legible in sticky-note boxes;
solid/dashed arrows correctly distinguished; language fills applied;
overall feel is warm and approachable while remaining technically informative.
```

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
