# Cartoon & Sketch Visual Style Spec

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

## Code granularity — additional Cartoon & Sketch style rules

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

## Template C: Reporting Granularity

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

---

## Template D: Code Granularity

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
