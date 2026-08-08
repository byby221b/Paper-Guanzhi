# Temporal Difference Learning and TD-Gammon 精读报告

> Paper Guanzhi #42。本报告把「论文事实」「后续资料」「个人分析」与「补充推导」分开标示。论文事实主要依据 Gerald Tesauro 1995 年发表于 *Communications of the ACM* 的文章，以及经 ACM、作者和 IBM 许可公开的全文转录版。

## 元信息

- 标题：*Temporal Difference Learning and TD-Gammon*
- 作者：Gerald Tesauro（发表时隶属 IBM Thomas J. Watson Research Center）
- 发表：*Communications of the ACM*, 38(3), March 1995, pp. 58–68
- DOI：[10.1145/203330.203343](https://doi.org/10.1145/203330.203343)
- ACM 页面：[dl.acm.org/doi/10.1145/203330.203343](https://dl.acm.org/doi/10.1145/203330.203343)
- 合法可读全文：[Backgammon Galore 授权转录版](https://www.bkgm.com/articles/tesauro/tdl.html)
- 精读日期：2026-08-08
- 对应小红书期号：#42

### 原文取得与核验

- **论文事实**：Crossref 元数据确认作者、题名、卷期、页码、日期与 DOI；正式出版者为 ACM。
- **访问情况**：本轮访问 ACM 正式 PDF 返回 HTTP 403，因而没有声称逐页核读 ACM 原始排版文件。
- **替代全文**：保存的 `42-td-gammon-1995.pdf` 是西安大略大学课程站点提供的 16 页 PDF，由获许可 HTML 全文打印而成。文件为 PDF 1.2，69,413 字节，16 页，正文、Figure 1–3、Table 1–3 和未编号更新公式均可读；标题页明确写明原载 CACM、版权归 ACM，并说明转录获得 ACM 许可。
- **证据缺口**：该 PDF 不保留 CACM 原始内页分页，故下文只引用文章整体页码 58–68、章节名、图表号，以及转录 PDF 页码；不虚构 CACM 内页页码。
- **版面核查**：重点目视核对转录 PDF 第 5–7、9–14 页，覆盖网络图与公式、版本实验表、两个 rollout 案例、学习机制解释、结论与性能评估附录。

## 作者背景

### Gerald Tesauro

- **论文事实**：1995 年文末作者简介称 Tesauro 为 IBM research staff member，地址为 IBM Thomas J. Watson Research Center。论文与 Crossref 的作者机构字段相符。
- **可靠背景**：Institute for Advanced Study 官方档案显示，他于 1985 年 9 月至 1986 年 6 月在 School of Natural Sciences 任 Member，并于 1986 年获 Princeton University 博士学位。[IAS 档案](https://www.ias.edu/scholars/gerald-tesauro)
- **此前工作**：他先开发了用专家棋谱监督训练的 Neurogammon；随后在 1992 年 *Machine Learning* 论文 *Practical Issues in Temporal Difference Learning* 中报告 TD(λ) 在双陆棋上的早期实验。IBM 官方出版记录将该工作描述为 TD(λ) 首次用于复杂、非平凡任务的重要案例之一。[IBM 记录](https://research.ibm.com/publications/practical-issues-in-temporal-difference-learning)
- **后续轨迹**：2002 年，Tesauro 在 *Artificial Intelligence* 回顾如何把自博弈网络、浅层搜索与 doubling 算法组合成更强系统。[IBM 记录](https://research.ibm.com/publications/programming-backgammon-using-self-teaching-neural-nets)
- **证据边界**：没有找到可可靠确认其博士导师的权威来源，因此省略师承；也不据旧简介推断其 2026 年实时职务。

## 历史语境

### 当时的学术主流

20 世纪后半叶的博弈程序主要依赖两类能力：向前搜索，以及由人类专家设计的局面评价函数。国际象棋的深搜索路径已经显示出工程威力；双陆棋每个回合还要面对随机骰子，典型分支数约为 400，深搜成本更高。论文据此把精力放在「学出一个强评价函数」上，而非无限加深在线搜索（章节 *Complexity in the Game of Backgammon*）。

强化学习当时已有 Samuel 的西洋跳棋工作和 Sutton 的 temporal-difference 方法。Tesauro 指出的实践难题有两个：延迟回报下的时间信用分配仍很困难；传统方法常局限于查表或线性函数，难以表达复杂局面。与此同时，多层感知机为非线性函数逼近提供了工具。

### 待解决的核心问题

给定一盘从开局到终局的状态序列，智能体只在终局看到胜负结果。它需要同时完成：

1. 把终局结果的信用分配给此前许多状态；
2. 在巨大状态空间中用函数逼近实现泛化；
3. 用正在学习的同一个评价器控制双方走子，从自博弈中生成训练分布；
4. 在没有逐局专家标签的条件下，达到可与强人类棋手比较的水平。

### 直接前驱

- **Sutton, 1988**：提出 TD 方法与 TD(λ)，为延迟预测提供时间差更新。[Tesauro, 1995, Ref. 13]
- **Rumelhart, Hinton & Williams, 1986**：反向传播训练多层网络，为非线性函数逼近提供通用架构。[Ref. 9]
- **Samuel, 1959**：西洋跳棋自学习与评价函数研究，是博弈学习的早期范例。[Ref. 10]
- **Tesauro, 1992**：更详细分析 TD(λ) 在双陆棋中的实现与早期结果。[Ref. 15]
- **Neurogammon**：Tesauro 先前以专家棋谱和人工特征监督训练的系统，构成本文的重要基线。

### 与前人路线的关系

本文没有发明 TD，也没有提出 Q-learning。它把 Sutton 的 TD(λ)、多层感知机、贪心自博弈与双陆棋环境组合起来，展示非线性函数逼近在复杂控制任务中的规模化实践。其关键历史意义来自组合与实证，而非单一算法原语的首次提出。

## 问题形式化

> 以下「状态、输出、行动与目标」为依据原文叙述所作的补充形式化；论文实际展示的唯一更新公式见后文。

### 状态、输出与回报

一局双陆棋产生状态序列 $x_1,x_2,\ldots,x_f$，每个时间步是一方的一次走子，即一个 ply。神经网络以参数 $w$ 实现：

$$
Y_t=f_w(x_t)\in[0,1]^4.
$$

四个输出分别对应白方普通胜、白方 gammon、黑方普通胜、黑方 gammon 的预期结果。极少出现的 triple-value backgammon 没有建模。终局观察到四维结果向量 $z$。

### 行动与控制

对当前骰子产生的每个合法走法 $a$，系统生成后继局面并用网络计算 equity，选择当前一方预期结果最大的走法：

$$
a_t=\arg\max_{a\in\mathcal A(x_t)} E_w(x_{t+1}(a)).
$$

这里的网络是状态结果评价器，不是直接输出动作概率的策略网络，也不是显式的动作价值 $Q(s,a)$ 网络。行动选择来自「枚举合法走法 + 评价后继状态 + 贪心选择」。

### 学习目标

每个非终局状态的预测以后一时刻的预测为 bootstrap 目标，终局以真实结果 $z$ 为目标。其目标可理解为：让状态评价沿时间保持一致，并最终与整局结果一致。

### 关键假设

- 环境规则与合法走法生成器已知，整局可以高速模拟；
- 游戏终将到达可观测终局，终局结果能提供学习信号；
- 相近候选局面的估计误差具有相关性，因而相对排序可以比绝对数值更准确；
- 随机骰子带来状态空间探索，并使终局成为吸引态；
- 原始棋盘变量中存在较容易先学到的线性成分，为后续非线性概念提供起点。

## 核心方法

### 方法直觉

TD-Gammon 让同一网络同时扮演双方。每走一步，下一状态的估值就成为上一状态的临时教师；整局结束时，真实胜负取代临时教师。TD(λ) 的 eligibility trace 又把当前预测差沿时间向前传播，使较早状态也得到衰减后的更新。

骰子随机性不断扰动自博弈轨迹。网络即使从随机权重起步，也会遇见多样局面并最终得到终局反馈。论文观察到，网络先学会「少留 blot」「建立 point」等可由原始变量线性表达的规则，再逐渐学到依赖上下文的非线性概念。

### 网络架构

- 标准前馈多层感知机（Figure 1；转录 PDF 第 5 页）；
- 中间层使用 sigmoid 非线性，输出映射至单位区间；
- 1995 正文没有公开增强版本的完整输入特征表或精确输入维数；
- 初始实验只编码各位置的黑白棋子数量；后续版本加入 Neurogammon 使用过的人工特征，例如 blockade strength、being hit probability；
- 版本 1.0 起并非「完全零人工知识」。棋盘编码、规则、合法走法生成、网络架构以及附加特征均有人为设计。

### 训练算法

```text
初始化网络权重 w
重复进行自博弈：
  从开局状态 x_1 开始
  对每个时间步 t：
    根据骰子枚举所有合法走法
    用网络评价每个后继状态
    贪心选择当前一方 equity 最大的走法
    观察新状态 x_{t+1}
    计算时间差 Y_{t+1} - Y_t
    用 TD(λ) eligibility trace 更新 w
  到达终局后：
    用真实结果 z 替代下一状态预测，完成终局更新
```

### 与其他方法的区别

| 路线 | 监督信号 | 表示 | 控制方式 | 本文边界 |
|---|---|---|---|---|
| Neurogammon | 专家棋谱标注 | 原始编码 + 人工特征 | 网络评价走法 | 依赖专家示例 |
| TD-Gammon raw 基线 | 自博弈终局结果 | 原始棋盘编码 | 贪心评价后继状态 | 40 hidden、200,000 局，约等于 Neurogammon |
| TD-Gammon 1.0–2.1 | 自博弈终局结果 | 原始编码 + 人工特征 | 1-ply 或 2-ply | 强度同时来自学习表示、特征与浅层搜索 |
| Q-learning | 奖励与动作价值 bootstrap | 通常学习 $Q(s,a)$ | 对动作价值取最大 | 本文网络直接评价状态结果，不应混称 Q-learning |

### 作者对成功机制的解释

1. **相对误差小于绝对误差**：网络的 equity 绝对误差常超过 0.1，但同一状态的候选走法彼此相似，估计误差高度相关；比较时公共偏差可能抵消（章节 *Absolute Accuracy vs. Relative Accuracy*；转录 PDF 第 10–11 页）。
2. **随机环境提供探索**：骰子让自博弈覆盖更广状态，并减少确定性自博弈陷入狭窄循环策略的风险（*Stochastic Environment*；第 11 页）。
3. **先学习线性概念**：早期学到的简单概念可由原始输入的线性函数表示，形成远好于随机策略的学习起点；上下文相关的非线性概念稍后出现（*Learning Linear Concepts First*；第 11–12 页）。

这些解释是作者基于现象提出的机制分析。论文明确承认，对非线性 TD 自学习过程的完整理解仍很遥远。

## 关键公式推导

### 公式 1：TD(λ) 权重更新

**原文表述（未编号公式；Figure 1 后，转录 PDF 第 5 页）：**

$$
w_{t+1}-w_t
=\alpha\bigl(Y_{t+1}-Y_t\bigr)
\sum_{k=1}^{t}\lambda^{t-k}\nabla_wY_k.
$$

原文先按单个输出写式；四输出情形对各输出单元的权重变化求和。终局把 $Y_{f+1}-Y_f$ 换为 $z-Y_f$。公式没有折扣因子 $\gamma$。

**逐步推导（补充推导；假设一次更新内网络梯度按当前权重计算）：**

Step 1：定义一步预测差

$$
\delta_t=Y_{t+1}-Y_t,
$$

终局则定义 $\delta_f=z-Y_f$。依据是相邻预测应沿同一条自博弈轨迹保持一致，并在终局锚定真实结果。

Step 2：当前输出对权重的敏感度为 $\nabla_wY_t$。若只修正当前状态，最直接的增量是 $\alpha\delta_t\nabla_wY_t$。

Step 3：为了把当前差分归因给更早状态，引入几何衰减的 eligibility trace：

$$
e_t=\sum_{k=1}^{t}\lambda^{t-k}\nabla_wY_k.
$$

其中较早梯度相隔 $t-k$ 步，权重为 $\lambda^{t-k}$。

Step 4：将一步差分乘以 eligibility trace，得到

$$
\Delta w_t=\alpha\delta_te_t.
$$

代入 Step 1 与 Step 3，即得原文公式。

Step 5：trace 可递归计算：

$$
e_t=\lambda e_{t-1}+\nabla_wY_t.
$$

这避免每一步重新遍历全部历史。该递推是对求和式直接拆出最后一项所得。

Step 6：对四个输出 $j$ 分别计算 $\delta_t^{(j)}$ 与 $e_t^{(j)}$，总更新为

$$
\Delta w_t=\alpha\sum_{j=1}^{4}\delta_t^{(j)}e_t^{(j)}.
$$

这对应原文「multiple output units 时，对各输出贡献求和」的说明。

**直觉理解：** $Y_{t+1}-Y_t$ 告诉网络「刚才的判断应往哪个方向修正」，$e_t$ 告诉它「此前哪些参数活动仍应对当前误差负责」。$\lambda=0$ 只更新最近一步；$\lambda=1$ 让误差不衰减地传播到更早状态；中间值平滑连接两端。

### 公式 2：TD 误差与 λ-return 的关系

**补充推导，不是论文展示的第二个公式。** 为解释为何多个一步差分能还原面向终局的目标，暂时假设一局内比较这些量时权重固定。令 $\delta_m=Y_{m+1}-Y_m$（$m<f$），$\delta_f=z-Y_f$，则：

$$
\sum_{m=t}^{f}\lambda^{m-t}\delta_m
=-Y_t+(1-\lambda)\sum_{n=1}^{f-t}\lambda^{n-1}Y_{t+n}
+\lambda^{f-t}z.
$$

推导方法是展开左侧并收集每个 $Y_{t+n}$ 的系数：它从前一项获得 $+\lambda^{n-1}$，从后一项获得 $-\lambda^n$，净系数为 $(1-\lambda)\lambda^{n-1}$；首项留下 $-Y_t$，终局留下 $\lambda^{f-t}z$。

右侧除 $-Y_t$ 外，是从短期 bootstrap 到整局结果的几何混合目标。因而 eligibility trace 形式可理解为同时利用多个时间尺度的回报。这里的恒等式用于解释，不构成本文对非线性函数逼近收敛性的证明。

## 实验分析

### 实验设置

- **训练环境**：完整双陆棋模拟器，自博弈生成经验；网络从随机权重开始。
- **原始输入基线**：只编码棋盘上各位置的黑白棋子数量。
- **增强输入**：加入 Neurogammon 的人工特征；1995 正文未公开完整特征表和输入维数。
- **搜索**：1.0 为 1-ply；2.0、2.1 为 2-ply（Table 1 注释）。
- **评价手段**：与基准程序的大样本自动对局、与人类大师的小样本对局及逐步评议、候选走法的计算机 rollout（Appendix: *Performance Measures*）。

### 原始输入实验

论文报告，最佳 raw-encoding 网络有 40 个隐藏单元，训练 200,000 局，达到 strong intermediate、约与 Neurogammon 相当（章节 *Results of Training*；转录 PDF 第 6 页）。这说明原始状态编码加 TD 自博弈确实能学到有效评价，但该结果不是后续世界级表现的全部来源。

### 版本结果（Table 1；转录 PDF 第 7 页）

| 版本 | 自博弈局数 | 隐藏单元 | 搜索 | 人类对手测试 | 平均点差 |
|---|---:|---:|---:|---|---:|
| TD-Gammon 1.0 | 300,000 | 80 | 1-ply | 对 Robertie、Davis、Magriel，51 局净负 13 分 | −0.25 point/game |
| TD-Gammon 2.0 | 800,000 | 40 | 2-ply | 对 Goulding、Woolsey、Snellings、Russell、Sylvester，38 局净负 7 分 | −0.18 point/game |
| TD-Gammon 2.1 | 1,500,000 | 80 | 2-ply | 对 Robertie，40 局净负 1 分 | −0.02 point/game |

**必须保持的口径：**

- 论文没有把 raw 基线命名为「TD-Gammon 0.0」；这是后来的常见简称，不应回填为原文版本号。
- 300,000 局属于 1.0；raw 基线是 200,000 局。
- 2.0 的隐藏单元数是 40，不是 80。
- 2.1 在 40 局中仍净负 1 分。作者称 near parity，并转述 Robertie 对 strong master level 的评估；这不是统计证明「击败世界冠军」或「超越全体人类」。
- 2.0 与 2.1 的结果含 2-ply 搜索，不能全部归因于神经网络一次前向评价。

### 两个走法案例

**Figure 2 / Table 2（转录 PDF 第 9 页）：**开局 4-1 时，传统 slotting 与 split 的比较。

| 走法 | 网络 1-ply 估计 | 10,000 次 rollout |
|---|---:|---:|
| 13-9, 6-5 | −0.014 | −0.040 |
| 13-9, 24-23 | +0.005 | +0.005 |

**Figure 3 / Table 3（转录 PDF 第 10 页）：**1988 World Cup 局面。

| 走法 | 网络 1-ply 估计 | 10,000 次 rollout |
|---|---:|---:|
| 8-4*, 8-4, 11-7, 11-7 | +0.184 | +0.139 |
| 8-4*, 8-4, 21-17, 21-17 | +0.238 | +0.221 |

两张表的 rollout 都是用不同随机骰子把候选局面完成 10,000 次，标准差约 0.01。它们显示网络排序与 rollout 排序一致，并给出挑战传统走法的具体实例。论文同时说明 rollout 仍由不完美程序执行；对 doubling decision 尤其可能不如普通走法可靠。

### 实验设计评价

**优点：**

- 同时报告训练规模、网络规模、搜索深度与对局结果，便于区分版本；
- 除胜负外给出具体局面 rollout，帮助理解「相对排序优于绝对估值」；
- 附录主动比较三类评估法的长处和缺陷，没有把人机短赛包装成严格统计结论。

**不足：**

- 人类对局只有 38–51 局，随机骰子的方差很大；
- 对手集合与测试条件随版本变化，版本之间不构成完全受控实验；
- 从 1.0 到 2.1 同时改变训练局数、隐藏单元和搜索深度，不能做干净的单因素归因；
- 增强版本的特征列表、输入维数、训练超参数和复现实作细节不足；
- 专家逐步评议带主观性，论文也明确承认可能并非 100% 准确。

## 局限性

### 作者自述

- 2.1 仍有少量残局技术错误和 doubling cube 错误；实时搜索仅到 heuristic 2-ply。
- 可尝试 3-ply、残局专用特征、更多隐藏单元与更多训练，但这些在 1995 论文中是未来方向。
- 学习方法只探索了很小范围：动态调整 $\lambda$、非贪心自博弈、与专家短期训练交替、使用棋谱、替换函数逼近器、训练中动态扩展网络均未系统验证。
- 对一般非线性 TD 的理论理解仍不充分。
- 双陆棋原始变量中容易学习的线性成分，在国际象棋或围棋中可能不够有用；更好的表征是迁移成功的前提。
- 确定性游戏缺少骰子噪声，可能需要显式探索机制。
- 现实任务往往没有完整模拟器、已知合法动作集合和几乎无限的自生成训练经验。

### 容易被忽略的实现边界

- doubling cube 没有随主网络一起学习；后续回顾说明它使用单独的理论/启发式处理。
- 输出只覆盖四种普通胜或 gammon 结果，没有建模 triple-value backgammon。
- 「零知识」只适用于 raw-input 初始实验，而且仍以规则、棋盘编码、合法走法与 MLP 结构为先验；1.0 以后明确使用人工特征。
- 贪心自博弈可能强化当前策略分布中的盲区。本文借助随机骰子获得探索，但没有给出对一般环境有效的探索保证。

### 假设检验

**个人分析：** TD-Gammon 的成功依赖一组彼此配合的条件：环境可完全模拟、终局反馈明确、随机性天然提供探索、合法动作可枚举、候选局面局部相似、原始表示含可学习的低阶结构。这些条件解释了该方法为何在双陆棋上格外合适，也限制了它被直接移植到现实控制任务的范围。

## 后续影响

### 直接后继

- **Tesauro & Galperin, 1996**：在 NeurIPS 研究用 Monte Carlo 模拟做在线策略改进，并以 TD-Gammon 为强基线。[IBM 记录](https://research.ibm.com/publications/on-line-policy-improvement-using-monte-carlo-search)
- **Tesauro, 2002**：系统回顾自学习网络、浅层搜索和 doubling 算法如何结合，强调更强版本的表现不能只归因于单一学习器。[IBM 记录](https://research.ibm.com/publications/programming-backgammon-using-self-teaching-neural-nets)
- **Mnih et al., 2015**：DQN 论文直接引用 1995 CACM 文章，同时指出此前强化学习成功常受限于人工特征或低维完整状态；DQN 的像素输入、卷积表示、experience replay 与 target network 都超出 TD-Gammon 范畴。[Nature 论文](https://www.nature.com/articles/nature14236)

### 开创的方向

更准确的定位是：TD-Gammon 成为「自博弈 + temporal-difference learning + 神经网络函数逼近」的一项早期里程碑。它给后来研究留下的经验是，强策略可以从自生成经验与结果信号中形成，而表示、探索和搜索深度同样决定最终表现。

### 当代回响

今天的深度强化学习继续使用 bootstrap value learning、神经网络函数逼近、自博弈和搜索的组合，但工程机制已有显著扩展。把 DQN、AlphaGo 或 AlphaZero 概括成 TD-Gammon 的简单放大版会抹去卷积表示、经验回放、目标网络、MCTS、策略网络等关键差别。可把它们放在较宽的技术谱系中比较，不应声称 1995 论文直接给出了后来的完整方案。

### 引用统计

- Semantic Scholar API：2,224 citations（查询日期 2026-08-08，DOI 精确匹配；动态值）。
- OpenAlex：1,489 cited-by works（查询日期 2026-08-08，记录 `W2131600418`；动态值）。
- Crossref：`is-referenced-by-count` 为 1,020（查询日期 2026-08-08；动态值）。

不同数据库的收录范围和去重方式不同，三个数字不应相互替代或硬对齐。

## 个人笔记

最让我停下来的不是「1.5 million games」这个规模，而是作者在 *Absolute Accuracy vs. Relative Accuracy* 中承认：网络的绝对 equity 估计常常能错到 0.1 以上，走法排序却仍可达到大师水准。

这把评价函数的任务说得很清楚。决策未必要求一把精确的尺；它首先要求同一组候选局面被一把偏差相近的尺稳定排序。Figure 3 的两个走法就是具体证据：网络估计与 10,000 次 rollout 的绝对数值并不一致，但两者把同一个反直觉走法排在前面。

我也因此更谨慎地看待「自博弈从零学会」这句话。真正有效的系统包含环境随机性、人工表示、合法动作枚举、浅层搜索和评估协议。学习算法是中心，却不是孤立的奇迹。这种把成功拆回条件的写法，是本文比传奇叙事更值得保留的部分。

## 小红书写作备忘

### Hook 素材

1. 一个 equity 绝对误差常超过 0.1 的网络，仍能把极相似的候选走法排出大师级次序。
2. TD-Gammon 2.1 训练 150 万局后，与 Robertie 的 40 局测试仍净负 1 分；论文的准确说法是 near parity。
3. 骰子的随机性在这里承担了探索器的角色，让自博弈不易困在狭窄轨迹中。

### 核心 Insight（一句话）

TD-Gammon 的力量来自时间差学习、函数逼近、自博弈、随机探索、人工表示与浅层搜索的共同作用，而相对排序的准确性比绝对估值更接近决策本质。

### 自查重点

- 不把本文写成 Q-learning、策略网络，或 TD 的发明论文。
- 不把 raw 基线称作原文的「0.0」，不混淆 200,000 与 300,000 局。
- 不称所有版本为零人工知识；1.0 起加入人工特征。
- 不把 2.1 的 40 局净负 1 分写成击败世界冠军或统计证明超人类。
- 不把 2-ply 搜索、doubling 启发式或后来的 3.x 版本归入网络单独学得的能力。

### 动态 Hashtags

#强化学习 #自博弈 #TemporalDifference #TDGammon

## 参考来源

1. Gerald Tesauro. “Temporal Difference Learning and TD-Gammon.” *Communications of the ACM* 38(3), 58–68, 1995. [DOI](https://doi.org/10.1145/203330.203343)
2. Tom Keith transcriber. [ACM、Gerald Tesauro 与 IBM 授权的全文转录](https://www.bkgm.com/articles/tesauro/tdl.html)
3. [Crossref DOI metadata](https://api.crossref.org/works/10.1145%2F203330.203343)
4. [IBM Research: Practical Issues in Temporal Difference Learning](https://research.ibm.com/publications/practical-issues-in-temporal-difference-learning)
5. [IBM Research: TD-Gammon Achieves Master-Level Play](https://research.ibm.com/publications/td-gammon-a-self-teaching-backgammon-program-achieves-master-level-play)
6. [IBM Research: Programming Backgammon Using Self-Teaching Neural Nets](https://research.ibm.com/publications/programming-backgammon-using-self-teaching-neural-nets)
7. [Institute for Advanced Study: Gerald Tesauro](https://www.ias.edu/scholars/gerald-tesauro)
8. Mnih et al. “Human-level control through deep reinforcement learning.” *Nature* 518, 529–533, 2015. [Nature](https://www.nature.com/articles/nature14236)
9. [Semantic Scholar API, DOI exact record](https://api.semanticscholar.org/graph/v1/paper/DOI:10.1145/203330.203343?fields=title,authors,year,citationCount,externalIds,url)
10. [OpenAlex record W2131600418](https://api.openalex.org/works/https:%2F%2Fdoi.org%2F10.1145%2F203330.203343)
