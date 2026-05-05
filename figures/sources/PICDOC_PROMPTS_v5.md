# PicDoc v5 提示词（fig8 + fig9 重出）

> **背景**：v4 版本 fig8 / fig9 用户反馈"配色单调、布局拥挤、特殊符号过多、不够 SCI 风格"。
> **v5 目标**：SCI 期刊视觉规范 + 信息密度可控 + 配色克制但不寡淡 + 适当虚线框/分组指示加强层级。
> **复制后直接喂 picdoc**：每张图一段，提示词含**风格规范 + 内容骨架 + 颜色表 + 禁止项**四段。

---

## 通用风格规范（fig8 / fig9 共享，前置说明）

```
全局风格要求（SCI 期刊插图规范，所有图必须遵守）：
- 矢量级清晰度：导出 PDF 优先；若仅能 PNG，则≥600 dpi，画布宽 ≥3000 px
- 字体：英文用 Helvetica / Arial，中英混排时中文用 Source Han Sans / 思源黑体；正文 9-10pt，标题 11-12pt
- 配色：低饱和、SCI 期刊常见学院风（参考 Nature/IEEE 配色），主色不超过 4 种
- 禁止使用：五角星 ★、爱心、卡通图标、emoji、霓虹色（#FF00FF 类）、阴影（drop-shadow）、3D 立体、圆角过大（>4pt）、彩虹渐变
- 必须使用：实线/虚线区分主路径与控制路径；浅灰底色或细边框做分组；箭头粗细 0.8-1.2pt 统一
- 子图标识：(a) / (b) / (c) 黑色加粗，置于子图左上角，与子图标题分行
- 图例置于画布底部居中，单行排版；不要内嵌进子图
- 留白：每个子图四周 padding ≥ 8% 画布尺寸，相邻子图间距 ≥ 6%
```

---

## fig8 提示词（论文 §2.2.6 图 2.8 — PPSC 机制演化图）

**对应 thesis.tex**：`fig:zbabm_kmap_flat`，line 475-593（替换原 TikZ 三栏图）

**叙事核心**（picdoc 必读）：
> 标准 Radix-4 Booth → R4ABE2 近似（去掉 ±2A 移位器，节省 80% 晶体管，但 Row-0 引入 μ=−0.125 偏差） → ZBA-BM + PPSC（在 Row-0 加一个 2:1 MUX 局部还原精确 −2A 路径，将 μ 拉回 0；面积 −32%，功耗 −27%）。**演化箭头是图的灵魂**：第一步标 "drop ±2A (savings)"，第二步标 "add Row-0 MUX (fix bias)"。

**提示词（直接喂 picdoc）**：

```
画一张三栏并排的横向架构演化图（横版，约 16:9 画幅），用于 SCI 期刊论文，主题是"近似 Radix-4 Booth 乘法器从 baseline 到 R4ABE2 再到 ZBA-BM + PPSC 的结构演化"。

整体布局：
- 三个子图并列，左 (a) → 中 (b) → 右 (c)，画布上下分四层
- 顶层（每个子图）：彩色横条做 panel header，写子图标识 + 设计名（中英文）
  · (a) Standard Radix-4 Booth (baseline)  — 中性灰
  · (b) R4ABE2 Approx (drop ±2A)           — 暖黄
  · (c) ZBA-BM + PPSC (Ours)                — 暖橙（视觉重点，色饱和度比 b 略高 10%）
- 第二层：Booth window 表达式 B_W = {b_{2i+1}, b_{2i}, b_{2i-1}}，三栏对齐
- 第三层（核心，三栏不同结构）：
  · (a) 5-case MUX：列出全部 8 种 Booth 编码 → 输出（含 ±2A，红色高亮 011→+2A 与 100→−2A 表示"代价大"）
  · (b) Approx MUX：同上但 011 → +A（中划线删除 +2A）, 100 → −A（中划线删除 −2A），下方接 K-Map 单式：pp_j = (¬z) ∧ (a_j ⊕ n)，标注 "1 AND + 1 XOR / bit"
  · (c) K-Map 单式（同 b）+ 下方一个高亮的 PPSC 块：
        sel = (i=0) ∧ (B_W = 100)
        sel=1: a_{j-1} << 1 (exact −2A)
        sel=0: pp_j (K-Map approx)
- 第四层（输出）：每栏一个数据通路 box，写 pp_j（a/b 栏）、pp_j^PPSC（c 栏）
- 最底部：legend（baseline / approx / our innovation 三色块）+ 数据流箭头标识

子图间演化箭头（关键叙事，必须显眼）：
- (a) 中 MUX 块右侧 → (b) 中 MUX 块左侧：粗实线箭头，颜色与 (b) panel header 同色，
  上方文字 "drop ±2A"，下方文字 "(savings)"
- (b) 中下方 K-Map 块右侧 → (c) 中 K-Map 块左侧：粗实线箭头，颜色与 (c) panel header 同色，
  上方文字 "add Row-0 MUX"，下方文字 "(fix bias)"

每栏底部 stats 行（三行小字，紧凑左对齐）：
- (a) transistors: 100% (baseline) | bias: μ=0 | (note: ±2A shifters expensive)
- (b) ✓ transistors: −80% | △ Row-0 bias: μ=−0.125 | (cannot self-cancel: b_{−1}=0)
- (c) ✓ μ ≡ 0 (HW-guaranteed) | ✓ area −32%, power −27% | (only triggers on Row-0 ∧ B_W=100)

配色表（严格遵守）：
- (a) baseline 灰：fill #E8E8E8, border #5B5B5B
- (b) approx 暖黄：fill #FFF4D6, border #B8862C
- (c) ours 暖橙：fill #FFE0B8, border #C25E1F（突出但不刺眼）
- 红色强调（±2A 项 / 删除线 / Row-0 偏差）：#C0392B（不要用纯红 #FF0000）
- 绿色 ✓：#2E7D32  橙色 △：#C25E1F  红色 △：#C0392B
- 数据通路箭头：#333333；演化箭头：与目标 panel header 同色

禁止：五角星 ★、emoji、阴影/drop-shadow、3D 立体、霓虹色、圆角 >3pt。

输出：PDF 矢量优先；备选 PNG ≥600 dpi，画布宽 ≥3000 px。
```

---

## fig9 提示词（论文 §4.2.1 图 4.3 — 系统创新双 Tier 图）

**对应 thesis.tex**：`fig:sys_innov`，line 1096-1232（替换原 TikZ 双 Tier 图）

**叙事核心**（picdoc 必读）：
> 本工作的两个**正交**系统级创新。Tier I（上半幅）：层级精度切换——RoCC 自定义指令向 Gemmini 16×16 Mesh 注入 approx_level，ResNet 不同 stage（S1/S2/S3/S4/S5）切换不同精度档位（S1/S5=4E0A 精确；S2/S3=2E2A；S4=1E3A）；结果："混合 vs 统一精度：精度 −1.0%, 功耗 −14%"。Tier II（下半幅）：单 PE 内部双机制——GatedPE 在输入级前置零检测（A=0 或 B=0 时 clk_en=0 + bypass=0，挖 ReLU 稀疏功耗，不侵入加法树），下接 exact_math / approx_math (ZBA-BM PPSC) 双路 MAC 通过 2:1 MUX 由 approx_level 选择。

**提示词（直接喂 picdoc）**：

```
画一张上下两层（Tier I + Tier II）的系统架构图，用于 SCI 期刊论文，主题是"基于 RoCC 自定义指令的层级精度切换 + 单 PE 内部双路 MAC 与零门控的两大正交系统级创新"。

整体布局（竖版偏长方形，约 16:11 画幅）：
- 上半幅（Tier I）：层级精度分配——左侧 Gemmini 16×16 Mesh 缩略图（4×4 小方格表示 mesh），右侧 ResNet 5 个 stage 的 horizontal pipeline
- 中间一条横向虚线分隔（细灰色 dashed，0.4pt）
- 下半幅（Tier II）：单 PE 数据流——左到右，输入端 → GatedPE → 双路 MAC → 2:1 MUX → 加法器 → 输出

Tier I（上半幅，标题"Tier I: Layer-wise Approx-Level Allocation across ResNet Stages"，11pt 加粗）：
- 左侧：4×4 网格小方格代表 mesh，每方格 4×4mm 浅灰，整体下方写 "Mesh 16×16" 8pt
- 右侧：5 个圆角矩形 stage 横向排开，从左到右
  · S1 (Stem)  — 配色：冷蓝（精确档位）  标签下方写 "4E0A"
  · S2          — 配色：暖橙（中等近似）  标签下方写 "2E2A"
  · S3          — 配色：暖橙（同上）       标签下方写 "2E2A"
  · S4          — 配色：暖红（深度近似）   标签下方写 "1E3A"
  · S5 (Head)   — 配色：冷蓝（精确档位）   标签下方写 "4E0A"
- stage 之间用细实线箭头串联
- mesh 与 S1 之间一条细虚线箭头（蓝色），表示控制信号注入
- mesh 下方一行斜体灰字："approx_level per-stage, injected by RoCC custom instruction"
- stage 链右下方一行绿字（10pt 加粗）：
  "✓ Mixed S2-S4 approx vs. uniform: −1.0% accuracy, +14% power saving"

Tier II（下半幅，标题"Tier II: Inside one PE -- BehavioralApproxMacUnit"，11pt 加粗）：
- 数据流从左到右共 5 个主要单元：
  1. 输入端三个端口堆叠：in_a [8b]、in_b [8b]、in_c [32b]（小灰色矩形）
  2. GatedPE 块（绿色调，浅绿填充 + 中绿边框）：
     标题 "GatedPE"
     内部 3 行小字：
       "Latch + Zero-Detect"
       "A=0  ∨  B=0"
       "→ clk_en = 0"
       "→ bypass = 0"
  3. 双路 MAC（并列上下两行，cooler 配色）：
     上路 exact_math（蓝色调）：
       "exact_math"
       "a.asSInt * b.asSInt"
       "(Chisel native)"
     下路 approx_math（红色调）：
       "approx_math"
       "Module(new KMap_V3_Final_SchemeD)"
       "(ZBA-BM + PPSC)"
  4. 2:1 MUX（菱形或六边形，橙色填充，加粗边框）：
     标题 "2:1 MUX"
     上方一条蓝色细线箭头从顶部进入，标签 "approx_level from RoCC"（蓝色斜体小字）
  5. 加法器 + in_c：方框写 "+ in_c"，输出 out_d [32b]
- GatedPE 底部一条绿色 dashed 箭头绕到 MUX 底部，标签 "bypass=0"（绿色斜体小字）
- 数据流主线箭头粗 1.0pt 黑色；控制信号细 0.5pt 蓝色
- Tier II 底部三行小字（左对齐，灰色斜体）：
  "Source: PE.scala line 47, Module(new KMap_V3_Final_SchemeD())"
  "Configs: ApproxGemminiRocketConfig / TOSAMGemminiRocketConfig"
- Tier II 底部最后两行加粗橙色（10pt）作为创新点总结：
  "Innovation 1: GatedPE — ReLU sparsity exploit, no critical-path penalty"
  "Innovation 2: Dynamic precision switching — exact/approx selectable per RoCC instruction"

配色表（严格遵守）：
- 精确档位（S1/S5/exact_math）冷蓝：fill #D6E4F0, border #2C5F8D
- 中等近似（S2/S3/2E2A）暖橙：fill #FFE0B8, border #C25E1F
- 深度近似（S4/1E3A/approx_math）暖红：fill #F8D7D5, border #A53C3C
- GatedPE 绿色：fill #D7EFD3, border #3F8B3A
- MUX 强调橙：fill #FFB97A, border #A0481C, 边框 1.0pt 略粗
- mesh 灰：fill #ECECEC, border #888888
- 普通 box：fill #F4F4F4, border #5B5B5B
- 控制信号箭头：#2C5F8D
- 数据流箭头：#333333

禁止：五角星 ★、emoji、阴影/drop-shadow、3D 立体、霓虹色、圆角 >3pt、过大字号（标题 ≤12pt）。

输出：PDF 矢量优先；备选 PNG ≥600 dpi，画布宽 ≥3500 px（fig9 比 fig8 信息密度更大）。
```

---

## v5 vs v4 改进点（picdoc 重出后用户验收清单）

| 项 | v4 问题 | v5 修复 |
|---|---|---|
| 配色 | 单调灰白，缺层级 | 4 类配色（冷蓝/暖橙/暖红/绿）按"精确度梯度"语义对应 |
| 特殊符号 | 五角星 ★ 等卡通元素 | 全删，改用边框粗细 / 颜色饱和度区分重点 |
| 排版 | 字密、无 padding | 子图四周留白 ≥8%，子图间距 ≥6% |
| (a)/(b)/(c) 标识 | 与子图标题混排 | 独立左上角加粗标识 + 分行 |
| 演化箭头 | v4 已有但弱 | v5 加粗 1.4pt + 颜色与目标 panel 同色，强化叙事 |
| 阴影 | 有 drop-shadow | 全删，SCI 期刊不接受 |
| 输出 | PNG（fig9 仅 1150×647，<300 dpi） | PDF 矢量优先 / PNG ≥600 dpi 画布 ≥3000 px |

---

## 用户操作步骤

1. 打开 PicDoc，新建对话
2. 把"通用风格规范"段落作为 system prompt 或第一条消息
3. 然后分别贴 fig8 / fig9 的提示词（每张图独立对话避免污染）
4. 输出后存入 `figures/PicDoc/fig8_v5.pdf`（或 `.png`） + `fig9_v5.pdf`
5. 通知本 session，由我替换 thesis.tex 中 TikZ 段为 `\includegraphics{}` 引用

---

## 备选方案（picdoc 出图仍不满意时）

- **A**：保留现有 TikZ 段不替换（thesis.tex line 475-593 / 1096-1232）— TikZ 已可读，只是不够"图形化"
- **B**：用 mermaid + 手工后期 PS — 时间不够，不推荐
- **C**：用 draw.io 重画并导出 PDF — 已尝试 v3，被 picdoc 取代
