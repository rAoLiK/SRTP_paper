# 科研级绘图重做方案 v0

> 路线：Claude 4.7 生成 draw.io XML → VSCode draw.io 插件预览/微调 → 导出 PDF/PNG → 嵌入 thesis.tex
> 标杆：Fig 7（TOSAM 硬件框图，彩色分模块+位宽标注+斜线填充）/ Fig 17（Gemmini 阵列+PE 内部展开层次结构）

## 处理决定汇总

| 当前 | 处理 | 替代物 |
|---|---|---|
| **Fig 8** `row0_asymmetry.png`（matplotlib bar chart）| **删除** | 论述放回 §2.2.5 公式 \eqref{eq:zbabm_mu_row0}，文字已足够 |
| **Fig 9** `fig4_kmap_flat.png`（单色矩形框图，含废弃命名）| **重做** | 新 Fig 8: `zbabm_ppsc_arch.pdf` ZBA-BM PPSC 硬件电路框图 |
| **新增** | **新增** | 新 Fig 17b（紧跟 §4 PE 改造段后）: `pe_dual_mac.pdf` ZBA-BM × Gemmini PE 双路 MAC 数据通路 |

净效果：Fig 总数 18 → 18（删 1 + 加 1），但两张关键图大幅升级。

---

## 新 Fig 8: ZBA-BM PPSC 硬件电路框图

**对标**: TOSAM Fig 7（`06_TOSAM4.png`）—— 横向 16:9，彩色分模块，位宽标注，斜线填充

### 模块组成（左→右数据流）

| 模块 | 颜色 | 作用 |
|---|---|---|
| **输入端** $a$ / $b$ | 灰底斜线 | 8 bit 有符号操作数 |
| **Radix-4 Booth 编码器** | 蓝色填充 | 4 行并行解码 $B_W^{(i)}$，输出 \texttt{neg}/\texttt{zero}/\texttt{is\_neg2A} |
| **K-Map 部分积阵列**（Row 0..3，每行 9 比特）| 浅蓝填充 | 按 \eqref{eq:zbabm_pp} 单解析式 1AND+1XOR 生成 \texttt{approx\_bit} |
| **PPSC 局部补偿支路**（仅 Row 0）| **橙红高亮** | 2:1 MUX 在 $B_W^{(0)} = \mathtt{100}$ 时输出 $a_{j-1}$，恢复 $-2A$ |
| **Dadda 加法树**（h=4）| 浅绿填充 | 4 行 + 1 稀疏操作数压缩为 16 bit |
| **稀疏外挂第 5 操作数** $\mathtt{neg\_bits}_3 \!\ll\! 6$ | 紫色虚线框 | 独立通路注入加法树（标注 §2.2 N/2+1 处置）|
| **输出端** $\text{product}$ | 灰底斜线 | 16 bit 有符号 |

### 位宽标注（关键）

每条数据线斜线 + 数字（仿 TOSAM Fig 7 风格）：
- 输入: `8` 
- $B_W^{(i)}$: `3`
- \texttt{neg}/\texttt{zero}/\texttt{is\_neg2A}: `1` 
- K-Map 部分积单行: `9`
- 4 行汇总到 Dadda 树: `4 × 9 = 36 bit 输入`
- 输出: `16`

### 图例（图右下角）

- 蓝色: 控制路径
- 浅蓝: 部分积生成
- **橙红: PPSC 补偿（创新点）**
- 浅绿: 加法树
- 紫: 稀疏外挂

### Caption（约 90 字）

> ZBA-BM 的 PPSC 硬件电路框图。Radix-4 Booth 编码器并行生成 4 行 K-Map 部分积，每比特仅用 1 AND + 1 XOR 实现式~\eqref{eq:zbabm_pp}；Row-0 处叠加局部补偿支路（橙色），在 $B_W^{(0)} = \mathtt{100}$ 时通过 2:1 MUX 选择 $a_{j-1}$ 恢复 $-2A$ 部分积，将均值偏差强制归零。$\mathtt{neg\_bits}_3$ 以稀疏第 5 操作数形式接入 Dadda 树，规避 $N/2+1$ 行溢出。

---

## 新增 Fig 17b: 系统级架构创新合一图（动态精度 + GatedPE + 双路 MAC）

**位置**: §4.2 Scala 生成器逻辑修改与底层适配 末尾（line 1083 后）
**对标**: Gemmini Fig 17 三段式层次 + TOSAM Fig 7 彩色模块
**素材源**: DAC main_zh.tex §IV.A（动态精度异构分配 line 60-207）+ §IV.B（GatedPE 稀疏隔离 line 208-216）

### 布局（横向 16:9，三段并列子图）

```
┌──────────────────────────────────────────────────────────────────────────────┐
│   (a) Layer-wise 异构精度分配           (b) GatedPE 零门控           (c) 双路 MAC + RoCC 切换   │
├──────────────────────────────────────┬──────────────────────────┬────────────────────────────┤
│   ResNet stages → approx_level 矩阵   │  Latch + Zero Detect     │  exact_math (蓝)           │
│   ┌─┬─┬─┬─┬─┐  S1: 4E0A (精确)        │  ↓                       │  approx_math (橙)          │
│   │ │ │ │ │ │  S2: 2E2A (中近似)      │  if a=0 or b=0 →         │  ┌──────┐                  │
│   ├─┼─┼─┼─┼─┤  S3: 2E2A               │    clk_en ← 0            │  │ 2:1  │ ← approx_level   │
│   │ │ │ │ │ │  S4: 1E3A (深近似)      │    bypass → 0            │  │ MUX  │   (RoCC 注入)    │
│   ├─┼─┼─┼─┼─┤  S5: 4E0A (精确)        │  else → MAC unit         │  └──┬───┘                  │
│   └─┴─┴─┴─┴─┘                         │                          │     ↓                      │
│   敏感层精确 / 中间层近似             │  ★ 动态功耗骤降          │  product → 累加器          │
└──────────────────────────────────────┴──────────────────────────┴────────────────────────────┘
                       ↓                          ↓                           ↓
        【精度-功耗帕累托优势】         【ReLU 稀疏感知节能】       【单 RoCC 指令切换全阵列】
```

### 三子图各自模块要素

**(a) 层级异构精度分配**：
- ResNet-50 缩略（5 个 stage 块，各标 S1..S5）
- 每个 stage 旁标 approx_level 配置：4E0A / 3E1A / 2E2A / 1E3A
- 数据来源：DAC Tab.4 `tab_mixed`（混合策略 S2-S4）
- 视觉重点：S2-S4 中间区域用橙色填充（近似），S1/S5 用蓝色（精确）
- 注释："混合分配比均匀分配挽回 11.6 个百分点精度（DAC §IV.A）"

**(b) GatedPE 零门控**：
- 上行：操作数 a, b → Zero-Detector（OR 门：a=0 ∨ b=0）
- 中行：Detector 输出 → 时钟使能控制（clk_en gating）
- 下行：MAC 单元（虚线框，被屏蔽态）+ 旁路通道直输 0
- 视觉重点：被屏蔽时整个 MAC 模块灰化 + 旁路通道高亮绿色
- 注释："ReLU 后 50%+ 元素为零；前置 Latch 不侵入加法树关键路径"

**(c) 双路 MAC + RoCC 切换**：
- exact_math（蓝色块）：`a.asSInt * b.asSInt`
- approx_math（橙色块）：`Module(new KMap_V3_Final_SchemeD())`
- 2:1 MUX 选择
- approx_level 控制信号（侧入箭头）标注"来自 RoCC 指令"
- 注释："PE.scala line 47；双 Config: ApproxGemminiRocketConfig / TOSAMGemminiRocketConfig"

### 三子图统一标注（图底部）

- 共同上下文："Mesh 16×16 PE 阵列；这三种正交机制共同构成本工作的系统级架构创新"
- 三机制正交性箭头：(a) ⟂ (b) ⟂ (c)，强调三者可独立启用、互不干扰

### Caption（约 110 字，简洁版）

> 本工作系统级架构创新的三个正交维度：(a) 层级异构精度分配——按 ResNet 各 stage 精度敏感度配置 \texttt{approx\_level}，敏感层走精确通路、中间层用 2/3 行近似；(b) GatedPE 零门控——前置零检测在 $A=0$ 或 $B=0$ 时屏蔽时钟使能，旁路输出 0；(c) 双路 MAC——精确与近似两路在 PE 内并列，由 RoCC 指令的 \texttt{approx\_level} 选择。

---

## 配色方案（双图统一）

| 类别 | 颜色 | RGB |
|---|---|---|
| 控制 / 接口 | 蓝灰 | `#4A6FA5` |
| 部分积 / 数据通路 | 浅蓝 | `#A8C5E8` |
| **PPSC / ZBA-BM 创新（视觉重点）** | **橙红** | `#E8825D` |
| 加法树 / 累加 | 浅绿 | `#A8D8B9` |
| 稀疏外挂 / 辅助 | 紫 | `#C8A8E8` |
| 边框 | 深灰 | `#333333` |
| 文字 | 黑 | `#000000` |

---

## 工作流

```
[1] Claude 生成 .drawio XML 源文件
    ├─ figures/sources/fig8_zbabm_ppsc.drawio       (~ 200 元素)
    └─ figures/sources/fig17b_pe_dual_mac.drawio    (~ 150 元素)

[2] 用户在 VSCode 装 hediet.vscode-drawio 扩展，双击 .drawio 预览
    （或上传 https://app.diagrams.net/ 在线打开）

[3] 用户审视后微调（字号、模块位置、标注）

[4] 在 draw.io 中 Export → PDF（"Crop to content" 选中）
    输出 fig8_zbabm_ppsc.pdf / fig17b_pe_dual_mac.pdf

[5] 也可同时导出 PNG @ 600 DPI 备用

[6] Claude 修改 thesis.tex:
    - 删除 Fig 8 (line 459-468) row0_asymmetry figure 块
    - 改 Fig 9 → 新 Fig 8: includegraphics{fig8_zbabm_ppsc.pdf} + 新 caption
    - 在 §4 PE 改造段后插入新 Fig 17b

[7] bash build.sh full 重编译验证
```

---

## 备选格式

若 draw.io 路线遇到工程问题，备选：
- **PPT (.pptx)**: Claude 生成 OOXML，PowerPoint 打开编辑，导出 EMF/PDF
- **TikZ**: 直接 LaTeX 源码绘图，与 thesis.tex 同源，但 Claude 一遍写对的概率低，需多轮调整
- **mermaid + manual touch-up**: 适合极简结构图，本任务不够

