# 《A Fast Learning Algorithm for Deep Belief Nets》精读报告

## 元信息

- 标题：*A Fast Learning Algorithm for Deep Belief Nets*
- 作者：Geoffrey E. Hinton、Simon Osindero、Yee-Whye Teh
- 发表：*Neural Computation*, 18(7):1527–1554，2006 年 7 月
- DOI：[10.1162/neco.2006.18.7.1527](https://doi.org/10.1162/neco.2006.18.7.1527)
- 官方元数据：[MIT Press](https://direct.mit.edu/neco/article/18/7/1527/7065/A-Fast-Learning-Algorithm-for-Deep-Belief-Nets)
- 可访问全文：[作者公开稿](https://www.cs.toronto.edu/~hinton/absps/fastnc.pdf)
- 精读日期：2026-08-21
- 对应小红书期号：#47

### 原文验证与页码口径

MIT Press 页面确认论文于 2006 年 7 月发表于 *Neural Computation*，卷 18、期 7、正式页码 1527–1554。出版社正文当前需要访问权限；本次精读使用 Geoffrey Hinton 主页公开的作者稿。该 PDF 返回 HTTP 200 与 `application/pdf`，633,269 字节，PDF 1.2，共 16 页，正文提取 83,212 字节。已结合页面核查标题页、图 1–5、式 (1)–(10)、第 6 节训练与测试细节、图 6–9、表 1、第 8 节局限及附录 A。

作者稿是完整可读的 accepted-manuscript 版式，首页仍写有 “To appear in Neural Computation 2006”，页数与出版社 28 个印刷页不一致。因此下文优先使用**章节、公式号、图表号**定位；出现“PDF 第几页”时，专指这份 16 页作者稿，不能直接换算为期刊页码。

## 作者背景

### Geoffrey E. Hinton

- 发表时身份：论文首页列为 University of Toronto Department of Computer Science。论文致谢还写明他当时持有 Canada Research Chair in Machine Learning，并为 Canadian Institute for Advanced Research fellow。
- 学术脉络：Hinton 官方简历记录其 1978 年在 University of Edinburgh 获人工智能博士学位；2004–2013 年主持 CIFAR “Neural Computation and Adaptive Perception” 项目。Boltzmann machine、wake-sleep、products of experts 与 contrastive divergence（CD）构成本文最直接的研究积累。
- 合作关系：Hinton 的官方 CV 将 Simon Osindero 列为 2004 年完成博士的学生，将 Yee-Whye Teh 列为 2003 年完成博士的学生。本文因此也是其 Toronto 学术共同体在能量模型与近似推断方向上的延续。
- 可靠来源：[Hinton 官方简介](https://www.cs.utoronto.ca/~hinton/bio.html)；[Hinton 官方 CV](https://www.cs.toronto.edu/~hinton/fullcv14.pdf)

### Simon Osindero

- 发表时身份：论文首页列为 University of Toronto Department of Computer Science。
- 师承与前置工作：Hinton 官方 CV 记录 Osindero 的博士论文为 *Contrastive Topographic Models: Energy-based density models applied to the understanding of sensory coding and cortical topography*，2004 年完成；Hinton 官方页面也把他列入 former postdocs。本文发表时的具体职务未在论文或检索到的官方资料中明确，本报告不作更精确推断。
- 研究联系：他此前与 Hinton、Teh、Max Welling 合作研究 energy-based models 与稀疏表征；这些工作为本文把 RBM 逐层堆叠成深层生成模型提供了方法背景。
- 可靠来源：[Hinton 官方 CV](https://www.cs.toronto.edu/~hinton/fullcv14.pdf)；[Hinton former postdocs](https://www.cs.toronto.edu/~hinton/postdocs.html)

### Yee-Whye Teh

- 发表时身份：论文首页列为 National University of Singapore Department of Computer Science。其官方 CV 进一步记录，2005 年 8 月至 2006 年 12 月为 NUS Lee Kuan Yew Postdoctoral Fellow。
- 师承：2003 年在 University of Toronto 获计算机科学博士学位，导师为 Geoffrey Hinton；博士论文研究 Bethe free energy 与 contrastive divergence 对无向图模型的近似。
- 后续轨迹：随后在 UCL Gatsby Unit 任 Lecturer/Reader，后任 Oxford Professor of Statistical Machine Learning；其研究延伸到贝叶斯非参数、概率学习和大规模机器学习。
- 可靠来源：[Teh Oxford 官方主页](https://www.stats.ox.ac.uk/people/yee-whye-teh)；[Teh 官方 CV](https://www.stats.ox.ac.uk/~teh/aboutme/cv.pdf)

### 贡献归属边界

原文没有作者贡献声明。师生与合作关系可以由官方履历确认，算法、证明、实验或写作的个人分工不能仅凭作者顺序推定。

## 历史语境

### 深层生成模型为何难学

2006 年前，神经网络已有反向传播，概率模型也已有 Helmholtz machine、wake-sleep 与 Boltzmann machine。困难集中在深层、稠密、含多个隐藏层的**有向信念网络**：给定观测数据后，不同隐藏原因会因共同解释同一结果而产生强相关，这就是 explaining away（解释消除）。隐藏层后验难以分解，精确推断通常不可行；简单 mean-field 近似在高层尤其容易偏离真实后验。（第 1–2 节，PDF pp. 1–3）

与此同时，直接从随机初始化联合训练多层网络，计算和优化都很困难。本文没有把问题简化成“反向传播失效”这一单一命题。作者面对的是生成模型中的后验推断、全局学习成本和深层表示初始化三项耦合困难。

### 四条直接前驱

1. **Wake-sleep** [Hinton et al., 1995]：分别训练生成权重和识别权重，为含潜变量的深层生成模型提供可扩展近似，但 sleep phase 可能出现 mode averaging。
2. **Restricted Boltzmann Machine（RBM）**：二部无向图使给定一层时另一层条件独立，便于并行 Gibbs 更新。
3. **Contrastive Divergence（CD）** [Hinton, 2002]：用从数据出发的短 Markov chain 近似最大似然中的模型期望，训练 RBM 更快；它同时放弃了精确最大似然保证。
4. **Variational free energy** [Neal & Hinton, 1998]：把潜变量推断写成边际对数似然下界，为“逐层训练为何能改善整体模型”提供证明工具。

### 本文选择的切口

作者把一个 RBM 解释成“权重反复共享的无限深有向网络”，再利用 complementary prior（互补先验）抵消 likelihood 引起的隐藏变量相关性。这样，第一层可以用容易训练的 RBM 学好；把其后验活动当作下一层的新数据，再训练第二个 RBM。每加一层，数据都会被重新表示，而非在原空间反复拟合。（第 2–4 节，图 3–5）

## 问题形式化

### 输入、模型与目标

给定二值或归一化观测向量 $\mathbf v^{(0)}$，构造一个深层生成模型：顶端两层以无向连接形成 associative memory，下面各层以有向生成连接向下产生观测。希望同时满足：

- 对数据赋予较高边际概率 $p(\mathbf v^{(0)})$；
- 以快速的自底向上识别过程近似 $p(\mathbf h\mid\mathbf v)$；
- 逐层增加隐藏层时，整体生成模型具有可解释的改进条件；
- 训练后能生成样本，并在标签与图像联合模型上完成分类。

### Logistic belief net

对随机二值单元 $s_i$，给定直接父节点状态 $s_j$：

$$
p(s_i=1)=\sigma\!\left(b_i+\sum_j s_jw_{ij}\right)
=\frac{1}{1+\exp[-b_i-\sum_j s_jw_{ij}]}.
$$

这是原文式 (1)。若只有一层隐藏变量且先验独立，观测会在后验中把共同原因耦合起来。互补先验的目的，是提供方向相反的相关结构，使乘积后的后验重新因子化。

### RBM

可见层 $\mathbf v$ 与隐藏层 $\mathbf h$ 构成二部无向图。补充写出其常见能量形式：

$$
E(\mathbf v,\mathbf h)=-\mathbf a^\top\mathbf v-\mathbf b^\top\mathbf h-\mathbf v^\top W\mathbf h,
\qquad
p(\mathbf v,\mathbf h)=\frac{e^{-E(\mathbf v,\mathbf h)}}{Z}.
$$

因为同层无连接，条件分布可以并行计算：

$$
p(h_j=1\mid\mathbf v)=\sigma\!\left(b_j+\sum_i v_iw_{ij}\right),\quad
p(v_i=1\mid\mathbf h)=\sigma\!\left(a_i+\sum_j h_jw_{ij}\right).
$$

这两式属于依据原文结构作出的**补充展开**，与式 (1)、图 4 的交替 Gibbs 过程一致。

## 核心方法

### 互补先验如何消除 explaining away

附录 A 定义：对 likelihood $P(x\mid y)$，若某先验 $P(y)$ 使后验严格分解为 $P(y\mid x)=\prod_jP(y_j\mid x)$，它就是 complementary prior。作者在分布严格为正的假设下，借 Hammersley–Clifford 定理刻画了可具有此类先验的 likelihood 家族（式 11–17）。

图 3 的无限权重共享网络给出直观构造：相邻层轮流使用 $W$ 与 $W^\top$。把交替条件采样沿深度方向展开，等价于 RBM 中随时间进行的交替 Gibbs sampling。给定底层数据后，逐层向上采样得到真实因子化后验；从很深一层向下生成则得到同一平稳分布。（第 2.1 节、附录 A）

这一等价关系有两个边界：链必须能混合到平稳分布；附录证明还依赖严格正概率。它是针对特定模型族的精确构造，不能直接推广为任意深层生成网络的通用后验分解。

### CD 学习一层 RBM

最大似然梯度可写成数据相位与模型相位相关性的差：

$$
\frac{\partial\log p(\mathbf v)}{\partial w_{ij}}
=\langle v_ih_j\rangle_{\text{data}}
-\langle v_ih_j\rangle_{\text{model}}.
$$

精确模型期望需要 Markov chain 充分混合。CD-$k$ 从数据初始化，运行 $k$ 个完整 Gibbs step 后，用短链相关性替代平衡相关性。原文式 (6) 将 CD 目标联系到两项 KL divergence 的差：

$$
\mathrm{KL}(P^0\Vert P_\theta^\infty)
-\mathrm{KL}(P_\theta^k\Vert P_\theta^\infty).
$$

短链带来速度，也使更新不再等于精确似然梯度。原文第 4 节明确承认：以 CD 代替 full maximum-likelihood Boltzmann-machine learning 会使随后逐层改进的理论保证失效。

### 贪心逐层训练

图 5 对应的流程可以写成：

1. 假定所有更高层权重都与底层 $W_0$ 共享，训练底层 RBM。
2. 固定 $W_0$，并承诺用 $W_0^\top$ 对数据推断第一隐藏层的因子化表示 $Q(h^{(0)}\mid v^{(0)})$。
3. 把这些隐藏活动视为新数据，训练更高层 RBM；其余更高权重继续共享。
4. 对每层递归重复。最终保留顶端 RBM，下面的权重解开并作为有向生成连接。

与 boosting 的类比只在“顺序训练弱模型”层面成立：boosting 重加权样本，本文每一层重新表示样本。（第 4 节，PDF p. 5）

### Up-down 微调

逐层训练给出初始化，却不会让早期层适应后来学到的高层结构。第 5 节因此解开 recognition weights 与 generative weights：

- **up-pass**：自底向上按识别权重采样隐藏状态；更新向下生成权重，顶端 RBM 做正相位统计。
- **top-level CD**：在顶端 associative memory 中执行若干轮 Gibbs sampling，得到负相位。
- **down-pass**：从短链终点沿生成连接向下采样；用生成样本训练自底向上识别权重。

作者称其为 contrastive wake-sleep。它让识别网络在接近真实数据后验的区域学习，缓解纯 sleep phase 的 mode averaging；完整 MATLAB 风格伪代码见附录 B。

## 关键公式推导

### 公式一：变分下界与后验误差

**原文定位：** 第 4 节式 (7)–(9)，PDF pp. 5–6。

对观测 $v$ 与第一隐藏层 $h$，引入任意近似后验 $Q(h\mid v)$：

$$
\begin{aligned}
\log p(v)
&=\log\sum_h Q(h\mid v)\frac{p(v,h)}{Q(h\mid v)}\\
&\ge \sum_h Q(h\mid v)
\log\frac{p(h)p(v\mid h)}{Q(h\mid v)}\\
&\equiv \mathcal L(Q,p).
\end{aligned}
$$

第一步乘除 $Q$，第二步依据 Jensen 不等式。继续补全原文未逐行写出的恒等式：

$$
\log p(v)-\mathcal L(Q,p)
=\mathrm{KL}\!\left(Q(h\mid v)\Vert p(h\mid v)\right)\ge0.
$$

所以 $Q$ 恰为真实后验时下界取等号。直觉上，下界同时奖励模型解释数据、惩罚近似后验偏离真实后验。

### 公式二：逐层改进保证的证明骨架

**原文定位：** 第 4 节式 (8)–(9)，脚注 4。

训练第一层 RBM、所有更高权重仍共享时，互补先验使自底向上的 $Q_0(h\mid v)$ 等于真实后验。因此：

$$
\mathcal L_0(Q_0,p_0)=\log p_0(v).
$$

随后固定 $Q_0(h\mid v)$ 与底层 $p(v\mid h)$，只优化更高层定义的 prior $p(h)$。下界中随高层变化的部分为原文式 (9)：

$$
\sum_hQ_0(h\mid v)\log p(h).
$$

若用精确最大似然把它从 $\mathcal L_0$ 提高到 $\mathcal L_1$，则：

$$
\log p_1(v)\ge\mathcal L_1
\ge\mathcal L_0=\log p_0(v).
$$

这就是“增加一层不会降低数据对数概率”的证明骨架。原文脚注说明保证针对期望变化；实际算法使用 CD 近似，作者随即声明该保证被 voided。报告与草稿均不得省略这条现实边界。

### 公式三：无限共享有向网为何得到 RBM 更新

**原文定位：** 第 2.1–3 节式 (2)–(5)，PDF pp. 3–4。

某一层生成权重的梯度含重建残差：

$$
\frac{\partial\log p(v^{(0)})}{\partial w_{ij}^{(0)}}
=\langle h_j^{(0)}(v_i^{(0)}-v_i^{(1)})\rangle.
$$

因权重在无限层复制，总梯度要对所有层求和。相邻层项纵向抵消，留下起始数据相关性与平稳状态相关性的差：

$$
\frac{\partial\log p(v^{(0)})}{\partial w_{ij}}
=\langle v_i^{(0)}h_j^{(0)}\rangle
-\langle v_i^{(\infty)}h_j^{(\infty)}\rangle.
$$

这正是 RBM 的 maximum-likelihood 形式。推导成立依赖无限共享构造与链到达平稳分布；CD 用有限步状态近似第二项。

## 实验分析

### 设置

- 数据：MNIST，60,000 张训练图像、10,000 张官方测试图像。
- 任务：permutation-invariant basic task，不提供像素几何、不作特殊预处理或数据增强。
- 架构：图 1 的 $784\rightarrow500\rightarrow500\leftrightarrow2000\leftrightarrow10$；顶端 500–2000 隐藏单元与 10 个 label units 共同形成 associative memory，约 170 万权重。
- 训练划分：先用 44,000 张训练、10,000 张验证；44,000 张组成 440 个类别平衡 mini-batch。
- 贪心预训练：每层 30 epochs；在 3GHz Xeon 上用 MATLAB 每层数小时，完成后测试错误率 2.49%。
- 微调：up-down 共 300 epochs；每 100 epochs 顶层 Gibbs steps 从 3 增至 6、再增至 10。验证集选出的网络测试错误率 1.39%，再用全部 60,000 张训练 59 epochs，总训练约一周，最终错误率 1.25%。（第 6.1 节，PDF pp. 7–8）

### 主要结果

表 1 在同一 permutation-invariant 口径下列出：

| 方法 | 测试错误率 |
|---|---:|
| 本文生成模型 | 1.25% |
| degree-9 polynomial SVM | 1.40% |
| 784–500–300–10 backprop，cross-entropy + weight decay | 1.51% |
| 784–800–10 backprop，cross-entropy + early stopping | 1.53% |

这些数字支持的结论是：在论文选定的无几何先验、无数据增强设置中，逐层预训练加 up-down 的生成模型达到当时很有竞争力的分类误差。它没有证明生成模型普遍优于判别模型。表 1 同时列出使用卷积结构或形变数据的系统，最低可到 0.4%；这些设置利用了额外几何结构或增强，不能与 1.25% 作无条件排名。

### 生成与解释实验

图 8 在固定类别标签后，每隔 1000 次 Gibbs updates 采样，展示每类生成图像；图 9 从随机二值图像出发，每 20 次更新向下生成一次，展示顶层自由能“沟谷”内状态逐渐变化。它们说明模型能联合建模图像与标签，并把高层状态投影回像素。图像质量主要由定性样本判断，原文没有 FID、likelihood 对数值或人类评测。

### 实验设计评价

**优点：**

- 清楚区分无几何先验的 basic task 与使用卷积/增强的结果。
- 报告贪心阶段 2.49%、验证选择 1.39%、全量训练 1.25%，呈现训练阶段贡献。
- 图 6–7展示近似错误与全部 125 个错误样例，便于读者检查失败模式。
- 脚注披露超参数选择、训练时长与继续训练六周后的波动范围。

**不足：**

- 没有随机种子、多次独立运行的均值与方差，1.25% 是单个选定网络的点估计。
- 没有逐层预训练、up-down、顶层 Gibbs 次数的系统消融；2.49% 到 1.25% 只能说明完整后续训练相关，无法分离各因素。
- SVM 与 backprop 数字部分来自外部文献或个人通信，训练预算与超参数搜索不完全匹配。
- 最终网络在测试集上长期监控；脚注 9 说明六周内测试错误率在 1.12%–1.31% 波动，并选训练错误最低 epoch 报 1.18% 作为补充观察。这种测试集反复观察不符合今天更严格的盲测习惯。

## 局限性

### 作者自述

第 8 节列出当前模型的具体边界：

1. 非二值图像值必须能解释成概率，天然图像通常不满足。
2. 自上而下反馈只存在于顶端两层 associative memory。
3. 没有系统处理感知不变性。
4. 假定分割已经完成。
5. 不会学习按顺序关注最有信息的局部。
6. fine-tuning 很慢；作者尚未系统探索形变数据增强。

作者还直言，尚不清楚“贪心预训练后再做慢速微调”是否是最佳用法；也许用其速度训练更大、更深的网络或 ensemble 更合适。（第 8 节，PDF p. 10）

### 理论保证的适用条件

- 逐层单调改进保证要求每个 RBM 用充分耐心进行 exact maximum-likelihood learning；实际 CD 训练取消该保证。
- 为方便保证，论证先考虑等宽层，使高层可由既有权重初始化；作者称不同宽度仍可应用算法，但该便利证明条件不再原样成立。
- 互补先验构造依赖特定 exponential-family 形式、正概率与 Markov chain 混合。
- 逐层解开权重后，低层先验不再互补，自底向上转置权重也不再给出真实后验，只是后续可训练的近似识别模型。

### 后来视角

这篇论文恢复了训练深层网络的可行路径，却没有给出现代深度学习的统一优化原理。后来大规模监督数据、ReLU、卷积、GPU、残差连接、归一化和更成熟的优化器，使许多判别网络可以端到端训练；DBN 的具体训练配方不再是主流默认。其历史影响更集中在“层级表示可以被有效初始化并逐步组织”以及深度学习研究重新获得可复现实验支点。

## 后续影响

### 直接后继

- Hinton & Salakhutdinov (2006), *Reducing the Dimensionality of Data with Neural Networks*：用逐层预训练初始化深层自编码器，再以反向传播微调。
- Bengio et al. (2007), *Greedy Layer-Wise Training of Deep Networks*：系统研究逐层贪心训练及其优化作用。
- Salakhutdinov & Hinton (2009), *Deep Boltzmann Machines*：把多层无向生成模型与逐层初始化继续推进。
- Hinton et al. (2012), *Deep Neural Networks for Acoustic Modeling in Speech Recognition*：展示深层网络在大规模语音识别中的工程转折；方法已从本文具体 DBN 结构继续演化。
- Krizhevsky, Sutskever & Hinton (2012), *ImageNet Classification with Deep Convolutional Neural Networks*：以卷积、ReLU、GPU 与监督训练取得视觉突破；它与本文共享“深层表示”的研究谱系，训练机制不同。

### 引用统计

OpenAlex work [W2136922672](https://openalex.org/W2136922672) 于 2026-08-21 返回 `cited_by_count = 16,492`。这是 OpenAlex 根据可匹配参考文献构建的聚合计数，不等同于 Google Scholar；数据库会继续更新，数字必须与来源和查询日同时使用。

### 历史评价边界

本文常被视为 2000 年代深度学习复兴的重要节点。更准确的表述是：它把 RBM、互补先验、变分下界与逐层训练组合成一条可运行路线，并在 MNIST 上给出引人注目的生成与分类结果。深度学习此后的扩张还依赖数据、计算硬件、卷积结构、非线性与优化方法等多条并行进展。

## 个人笔记

最值得反复读的是第 4 节的一处诚实转折。作者先用“下界起点恰好取等、之后只优化高层 prior”证明逐层加深不会降低数据概率；紧接着又说明实际为了速度使用 CD，这会使保证失效。理论没有被当作算法效果的装饰，它明确告诉读者：理想化过程为何合理，工程近似在哪一步越过了定理边界。

第二个触点来自图 5。“加一层”在这里不是把更多参数同时塞进同一个优化问题。第一层先把原始数据变成一种新表示，下一层学习这个表示的分布。深度因此对应一串逐次改写的数据空间。这个视角也解释了文中与 boosting 的细微对照：前者重写样本权重，本文重写样本表示。

最后是第 8 节的限制清单。作者没有把 MNIST 结果直接推广到自然图像，反而逐项列出连续像素解释、不变性、分割、注意与反馈范围。这种写法让经典论文的价值更清晰：它给出一条当时能走通的路径，也把路的边界画在同一张地图上。

## 小红书写作备忘

### Hook 素材

1. 一份 16 页作者稿把“深层网络难以联合训练”改写成“一层一层改变数据表示”。
2. 论文给出逐层加深的似然改进证明，随后明确承认实际采用 CD 会取消严格保证。
3. 贪心预训练后错误率为 2.49%，完整 up-down 训练达到 1.25%；同一实验也耗时约一周。

### 核心 Insight（一句话）

把顶层 RBM 看作无限共享权重的有向网络后，每一层都能先用简单的无向模型学习，再把后验活动变成下一层的数据，从而为深层生成模型提供可计算的初始化。

### 自查重点

- DBN 的顶端两层是无向 associative memory，下面连接是向下的有向生成连接；不能把整网写成普通前馈网络。
- “逐层保证不降 likelihood”属于 exact maximum-likelihood 理想条件；实际 CD 使保证失效。
- 1.25% 只对应 permutation-invariant MNIST 的特定设置；卷积与增强系统使用额外结构。
- 作者公开 PDF 为 accepted manuscript；正式卷期页码来自 MIT Press，报告引用优先用节、式、图表号。
- “深度学习复兴节点”是后续历史评价；论文自身实验证据局限于 MNIST 联合生成与分类。
- OpenAlex 16,492 条引用须保留 2026-08-21 查询日与数据库口径。

### 动态 Hashtags

#深度置信网络 #RBM #无监督预训练 #生成模型 #Paper观止

## 来源与证据分层

### 原文、作者与后继资料

1. Hinton, Osindero, Teh (2006), *A Fast Learning Algorithm for Deep Belief Nets*. [MIT Press](https://direct.mit.edu/neco/article/18/7/1527/7065/A-Fast-Learning-Algorithm-for-Deep-Belief-Nets)；[作者公开稿](https://www.cs.toronto.edu/~hinton/absps/fastnc.pdf)
2. Geoffrey Hinton, official biography and CV. [简介](https://www.cs.utoronto.ca/~hinton/bio.html)；[CV](https://www.cs.toronto.edu/~hinton/fullcv14.pdf)
3. Yee-Whye Teh, Oxford profile and CV. [主页](https://www.stats.ox.ac.uk/people/yee-whye-teh)；[CV](https://www.stats.ox.ac.uk/~teh/aboutme/cv.pdf)
4. Hinton & Salakhutdinov (2006), *Reducing the Dimensionality of Data with Neural Networks*. [Science](https://doi.org/10.1126/science.1127647)
5. Bengio et al. (2007), *Greedy Layer-Wise Training of Deep Networks*. [NeurIPS](https://proceedings.neurips.cc/paper/2006/hash/5da713a690c067105aeb2fae32403405-Abstract.html)
6. Salakhutdinov & Hinton (2009), *Deep Boltzmann Machines*. [PMLR](https://proceedings.mlr.press/v5/salakhutdinov09a.html)
7. OpenAlex work W2136922672. [记录](https://openalex.org/W2136922672)

### 证据标记

- **论文事实**：来自作者稿的章节、公式、图、表与脚注，并以 MIT Press 元数据校正正式出版信息。
- **后续资料**：作者履历、后继模型和引用统计单列来源，不反向充当 2006 年实验结论。
- **补充推导**：RBM 能量式、ELBO 与 KL 恒等式、逐层保证不等式链补全了原文省略的中间步骤，沿用原文假设。
- **个人分析**：对“表示被逐层重写”、定理与 CD 工程近似关系的评价均置于个人笔记，不冒充作者自述。
