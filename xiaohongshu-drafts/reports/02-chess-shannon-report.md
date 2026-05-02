# 精读报告 #02: Programming a Computer for Playing Chess

---

## 元信息

- 标题：Programming a Computer for Playing Chess
- 作者：Claude E. Shannon（Bell Telephone Laboratories, Murray Hill, N.J.）
- 发表：*Philosophical Magazine*, Ser. 7, Vol. 41, No. 314, March 1950, pp. 256–275
- 首次报告：National IRE Convention, March 9, 1949, New York
- 原文链接：https://www.tandfonline.com/doi/abs/10.1080/14786445008521796
- 精读日期：2026-05-02
- 对应小红书期号：#02

---

## 作者背景

### Claude Elwood Shannon (1916–2001)

- **发表时身份**：Bell Telephone Laboratories 数学研究部研究数学家。Shannon 自 1941 年加入 Bell Labs，此时正处于其学术生涯的巅峰期——两年前刚发表了改变世界的信息论论文。
- **师承**：博士导师为 Vannevar Bush（MIT），1940 年在 MIT 获得数学博士学位，博士论文题目为 *An Algebra for Theoretical Genetics*。Bush 是微分分析仪（differential analyzer，一种早期模拟计算机）的发明者，后来在二战期间担任美国科学研究与发展办公室主任，主导了曼哈顿计划等重大科研项目。
- **此前工作**：
  · **硕士论文**（1937）：*A Symbolic Analysis of Relay and Switching Circuits*——年仅 21 岁时在 MIT 完成，证明了 Boolean 代数可以用于分析和优化电子开关电路。Howard Gardner 称其为「可能是本世纪最重要、也最著名的硕士论文」。这篇论文奠定了所有数字电路设计的理论基础。
  · **A Mathematical Theory of Communication**（1948）：发表于 *Bell System Technical Journal*，分上下两部分（7 月和 10 月），单枪匹马地创立了信息论。引入了 bit 作为信息的基本单位，建立了信道容量的数学框架，定义了通信系统中的熵。这篇论文被广泛认为是 20 世纪最有影响力的科学论文之一。
- **后续轨迹**：1956 年转入 MIT，担任电气工程与数学教授（Donner Professor of Science），直至 1978 年。一生热爱趣味数学、机械装置和游戏——在 Bell Labs 以骑独轮车穿走廊、同时抛接球而闻名。晚年罹患阿尔茨海默病，2001 年 2 月 24 日去世，享年 84 岁。

---

## 历史语境

### 当时的学术主流

1950 年前后，电子计算机刚刚从理论构想变为现实。ENIAC（1945）和 Manchester Mark 1（1948）等机器证明了大规模数值计算的可行性。然而，「计算机能否处理非数值问题」这一议题才刚刚被提出。

在博弈论方面，von Neumann 和 Morgenstern 于 1944 年出版 *Theory of Games and Economic Behavior*，为零和博弈提供了严格的数学框架——特别是极小极大定理（minimax theorem）。但将这一理论应用于具体的棋类游戏、并在实际计算机上实现，尚无先例。

### 待解决的核心问题

国际象棋被认为需要「思考」才能下好。如果计算机能下棋，这是否意味着它在「思考」？更具体地说：如何将一个需要判断力、想象力和大量试错的问题——而不是纯粹的数值计算——编码到计算机中？

直觉上，完美下棋在原理上是可能的（博弈论已证明），但 Shannon 的计算表明，穷举搜索需要评估 $10^{120}$ 种变化，即使每微秒评估一种，也需要 $10^{90}$ 年。因此，核心问题变成了：如何在有限的计算资源下，下出「尚可」的棋？

### 同时期的相关工作

- Turing, *Computing Machinery and Intelligence*（1950）——同年发表，从哲学层面讨论机器智能，恰好以「下棋」为示例之一。
- Turing & Champernowne, *Turochamp*（约 1948）——可能是最早的完整国际象棋算法，但从未在实际计算机上运行，Turing 用手工模拟执行。Shannon 在论文中未直接引用此工作，尽管两人在二战期间曾在 Bell Labs 见面并讨论过计算和国际象棋。
- von Neumann & Morgenstern, *Theory of Games and Economic Behavior*（1944）——Shannon 直接引用了这部著作，并将 minimax 原理应用于国际象棋。
- Torres y Quevedo, *El Ajedrecista*（1912 年建造，1913 年首次演示，1914 年巴黎展出）——世界上第一台真正的博弈自动机，能用车和王对单王下残局并将杀。Shannon 在论文中引用了 Vigneron (1914) 的记述。

### 直接前驱

1. von Neumann & Morgenstern (1944) — minimax 定理，Shannon 方法论的理论基石。
2. Torres y Quevedo (1914) — 实际的国际象棋残局机，证明了机械下棋的可能性。
3. De Groot (1946) — 对国际象棋大师思维过程的心理学研究，Shannon 从中获取了关于搜索深度和分支因子的经验数据。
4. Hardy & Wright, *The Theory of Numbers* (1938) — Shannon 引用的关于 Nim 游戏评价函数的数学结果。
5. Wiener, *Cybernetics* (1948) — 提供了关于机器与智能行为的更广泛思想背景。

---

## 问题形式化

### 问题定义

给定一个国际象棋局面 $P$（包括棋子位置、走棋方、易位权利、过路兵状态等），设计一个**策略**（strategy）来选择最优走法。

形式化地，策略是一个函数：
$$S: \mathcal{P} \rightarrow \mathcal{M}$$
其中 $\mathcal{P}$ 是所有合法局面的集合，$\mathcal{M}$ 是所有合法走法的集合。

### 输入与输出

- **输入**：当前局面 $P$（64 个格子的状态，编码为 -6 到 +6 的整数序列，加上附加状态变量）
- **输出**：一个走法 $(a, b, c)$，其中 $a$ 是起始格，$b$ 是目标格，$c$ 是升变选择（如适用）

### 目标 / 评价准则

Shannon 明确指出，目标不是**完美下棋**（impractical）也不是**合法下棋**（trivial），而是下出 **tolerably good** 的棋——大致相当于优秀人类棋手的水平。

---

## 核心方法

### 直觉

Shannon 的核心洞见是将国际象棋问题分解为两个可独立设计的组件：

1. **评价函数**（evaluation function）$f(P)$——对任意局面给出一个数值评分，衡量局面对某一方的有利程度。
2. **搜索策略**（search strategy）——基于评价函数，通过向前搜索若干步来选择最优走法。

他进一步提出两种根本不同的搜索策略：
- **Type A**（穷举型）：搜索所有变化至固定深度，然后评估。
- **Type B**（选择型）：只搜索有意义的变化（将军、吃子、攻击性走法），且搜索至局面稳定（quiescent）时才评估。

### 形式化描述

#### 评价函数

$$f(P) = 200(K - K') + 9(Q - Q') + 5(R - R') + 3(B - B' + N - N') + (P_w - P_b)$$
$$- 0.5(D - D' + S - S' + I - I') + 0.1(M - M') + \ldots$$

其中：
- $K, Q, R, B, N, P_w$：白方的王、后、车、象、马、兵的数量
- 带撇号 ($'$) 的对应量为黑方
- $D, S, I$：白方的叠兵（doubled）、落后兵（backward）、孤兵（isolated）数量
- $M$：白方的机动性（合法走法数）

王被赋予 200 的值（人为地大于其他所有项之和），以保证将杀在评价函数中占据绝对主导。

#### Minimax 搜索

对于搜索深度为 2（双方各走两步）的 Type A 策略：

$$\text{best move} = \arg\max_{M_i} \min_{M_{ij}} \max_{M_{ijk}} \min_{M_{ijkl}} f(M_{ijkl} M_{ijk} M_{ij} M_i P)$$

即：白方第一步最大化，黑方最小化，白方第二步最大化，黑方最小化——这正是 von Neumann minimax 原理的直接应用。

#### 静态评估条件（Quiescence）

Shannon 定义了一个静态判断函数 $g(P)$：

$$g(P) = \begin{cases} 1 & \text{若有棋子被低价值棋子攻击，或被多子攻击少子防守，或有不安全格上的将军} \\ 0 & \text{否则} \end{cases}$$

搜索时，变化展开至 $g(P) = 0$（局面安静），但始终至少展开 2 步，最多 10 步。

#### 程序架构

Shannon 将完整的 Type A 程序分为 10 个子程序：
- $T_0$：执行走法
- $T_1$–$T_6$：分别生成兵、马、象、车、后、王的合法走法
- $T_7$：综合调度，生成所有合法走法
- $T_8$：计算评价函数 $f(P)$
- $T_9$：主程序，执行 minimax 搜索并选择最优走法

### 关键定理与证明

Shannon 的论文不包含定理-证明结构。但他给出了几个重要的计算论证：

**论证 1：穷举搜索的不可行性**

- 每步约有 30 种合法走法
- 一盘棋约 40 步（至投降）
- 变化总数：$30^{80} \approx 10^{120}$（后人称之为 Shannon number）
- 以每微秒评估一种变化的速度，需要 $10^{90}$ 年

**论证 2：字典法的不可行性**

- 所有可能的局面数：约 $\frac{64!}{32!(8!)^2(2!)^6} \approx 10^{43}$
- 为每个局面存储最优走法同样不可行

因此，必须采用近似评价 + 有限深度搜索的方法。

### 与前人方法的本质区别

在 Shannon 之前：
- 博弈论（von Neumann）提供了抽象的最优策略理论，但没有讨论实际计算。
- Torres y Quevedo 的残局机是硬接线的专用设备，只能处理一种特定残局。
- Turing 的 Turochamp 是另一个独立的尝试，但从未公开发表。

Shannon 的独创性在于：
1. 首次系统地将博弈论的 minimax 原理**工程化**——给出了完整的程序架构。
2. 首次提出**评价函数**的概念并给出具体实例——将棋类判断量化为数值。
3. 明确区分了 Type A 和 Type B 两种根本不同的策略——这一分类框架影响了此后六十年的计算机国际象棋研究。
4. 引入了**静态性判断**（quiescence）的概念——这在所有现代棋类引擎中都是标准技术。

---

## 关键公式推导

### 公式 1：评价函数

**原文表述：**

$$f(P) = 200(K-K') + 9(Q-Q') + 5(R-R') + 3(B-B'+N-N') + (P-P') - 0.5(D-D'+S-S'+I-I') + 0.1(M-M') + \ldots$$

**逐步分析：**

Step 1: 物质项——Shannon 采用了传统的棋子相对价值（后=9, 车=5, 象=马=3, 兵=1）。这些值来自数百年的国际象棋经验，而非理论推导。王被赋予 200，使得失去王（被将杀）的代价远大于失去所有其他棋子之和（$9+2\times5+2\times3+2\times3+8\times1 = 39$）。
— 依据：传统国际象棋估值 + 确保将杀主导

Step 2: 兵形项——叠兵（D）、落后兵（S）、孤兵（I）各减 0.5。这些是公认的结构性弱点，Shannon 承认系数 0.5 是粗略估计。
— 依据：经验棋理（empirical chess principles）

Step 3: 机动性项——$M$ 为合法走法数，系数 0.1。机动性高意味着棋子活跃度高，但其重要性远低于物质优势。
— 依据：棋理格言「the side with the greater mobility... has the better game」

**直觉理解：**

这个函数本质上是一个**加权线性组合**——将多个棋局特征（物质、兵形、机动性）映射为一个标量分数。正值对白方有利，负值对黑方有利。它是精确评价（只有赢/平/输三个值）的一个**连续近似**——这恰恰对应了实战中局面「大优」「微优」「均势」等连续的优势梯度。

### 公式 2：双步 Minimax

**原文表述：**

$$\max_{M_i} \min_{M_{ij}} \max_{M_{ijk}} \min_{M_{ijkl}} f(M_{ijkl} M_{ijk} M_{ij} M_i P)$$

**逐步推导：**

Step 1: 白方走第一步 $M_i$，得到局面 $M_i P$。
Step 2: 黑方回应 $M_{ij}$，得到局面 $M_{ij} M_i P$。黑方选择使 $f$ 最小的回应——因为黑方的目标是让白方的评估尽可能差。
Step 3: 白方走第二步 $M_{ijk}$，得到局面 $M_{ijk} M_{ij} M_i P$。白方选择使 $f$ 最大的走法。
Step 4: 黑方走第二步 $M_{ijkl}$，得到最终评估局面。黑方再次选择最小化。
Step 5: 回溯：从叶节点回溯至根节点，交替取 min 和 max，最终白方选择使回溯值最大的第一步。

— 依据：von Neumann minimax 定理在有限深度博弈树上的直接应用

**直觉理解：**

这个表达式描述了一种「假设对手最优」的搜索过程。在每一层：
- 轮到白方时，选择对白方最有利的走法（max）；
- 轮到黑方时，假设黑方选择对白方最不利的走法（min）。

这样得到的评估值是**在双方最优对弈下**白方所能获得的最好结果。

---

## 实验分析

### 实验设置

Shannon 没有在计算机上运行实际实验。但他进行了两类思想实验：

1. **随机策略的对弈测试**：Shannon 亲自与随机策略（每步随机选择合法走法）对弈，结果「generally in four or five moves」即可将杀。他给出了一盘实际的棋谱：1. g3 e5 2. d3 Bc5 3. Bd2 Qf6 4. Nc3 Qxf2 mate。
2. **计算量估算**：以三步深度（双方各三步）的 Type A 策略为例，需评估约 $30^6 \appro 10^9$ 个局面。每个局面评估需 1 微秒（「very optimistic」），则每步需要超过 16 分钟，一盘 40 步的棋需要 10 小时以上。

### 主要结果

Shannon 的核心实验性结论是：
- 随机策略极其糟糕（「unbelievably bad」），证明了合理策略的必要性。
- 纯 Type A 策略在 1950 年的硬件条件下太慢，且因为搜索深度有限（约 3 步），棋力较弱。
- Type B 策略（选择性搜索 + quiescence）有望在与人类相当的速度下达到「fairly strong」的水平。

### 关键发现

Shannon 观察到一个有趣的经验事实：国际象棋中每步的合法走法数（约 30）在整盘棋中相当稳定，直到残局阶段才明显下降。他引用了 De Groot 的实验数据来支持这一点。

他还指出了一个重要的洞见：计算机下棋的几个优势——速度、无误计算、不会偷懒、不会受情绪影响——必须与人类的「灵活性、想象力、归纳和学习能力」相权衡。

### 实验设计评价

- **优点**：Shannon 的计算量估算精确且有说服力，为后续研究提供了清晰的计算复杂度框架。与随机策略的实际对弈虽然简单，却直观地证明了评价函数的必要性。
- **不足**：没有在实际计算机上运行程序——Shannon 承认「it is planned, however, to experiment with a simple strategy on one of the numerical computers now being constructed」，但这一实验在论文发表时尚未完成。

---

## 局限性

### 作者自述

1. Shannon 明确承认评价函数的系数（如 0.5 和 0.1）是「merely the writer's rough estimate」。
2. 他承认 Type A 策略既慢又弱——搜索深度不够，且在非静态局面下评价不准确。
3. 论文未讨论残局策略，Shannon 只说「there seems no reason... why an end game strategy cannot be designed and programmed equally well」。
4. Shannon 坦言程序「will not learn by mistakes」——改进只能通过修改程序来实现，而非让机器自我学习。

### 后续批评

1. **Type A 的胜利**：Shannon 本人倾向于 Type B 策略（更像人类的选择性搜索），但历史证明 Type A 路线（配合高效的 alpha-beta 剪枝和强大的硬件）最终取得了更大的成功。Deep Blue 在 1997 年击败 Kasparov，本质上走的是 Type A 路线——每秒评估 2 亿个局面。
2. **评价函数的简单性**：Shannon 的线性加权评价函数过于简单。后续研究引入了数百甚至数千个手工调参的评价特征（如 Stockfish），最终被神经网络（如 AlphaZero 的 self-play）所取代。
3. **未预见 alpha-beta 剪枝**：Shannon 没有提到剪枝技术——alpha-beta 剪枝在 1950 年代末被独立发现，可以在不改变结果的情况下将搜索量减少至 $O(\sqrt{N})$（最优情况下），极大地提高了 Type A 策略的实用性。

### 假设检验

Shannon 的核心假设是：国际象棋的复杂性可以通过评价函数 + 有限深度搜索来近似处理。这一假设在以下条件下可能失效：
- 需要极深的策略计算（如某些残局需要 50 步以上的精确计算）——有限深度搜索无法覆盖。
- 评价函数的线性结构无法捕捉非线性的局面特征（如复杂的战术组合）。
- 然而，这一基本框架（搜索 + 评价）最终证明是正确的，在加入足够的工程改进后，确实达到了超越人类最强棋手的水平。

---

## 后续影响

### 直接后继

1. **Bernstein et al.**（1958）——在 IBM 704 上实现了第一个可运行的国际象棋程序，基本遵循 Shannon 的 Type B 策略。
2. **Newell, Shaw & Simon**（1958）——NSS 国际象棋程序，同样是 Type B 路线。
3. **Samuel, *Some Studies in Machine Learning Using the Game of Checkers***（1959）——将 Shannon 的评价函数思想扩展到跳棋，并引入了学习机制。
4. **Greenblatt, *Mac Hack VI***（1967）——第一个在锦标赛中击败人类棋手的程序。
5. **Knuth & Moore, alpha-beta 剪枝的分析**（1975）——为 Shannon 框架的关键优化提供了理论基础。

### 开创的方向

Shannon 这篇论文开创了**计算机博弈**（Computer Game Playing）这一整个研究领域。更重要的是，他提出的「评价函数 + 搜索」框架远远超出了国际象棋，成为了：
- 所有棋类 AI 的基本范式（围棋、将棋、跳棋……）
- 规划和决策系统的基本思路
- 游戏 AI（pathfinding, 策略游戏）的理论基础
- 强化学习中 value function + tree search 方法的先驱

### 当代回响

- **Deep Blue**（1997）：IBM 的超级计算机击败 Kasparov，本质上是 Shannon Type A 策略的极致工程实现。
- **Stockfish**：当今最强的传统象棋引擎，使用 alpha-beta 搜索 + 数百个手工调参的评价特征——Shannon 框架的直接延续。
- **AlphaZero**（2017, DeepMind）：用深度神经网络替代手工评价函数，用 Monte Carlo Tree Search 替代 alpha-beta——这是对 Shannon 框架的一次根本性革新，但「搜索 + 评价」的二元结构仍然保留。
- **Shannon number**（$10^{120}$）已成为复杂性理论和博弈论中的标准参考量。

### 引用统计

- Semantic Scholar 引用数：约 1,026（截至 2026 年 5 月）
- Google Scholar 引用数：估计约 2,000–3,000+（截至 2026 年 5 月）

---

## 个人笔记

1. **最让我惊叹的 insight**：Shannon 对 quiescence（静态性）概念的提出。这看起来如此自然，以至于容易被忽略——但它解决了一个根本性问题：在何处停止搜索并信任评价函数。如果在吃子交换的中间停下来评估，结果会完全失真。Shannon 将国际象棋大师的直觉——「看到安静的局面才做判断」——形式化为一个简洁的函数 $g(P)$。这一技术至今仍是所有严肃棋类引擎的标配。

2. **对比 Turing 的风格**：Shannon 的论文极为工程化——它给出了完整的数据结构（棋盘表示为 64 个整数）、模块化的程序架构（$T_0$–$T_9$）、具体的内存估算（约 3000 bits）。这与 Turing 同年发表的哲学性论文形成了鲜明对比。两篇论文像是一枚硬币的两面：Turing 问「机器能否思考？」，Shannon 则问「让我们具体来编程看看」。

3. **Shannon 对机器学习的看法**：他在第七节末尾简短地讨论了自我改进的可能性——「some thought has been given to designing a program which is self-improving but... the methods thought of so far do not seem to be very practical.」这与 AlphaZero 通过 self-play 学会下棋形成了有趣的历史呼应——Shannon 看到了方向，但承认 1950 年的技术还够不到。

4. **随机策略的那盘棋**：Shannon 亲自和随机策略下了一盘棋，四步将杀（1. g3 e5 2. d3 Bc5 3. Bd2 Qf6 4. Nc3 Qxf2#）。这个小细节既有趣又有教育意义——它以最直观的方式展示了为什么评价函数是必需的。

---

## 小红书写作备忘

### Hook 素材

1. 1950 年，信息论之父在国际象棋棋盘上留下了一个数字：$10^{120}$——这是国际象棋的博弈树复杂度，比可观测宇宙中的原子数还要多出 40 个数量级。
2. Shannon 和 Turing 在同一年各发了一篇论文。Turing 问「机器能思考吗？」，Shannon 的回答是：让我们先教它下棋试试。
3. 从 Shannon 1950 年的评价函数到 AlphaZero 2017 年的神经网络，变的是技术，不变的是框架——搜索 + 评价。

### 核心 Insight（一句话）

Shannon 的真正贡献不是某个具体的国际象棋程序，而是一个二元框架：评价函数告诉你「这里好不好」，搜索策略告诉你「往哪里看」——六十年后的 AI 仍然在这个框架内工作。

### 自查重点

1. Shannon 是在 Bell Labs 而非 MIT 工作时写的这篇论文（他 1956 年才去 MIT）。
2. 论文发表于 *Philosophical Magazine*（不是 *Bell System Technical Journal*），首次报告于 1949 年 3 月的 IRE 大会。
3. Shannon number 是 $10^{120}$（博弈树复杂度），不是 $10^{43}$（状态空间复杂度）——二者不同。
4. Type A 是穷举型，Type B 是选择型——不要混淆。
5. 评价函数中王的系数是 200（不是 infinity 或某个更大的数）。

### 动态 Hashtags

#计算机国际象棋 #博弈论 #信息论之父
