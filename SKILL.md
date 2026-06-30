---
name: control-system-structure-block-diagram-drawing
description: Draw rigorous academic control-system structure block diagrams from Simulink screenshots, textbook diagrams, flight-control models, or user sketches. Use when the user asks for 控制系统结构框图, 控制律框图, flight-control block diagrams, Simulink-to-paper diagrams, 横平竖直 lines, academic/Nature-style diagram polishing, Chinese-labeled control diagrams, or asks to compare/fix diagram topology, feedback loops, summing junctions, branch points, transfer functions, gains, and signal routing.
---

# Control System Structure Block Diagram Drawing

## Core Principle

Draw the control logic first and the picture second. A good diagram is not only neat; it preserves signal topology, feedback signs, branch points, and every input-output relationship from the source model.

For raster effect images and academic-looking block diagrams, use the built-in Codex image generation tool by default. Do not switch to Python plotting for these effect diagrams unless the user explicitly asks for deterministic code-native output.

## Figure Type

Choose the intended level before drawing:

- **Detailed control-law diagram**: Preserve low-level modules such as Selector, Dot Product, DP, transfer functions, limiters, and exact signal names. Use when the user needs implementation fidelity or Simulink correspondence.
- **System structure diagram**: Merge low-level blocks into functional modules such as 状态反馈选择, 误差与指令形成, 增益调度, 求和与输出. Use when the user needs a thesis/paper overview figure.
- **Hybrid academic diagram**: Preserve true topology from the detailed model while cleaning layout, Chinese labels, and visual hierarchy. Prefer this when the user asks for both 学术严谨性 and 美观.

When the user asks “有什么区别”, explain whether the change is topological, semantic, or only presentational.

## Workflow

1. **Extract topology**
   - List external inputs and outputs with port numbers.
   - List blocks and their roles.
   - List summing junctions and signs.
   - List branches and branch-dot locations.
   - List feedback loops and feed-forward paths.
   - List gain-scheduling outputs and their corresponding multipliers.

2. **Create a connection table before generating**

   Use this internal pattern:

   ```text
   Inputs:
   1 高度 H -> fcsnz.H and Look-Up Table
   2 马赫数 M -> fcsnz.M

   Blocks:
   fcsnz -> KNz_alpha -> Dot Product KNz_alpha
   x0, y -> Sum(+x0, -y) -> Selector x -> delta_alpha, delta_q, Nz

   Outputs:
   Sum/output -> DP4 -> Nz_control
   Integral path -> DP1 -> fbi
   ```

   Keep the table concrete enough that a disconnected block would be obvious.

3. **Run a topology audit**

   Check these before calling image generation:

   - Every non-port block has at least one visible input and one visible output.
   - Every multiplier/dot-product block has both a signal input and a gain input.
   - Every scheduled gain label connects to the correct multiplier; do not merge distinct gains into an ambiguous bus unless the source model does.
   - Every summing junction has explicit `+` and `-` signs.
   - Every feedback path closes back into the correct summing junction or output path.
   - Branches have solid black dots; crossings without connection have no dots.
   - Selector and measurement-derived blocks are not floating.
   - Output ports match the source labels and numbers.

4. **Generate with strict diagram constraints**

   Use the image tool prompt as a specification, not as a vibe request. Include:

   - Use case: `scientific-educational / infographic-diagram`.
   - Exact title and level tag.
   - Source image roles, if images are provided.
   - Required blocks and exact labels.
   - Connection table or numbered topology.
   - Orthogonal wires only: horizontal/vertical with 90-degree turns.
   - Arrowheads showing signal direction.
   - Branch-dot and crossing rules.
   - Circular summing junctions with `+/-`.
   - No floating modules, no disconnected arrows, no diagonal wires.
   - Chinese text must fit inside boxes.
   - Restrained academic style: white background, black lines, optional pale blue for gain scheduling, pale red for compensators, pale green/orange for command/limiter emphasis.

5. **Save and inspect**

   - Copy the generated image into the project workspace when it belongs to the project.
   - Inspect the saved image visually.
   - If the image tool distorts exact variable names, report that final thesis/paper use requires text proofreading.
   - Iterate targeted fixes when topology is wrong; do not accept a pretty but incorrect diagram.

## Academic Drawing Standards

- Keep lines horizontal and vertical. Route wires around blocks; never through labels.
- Keep labels short and inside boxes. Use Chinese function labels with code names on a second line when helpful.
- Use enough whitespace for readability.
- Put inputs on the left, processing blocks in the middle, gains/multipliers to the right, and outputs on the far right unless the source convention requires otherwise.
- Use circular summing nodes for algebraic sums and triangular blocks for scalar gains.
- Use transfer-function blocks for filters/compensators, preserving formulas such as `(15s+10)/(s+10)` or `5s/(5s+1)`.
- Prefer black-and-white academic style when the figure is a detailed control-law diagram; use restrained color only to clarify functional groups.
- Do not add marketing-style decoration, heavy shadows, gradient backgrounds, or unrelated icons.

## Common Control-Diagram Pitfalls

- Do not draw a block just because it appears in the source; connect its inputs and outputs.
- Do not omit measurement branches feeding selector, correction, or feedback blocks.
- Do not hide gain-to-multiplier connections. Each gain path such as `KNz_alpha`, `KNz_q`, `K_Vt`, or `K_I` must visibly reach its own multiplier.
- Do not confuse a detailed implementation diagram with a high-level structure diagram. If abstracting, preserve the logic and state what was merged.
- Do not treat a visual crossing as a connection. Only branch dots indicate connection.
- Do not invent an output or remove one unless the user asks for a simplified figure.

## Prompt Template

Use and adapt this prompt for image generation:

```text
Use case: scientific-educational / infographic-diagram.
Asset type: academic control-system block diagram with Chinese labels.
Primary request: redraw the provided control-system diagram into a rigorous publication-ready structure block diagram.

Exact title: "<title>".
Top-left tag: "<level>".

Strict rules:
- All wires are horizontal/vertical with 90-degree turns.
- Every non-port block has visible input and output.
- Every multiplier has one signal input and one gain input.
- Use circular summing junctions with explicit + and - signs.
- Use solid black dots only for true branches.
- Crossings without connection have no dot.
- No floating modules, no disconnected arrows, no diagonal wires.
- Chinese labels must fit inside boxes.

Connection table:
1. <source> -> <block/input>
2. <block/output> -> <next block>
3. <feedback path> -> Sum(+/-)

Layout:
Left inputs; middle error/selector/feedback processing; upper gain scheduling and lookup tables; right multipliers/gains; far-right summing and outputs.
Restrained academic style, white background, black signal lines, optional pale functional colors.
```

## Response Style

When delivering the result, briefly state:

- Which level was drawn: detailed, structural, or hybrid.
- Where the file was saved.
- Any remaining risks, especially variable-name proofreading or topology items that need user confirmation.
