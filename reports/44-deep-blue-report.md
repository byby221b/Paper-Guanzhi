# Deep Blue 精读报告

## 元信息

- 标题：*Deep Blue*
- 作者：Murray Campbell、A. Joseph Hoane Jr.、Feng-hsiung Hsu（原文注明按姓氏字母顺序排列）
- 发表：*Artificial Intelligence*, 134(1–2):57–83, 2002
- DOI：[10.1016/S0004-3702(01)00129-1](https://doi.org/10.1016/S0004-3702(01)00129-1)
- 正式条目：[IBM Research](https://research.ibm.com/publications/deep-blue)；[Elsevier / ScienceDirect](https://www.sciencedirect.com/science/article/pii/S0004370201001291)
- 精读版本：Elsevier 排版版，27 页；出版社 PDF 本次访问返回 HTTP 403，全文由 [SUNY Oswego 课程资料库](https://www.cs.oswego.edu/~mgrzenda/CSC466/Paper%20Sources/Deep%20Blue.pdf)取得，并以正式条目、DOI、页码和正文逐项核对
- 精读日期：2026-08-14
- 对应小红书期号：#44
- 元数据说明：排期表原列 1997，现据 IBM Research 与期刊正式条目更正为 2002。1997 是 Deep Blue II 击败 Garry Kasparov 的比赛年份，不是本文发表年。

## 作者背景

### Murray Campbell

- 发表时身份：论文首页署名 IBM T. J. Watson Research Center。
- 项目关系：他与 Feng-hsiung Hsu 在 Carnegie Mellon University 的 ChipTest / Deep Thought 项目中合作；二人于 1989–1990 年间进入 IBM Research，继续研制世界级国际象棋机器。A. Joseph Hoane Jr. 于 1990 年末接替 Thomas Anantharaman 加入小组（Section 1.2）。
- 可核身份：IBM Research [官方个人页](https://research.ibm.com/people/murray-campbell)记载，Campbell 1987 年获 Carnegie Mellon University 计算机科学博士学位，Deep Blue 是他加入 IBM 后的第一个项目；该页同时列出 1997 Fredkin Prize、1997 Allen Newell Medal 等荣誉。导师信息本次没有取得可靠来源，故省略。

### A. Joseph Hoane Jr.

- 发表时身份：论文首页署名 Sandbridge Technologies；他在 Deep Blue 开发期是 IBM 团队成员。
- 项目分工：原文把 Hoane 列入从 Deep Thought 2 延续到 Deep Blue 的核心小组，但没有在作者简介中逐项划分个人贡献。本文以三位作者的共同系统说明为证据，不擅自指定某一模块的唯一作者。

### Feng-hsiung Hsu

- 发表时身份：论文首页署名 Compaq Computer Corporation, Western Research Laboratory。
- 前置工作：原文参考文献列出 Hsu 1987 年单芯片走法生成器论文、1990 年关于大规模并行 alpha–beta 搜索的 Carnegie Mellon 博士论文，以及 1999 年对 Deep Blue chess grandmaster chip 的说明。IBM [官方历史页](https://www.ibm.com/history/deep-blue)也确认他从 ChipTest 起主导定制棋类芯片方向，并于 1989 年与 Campbell 一同进入 IBM Research。
- 后续记录：他在 2002 年出版 *Behind Deep Blue*；该书被本文作为更完整的项目史引用。具体师承本次没有可靠来源，故省略。

### 合作关系与身份边界

三位作者是同一工程谱系的同事，而非本文可确认的师生关系。论文写作时三人已分属 IBM、Sandbridge 与 Compaq，但叙述对象是 1990 年代中期在 IBM Research 完成的系统。论文首页的当前单位不能反向当作 1997 年比赛时的项目组织结构。

## 历史语境

### 计算机国际象棋的两条主线

自 Shannon 1950 年形式化计算机下棋以来，主流系统一方面改进博弈树搜索：iterative deepening、alpha–beta pruning、quiescence search、transposition table、NegaScout；另一方面为局面评价编码棋类知识。1970–1980 年代的 Chess 4.5、Belle 等系统证明，专用硬件可以显著扩大搜索规模。Deep Blue 继承的关键问题，是如何把更大的计算量转化为棋力，而非只增加均匀搜索深度。

### 从 ChipTest 到 Deep Blue II

原文 Section 1 与 Appendix B 给出清晰谱系：ChipTest（1986）约每秒 5 万节点；Deep Thought（1988）约每秒 70 万至 200 万节点，并成为首个在正式赛事中击败特级大师的棋机；Deep Thought 2（1991）加入中等规模并行、改进搜索软件和 extended book。Deep Blue I 以 216 枚棋类芯片达到每秒 5000 万至 1 亿局面，却在 1996 年以 2–4 负于 Kasparov。

1997 年的 Deep Blue II 增至 480 枚芯片，评估特征由约 6400 项扩展到 8000 余项，加入重复局面检测、专用走法生成模式与调试/调参工具。团队在两场比赛之间把绝大多数时间用于新评估函数的设计、测试和调校，而非重写已经可用的搜索主体（Section 1.4）。这一事实说明，作者自己并未把胜利归因于单纯“暴力穷举”。

### 待解决的系统问题

Deep Blue 面对四个相互牵制的问题（Section 2）：

1. 搜索能力巨大，却必须在最低全宽深度与战术线的非均匀延伸之间分配算力。
2. 硬件评估速度固定，可以容纳复杂特征，却难以临时增加新知识，8000 余项权重也难以调校。
3. 灵活的软件搜索位于树根，硬连线搜索位于叶端；两者能力不一致会产生边界与 horizon effect。
4. 500 余个异构处理单元可以并行，但选择性搜索使子树大小高度不均，主节点调度容易成为瓶颈。

### 直接前驱

- Anantharaman、Campbell、Hsu（1988/1990）的 singular extensions：为强制线分配额外深度，是本文 delayed extensions 的直接基础。
- Knuth 与 Moore（1975）的 alpha–beta 分析：提供基本搜索框架和节点类型术语。
- Slate 与 Atkin（1977）的 Chess 4.5：提供 iterative deepening、transposition table、quiescence search 等成熟工程基础。
- Condon 与 Thompson（1982）的 Belle：证明棋类专用硬件的可行性，并影响 Deep Thought / Deep Blue 芯片设计。
- Tesauro（1989、2001）的 comparison training：成为本文少量自动评估权重调整的工具来源。

## 问题形式化

### 博弈树任务

给定当前棋局状态 $s$、合法走法集合 $A(s)$ 与严格的时间控制，系统需要选择一步 $a^*\in A(s)$。终局效用可按胜、和、负排序；非终局节点无法穷尽到终局，因此需要有限深搜索、静态局面评价和额外的 quiescence search。

采用 negamax 写法，可把深度受限值形式化为：

$$N(s,d)=-\max_{a\in A(s)}N(T(s,a),d-1),\qquad N(s,0)=V(s),$$

其中 $T$ 是走法后的状态转移，$V$ 是静态评价。alpha–beta 只剪去不可能改变根节点决策的分支；selective extensions 则改变不同路径的有效深度。该式是对论文 Figure 1 基本框架的形式化整理，原文实现还包含窗口、信用、重复局面、宁静搜索、开局库、残局库与时间控制。

### 输入与输出

- 输入：完整棋盘、轮到哪方、王车易位与吃过路兵等状态、历史重复局面信息、比赛剩余时间，以及当前比赛策略配置。
- 输出：当前一步走法；内部还需要给出主变化、局面分数和搜索完成状态，以支持迭代加深与时间控制。
- 评价准则：比赛结果是最终标准；系统层面同时考察每秒局面数、全宽与选择性搜索深度、并行 speedup / efficiency、评估函数质量及在时间限制内的稳定性。

### 证据边界

论文报告的是一个完整系统及少量组件测量，没有把胜负还原为可独立重复的单变量实验。1997 年六局比赛的 3.5–2.5 是总体结果，不能据此计算每个模块的因果贡献。

## 核心方法

### 三层异构搜索架构

Deep Blue II 由 30 个 IBM RS/6000 SP 节点和 480 枚棋类搜索芯片组成，每个 SP 节点连接 16 枚芯片。一个 SP 节点作为 master 搜索树顶层并分配任务，另外 29 个 worker 再搜索若干层，最终把叶局面交给芯片。软件层用 C 实现、灵活、有 transposition table；芯片层用状态机实现、执行固定深度 null-window alpha–beta 与复杂 quiescence search，但没有 transposition table（Section 2, Table 1）。

1997 年比赛中，超过一分钟的搜索平均每秒处理 1.26 亿局面，最大持续速度 3.30 亿；战术局面典型约 1 亿，安静局面接近 2 亿。差异来自选择性延伸、宁静搜索和并行负载，而非固定 FLOPS 到节点数的简单换算。

### 棋类芯片：生成、评价、控制

芯片分为 move generator、evaluation function、search control 三部分（Section 3）。走法生成器是一块 8×8 组合逻辑“硅棋盘”，并通过仲裁网络按启发顺序逐一输出走法。fast evaluation 在一个时钟周期内处理子力与位置等高价值项；slow evaluation 按列扫描棋盘，识别方格控制、牵制、X-ray、王安全、兵形、通路兵等概念。搜索控制以状态机实现 null-window alpha–beta，并用 32 项循环缓冲检测重复局面。

外接 FPGA 原本可支持更灵活的搜索、额外评价项和外部 transposition table，但由于时间限制从未在 Deep Blue 中启用（Section 3.4）。因此“系统具备接口”与“比赛系统实际使用”必须区分。

### 双信用与延迟扩展

软件搜索最有辨识度的机制是 dual credit with delayed extensions（Section 4, Figure 1）。其出发点是：强制着与唯一应手值得加深，但每组都完整延伸两层会使搜索树爆炸。

算法沿路径分别维护双方的信用 `myCredit` 与 `hisCredit`：

1. 只有当前走法超过此前最佳分数，才可能经 `GenerateCredit()` 获得信用；fail-low 走法不视为强制。
2. singular、absolute singular、threat / mate threat、influence、将军应对、通路兵推进等条件可产生不同、可分数化的信用；Deep Blue 的粒度为四分之一 ply。
3. 信用先累积，达到 `CREDIT_LIMIT` 后才兑现为整数层延伸；Deep Blue 使用阈值 2。
4. 双方信用分开累计；一方兑现延伸时，另一方信用同步扣减相同层数，防止 principal variation 上双方连续获得过量延伸。
5. 实现还要保存先前到达同一局面的搜索 envelope，避免迭代之间振荡与重复重搜。

这套机制使 12 次迭代在论文两个样例局面中达到约 29–39 或 30–40 ply 的估计最大组合深度，同时保留约定的最低全宽搜索。该深度含选择性延伸和宁静搜索，不能与均匀搜索深度直接比较（Section 4.2, Tables 2–3）。

### 并行搜索与负载治理

系统采用静态处理器树和集中式控制（Section 6）。PV 节点需先完成第一步，再并行搜索其他候选；不同 node type 根据 fail-high / fail-low 状态开放不同并行度。选择性延伸会让同深度任务的树规模相差很大，因此超过 8000 节点的硬件搜索可被中止，把任务上移到软件层继续切分。worker 还保持一个 “on deck” 任务，以减轻 master 与 worker 通信延迟。

这一设计能运行，却没有追求理想并行效率。master processor 是作者明确指出的性能瓶颈；worker 之间不直接通信，代价是共享受限。搜索时序与任务分配还会造成非确定性，使调试更困难。

### 评估函数、开局与残局知识

局面评价本质上是约 8000 个可识别 pattern 的加权和。硬件暴露 54 个寄存器和 8096 个表项，共 8150 个可设置参数；其中部分组合不可实现或用于控制，实际多值参数还更多（Section 7.1, Tables 4–5）。评估函数生成器在根节点按局面阶段建立参数关系；它没有在树内反复运行，因为完整生成与下载需要可测的墙钟时间。

绝大多数特征与权重由人工创建和调校。作者只报告两类自动分析：hill climbing 用于发现对权重不敏感的 noisy features；comparison training 用于调整 pawn shelter 权重，结果显示手调值系统性偏低，团队在 1997 年比赛前将其提高（Section 7.3）。这属于辅助调参证据，不是从棋谱端到端学习评估函数。

此外，手工 opening book 约含 4000 个局面；extended book 从 70 万盘特级大师棋谱汇总频率、棋手强度、胜负、年代与注释等信号，以非线性 ad hoc 函数给候选着最多约半兵奖励。残局库覆盖全部五子及部分六子局面。作者明确说残局库在两次 Kasparov 比赛中没有起关键作用（Section 8.3）。

## 关键公式推导

### 公式一：局面评价的线性骨架

**原文表述：**Section 7.1 称 evaluation function “essentially a sum of feature values”，并说明静态值、动态值和查表缩放。

**形式化整理：**

$$V(s)=\sum_{j=1}^{m}w_j(s_0,m(s))\,\phi_j(s),\qquad m\approx8000.$$

这里 $\phi_j(s)$ 表示芯片在局面 $s$ 中检测到的 pattern；$w_j$ 由根局面 $s_0$ 的 evaluation function generator 初始化，并可随当前子力材料状态 $m(s)$ 通过查表缩放。

**逐步解释：**

Step 1：走子后，芯片并行或逐列检测 piece placement、king safety、pawn shelter、rooks on files 等模式。依据：Section 3.2 与 Appendix A。

Step 2：每个模式映射到寄存器或表项。一个高层概念可展开成许多离散组合，例如 “rooks on files” 单方表按敌兵、阻塞、半开放线、车数量和中心性索引。依据：Section 7.2。

Step 3：一些值固定，一些值按棋盘剩余子力缩放。王安全在中局权重大，在残局可趋近于零。依据：Section 7.1–7.2。

Step 4：各项累加为静态分数，供叶节点 negamax / alpha–beta 回传。由于硬件评价耗时近似固定，增加已布线的特征不会像软件函数那样逐项增加执行时间；代价转移到芯片面积、设计周期和参数调校。

**直觉：**速度来自把大量棋类概念固化为并行电路和查表，棋力仍依赖特征是否恰当、权重是否合适，以及搜索能否到达需要评价的位置。

### 公式二：信用兑现如何限制延伸

**原文 Figure 1：**当 `hisCredit >= CREDIT_LIMIT` 时，

$$e=\left\lceil c_{\mathrm{his}}-C\right\rceil,$$

随后把 `depthToGo` 增加 $e$，并令双方信用分别减去 $e$、且不低于零。Deep Blue 取 $C=2$。

**补充推导：**

Step 1：若 $c_{\mathrm{his}}=2.5$，则 $e=\lceil0.5\rceil=1$，兑现一层延伸；若信用为 3.25，则 $e=\lceil1.25\rceil=2$。

Step 2：兑现后 $c_{\mathrm{his}}\leftarrow c_{\mathrm{his}}-e$，把它压回阈值附近，避免同一笔信用重复使用。

Step 3：同时令 $c_{\mathrm{my}}\leftarrow\max(c_{\mathrm{my}}-e,0)$。这一步针对 principal variation 上双方都持续“符合预期”的情形，阻止双方信用各自无界增长。

Step 4：信用可以四分之一 ply 累积，延伸以整数 ply 兑现；于是多次相邻强制信号可以共同触发加深，孤立信号通常只留下未兑现信用。

**直觉：**算法把“这条线有多强制”变成可积累预算，并在预算过阈值时延长搜索。双账本及相互扣减给这种选择性加入刹车。

### 公式三：论文并行效率数字的复核

对 $p$ 个芯片，speedup 为 $S_p=T_1/T_p$，observed efficiency 为

$$E_p=\frac{S_p}{p}.$$

论文在单节点 24 芯片系统上报告：深战术线平均 speedup 约 7，因此 $E_{24}\approx7/24=29.2\%$，与文中“约 30%”一致；安静局面 speedup 约 18，因此 $E_{24}=18/24=75\%$。全 30 节点系统只有间接证据，作者估计战术局面约 8%、安静局面约 12%（Section 6.3）。

这些数值说明节点吞吐并不随处理单元数量线性增长。它们只来自有限位置与当时系统，不能外推为所有棋局或现代并行架构的一般规律。

## 实验分析

### 证据构成

本文不是按现代机器学习格式组织的 benchmark 论文。它的证据由四类组成：系统规格与比赛运行统计；两个局面的搜索深度示例；24 芯片与单芯片的并行 speedup；1996 与 1997 两代系统及比赛结果的历史对比。

### 主要结果

| 证据 | 原文结果 | 可以支持的结论 |
|---|---:|---|
| 1997 正式比赛 | Deep Blue II 以 3.5–2.5 胜 Kasparov | 完整系统在这场六局制标准时限比赛中获胜 |
| 比赛长搜索 | 平均 1.26 亿局面/秒，最大持续 3.30 亿 | 实机运行规模；不等同于固定算法效率 |
| 三分钟搜索 | 平均最低全宽深度 12.2 ply | 系统在选择性搜索之外保留一定“保险” |
| 选择性样例 | 迭代 12 估计最大组合深度约 29–40 ply | 强制线可远深于最低全宽；该最大深度为估计值 |
| 24 芯片并行 | speedup 约 7（战术）或 18（安静） | 非均匀树造成明显位置依赖与负载不平衡 |
| 世代对比 | Deep Blue I 216 芯片、5000 万–1 亿节点/秒；II 480 芯片、1 亿–2 亿 | 硬件、评估、工具同时变化，不能当作单因素消融 |

### 关于“学习”的实际证据

Section 7.3 的 hill climbing 只用于找出 noisy features，comparison training 只调了 pawn shelter 相关权重；作者还写明绝大多数特征和权重由人工创建/调校。extended book 汇总了 70 万盘大师棋谱，但其打分函数是人工设计的非线性规则。因而本文证据支持“知识工程、搜索与有限自动调参的混合系统”，不支持“Deep Blue 通过自我对弈端到端学会国际象棋”。

### 实验设计评价

- 优点：作者给出系统内部结构、运行数值、失败路径和工程取舍；Table 1 与 Appendix B 使硬件/软件边界和代际变化可核查。
- 不足：没有公开完整代码、芯片描述、参数或所有比赛配置；并行效率样本有限；1996 到 1997 同时改变芯片数、特征、速度与工具，无法辨认各因素独立贡献。
- 可复现性：今天可以复现 alpha–beta、iterative deepening 或 credit extension 的软件近似，却无法仅凭本文复原 1997 比赛系统及其棋力。

## 局限性

### 作者自述

1. 并行搜索效率仍有明显提升空间；master 是瓶颈，full-system efficiency 只有间接估计。
2. 外接 FPGA 若完成，硬件搜索与评价可更灵活，但比赛系统没有使用。
3. 作者认为加入 pruning 可能显著改善搜索，实际系统为了最低深度“保险”采取了较保守选择。
4. 自动和人工评价函数调校都“far from complete”。生成器只在根节点运行，局面发生重大变化后某些静态权重可能不理想。
5. 许多设计备选没有探索；论文结论明确拒绝把成功归因于单一因素。

### 后见之明与适用范围

Deep Blue 针对规则封闭、状态完全可见、终局定义明确的双人零和棋类。它依赖可高速枚举的合法走法、强领域特征和大量定制硬件。这些条件不自然延伸到感知噪声、开放世界任务或需要语言交互的系统。

2017 年 AlphaZero 的正式论文使用自我对弈训练的神经网络与 Monte Carlo tree search，代表另一套知识获得与搜索组合。将两者比较有助于观察范式变化，但 AlphaZero 的训练方法不是 Deep Blue 本文的组成部分，也不能把二十年后的结果写成本文未做的消融。

### 工程分析（非论文声称）

硬件固化把推理时延压低，也形成很高的更新成本：新增特征需要改芯片或勉强构造 surrogate。今天的加速器与可编程编译栈常在固定吞吐和模型可修改性之间寻找新的平衡；这是一种工程类比，不是作者对现代系统的预测。

## 后续影响

### 历史地位

IBM [官方历史页](https://www.ibm.com/history/deep-blue)把 1997 年结果表述为：首个在标准比赛时限的完整对抗赛中击败在任世界冠军的计算机系统。需要同时保留更早的两个里程碑：Deep Thought 1988 年已在赛事中击败特级大师；Deep Blue I 1996 年第一局则曾首次在标准时限单局击败在任世界冠军。不同“首次”对应不同条件。

### 技术回响

本文留下的主要系统经验是：搜索深度、选择性、评估知识、专用硬件、并行调度和棋谱数据库必须协同。它也提供了一份少见的失败公开记录：主节点过载、硬件/软件边界效应、不可用的 FPGA 接口、非确定性调试与未完成的调参都被写入正文。

后来的顶尖棋类系统采用更强软件搜索、commodity hardware、自动参数优化，或神经网络与树搜索。Deep Blue 的具体芯片没有成为这些系统的统一模板；它证明了在一个清晰任务上，算法—硬件—领域知识共同设计可以达到世界冠军级表现。

### 引用统计

- Crossref `is-referenced-by-count`：843（查询日 2026-08-14）。该口径只统计 Crossref 能解析的引用关系，不与 Google Scholar 或 Semantic Scholar 数值直接等同。
- Google Scholar / Semantic Scholar：本次未获得可稳定复核的官方计数，因此省略具体数字。

## 个人笔记

读到 Section 6.3 时，我最意外的是作者把并行效率写得这样坦率：24 枚芯片在战术局面只有约 30%，全系统的间接估计更低。Deep Blue 常被浓缩为“每秒两亿步”，而正文真正描画的是一台持续与不规则搜索树搏斗的机器——长任务会被中止并上移拆分，worker 预装下一份工作，master 仍然过载，执行顺序还会让结果非确定。

另一个具体触点是 Figure 1 的两份信用。它没有给每个看似强制的着法立即加深，而是让四分之一 ply 的证据沿路径积累，达到阈值后才兑现；一方兑现时还会削减另一方信用。这像一个写在搜索器内部的预算制度：允许计算向战术线倾斜，同时保留最低全宽的保险。作者所说的“非均匀”因此不是口号，而是一组防止树爆炸的细致约束。

最后，Section 7.3 也改变了我对这套系统的概括。8000 个 pattern 听来规模巨大，绝大多数仍是手工知识；自动工具只参与了噪声特征诊断和一小组兵盾权重调节。读完之后，Deep Blue 在我眼中更像一项高度克制的系统工程：它没有用单一方法取胜，而是让许多不完美模块在严格时限内彼此补足。

## 小红书写作备忘

### Hook 素材

1. “每秒两亿局面”之外，论文报告全系统并行效率在战术局面只有约 8% 的间接估计；规模与效率需要同时阅读。
2. 软件在树根灵活搜索，480 枚芯片在树叶高速搜索；一套棋局被切成三层异构计算。
3. 1996 年失败后，团队把绝大多数时间花在评估函数，而非继续扩大搜索算法改动。

### 核心 Insight（一句话）

Deep Blue 的棋力来自选择性搜索、硬件吞吐、手工评估知识与比赛工程的协同，论文最有价值之处是把这些模块的接口、瓶颈和未完成部分一并公开。

### 自查重点

- 论文发表于 2002 年，叙述的核心系统与比赛发生在 1997 年。
- 系统是 30 个 SP 节点加 480 枚棋类芯片；IBM 历史页的“32 processors”是另一种概述口径，报告的架构数字以技术论文为准。
- 每秒局面数是运行吞吐，不能等同于穷举全部棋局，也不能单独解释胜利。
- 大部分评估特征与权重为手工设计；两种自动工具属于局部分析与调参。
- 搜索最大深度约 40 ply 是特定样例的估计组合深度，最低全宽深度约 12.2 ply。
- 1997 比赛结果是 3.5–2.5；论文没有提供模块级消融来分解胜因。

### 动态 Hashtags

#DeepBlue #博弈树搜索 #计算机国际象棋 #并行计算 #Paper观止

## 来源

1. Campbell, M.; Hoane Jr., A. J.; Hsu, F.-h. (2002). [Deep Blue](https://doi.org/10.1016/S0004-3702(01)00129-1). *Artificial Intelligence*, 134(1–2), 57–83。精读以正文 Section 1–9、Figure 1、Tables 1–5、Appendix A–B 为主。
2. [IBM Research: Deep Blue](https://research.ibm.com/publications/deep-blue)，正式书目信息，访问于 2026-08-14。
3. [IBM: Deep Blue history](https://www.ibm.com/history/deep-blue)，比赛史与团队沿革，访问于 2026-08-14。
4. [IBM Research: Murray Campbell](https://research.ibm.com/people/murray-campbell)，作者身份与履历，访问于 2026-08-14。
5. Silver, D. et al. (2018). [A general reinforcement learning algorithm that masters chess, shogi, and Go through self-play](https://doi.org/10.1126/science.aar6404). *Science*, 362(6419), 1140–1144。用于后续范式对照，不作为 Deep Blue 本文实验。
6. [Crossref metadata for DOI 10.1016/S0004-3702(01)00129-1](https://api.crossref.org/works/10.1016/S0004-3702(01)00129-1)，查询于 2026-08-14。
