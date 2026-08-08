# 精读报告 #39：Learning from Delayed Rewards

## 元信息

- 标题：*Learning from Delayed Rewards*
- 作者：Christopher John Cornish Hellaby Watkins
- 发表：剑桥大学 King's College 心理学博士论文，1989 年 5 月提交；学位授予机构为 University of Cambridge
- 导师：Richard Young
- 原文：[Cambridge Apollo 仓储条目（DOI: 10.17863/CAM.132164）](https://doi.org/10.17863/CAM.132164)
- 作者提供的扫描本：[Chris Watkins 个人学术主页](https://www.cs.rhul.ac.uk/~chrisw/thesis.html)
- 精读日期：2026-08-08
- 对应小红书期号：#39

**版本与页码说明**：本次使用 Cambridge Apollo 仓储的 Primary Thesis，文件 7.52 MB、241 个 PDF 页面。它是由复印件扫描而成的 1989 年论文，无文本层；正文从 PDF 第 8 页开始，正文与附录印刷页码 1–228，另有前置页和参考文献。前置材料中有两页 **Corrigenda**，分别修订印刷页 p. 90、p. 91、p. 98、p. 227 与 p. 228；当前扫描本未标出勘误加入日期。下文的“p.”均指论文印刷页码，不指 PDF 物理页码。正文通过 OCR 辅助检索，标题页、目录、勘误、关键公式、实验图表与证明页均回到扫描页面核对。

**证据边界**：论文事实以当前扫描本为准，并区分原印刷页与前置 Corrigenda；Watkins 后来对写作经过的回忆、1992 年收敛证明、2010 年 Double Q-learning 与 2015 年 DQN 均标为“后续资料”。后来的结果不倒写成论文已经完成的工作。

---

## 作者背景

### Christopher J. C. H. Watkins

- **发表时身份（论文事实 + 官方仓储）**：King's College, Cambridge 的博士候选人。Cambridge Apollo 记录论文提交日为 1989-05-10、导师为 Richard Young、学位为 PhD、归入 Theses - Psychology。
- **同期工作（作者后续自述）**：Watkins 回忆自己 1982–1985 年在 King's College 研究皮亚杰理论，随后进入 Philips Research Labs 的 AI 组，从事专家系统与决策树工作，同时继续博士研究。1987 年参加 UC Irvine 的第四届 International Workshop on Machine Learning 后，他开始把动物学习问题与 Markov decision process（MDP）及 dynamic programming 联系起来。[来源：作者《Reinforcement Learning: some history》](https://www.cs.rhul.ac.uk/home/chrisw/RL_some_history.html)
- **学术渊源（可确认部分）**：导师 Richard Young 见论文序言与 Cambridge 仓储。Watkins 在序言中感谢 Rich Sutton 提供技术报告并指出 Q-learning 的一个困难，也感谢 Andy Barto 的讨论；作者后来的回忆称 Barto 在 1989 年春担任其博士答辩外审。这里是学术交流和评审关系，未发现可靠证据支持更强的“师承”表述。
- **后续轨迹**：现为 Royal Holloway, University of London 计算机科学教授；其官方主页称自己在 1980 年代后期将强化学习与 MDP 联系起来并提出 Q-learning。[来源：Royal Holloway 个人主页](https://www.cs.rhul.ac.uk/~chrisw/)

### 一段值得保留的写作史

**后续资料**：Watkins 自述，Sutton 1983 年与 Barto、Anderson 合作的杆平衡论文促使他寻找算法背后的理论框架；他从 Bellman 与 Dreyfus 的 *Applied Dynamic Programming* 中找到 MDP。关键转向是把“已知整个 MDP 后做精确计算”改写为“身处 MDP 的小动物只凭逐步经验学习”。作者后来回忆，Peter Dayan 指出其 1989 年证明草图没有得到概率 1 收敛，二人遂在 1992 年发表完整证明。当前扫描本的前置 Corrigenda 已给 p. 228 补入强大数定律和概率 1 陈述，但未注明加入时间；这一版本张力需要在证明部分单独交代。

---

## 历史语境

### 当时的学术主流

1980 年代末，三条线在这里汇合：

1. **动态规划**：Bellman 的最优性原理已经能在转移概率与奖励模型已知时计算最优策略，但这更像离线规划。
2. **动物学习与联结主义**：Barto、Sutton 与 Anderson（1983）的 adaptive heuristic critic 用可学习评价器控制杆平衡，展示了延迟奖励下的在线学习。
3. **时间差分学习**：Sutton（1988）系统化 TD 方法，用相邻时刻预测的差来更新价值估计，避免等到整个试验结束。

Watkins 的问题意识来自动物行为学。论文开篇先问：最优觅食理论可以用随机动态规划算出动物“应当”如何行动，但动物自身怎样从经验中学到高效策略？这一视角让“算法是否计算出最优策略”和“一个有限记忆的行动者怎样逐步学会它”成为两个不同问题（Summary；ch. 1）。

### 待解决的核心问题

传统动态规划需要环境模型：每个状态—动作对的转移分布与期望奖励。Watkins 希望学习者只观察四元组“当前状态、动作、即时奖励、下一状态”，就能直接学习最优动作价值，并允许实际行为策略与当前估计的最优策略不同。

论文第 7 章把这种方法称为 **primitive learning**：它是以真实经验替代转移与奖励模型的增量式 Monte Carlo dynamic programming。行动者舍去内部模型中的“心智实验”，直接在世界中做实际实验（ch. 7, p. 81）。

### 直接前驱

- Bellman（1957）与 Bellman、Dreyfus（1962）：动态规划和 MDP 的计算框架。
- Barto, Sutton & Anderson（1983）：adaptive heuristic critic 与杆平衡。
- Sutton（1988）：TD($\lambda$) 与 prediction difference。
- Witten（1977）：论文讨论的早期 action-strength / adaptive control 工作。
- Werbos（1977）：价值预测与自适应控制的相关思想。

**评价**：论文的原创定位宜写成“把无模型的动作价值学习明确组织为增量动态规划，并提出 one-step Q-learning”，不宜把强化学习整体的起源归于一人。Watkins 本人在 p. 96 写得很克制：这个简单想法据他所知此前未被提出，但随机动态规划已研究三十余年，他不认为从未有人想到类似 Monte Carlo 方法。

---

## 问题形式化

### 问题定义

考虑有限折扣 MDP $\langle \mathcal S,\mathcal A,P,R,\gamma\rangle$：

- $x_t\in\mathcal S$：时刻 $t$ 的状态；
- $a_t\in\mathcal A(x_t)$：执行的动作；
- $r_t$：执行后观察到的即时奖励；
- $x_{t+1}$：下一状态；
- $0\leq\gamma<1$：折扣因子。

从状态—动作对 $(x,a)$ 出发并在之后按策略 $f$ 行动，其动作价值是未来折扣回报的条件期望。最优动作价值记为 $Q^*(x,a)$，最优状态价值满足 $U^*(x)=\max_a Q^*(x,a)$。

### 输入与输出

- **输入**：逐步或彼此断开的经验样本 $[x_t,a_t,r_t,x_{t+1}]$；算法不需要显式知道 $P$ 与 $R$。
- **输出**：每个有限状态—动作对的估计 $Q(x,a)$，以及贪心策略 $f^Q(x)\in\arg\max_a Q(x,a)$。
- **目标**：在满足覆盖与学习率条件时，使估计逼近 $Q^*$，从而得到最优策略。

### 评价准则

论文关心三件事：

1. 能否仅凭短期局部经验学习延迟回报；
2. 行为可自由探索时，价值估计是否仍指向最优策略；
3. 学习规则能否被解释为动态规划，并给出收敛论证。

第 3 点的论证只覆盖有限状态与动作、逐状态—动作存储价值的设置；对一般函数逼近没有同样保证。

---

## 核心方法

### 直觉：用一次真实转移近似一次 Bellman 备份

若环境模型已知，Bellman 最优方程要对所有下一状态及奖励求期望。Q-learning 每次只看一条真实转移，用

$$r_t+\gamma\max_a Q_t(x_{t+1},a)$$

作为当前 $(x_t,a_t)$ 的新目标，再把旧估计向这个目标移动一小步。反复访问所有状态—动作对，许多带噪声的局部备份共同逼近模型已知时的 value iteration。

### 论文中的 one-step Q-learning

论文先由 $Q_t$ 定义当前状态价值与贪心策略（ch. 7, p. 95）：

$$U_t^Q(x)=\max_a Q_t(x,a),\qquad f_t^Q(x)\in\arg\max_a Q_t(x,a).$$

随后给出更新式（ch. 7, p. 96）：

$$Q_{t+1}(x_t,a_t)=(1-\alpha_t)Q_t(x_t,a_t)+\alpha_t\left[r_t+\gamma U_t^Q(x_{t+1})\right],$$

其他状态—动作对保持不变。这里 $\alpha_t$ 是小的正学习因子。

### 行为策略与目标策略的分离

论文明确说，算法没有规定行动者必须采取什么动作；它可以按任意方式行动。为确保最终找到最优动作价值，每个状态中的每个动作都必须被尝试很多次（p. 96），在附录的极限论证里进一步要求每个状态—动作对有无限多条观测（p. 227）。

这构成后来所谓 **off-policy** 性质的核心：采样动作由行为过程决定，更新目标始终使用下一状态的最大动作价值。1989 年正文没有把“off-policy”作为主标签，但结构已经清楚存在。

### 论文中的方法家族

第 7 章还讨论了使用多步回报的 Q-learning 家族、价值—策略共同学习、action-gradient learning、随机策略技巧与分层控制。Watkins 只对 one-step Q-learning 给出了附录中的收敛论证；本章总结明确承认，其他方法未找到收敛条件（pp. 112–113）。因此不能把整章所有算法都写成“已证明收敛的 Q-learning”。

---

## 关键公式推导

### 公式 1：从最优回报到单步目标

**论文基础**：未来折扣回报可写为

$$G_t=r_t+\gamma r_{t+1}+\gamma^2r_{t+2}+\cdots.$$

**补充推导（假设有限 MDP、$0\leq\gamma<1$、奖励有界）**：

Step 1：把首个奖励拆出：

$$G_t=r_t+\gamma G_{t+1}.$$

依据：折扣回报定义。

Step 2：若首个动作固定为 $a$，后续采取最优动作，则

$$Q^*(x,a)=\mathbb E\!\left[r_t+\gamma\max_{a'}Q^*(x_{t+1},a')\mid x_t=x,a_t=a\right].$$

依据：Bellman 最优性原理。

Step 3：模型未知时，用一次实际观测构造随机目标

$$Y_t=r_t+\gamma\max_{a'}Q_t(x_{t+1},a').$$

在当前估计接近真实值且样本来自相应转移分布时，$Y_t$ 是 Bellman 目标的一次带噪声观测。

Step 4：以随机逼近形式向目标移动：

$$Q_{t+1}(x_t,a_t)=Q_t(x_t,a_t)+\alpha_t\bigl(Y_t-Q_t(x_t,a_t)\bigr).$$

展开后正是论文 p. 96 的 $(1-\alpha)Q+\alpha Y$ 形式。

**直觉**：每条经验只把一个表格单元向“即时奖励 + 下一状态目前最好的估计”推近一点；最大化负责策略改进，抽样负责替代环境模型。

### 公式 2：TD 误差为何同时承担评价与改进

定义

$$\delta_t=r_t+\gamma\max_{a'}Q_t(x_{t+1},a')-Q_t(x_t,a_t).$$

则被访问单元的更新为 $Q_{t+1}(x_t,a_t)=Q_t(x_t,a_t)+\alpha_t\delta_t$，其他单元保持不变。

- $\delta_t>0$：结果比当前估计更好，提高该动作价值；
- $\delta_t<0$：结果更差，降低该动作价值；
- 下一状态取 $\max$：即使行为动作含探索，学习目标仍朝向贪心策略。

**补充分析**：这一“同一估计既选最大项、又评价最大项”的结构会产生正向最大化偏差。论文 p. 177 已谨慎指出，用估计的最大值估计真实最大值在统计上可疑；2010 年 van Hasselt 的 Double Q-learning 才系统分析并用双估计器缓解该偏差。[后续资料：NeurIPS 2010](https://proceedings.neurips.cc/paper/2010/hash/091d584fced301b442654dd8c23b3fc9-Abstract.html)

### 公式 3：1989 年附录的 action-replay 证明骨架

附录把第 $n$ 条观测加入一个纯证明用的 **action-replay process（ARP）**：ARP 有按观测编号排列的状态层 $\langle x,k\rangle$。执行动作时，过程向后寻找相同 $(x,a)$ 的历史观测，以相应学习率为概率“重放”该观测，并跳到更早的一层；若没有观测可重放，则以初始值 $Q_0(x,a)$ 终止（pp. 220–224）。

证明分三步：

1. **构造 ARP**：每次动作都跳向更低层，因此过程有限终止。
2. **Action-Replay Theorem**：用归纳法证明，处理前 $n$ 条经验所得的 $Q_n(x,a)$，恰等于 ARP 第 $n$ 层的最优动作价值（pp. 225–227）。归纳步中的两条分支——以 $\alpha_n$ 重放新观测、以 $1-\alpha_n$ 沿用旧层——正好复现 Q-learning 更新。
3. **令 ARP 逼近真实过程**：原印刷版 p. 227 要求每个 $(x,a)$ 被无限观测，相关学习率为正、单调趋零且总和发散；原 p. 228 的结尾只把逼近概率写到“任意接近 1”。

**Corrigenda 的替换内容**：前置勘误要求，$x_n,a_n,\alpha_n$ 可依赖此前观测，而 $r_n,y_n$ 在给定 $(x_n,a_n)$ 后从只由该状态—动作对决定的联合分布抽样，并与其他观测条件独立；每个状态—动作对的奖励具有有限均值和方差；相应学习率子序列单调趋零且总和发散。替换后的 p. 228 用回放权重与强大数定律，明确声称 ARP 的期望奖励和转移概率以概率 1 一致收敛到真实过程。扫描本因此包含一份 **声称 almost-sure convergence 的勘误版论证**。

**版本张力与 1992 年定理**：当前扫描本没有给出 Corrigenda 日期；Watkins 的后续自述仍说 Dayan 指出其论文证明草图没有证明概率 1 收敛，1992 年论文也把自身定位为“基于 1989 年提纲的详细证明”。稳妥结论是：1989 原印刷页未完成概率 1 论证，当前扫描本的勘误已补入该声称，而可明确日期并被后续文献采用的完整定理来自 1992 年。该定理要求有限状态与动作的 lookup table、$0<\gamma<1$、有界奖励、每个状态—动作对无限访问、$0<\alpha<1$，并对每个状态—动作对满足 $\sum_i\alpha_i=\infty$ 与 $\sum_i\alpha_i^2<\infty$，从而得到 $Q_n(x,a)\to Q^*(x,a)$ almost surely。[后续资料：Watkins & Dayan, 1992 作者版 PDF](https://www.gatsby.ucl.ac.uk/~dayan/papers/cjch.pdf)

---

## 实验分析

### 实验性质

论文没有现代意义上的数据集、统一基线和显著性检验。第 11 章明确称为“两组演示”，目标是证明简单问题上性能会改善，并展示增量动态规划的定性特征（p. 147）。第一组使用 action-gradient learning；第二组才直接演示 Q-learning。二者必须分开叙述。

### 演示一：连续平面中的寻路与障碍适应

- 状态空间是边长 2、以原点为中心的二维方形；动作是二维位移向量。
- 价值函数与策略用 CMAC 近似；初始价值和动作均为零，探索通过给策略动作叠加随机偏移完成（pp. 148–153）。
- 目标位于右上角。作者展示 3、10、100、1000 次成功后的策略与价值曲面。
- 约 100 次成功后，价值“山峰”已向大部分状态空间扩散，并形成由经常走过的路径造成的山脊；策略箭头逐渐朝这些路径汇聚（pp. 166–167）。
- 1000 次成功后加入一个会让位移缩短十倍但成本不变的矩形障碍。再经历 500 次成功时价值已降低但策略尚未充分绕开；5000 次成功后，大部分状态的策略会绕过或跳过障碍（pp. 167–168）。

**解读**：这组演示使用 action-gradient learning，说明局部价值传播如何塑造路线习惯，也显示适应速度受访问分布控制；它不能直接验证 one-step Q-learning 的表格收敛定理。

### 演示二：Skinner box 类比下的 Q-learning

行动者每步选择“啄”或“不啄”；啄有小成本，食物奖励由 fixed interval（FI）、variable interval（VI）、classical fixed/variable interval（CFI/CVI）或 fixed ratio（FR）日程触发（pp. 173–175）。主观状态用“距上次奖励的时间”与“自上次奖励后的啄击量”两个压缩变量表示，并用两个 CMAC 近似两种动作的 $Q$（pp. 175–177）。

**设置**（pp. 179–183）：

- 默认 $\gamma=0.95$，奖励 10；多数日程的啄击成本为 0.5，FR 为 0.25；
- 每组参数运行 50 个随机种子；
- 每次运行学习 $20T$ 个 trial，默认 $T=100$ 即 2000 个学习 trial；
- 学习后关闭探索与更新，记录 20 个测试 trial；
- 汇总平均累计啄击数，并画价值函数与两动作价值差的等高线。

**主要结果**（pp. 208–214）：

- 默认参数在五种日程上都发生适应，但没有一种完全达到最优。
- 把学习期从一步增至三步（$M=3$）普遍改善表现，尤其 FR 多数试验学会连续啄到奖励，VI 也接近其周期性最优策略；作者解释为多步回报让奖励更快向前传播。
- VI 的主观状态缺少“距上次啄击的时间”，理论上不足以表达最优策略；在默认 one-step、默认参数条件下，学得策略约比最优低 25%。当学习期增至三步时，VI 表现已近似最优。
- CVI 中奖励与啄击无关，最优策略是不啄；学习者仍会保留啄击。作者把它解释为初期偶然相关形成的“迷信”：恰好在奖励前出现的动作会被重复，已形成的价值山脊消退很慢。

### 实验设计评价

**优点**：

- 报告了随机种子数、训练/测试分离、主要参数与状态表示；
- 主动展示失败和无法解释的现象，例如在 Plot 2 的无状态转移噪声条件下，FI/CFI 的 50 次运行均在第一步啄击，作者直言无法解释（p. 208）；
- 清楚区分近最优表现与因果理解，并用偶然相关解释“迷信”路径。

**不足**：

- 任务为作者构造的演示，缺少独立基线与系统消融；
- 第一组使用 action-gradient learning；第二组使用 CMAC 和特殊探索/占用率机制，难以把效果只归因于核心更新式；
- 状态表征在 VI 上已知不充分，故实验同时测量算法与表征失配；
- 结果以平均曲线为主，没有方差区间或统计检验；
- 附录定理针对有限表格 MDP，CMAC 实验超出严格证明范围。

---

## 局限性

### 作者自述

1. **探索没有通用解**：除非每个状态的每个动作都被尝试，primitive learning 无法保证最优；局部探索策略一般不能消除“稳定在次优习惯”的问题（ch. 7, pp. 107–113）。
2. **多数变体无证明**：第 7 章只有 one-step Q-learning 得到附录论证；其余方法的稳定条件未知（p. 112）。
3. **参数需自适应**：学习率太小浪费经验，太大会不稳定，智能体应学会自己选择参数（ch. 12, p. 216）。
4. **表示方法粗糙**：实验只使用 CMAC；作者把更好的函数表示与 connectionist mapping 列为后续问题（p. 216）。
5. **单一 MDP 过于简单**：动物的一生不能被当作一个 MDP；作者提出把多个决策过程连接为层级（p. 216）。

### 后续资料确认的边界

- **证明边界**：原印刷版 p. 227–228、扫描本前置 Corrigenda 与 1992 年论文是三层不同证据。Corrigenda 已写入强大数定律和概率 1 声称；1992 年 Watkins–Dayan 给出条件更完整、日期清楚的正式定理。
- **最大化偏差**：论文自己在 p. 177 预见了“取估计最大值”的统计偏差；Double Q-learning 后来专门处理这一问题。
- **函数逼近**：表格收敛保证不能直接搬到非线性函数逼近。DQN 的成功还依赖 replay memory、target network、卷积表示等稳定化设计，不能写成“把 Q 表换成神经网络”这么简单。
- **样本效率与覆盖**：无限访问条件是渐近保证，不给有限样本效率；大状态空间中“遍历所有状态—动作对”本身可能不可行。

---

## 后续影响

### 直接后继

1. **Watkins & Dayan (1992), “Q-learning”**：给出详细的概率 1 收敛定理，并澄清算法条件。[Springer DOI](https://doi.org/10.1007/BF00992698)
2. **Sutton & Barto 的强化学习体系**：Q-learning成为 off-policy TD control 的标准方法，与 SARSA 等 on-policy 方法形成清晰对照。
3. **van Hasselt (2010), Double Q-learning**：针对最大化偏差，引入两个价值估计器。[NeurIPS 论文页](https://proceedings.neurips.cc/paper/2010/hash/091d584fced301b442654dd8c23b3fc9-Abstract.html)
4. **Mnih et al. (2015), DQN**：把 Q-learning 与深度卷积网络、经验回放和目标网络结合，在 49 个 Atari 2600 游戏上从像素与分数学习控制策略。[Nature 518, 529–533](https://www.nature.com/articles/nature14236)

### 开创的方向

Q-learning把“学习环境模型后再规划”之外的一条路线形式化得格外清楚：直接估计最优动作价值，用真实转移完成异步 Bellman 备份。它成为 model-free、off-policy control 的基准构件，并推动了探索、函数逼近、离线数据学习、过估计修正与深度强化学习等后续研究。

### 引用统计

- ResearchGate 条目显示 7,017 次引用（查询日期：2026-08-08）。该数值是平台动态统计，不等同于 Google Scholar，也不与其他数据库硬对齐。[ResearchGate 条目](https://www.researchgate.net/publication/33784417_Learning_From_Delayed_Rewards)
- 本次未获得可稳定复核的 Google Scholar 数值，故不报告 Google Scholar 引用数。

---

## 个人笔记

最让我停下来的，是 p. 177 的一句自我警惕。Watkins 在用 $\max Q$ 构造状态价值时，直接承认“用估计值的最大值去估计最大值”在统计上值得怀疑；紧接着才说附录证明支持这一学习过程在受限条件下收敛。二十一年后，Double Q-learning 把这句旁注发展成完整方法。

这让我重新理解这篇论文的分量。耐久的工作往往同时留下两样东西：一个足够简洁、能够传播的机制，以及它最先会在哪里失真的线索。Q-learning 的影响来自前者，后续研究的入口藏在后者。

第二个印象来自第 11 章的“迷信”。在奖励与啄击无关的 CVI 日程中，初期偶然出现在奖励之前的动作会抬高相应价值，形成难以消退的路径习惯。算法没有因果模型，只能从试错中慢慢拆开偶然相关。这一失败案例比一条平滑收敛曲线更有解释力：信用分配解决了“奖励怎样向前传”，却没有自动识别哪些因素对奖励具有因果作用。

尚存疑问：当前扫描本未标明 Corrigenda 的加入日期，因而无法只凭文件确定勘误与 Dayan 指出证明缺口的先后顺序。本报告并列呈现原印刷页、勘误、作者后续自述与 1992 年论文，没有替作者补写新的随机逼近证明。

---

## 小红书写作备忘

### Hook 素材

1. 1989 年的一篇心理学博士论文，把动物在未知环境中的试错写成增量动态规划。
2. 教材级公式出现同页，作者随即要求每个状态中的每个动作都要被反复尝试。
3. 作者在 p. 177 预先指出 $\max$ 的统计偏差；后来的 Double Q-learning 正从这里继续。

### 核心 Insight（一句话）

Q-learning 用一条真实转移完成一次带噪声的 Bellman 最优备份，使行动者无需环境模型，也能在探索经验中直接逼近最优动作价值。

### 自查重点

1. 收敛史按原印刷页、前置 Corrigenda、作者后续自述与 1992 年正式定理四层证据表达。
2. 第 11 章第一组归类为 action-gradient learning，第二组归类为 Q-learning 奖励日程演示。
3. 收敛保证限定于有限、表格化、充分访问的设置；CMAC 与深度网络超出该定理范围。
4. “off-policy”标为后来的标准归类，正文只说明论文中已有的结构。
5. DQN 的后续机制同时列出经验回放、目标网络与卷积表示。

### 动态 Hashtags

#强化学习 #Qlearning #马尔可夫决策过程

---

## 主要来源

1. Watkins, C. J. C. H. (1989). *Learning from Delayed Rewards*. PhD thesis, King's College, University of Cambridge. [Cambridge Apollo](https://doi.org/10.17863/CAM.132164)
2. Watkins, C. “Reinforcement Learning: some history.” [作者自述](https://www.cs.rhul.ac.uk/home/chrisw/RL_some_history.html)
3. Watkins, C. J. C. H., & Dayan, P. (1992). “Q-learning.” *Machine Learning*, 8, 279–292. [作者版 PDF](https://www.gatsby.ucl.ac.uk/~dayan/papers/cjch.pdf)；[DOI](https://doi.org/10.1007/BF00992698)
4. van Hasselt, H. (2010). “Double Q-learning.” *NeurIPS 23*. [Proceedings](https://proceedings.neurips.cc/paper/2010/hash/091d584fced301b442654dd8c23b3fc9-Abstract.html)
5. Mnih, V. et al. (2015). “Human-level control through deep reinforcement learning.” *Nature*, 518, 529–533. [DOI](https://doi.org/10.1038/nature14236)
