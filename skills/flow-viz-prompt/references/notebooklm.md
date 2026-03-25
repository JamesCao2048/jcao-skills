# NotebookLM Visual Style Spec

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

## Code granularity — additional style rules

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

## Template A: Reporting Granularity

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

---

## Template B: Code Granularity

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
