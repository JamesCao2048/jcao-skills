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

After the user confirms the control flow, select the template matching the granularity and generate the prompt. Output the complete text directly so the user can copy-paste it into Gemini. Generate the prompt in the language confirmed in Step 0.5.

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

### Code granularity — additional style rules

Applied on top of the base spec above (does not replace it):

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

## Cartoon & Sketch Visual Style Spec

Inspired by [angie.temaly.com](https://angie.temaly.com/) — a playful, hand-drawn illustration aesthetic that makes technical diagrams feel approachable and engaging.

```
Visual aesthetic: hand-drawn, playful cartoon/sketch illustration style,
  as if drawn on a notebook page with colored markers and pens
Background: off-white paper texture with subtle grid or dot pattern (like a notebook page)
Node shape: hand-drawn rounded rectangles with slightly wobbly/imperfect edges,
  as if sketched by hand with a marker
Lines: hand-drawn style arrows with slight wobble, playful curved connectors
  instead of straight lines, arrow tips are hand-drawn triangles
Font: handwriting-style or casual rounded sans-serif (e.g., Comic Neue, Caveat, or
  Patrick Hand feel), slightly tilted text for playfulness

Color scheme (by layer):
  - Input layer: pastel yellow with hand-drawn border
  - Core processing nodes: pastel blue/lavender with doodle-style icons
  - Sub-regions / parallel paths: pastel green (primary) / pastel pink (secondary)
  - External references / databases: small hand-drawn icons (cloud doodle, folder sketch)
  - Output layer: pastel coral/orange with a small star or sparkle doodle
  - Containers: hand-drawn dashed borders, slightly irregular

Decorative elements:
  - Small doodle icons next to node labels (e.g., gear ⚙️ for processing,
    lightbulb 💡 for input, rocket 🚀 for output)
  - Occasional small stars, dots, or squiggles as decoration (not excessive)
  - Subtle drop shadows that look hand-drawn (cross-hatching style)

Layout direction: left-to-right (default) or top-to-bottom (when content is dense)
All text: [confirmed language]
```

### Code granularity — additional Cartoon & Sketch style rules

Applied on top of the Cartoon & Sketch base spec above:

```
Node internal structure (code granularity only):
  - Each node looks like a hand-drawn sticky note or index card
  - Top zone (title): step name in bold handwriting style
  - Bottom zone (code annotation): file path + function name in a
    smaller handwriting-monospace hybrid font, inside a small
    hand-drawn code block (like a torn piece of paper)
  - Code annotation format: 📝 path/to/file.rs → func_name()

Call relationship arrows (code granularity only):
  - Direct function call: hand-drawn solid arrow with slight curve
  - Cross-process / CLI / tool call: hand-drawn dashed arrow with
    a small lightning bolt or gear icon, labeled with call method
  - Arrow labels use casual handwriting font

Module boundaries:
  - Different languages / runtimes use different pastel background tints
    with hand-drawn borders:
    - TypeScript module: pastel blue sticky note
    - Rust module: pastel orange sticky note
    - Python module: pastel green sticky note
    - Shell / CLI: light gray torn paper
  - Boundaries have wobbly hand-drawn borders with a small doodle
    language icon at the top corner
```

---

## Prompt Generation Templates

### Style Selection

When generating prompts, select the visual style section matching the detected style:
- **NotebookLM**: Use Template A/B with NotebookLM visual style requirements
- **Cartoon & Sketch**: Use Template C/D with Cartoon & Sketch visual style requirements

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

Generate the prompt using the structure below, replacing `[...]` with the user-confirmed content:

```
Please generate a playful, hand-drawn style flowchart illustration, as if sketched on a notebook page with colored markers.

**Topic**: "[topic name]"

### Visual Style Requirements (Cartoon & Sketch Style)

- Visual aesthetic: hand-drawn cartoon/sketch illustration, playful and approachable,
  as if drawn on notebook paper with colored markers and pens
- Element style: hand-drawn rounded rectangles with slightly wobbly/imperfect edges,
  playful curved arrow connectors with hand-drawn triangle tips
- Background: off-white paper texture with subtle grid or dot pattern
- Color scheme: input layer in pastel yellow; core processing area in pastel blue/lavender;
  [if sub-paths: primary path pastel green, secondary path pastel pink]; output layer pastel coral/orange
- Typography: handwriting-style or casual rounded sans-serif font, slightly playful
- Decorative elements: small doodle icons next to labels (gear for processing, lightbulb for input,
  rocket for output), occasional small stars or squiggles as decoration (not excessive)
- Containers: hand-drawn dashed borders with slightly irregular edges

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One pastel-yellow hand-drawn node per input source, with a small lightbulb doodle,
listing key sub-items inside in handwriting font]
[All input nodes converge via curvy hand-drawn arrows to a small doodle merge point,
then a single curvy arrow continues right]

**[Column 2: Core Processing Area (main body, largest area)]**
[If large container: hand-drawn rounded container with wobbly border labeled "[Step name]",
containing sub-regions]
[Sub-regions wrapped in hand-drawn dashed borders with region labels]
[Each sub-node: bold handwriting label + indented list items, with small gear/tool doodles]
[External reference nodes: side-attached with small hand-drawn cloud/folder icons,
curvy arrow pointing into corresponding node, labeled "[label]"]

**[Last Column: Output Layer]**
Curvy hand-drawn arrow from core area pointing to a pastel coral/orange output node
with a small rocket or star doodle, labeled: "[output name]"
Inside the node, organized by dimension:
- [Dimension 1]: [value 1] | [value 2] | ...
- [Dimension 2]: [value A] | [value B] | ...

### Final Check
Ensure all text is clear and in [confirmed language]; the overall feel is playful and hand-drawn
but still technically accurate; doodle decorations enhance readability without cluttering;
flow direction is clear; suitable for blog posts, social media, or approachable presentations.
```

### Template D: Cartoon & Sketch — Code Granularity

```
Please generate a playful, hand-drawn style technical design flowchart, as if sketched on a notebook page.
Show code file-level and function-level call relationships in a fun, approachable way.

**Topic**: "[topic name]"

### Visual Style Requirements (Cartoon & Sketch Style · Code Granularity)

- Visual aesthetic: hand-drawn cartoon/sketch illustration, playful and approachable
- Element style: hand-drawn rounded rectangles with wobbly edges, curvy arrow connectors
- Background: off-white paper texture with subtle grid or dot pattern
- Color scheme: input layer in pastel yellow; core processing area in pastel blue/lavender;
  [if sub-paths: primary path pastel green, secondary path pastel pink]; output layer pastel coral/orange
- Typography: handwriting-style for step titles, handwriting-monospace hybrid for code paths,
  playful feel throughout

#### Code Granularity Style Rules (Sketch Edition)
- **Node internal structure**: each node looks like a hand-drawn sticky note or index card;
  top zone: step name in bold handwriting; bottom zone: code annotation in smaller
  handwriting-monospace inside a small hand-drawn code block (like a torn piece of paper),
  format: `📝 file/path → function_name()`
- **Call relationship arrows**: direct function call = hand-drawn solid curvy arrow;
  cross-process/CLI/tool call = hand-drawn dashed curvy arrow with small lightning bolt icon,
  labeled with call method
- **Arrow labels**: casual handwriting font
- **Module boundaries**: different languages use different pastel sticky-note backgrounds —
  TypeScript: pastel blue; Rust: pastel orange; Python: pastel green;
  Shell/CLI: light gray torn paper; wobbly hand-drawn borders with doodle language icon at top

### Flowchart Structure and Content ([layout direction] layout, all text in [confirmed language])

**[Column 1: Input Layer]**
[One pastel-yellow hand-drawn node per input source, with lightbulb doodle]
[Curvy hand-drawn arrows converge to a doodle merge point]

**[Column 2+: Core Processing Area]**
[Hand-drawn containers with wobbly borders]
[Sub-regions wrapped in hand-drawn dashed borders]
[Each sub-node top zone: step name in bold handwriting]
[Each sub-node bottom zone (smaller, in torn-paper code block):
  📝 [file path] → [entry function()]
  📝 [file path 2] → [helper function()] (if multiple files)]
[Arrows between nodes:
  - Solid curvy arrow = direct call, labeled in handwriting
  - Dashed curvy arrow + ⚡ = indirect call, labeled "[call method]: [target function]"]
[Different language modules on different pastel sticky-note backgrounds]
[External reference nodes: hand-drawn cloud/folder doodle icons]

**[Last Column: Output Layer]**
Curvy arrow to pastel coral/orange node with rocket doodle, labeled: "[output name]"
Inside the node:
- [Dimension 1]: [value 1] | [value 2] | ...
- [Dimension 2]: [value A] | [value B] | ...

### Final Check
Ensure all text is clear and in [confirmed language]; code paths are legible despite playful font;
hand-drawn style is consistent; solid/dashed arrows are visually distinct; the diagram is
technically accurate while feeling fun and approachable.
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
| Cartoon style used with wrong template | Cartoon & Sketch style must use Template C/D, not Template A/B — the visual language is completely different |
| Cartoon style too cluttered with doodles | Decorative elements should enhance, not overwhelm — max 1 small icon per node, sparse stars/squiggles |
| Mixing styles in a single prompt | Never mix NotebookLM and Cartoon & Sketch elements in one prompt — pick one and stay consistent |
