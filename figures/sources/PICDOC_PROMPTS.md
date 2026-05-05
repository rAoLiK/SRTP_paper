# picdoc 绘图 Prompt 集 v4（最终版）

## 设计哲学

v1 出图视觉效果好但有 4 处 SCI 违规；v2/v3 把约束打太死，picdoc 变得"怯懦"，
出图过简、过淡。v4 的关键洞察：**v1 真正违规的只有 4 件具体物**：

1. ★ / ⚠ / emoji 装饰符号
2. 底部图例栏（labeled colored squares row）
3. in-image caption（图里写的整句描述文字）
4. 顶部彩色 banner（白字深底的标题条幅）

v1 的**其他视觉手段都是 IEEE TVLSI / TCAS / TCAD 期刊里常见的、合法的、加强阅读的**：
- 虚线强调框
- 不同饱和度的 tint 色块
- 轻微 drop shadow（仅限创新模块）
- 粗一点的跨栏箭头 + 斜体短语
- 小型 inset 图

**v4 = v1 的视觉骨架 − 4 件违规物 + 显式列举"允许使用"的视觉手段**。

显式列举允许，是为了避免 picdoc 因"否定词太多"而变怯懦。

---

## 使用说明

- 模板选「流程图」（不要选机制图、信息图、PPT 内容、自由生图）
- 出图后保存到：
  - `figures/fig8_zbabm_ppsc_evolution.pdf`（推荐 PDF）或 `.png`
  - `figures/fig9_sys_innov.pdf` 或 `.png`
- 出图后告诉我"图已就位"，我替换 thesis.tex 中的 TikZ 块

---

## Prompt 1 — Figure 8: ZBA-BM PPSC three-stage evolution

Generate a publication-quality block diagram in the style of figures from
IEEE Transactions on VLSI Systems (TVLSI) and IEEE Transactions on Circuits
and Systems I (TCAS-I). The figure shows the three-stage architectural
evolution of an approximate Booth multiplier:
  baseline (a) → R4ABE2 approximation (b) → our novel ZBA-BM with PPSC (c).

==== ALLOWED VISUAL ELEMENTS (encouraged for clarity) ====

The following visual devices are STANDARD in IEEE journal block diagrams and
should be used for clarity:

1. **Per-panel tint backgrounds**, moderately saturated (NOT near-white):
     Panel (a):  neutral gray  fill #DDDDDD
     Panel (b):  warm yellow   fill #FFE9A8
     Panel (c):  warm orange   fill #FFCFA0
   These are reading-flow cues, not decoration.

2. **Dashed emphasis frames** around the novel sub-assembly in panel (c):
   draw a dashed orange rectangle (line weight 1.0 pt, dash 4-2) wrapping
   the K-map equation box + 2:1 MUX, to mark "this is the new contribution".
   This is a STANDARD IEEE figure idiom.

3. **Solid bold-outline frames** for primary functional blocks
   (MUX, equation box, pp_j box). 0.6–0.8 pt black outline, white interior fill,
   so they "pop" against the panel tint.

4. **A subtle drop shadow** (offset 1 pt, 25% opacity, blur 1.5 pt) is allowed
   ONLY on the 2:1 MUX in panel (c) — to give the novel module a single
   light-touch depth cue. No shadows anywhere else.

5. **Inter-panel arrows**, 1.2 pt thick black, with italic 9 pt labels
   above/below. Two arrows total: (a)→(b) labeled "approximate ±2A";
   (b)→(c) labeled "compensate row 0". These can be slightly curved if it
   helps avoid overlap with panel borders.

6. **Italic 9 pt fact annotations** below the pp_j output of each panel:
     Panel (a):  μ = 0  (exact)
     Panel (b):  μ = −0.125  (Row-0 bias)
     Panel (c):  μ ≡ 0  (HW-guaranteed)
   These are the figure's scientific payload — keep them.

7. **Strikethrough on +2A / −2A** in panel (b)'s MUX table, with replacement
   "+A" / "−A" written immediately to the right of the strike. Plain black
   strike, plain black replacement (no red).

8. **Standard IEEE trapezoidal MUX symbol** in panel (c) (NOT a rectangle —
   use the 2:1 MUX trapezoid shape, wider at left, narrower at right).

==== STRICTLY FORBIDDEN — DO NOT INCLUDE ====

These four items violate IEEE figure conventions and MUST be excluded:

1. **NO star / asterisk decorations**: no ★, ☆, ✱, ✦, or any character used
   purely to "draw attention". No checkmark ✓ or warning ⚠ either. No emoji.

2. **NO bottom legend bar**: do NOT add a row of small colored squares
   labeled "BASELINE", "INNOVATION", "approx", "control flow", or anything
   else at the bottom of the figure. The figure has NO legend strip at all —
   any legend goes in the LaTeX caption text, never in the image.

3. **NO in-image caption**: do NOT write a sentence like "Evolution of
   approximate Booth multipliers..." or any descriptive paragraph anywhere
   on the figure. The figure ends at the data.

4. **NO colored banner / header strip** behind the panel titles. Panel
   titles "(a)" "(b)" "(c)" sit as plain text on the panel's tint, NOT on
   a dark colored bar with white text.

Also forbidden: stats annotations like "−80% transistors" or "−32% area"
inside the figure (these belong in the paper text). Marketing-style words
like "BASELINE", "INNOVATION", "EXPENSIVE", in all caps, anywhere.

==== PANEL CONTENT ====

Each panel: 8 mm internal padding, 4 mm gap to next panel.
Aspect ratio of the whole figure: approximately 2.4 : 1 (wide horizontal),
suitable for full-text-width single-column figure.

PANEL (a) — header reads:  **(a) Radix-4 Booth**  (11 pt bold black, plain
text on the gray tint)
Top: "Booth window  B_W = {b_{2i+1}, b_{2i}, b_{2i-1}}" rectangle.
Below (vertical thin arrow): MUX rectangle with case-table:
    000       0
    001/010   +A
    011       +2A
    100       −2A
    101/110   −A
    111       0
Below MUX (arrow): pp_j rectangle.
Italic annotation below pp_j: "μ = 0  (exact)"

PANEL (b) — header reads:  **(b) R4ABE2 approximation**
Same structure: B_W → MUX → (intermediate equation box) → pp_j.
The MUX case-table has +2A and −2A strikethrough with +A / −A replacements.
Below the MUX, the K-map equation box (white interior, 0.6 pt black outline):
    pp_j = (¬z) ∧ (a_j ⊕ n)
Then arrow to pp_j box.
Italic annotation: "μ = −0.125  (Row-0 bias)"

PANEL (c) — header reads:  **(c) novel ZBA-BM with Row-0 PPSC**
where ONLY the word "novel" is in accent orange #D97706, the rest plain bold black.
Top: same B_W rectangle.
Below: K-map equation box  pp_j = (¬z) ∧ (a_j ⊕ n).
Below: standard IEEE trapezoidal 2:1 MUX, accent-orange outline 1.2 pt,
       white interior, with light drop shadow.
       - Top input (left side): "a_{j-1} << 1"
       - Bottom input (left side): "pp_j" (driven from the equation box above)
       - Select input (from below): "row0 ∧ B_W = 100"
       - Output (right side): "pp_j^c"
Wrap the equation box + MUX in a dashed orange rectangle (1 pt dash 4-2).
Italic annotation below output: "μ ≡ 0  (HW-guaranteed)"

==== FINAL RENDER CHECK ====
Confirm absence of: ★ star / ⚠ warning / ✓ check / emoji / bottom legend bar
of colored squares / caption sentence on figure / colored banner with white
title text / "BASELINE" or "INNOVATION" all-caps labels / drop shadows
anywhere except the (c) MUX / red coloring on strikethroughs.
If any are present, regenerate.

---

## Prompt 2 — Figure 9: Two-tier system-level innovation

Generate a publication-quality block diagram in the style of IEEE TVLSI /
TCAS-I figures, showing two tiers of system-level innovation in an
approximate hardware accelerator. The figure has TWO subfigures stacked
vertically: (a) on top, (b) on bottom.

==== ALLOWED VISUAL ELEMENTS ====

1. **Subfigure background tints** (subtle but visible, NOT near-white):
     Subfigure (a):  cool gray  fill #E0E5EA
     Subfigure (b):  warm cream fill #FFF4E0
   Each subfigure has a thin 0.6 pt black border.

2. **Dashed emphasis frames** around novel sub-assemblies:
   - In (a): a dashed orange rectangle (1 pt, dash 4-2) wraps the three
     non-baseline stage boxes S2, S3, S4 — to mark "these are approximated".
   - In (b): one dashed orange rectangle wraps the GatedPE box; another
     dashed orange rectangle wraps the {exact MAC + ZBA-BM PPSC + 2:1 MUX}
     sub-assembly. Two dashed regions = two innovation tiers.

3. **Stage boxes with differential styling** (subfigure a):
   - S1 and S5 (baseline / exact stages): 0.6 pt black outline, white interior.
   - S2, S3, S4 (approximated stages): 1 pt accent-orange outline #D97706,
     light-orange interior fill #FFE0C2.

4. **Standard IEEE trapezoidal MUX symbol** in (b) for the 2:1 dual-path MUX.

5. **Subtle drop shadow** allowed ONLY on the GatedPE outer box and the
   dual-path MUX in (b). Offset 1 pt, 25% opacity, blur 1.5 pt. Nowhere else.

6. **One dashed line** in (b): the "clk_en" output from GatedPE going up out
   of the box. This is the only dashed connection. All other connectors are
   solid.

7. **Bit-width labels on wires**: "8 b", "16 b", "32 b" in 9 pt italic.

==== STRICTLY FORBIDDEN — DO NOT INCLUDE ====

1. **NO star / asterisk decorations**: no ★, ☆, ✱, no checkmark ✓, no warning ⚠,
   no emoji.

2. **NO figure caption inside the image**. The figure stops at the data;
   a one-paragraph descriptive sentence anywhere on the image is forbidden.

3. **NO "Tier 1:" / "Tier 2:" / "Innovation 1:" / "Innovation 2:" textual
   labels**. The two subfigures are simply "(a)" and "(b)" with brief plain
   titles. Innovation is communicated by dashed-frame highlighting, not by
   the word "Innovation".

4. **NO bottom legend bar** with labeled colored squares.

5. **NO mini bar-chart icons** or marketing taglines like "1.0% accuracy
   drop, 14% power saving", "ReLU sparsity > 50%", "no critical-path penalty".
   These are paper-text content, not figure content.

6. **NO file paths, code line numbers, config class names** inside the
   figure (e.g., "PE.scala line 47", "ApproxGemminiRocketConfig").

7. **NO colored banner / horizontal title strip** above each subfigure.

==== SUBFIGURE (a) — Layer-wise approx_level allocation ====

Header (top-left, 11 pt bold black, plain text on the gray tint):
    **(a) Layer-wise approx_level allocation**

Layout: horizontal, left-to-right, on the gray tint background.

Left element: a 4×4 grid of small empty squares (16 cells), each cell 0.5 pt
black outline on white. Below the grid in 9 pt plain text:
    "Gemmini mesh 16×16"

Right of the mesh: five rectangular stage boxes in a row, equal size,
labeled inside (two text lines per box):
    Box 1: "S1 Stem"     "4E 0A"
    Box 2: "S2 Block 2"  "2E 2A"
    Box 3: "S3 Block 3"  "2E 2A"
    Box 4: "S4 Block 4"  "1E 3A"
    Box 5: "S5 Head"     "4E 0A"

Style:
- S1, S5: white interior, 0.6 pt black outline.
- S2, S3, S4: light-orange interior #FFE0C2, 1 pt accent-orange outline
  #D97706.
- A dashed orange rectangle (1 pt, dash 4-2) wraps S2 + S3 + S4 together
  as a group, signaling "the approximated stages".

Below the row of stage boxes: a single thin black arrow curves from one
non-baseline box (e.g., S3) downward and leftward to land on ONE PE cell
in the mesh icon. Beside the arrow, in 9 pt italic:  "approx_level"

==== SUBFIGURE (b) — Inside one PE: gating + dual-path MAC ====

Header (top-left, 11 pt bold black, plain text on the cream tint):
    **(b) Inside one PE: gating + dual-path MAC**

Layout: horizontal, left-to-right.

Inputs (left edge, three short horizontal arrows):
    "in_a (8 b)"
    "in_b (8 b)"
    "in_c (32 b)"

Stage 1 — GatedPE box (left):
- Outlined in 1 pt accent orange #D97706, light-orange fill #FFE0C2,
  with a subtle drop shadow.
- Inside, two text lines:  "GatedPE"  /  "zero-detect + clk-gate"
- One dashed line exits from the top of the box, labeled "clk_en"
  (going off-figure). This is the ONLY dashed connection.
- A dashed orange rectangle wraps just this GatedPE box.

Stage 2 — two parallel rectangles (center, no fill, 0.6 pt black outline):
    Top:    "exact MAC"
    Bottom: "ZBA-BM PPSC"
Both receive in_a and in_b from GatedPE; both feed Stage 3.

Stage 3 — 2:1 MUX (right of Stage 2):
- Standard IEEE trapezoidal MUX symbol, accent-orange outline 1.2 pt,
  white interior, subtle drop shadow.
- Top input: from "exact MAC".
- Bottom input: from "ZBA-BM PPSC".
- Select from below labeled  "approx_level"
- Output right side labeled  "product (16 b)"

A dashed orange rectangle wraps the {exact MAC + ZBA-BM PPSC + 2:1 MUX}
group as the second innovation cluster.

Stage 4 — Adder (far right):
- Plain black circle with "+" inside, 0.6 pt outline, no fill, no shadow.
- Inputs: MUX output ("product (16 b)") and "in_c (32 b)" routed in.
- Output exits right edge: "out_d (32 b)".

==== LAYOUT ====
- Subfigure (a) height ≈ 35% of total. Subfigure (b) height ≈ 65%.
- Total aspect ratio ≈ 4:3 (slight portrait).
- 6 mm internal padding, 4 mm gap between subfigures.
- Output: vector PDF / SVG preferred, PNG 600 dpi acceptable.

==== FINAL RENDER CHECK ====
Confirm absence of: ★ star / ⚠ / ✓ / emoji / "Tier" or "Innovation" textual
labels / mini bar-chart / bottom legend bar / caption sentence / file paths /
colored banner / drop shadows on more than three elements / red coloring.
If any are present, regenerate.

---

## 出图后的集成步骤（我做）

1. 你把 picdoc 输出的两张图保存到：
   - `figures/fig8_zbabm_ppsc_evolution.pdf`（推荐 PDF 矢量）或 `.png`
   - `figures/fig9_sys_innov.pdf` 或 `.png`

2. 告诉我"图已就位"

3. 我做：
   - 把 thesis.tex §2.2.6 的 TikZ 块替换为 `\includegraphics[width=\textwidth]{fig8_...}`
   - 把 thesis.tex §4.2.1 的 TikZ 块替换为 `\includegraphics[width=\textwidth]{fig9_...}`
   - 编译验证 + commit

---

## v1 / v2 / v3 / v4 视觉手段对照表

| 手段 | v1 | v2 | v3 | v4（终版）|
|---|---|---|---|---|
| 三档 tint 色 | 鲜艳橙红绿 | 全删 | 极淡 | 中等饱和（可见但不刺眼）|
| 虚线强调框 | 有 | 删 | 删 | **保留**（IEEE 标准手法）|
| Drop shadow | 多处 | 删 | 删 | **仅限创新模块**（1–2 处）|
| 粗跨栏箭头 | 有 | 细 | 细 | 1.2 pt + 斜体短语 |
| μ 偏差量 | 有 | 删 | 保留 | 保留 |
| accent color | 大块 | 不许 | 仅 outline | outline + 少量 fill + dashed 框 |
| ★ 五角星 | 有 | 删 | 删 | 删 |
| 底部 legend 栏 | 有 | 删 | 删 | 删（picdoc 仍可能私自加，需复查）|
| in-image caption | 有 | 删 | 删 | 删 |
| 顶部彩色 banner | 有 | 删 | 删 | 删 |

---

## 如果 picdoc 私自加了违规元素

直接告诉它：
> "Redraw the figure removing the following items that violate IEEE journal
> conventions: [具体列出，如 'the bottom row showing BASELINE/INNOVATION
> labels with colored squares', 'any star symbols', 'any caption sentences'].
> Keep everything else identical."

不要改 prompt 主体重新生成——picdoc 的"风格"已经定了，差异修正比从头生成更稳。
