# 《Latent Dirichlet Allocation》精读报告

## 元信息

- 标题：*Latent Dirichlet Allocation*
- 作者：David M. Blei、Andrew Y. Ng、Michael I. Jordan
- 发表：*Journal of Machine Learning Research*, 3:993–1022，2003 年 1 月
- 投稿：2002 年 2 月；发表：2003 年 1 月
- 编辑：John Lafferty
- 官方论文页：[JMLR](https://jmlr.org/papers/v3/blei03a.html)
- 官方 PDF：[JMLR PDF](https://www.jmlr.org/papers/volume3/blei03a/blei03a.pdf)
- 精读日期：2026-08-14
- 对应小红书期号：#46

### 原文验证

JMLR 官方 PDF 为 30 页、417,996 字节，PDF 1.1；正文可完整提取。已逐页核查标题与作者机构、图 1 概率图模型、图 6 变分推断算法、图 9 文档建模 perplexity、图 10 分类、图 11 协同过滤及第八节讨论。本报告页码采用论文印刷页码 993–1022，并同时给出章节、公式和图表号。

## 作者背景

### David M. Blei

- 发表时身份：论文首页列其为 UC Berkeley Computer Science Division 成员。Michael I. Jordan 的官方 CV 将 Blei 列为 1999–2004 年指导的研究生；Blei 的官方 CV 也将 Jordan 列为博士导师。因此，本文发表时 Blei 正处于 Berkeley 博士阶段。
- 合作位置：Blei 是本文第一作者。论文没有作者贡献声明，本报告不进一步推断三位作者的具体分工。
- 后续轨迹：Blei 后来任 Columbia University 统计学与计算机科学教授，长期研究概率主题模型、贝叶斯推断与因果推断。其后续工作包括动态主题模型、相关主题模型和随机变分推断。
- 可靠来源：[Blei 官方主页](https://www.cs.columbia.edu/~blei/)；[Blei 官方 CV](https://www.cs.columbia.edu/~blei/blei_cv.pdf)

### Andrew Y. Ng

- 发表时身份：论文首页列其机构为 Stanford University Computer Science Department。
- 师承与合作：Michael I. Jordan 的官方 CV 将 Ng 列入 1997–2003 年研究生指导名单。Ng 的 Stanford CV 记录其 UC Berkeley 博士背景以及与 Jordan 的多项联合研究。
- 后续轨迹：其后在 Stanford 从事机器学习与机器人研究，并先后参与创立 Google Brain、Coursera 等。这里仅作为作者后续轨迹，不用于解释本文实验结果。
- 可靠来源：[Ng Stanford CV](https://ai.stanford.edu/~ang/curriculum-vitae.pdf)；[Ng 官方主页](https://www.andrewng.org/)

### Michael I. Jordan

- 发表时身份：论文首页列其为 UC Berkeley Computer Science Division 与 Department of Statistics 成员。官方履历显示其 1998 年起任 Berkeley 教授。
- 学术关系：Jordan 是 Blei 与 Ng 的博士导师。其研究把统计学、概率图模型、优化和机器学习相连接，为本文的层次贝叶斯建模与变分推断提供直接学术语境。
- 后续轨迹：后任 Berkeley EECS 与统计学 Pehong Chen Distinguished Professor，2024 年起为荣休教授，并在 Inria/ENS 任职。
- 可靠来源：[Jordan 官方主页](https://people.eecs.berkeley.edu/~jordan/)；[Jordan 官方 CV](https://people.eecs.berkeley.edu/~jordan/jordan-cv.pdf)

### 合作关系边界

论文首页显示 Blei、Jordan 位于 Berkeley，Ng 位于 Stanford；Jordan 官方 CV 确认他指导过两位年轻作者。除此之外，原文没有贡献声明，不能从作者顺序推断算法、实验或写作的个人归属。

## 历史语境

### 从词频向低维语义表示

2003 年前，文本集合的经典表示大致形成三层谱系：

1. **tf–idf** 把每篇文档压成高维实向量，保留词频辨识度，却较少揭示词间或文档内的统计结构。
2. **Latent Semantic Indexing（LSI）** 用奇异值分解寻找低维线性子空间，能够压缩文档表示，但缺少完整的生成概率语义。
3. **probabilistic LSI（pLSI）** [Hofmann, 1999] 让每个词由文档特定的主题混合生成，赋予潜在维度概率解释；它成为 LDA 最直接的比较对象。

### pLSI 留下的两处缺口

第四章 §4.3 指出，pLSI 对每个训练文档都显式估计一组 $p(z\mid d)$。这带来两项问题：

- 文档索引 $d$ 只是训练集的索引；模型没有自然的生成机制为全新文档分配概率，实践中常用“folding in”重新拟合新文档主题比例。
- $K$ 主题、词表 $V$、训练文档数 $M$ 时，pLSI 约有 $KV+KM$ 个参数，随训练文档数线性增长，容易过拟合。

LDA 的关键改动，是把每篇文档的主题比例从自由参数改为随机变量：$\theta_d$ 由全局 Dirichlet 分布抽样。模型参数约为 $K+KV$，不随文档数增长；新文档也能从同一个先验中获得主题比例的后验。

### 概率图模型与近似推断

这一改动把主题表示纳入层次贝叶斯模型，也引入新的计算问题：给定文档后，主题比例与逐词主题的精确后验不可解。论文因此把**模型设计**与**近似推断**同时置于核心位置，以 mean-field 变分分布、坐标更新与 variational EM 建立可运行算法。

### 直接前驱

- Deerwester et al. (1990)：LSI，以线性代数实现潜在语义降维。
- Hofmann (1999)：pLSI/aspect model，允许一篇文档混合多个主题。
- de Finetti 交换性表示：把“次序置换不改变联合分布”联系到给定随机参数后的条件独立采样。
- Jordan et al. (1999)：概率图模型中的变分方法，为本文的下界优化提供通用工具。

## 问题形式化

### 数据与目标

设词表大小为 $V$，语料包含 $M$ 篇文档。文档 $d$ 有 $N_d$ 个词，写作 $\mathbf w_d=(w_{d1},\ldots,w_{dN_d})$；每个 $w_{dn}$ 是词表中的 one-hot 向量。给定主题数 $K$，希望学习：

- 每个主题的词分布 $\beta_i$；
- 控制文档主题比例分布的 Dirichlet 参数 $\alpha$；
- 对每篇文档推断主题比例 $\theta_d$ 和每个词的主题 $z_{dn}$；
- 对未见文档赋予概率，并得到固定维度表示以服务分类、检索或推荐。

### 三层生成过程

第三章给出每篇文档的生成过程：

1. $N_d\sim\mathrm{Poisson}(\xi)$；论文随后的推导把文档长度视为独立的辅助变量，通常忽略其随机性。
2. $\theta_d\sim\mathrm{Dirichlet}(\alpha)$。
3. 对每个位置 $n$：
   - $z_{dn}\sim\mathrm{Multinomial}(\theta_d)$；
   - $w_{dn}\sim\mathrm{Multinomial}(\beta_{z_{dn}})$。

其中 $\beta$ 是 $K\times V$ 矩阵，$\beta_{ij}=p(w^j=1\mid z^i=1)$。图 1 的外层 plate 复制文档，内层 plate 复制词位置；$\alpha,\beta$ 是语料级参数，$\theta_d$ 是文档级变量，$z_{dn},w_{dn}$ 是词级变量。

### “主题”的证据边界

脚注 1 明确说，作者把潜在 multinomial 变量称为 topic，是为了利用文本直觉；除其表示词集合概率分布的效用外，不作认识论声明。因此，模型输出首先是共现统计中的潜在成分。“主题名称”来自研究者阅读高概率词后的解释，不是模型自己证明的语义实体。

### 交换性假设

LDA 假定一篇文档中的主题变量无限可交换，词在给定主题后生成。交换性意味着联合分布对位置置换不变；它不等同于边缘独立，而是通过文档级随机比例 $\theta_d$ 形成条件独立结构。对文本而言，这就是 bag-of-words 的核心简化。

## 核心方法

### 文档是主题的随机混合

混合 unigram 模型让整篇文档只落在一个主题角点；pLSI 让每个训练文档占据主题单纯形上的一个自由点；LDA 则在单纯形上放置由 $\mathrm{Dirichlet}(\alpha)$ 控制的平滑分布，每篇新旧文档都从中抽取一个点。图 4 用几何方式展示了这三者的区别。

这使一篇文档可以同时包含多个主题，也让文档之间通过同一个先验共享统计强度。$\alpha_i<1$ 时，Dirichlet 密度可偏向单纯形边界，产生较稀疏的主题比例；但本文并未把所有实验都限定在对称或小于 1 的先验，不能把“LDA 必然稀疏”写成无条件结论。

### 精确后验不可解

给定词序列后，需要计算

$$
p(\theta,\mathbf z\mid\mathbf w,\alpha,\beta)
=\frac{p(\theta,\mathbf z,\mathbf w\mid\alpha,\beta)}{p(\mathbf w\mid\alpha,\beta)}.
$$

分母要求对 $\theta$ 积分、对所有逐词主题配置求和；$\theta$ 与 $\beta$ 在主题求和中耦合，通常没有可 tractable 的精确形式。论文选择凸性变分近似，也把 Laplace、Monte Carlo 与更高阶变分方法列为替代路线。

### Mean-field 变分族

图 5 删除原图中造成耦合的边，以自由参数构造

$$
q(\theta,\mathbf z\mid\gamma,\phi)
=q(\theta\mid\gamma)\prod_{n=1}^{N}q(z_n\mid\phi_n),
$$

其中 $q(\theta\mid\gamma)$ 是 Dirichlet，$q(z_n\mid\phi_n)$ 是 multinomial。$\gamma$ 可作为文档级低维表示，$\phi_{ni}$ 则近似第 $n$ 个词属于主题 $i$ 的后验概率。

### 坐标更新

附录 A.3 对变分目标求导，得到式 (6)–(8)：

$$
\phi_{ni}\propto \beta_{i,w_n}
\exp\left\{\Psi(\gamma_i)-\Psi\left(\sum_{j=1}^{K}\gamma_j\right)\right\},
$$

$$
\gamma_i=\alpha_i+\sum_{n=1}^{N}\phi_{ni}.
$$

图 6 先令 $\phi_{ni}=1/K$、$\gamma_i=\alpha_i+N/K$，再交替更新并归一化 $\phi$、重算 $\gamma$，直至收敛。每次迭代约为 $O(NK)$；论文称经验上迭代数与 $N$ 同阶，因此单文档总运算量粗略为 $O(N^2K)$。这是该实现的书中经验复杂度说明，不是所有后续 LDA 推断器的固定复杂度。

### Variational EM 学习全局参数

论文以 variational EM 最大化边际对数似然的下界：

1. **E-step**：对每篇文档迭代求 $\gamma_d^*,\phi_d^*$；
2. **M-step**：固定变分后验，用期望充分统计量更新 $\alpha,\beta$。

主题—词参数的式 (9) 是期望计数归一化：

$$
\beta_{ij}\propto\sum_{d=1}^{M}\sum_{n=1}^{N_d}
\phi_{dni}^* w_{dn}^{j}.
$$

$\alpha$ 用利用 Hessian 特殊结构的 Newton–Raphson 更新。§5.4 还为每个 $\beta_i$ 放置对称 Dirichlet 先验 $\eta$，以变分后验 $\lambda_{ij}=\eta+\text{期望计数}$ 平滑未见词。

## 关键公式推导

### 公式一：从生成过程得到文档边际概率

**原文定位：** 式 (2)、(3)，pp. 996–997。

给定一篇文档的主题比例、逐词主题与词，联合分布按图 1 分解：

$$
p(\theta,\mathbf z,\mathbf w\mid\alpha,\beta)
=p(\theta\mid\alpha)\prod_{n=1}^{N}
p(z_n\mid\theta)p(w_n\mid z_n,\beta).
$$

主题比例和逐词主题都不可见。先对每个 $z_n$ 求和，再对 $\theta$ 积分：

$$
p(\mathbf w\mid\alpha,\beta)=
\int p(\theta\mid\alpha)
\prod_{n=1}^{N}\left[
\sum_{z_n}p(z_n\mid\theta)p(w_n\mid z_n,\beta)
\right]d\theta.
$$

**依据：** 概率图的条件独立分解、离散潜变量边缘化、连续潜变量积分。

**直觉：** 观测到的一篇文档，其概率是“所有可能主题比例”与“所有可能逐词主题分配”共同解释它的总权重。LDA 为新文档赋概率的能力来自这个生成式边缘化，而非为每个训练文档记一张参数表。

### 公式二：变分下界为何等价于反向 KL 最小化

**原文定位：** 式 (5) 与附录 A.3，pp. 1003–1004、1018–1019。

对任意变分分布 $q(\theta,\mathbf z)$，在边际似然中乘除 $q$：

$$
\log p(\mathbf w)=
\log\int\sum_{\mathbf z}q(\theta,\mathbf z)
\frac{p(\theta,\mathbf z,\mathbf w)}{q(\theta,\mathbf z)}d\theta.
$$

由 Jensen 不等式：

$$
\log p(\mathbf w)\ge
\mathbb E_q[\log p(\theta,\mathbf z,\mathbf w)]
-\mathbb E_q[\log q(\theta,\mathbf z)]
\equiv\mathcal L(q).
$$

再用 Bayes 公式展开 KL：

$$
\begin{aligned}
\mathrm{KL}\bigl(q\,\|\,p(\theta,\mathbf z\mid\mathbf w)\bigr)
&=\mathbb E_q[\log q-\log p(\theta,\mathbf z\mid\mathbf w)]\\
&=\log p(\mathbf w)-\mathcal L(q).
\end{aligned}
$$

所以固定模型时，最大化 ELBO $\mathcal L(q)$ 等价于最小化论文式 (5) 的 $\mathrm{KL}(q\|p)$。只要变分族无法包含真实后验，差距仍为正；算法优化的是下界，不是精确似然本身。

### 公式三：两条坐标更新从何而来

**原文定位：** 式 (6)、(7)、(8)，附录 A.3。

Mean-field 坐标最优解满足

$$
\log q_j^*(x_j)=\mathbb E_{q_{-j}}[\log p(\mathbf x,\mathbf w)]+C.
$$

对 $z_n$，只保留含 $z_n$ 的两项：

$$
\log q^*(z_n=i)=
\mathbb E_q[\log\theta_i]+\log\beta_{i,w_n}+C,
$$

指数化便得到 $\phi_{ni}$ 更新。Dirichlet 下

$$
\mathbb E_q[\log\theta_i]
=\Psi(\gamma_i)-\Psi\left(\sum_j\gamma_j\right).
$$

对 $\theta$，先验与所有主题指示变量给出

$$
\log q^*(\theta)=
\sum_i\left(\alpha_i-1+\sum_n\phi_{ni}\right)\log\theta_i+C.
$$

这正是 Dirichlet 的对数密度，因此

$$
\gamma_i=\alpha_i+\sum_n\phi_{ni}.
$$

**直觉：** $\phi$ 同时看“这个词在主题里常不常见”和“这篇文档目前偏向哪些主题”；$\gamma$ 则等于先验伪计数加上所有词对该主题的软计数。两者互相依赖，所以必须迭代。

## 实验分析

### 初始化与共同设置

第七章指出混合模型的期望完全对数似然有局部极大点，某些成分可能重合。实验用每个条件 multinomial 由五篇文档播种、把有效总长度缩至两个词并在全词表平滑的办法初始化。各隐变量模型使用相同 EM 停止条件：期望对数似然平均变化低于 0.001%。这个细节说明结果依赖初始化，论文没有声称找到全局最优。

### 例示：100 个主题怎样解释一篇 AP 新闻

第六章用 16,000 篇 TREC AP 文档拟合 100-topic LDA。图 8 展示“Arts”“Budgets”“Children”“Education”等主题的高概率词，并对一篇未参与估计的文章推断 $\gamma$ 与 $\phi_n$：$\gamma_i-\alpha_i$ 近似该文档分配给主题 $i$ 的期望词数，$\phi_n$ 则给出逐词软分配。

这一例子也直接暴露缺陷：短语 “William Randolph Hearst Foundation” 中本应关联的词可被分到不同主题。作者将原因归于 bag-of-words，建议以部分交换性或词序 Markov 结构扩展。

### 文档建模：held-out perplexity

- 数据一：C. Elegans 科学摘要 5,225 篇，28,414 个 unique terms。
- 数据二：TREC AP 新闻 16,333 篇，23,075 个 unique terms。
- 划分：两者均用 90% 训练、10% 测试；各去除 50 个停用词，AP 另去除仅出现一次的词。
- 基线：smoothed unigram、smoothed mixture of unigrams、采用 folding-in 的 pLSI。
- 指标：测试集 per-word likelihood 的几何平均的倒数，即 perplexity，越低越好。

图 9 显示，在考察的主题数范围内，LDA 在两套语料上都持续优于这些基线。表 1 则展示未校正的 mixture of unigrams 与 pLSI 随主题数增加出现极端 perplexity；正式图中已用平滑或 folding-in 修正。作者特别说明，pLSI 在测试文档上重新拟合 $K-1$ 个参数，实际上获得了不利于 LDA 的额外自由度。

论文脚注 3 同时限定了指标解释：这些模型都是 unigram/bag-of-words 模型，perplexity 只用作比较密度估计；作者没有声称在做需要 trigram 等顺序模型的完整语言建模。

### 文档分类：极大压缩后的特征

- Reuters-21578 子集：8,000 篇文档，15,818 个词。
- 任务：EARN vs. NOT EARN、GRAIN vs. NOT GRAIN 两个二分类。
- 做法：先在所有文档上无监督拟合 50-topic LDA，再以文档后验 Dirichlet 参数 $\gamma^*(w)$ 训练 SVM；基线 SVM 使用全部词特征。
- 压缩：特征维数减少 99.6%。

图 10 显示，LDA 特征的准确率损失很小，多数训练比例下反而略有提高。作者的原话语气谨慎：这些结果仍需进一步证实，只表明主题表示可能适合作为快速特征筛选。不能把图 10 写成 LDA 普遍优于词特征的定理。

### 协同过滤：文档—词类比之外

- EachMovie 中，用户对应文档，用户正向评分的电影对应词。
- 仅保留至少正向评分 100 部电影的用户；正向定义为 5 星中至少 4 星。
- 3,300 名训练用户、390 名测试用户，电影词表大小 1,600。
- 测试时观察新用户除一部以外的所有喜欢电影，评价模型给被留出电影的概率，指标为 predictive perplexity。

图 11 中，LDA 获得最低 predictive perplexity。它说明 LDA 的集合生成结构可移植到非文本离散数据；它没有证明主题一定具有可读语义，也没有覆盖显式评分值、冷启动侧信息或在线推荐指标。

### 实验设计评价

**优点：** 三组任务分别检验生成概率、低维表示和跨域预测；新文档推断正面对应 pLSI 缺口；数据规模、划分、词表、初始化和停止条件记录较完整；结论文字保留“需要进一步证实”等限定。

**不足：** 文档建模只有两套英文 bag-of-words 语料；主题语义主要靠高概率词的人工观看，没有主题稳定性、一致性或人工评价；主题数由外部枚举，未解决选择 $K$ 的一般方法；实验没有现代意义的多随机种子统计与运行成本比较；分类在全体无标签文档上先拟合 LDA，适用到严格 inductive 设置时需要重新说明数据可见边界。

## 局限性

### 作者自述

- **词序被丢弃：** 完全交换性使短语内部词可被分到不同主题；作者建议部分交换性或 Markov 结构。
- **精确推断不可解：** 本文方法优化可计算下界；更高阶变分、expectation propagation 或 MCMC 可能提高精度。
- **模型结构简单：** 第八节明确把连续观测、Dirichlet 混合、时间结构和条件于段落/句法等外生变量列为扩展方向。
- **主题数固定：** 基本模型假定 $K$ 已知且固定。
- **局部最优：** 混合模型的 EM 需要谨慎初始化，论文没有全局最优保证。

### 后续研究揭示或处理的边界

- 单一 Dirichlet 难以直接表达任意主题相关结构；Correlated Topic Model 用 logistic normal 建模主题相关性。
- 固定 $K$ 可由 Hierarchical Dirichlet Process 等非参数贝叶斯模型放宽。
- 批量 variational EM 对超大语料代价高；Online LDA 与 stochastic variational inference 改为小批量随机优化。
- 无监督主题未必最适合预测标签；supervised LDA 将响应变量纳入联合生成过程。
- 在短文本、强语序任务或大规模预训练语言模型表示下，LDA 的 bag-of-words 假设和词表离散化可能成为主要瓶颈。这是后续工程判断，不是 2003 年实验所验证的结论。

### 可识别性与解释边界（个人分析）

主题编号可任意置换；不同初始化还可能收敛到不同局部模式。因此，高概率词列表只能说明某次拟合中的统计成分。若要把它用于社会科学解释或趋势判断，需要补做种子稳定性、时间/群体敏感性和人工编码一致性检查。

## 后续影响

### 主题模型家族

- Blei & Lafferty 的 **Dynamic Topic Models**（ICML 2006）让主题随时间演化。
- Blei & Lafferty 的 **Correlated Topic Models**（2007）用 logistic normal 表达主题相关。
- Teh et al. 的 **Hierarchical Dirichlet Processes**（JASA 2006）用非参数贝叶斯层次共享决定有效主题数。
- Blei & McAuliffe 的 **supervised LDA**（NeurIPS 2007）联合建模文档与响应变量。
- Hoffman, Blei & Bach 的 **Online Learning for LDA**（NeurIPS 2010）以及 Hoffman et al. 的 **Stochastic Variational Inference**（JMLR 2013）扩展到大规模流式/小批量学习。

这些工作是后续路线，不能冒充原文已完成的消融或扩展实验。

### 方法论影响

LDA 的长期影响不止“自动给文章分主题”。它展示了一个可复用范式：先用层次生成模型说明潜在结构如何产生观测，再以近似后验把不可见变量变成文档表示，最后把同一表示用于密度估计、分类和推荐。概率模块的可组合性也是第八节明确强调的优点。

### 引用统计

OpenAlex 记录 [W1880262756](https://openalex.org/W1880262756) 在 2026-08-14 查询时的 `cited_by_count` 为 **27,066**，并关联 DOI `10.5555/944919.944937`。这是 OpenAlex 的聚合口径，不等同于 Google Scholar、Crossref 或出版商实时计数；本文只保留这一来源与日期明确的数值。

### 后续工作来源

- [Dynamic Topic Models, PMLR](https://proceedings.mlr.press/r5/blei05a.html)
- [Correlated Topic Models, AOAS](https://doi.org/10.1214/07-AOAS114)
- [Hierarchical Dirichlet Processes, JASA](https://doi.org/10.1198/016214506000000302)
- [Supervised Topic Models, NeurIPS](https://proceedings.neurips.cc/paper_files/paper/2007/hash/ca3ec598002d2e7662e2ef4bdd58278b-Abstract.html)
- [Online Learning for LDA, NeurIPS](https://proceedings.neurips.cc/paper/2010/hash/71f6278d140af599e06ad9bf1ba03cb0-Abstract.html)
- [Stochastic Variational Inference, JMLR](https://www.jmlr.org/papers/v14/hoffman13a.html)

## 个人笔记

最让我停下的是图 4 的几何解释。pLSI 把每篇训练文档钉成主题单纯形上的一个点；语料越大，点越多，参数也跟着增长。LDA 在同一个单纯形上放置平滑分布，每篇文档只需从中抽取自己的比例。两幅图的差别很小，却把“能拟合训练文档”和“拥有新文档生成机制”分得极清楚。

第二个触点是 $\gamma_i=\alpha_i+\sum_n\phi_{ni}$。它让抽象的后验参数有了可读含义：先验伪计数加上逐词软计数。图 8 中，作者用 $\gamma_i-\alpha_i$ 找出一篇新闻实际激活的四个主题，再用 $\phi_n$ 回到每个词。文档级概括与词级解释由同一套后验连接，这正是 LDA 的优雅处。

这份优雅也有代价。为了让推断可算，模型丢掉词序，变分族又切断后验依赖。阅读时最值得保留的态度，是同时看见“建模假设带来的生成语义”和“近似假设带来的计算可行性”，并把两层假设分别检验。

## 小红书写作备忘

### Hook 素材

1. pLSI 把每篇训练文档记成主题单纯形上的一个参数点；LDA 改为从一张平滑分布中生成新旧文档。
2. 一篇新闻的表示可以写成“先验伪计数 + 每个词的软主题计数”。
3. 50 个主题把 Reuters 的 15,818 维词特征压缩 99.6%，分类表现却只小幅变化，并在多数设置中略有提高。

### 核心 Insight（一句话）

LDA 用 Dirichlet 先验把每篇文档的主题比例变成可生成、可共享、可对新文档推断的随机变量，再用变分后验把这一结构变成可计算表示。

### 自查重点

- LDA 中一篇文档是多个主题的混合；逐词主题 $z_{dn}$ 会重复采样。
- Dirichlet 先验加在文档主题比例 $\theta_d$ 上；基本论文中 $\beta$ 先作待估参数，§5.4 才给出带 Dirichlet 平滑的 fuller Bayesian 扩展。
- 变分 EM 优化边际似然下界，不是精确后验或全局最优保证。
- 图 9 的比较已给 pLSI folding-in 与混合 unigram 平滑；结论应保留该条件。
- 分类和协同过滤只是本文三组早期实验，不能泛化为所有文本或推荐任务。
- 27,066 是 OpenAlex 于 2026-08-14 的聚合计数，必须保留来源和日期。

### 动态 Hashtags

#主题模型 #概率图模型 #变分推断 #Paper观止

## 来源与证据分层

### 原文与作者资料

1. Blei, Ng, Jordan (2003), *Latent Dirichlet Allocation*, JMLR 3:993–1022. [官方页面](https://jmlr.org/papers/v3/blei03a.html)
2. David Blei official homepage and CV. [主页](https://www.cs.columbia.edu/~blei/)；[CV](https://www.cs.columbia.edu/~blei/blei_cv.pdf)
3. Andrew Ng official homepage and Stanford CV. [主页](https://www.andrewng.org/)；[CV](https://ai.stanford.edu/~ang/curriculum-vitae.pdf)
4. Michael I. Jordan official homepage and CV. [主页](https://people.eecs.berkeley.edu/~jordan/)；[CV](https://people.eecs.berkeley.edu/~jordan/jordan-cv.pdf)
5. OpenAlex work W1880262756. [记录](https://openalex.org/W1880262756)

### 证据标记

- “论文事实”：以原文页码、章节、公式、图表和脚注为依据。
- “后续资料”：作者轨迹、后继模型与引用统计均另列官方或原始论文来源。
- “补充推导”：ELBO 身份与 mean-field 坐标最优式展开了附录省略的中间代数，没有改写目标或假设。
- “个人分析/工程判断”：主题稳定性、现代表示场景和解释风险均显式标记，不冒充 2003 年实验结论。
