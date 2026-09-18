# 《Scaling Laws for Neural Language Models》精读报告

## 元信息

- 标题：*Scaling Laws for Neural Language Models*
- 作者：Jared Kaplan、Sam McCandlish、Tom Henighan、Tom B. Brown、Benjamin Chess、Rewon Child、Scott Gray、Alec Radford、Jeffrey Wu、Dario Amodei
- 发表：arXiv:2001.08361 v1，2020-01-23，未在所读版本中标明会议或期刊发表
- 原文：[arXiv 论文页](https://arxiv.org/abs/2001.08361)；[arXiv PDF](https://arxiv.org/pdf/2001.08361)
- 精读日期：2026-09-18
- 对应小红书期号：#62

### 原文验证

arXiv v1 PDF 返回 HTTP 200、application/pdf、Content-Length 2,493,004 字节且与本地大小相符；PDF 1.5、共 30 页（正文 19 页，随后为附录、图表索引和参考文献），SHA-256 为 a41bd7877fd1a6bcbba096b2619618bd2f90e02488f2365644903eb2f7c6a494。正文抽取 138,597 字节，30 页均成功渲染并核查；特别回看了 PDF p.5 Equation (1.5)、p.11 Figure 9 / Table 2、p.16 Figure 14、p.17 Figure 15 及附录 C。arXiv 元数据的“19 pages, 15 figures”描述正文篇幅；本地 PDF 含补充部分，不据此误判文件不全。以下使用论文印刷页码、节号和公式号；PDF 页序因封面/目录可能偏移。

## 作者背景

首页脚注把 Jared Kaplan 与 Sam McCandlish 标为 equal contribution，并明确两人领导研究。Tom Henighan 负责 LSTM 实验；Tom B. Brown、Rewon Child、Scott Gray、Alec Radford 开发优化的 Transformer 实现；Jeffrey Wu、Benjamin Chess、Alec Radford 开发文本数据集；Dario Amodei 提供项目指导。首页把 Kaplan 列为 Johns Hopkins University、OpenAI，其余九人列为 OpenAI。这里只复述论文自己的机构与贡献声明，不从“项目指导”推断博士师承或组织职级，也不补写未经一手资料确认的后续履历。

## 历史语境

### 从“更大是否更好”到可检验的资源分配

GPT-2 等自回归语言模型已显示规模与生成能力的相关性；计算、数据与参数却常同步变化，使人难以分辨哪一个因素限制测试损失。Hestness 等（2017）调查不同任务的数据/模型规模关系；McCandlish 等（2018）研究临界批量及梯度噪声；Rosenfeld 等（2019）讨论模型和数据共同变化。本文 §7 引用这些前驱，并指出先前不同实验设置可得不同数据扩展关系。论文的贡献是对 decoder-only Transformer 的非 embedding 参数量 $N$、数据 tokens $D$、训练计算量 $C$ 做较系统的控制、拟合联合公式，再推算固定计算预算下的分配。

### 为什么这不是物理定律

作者在 §8 将拟合关系类比宏观定律，是对经验可预测性的比喻；附录 C 直接说缺乏对这些指数的扎实理论理解。图 1 中的直线来自限定模型族、WebText2 语料、tokenizer、训练计划与测试交叉熵。论文自己在 §6.3 推出大尺度自相矛盾的两条外推曲线，说明至少一处幂律必在更大范围失效。报告将参数拟合、作者推论、后来的修订分开。

## 问题形式化

模型做自回归 next-token 预测，主要指标为 1024-token context 上逐 token 平均交叉熵 $L$，单位 nats；数值越低越好。$N$ 定义为**不含词表与位置 embedding** 的参数数，$D$ 是训练集的独立 token 数，$B$ 是一次更新使用的 token 数，$S$ 为更新步数，训练计算近似 $C\approx6NBS$ FLOPs（§1.3、§2.1）。$C_{\min}$ 则是为达到某个损失而以足够小、低于临界批量的 batch size 训练时估计的最小非 embedding 计算量；不能直接把 $C$ 与 $C_{\min}$ 当相同实测值。计算单位 PF-days，一 PF-day 为 $8.64\times10^{19}$ FLOPs。

问题分三层：单独增大 $N$、$D$ 或最优分配计算量时，测试损失怎样变化；两个约束同时存在时是否仍可预测；给定训练计算量，应该用多大的模型、批量、步数及数据。评价对象是测试 loss，不是对下游任务能力的直接测量。

## 核心方法与实验设计

§2：训练数据是 WebText2，扩展了 GPT-2 的 WebText：约 20.3M 文档、22.9B BPE tokens，其中约 0.66B 留作测试。词表 50,257；主要模型是 decoder-only Transformer，context length 1024，另训 LSTM 与 recurrent Transformer 用于比较。通常使用 Adam，超过 1B 参数的最大模型因内存改用 Adafactor；默认 512 条序列 × 1024 tokens、训练 250,000 steps，3,000-step warmup 后 cosine decay。实验也单独改变模型形状、数据子集、batch 和训练步数。主结论不能自动推广到任意训练 recipe。

Table 1 与 Equation (2.1) 用标准层维度近似 $N\approx12n_{\rm layer}d_{\rm model}^{2}$，不计 embedding 与较小项；Equation (2.2) 与反向成本给出每训练 token 约 $6N$ FLOPs。Figure 6 显示若把词表 embedding 也加进横轴，尤其小模型的尺度关系更散，因此作者选非 embedding 参数作为 $N$。§3.1、Figure 5 的“形状影响弱”限于试验中的宽深比例与可训练范围。

## 关键公式推导

### 1. 单变量幂律：斜率的实际含义

**原文依据：** Equations (1.1)–(1.3)，Figure 1。充足数据、近收敛时，作者拟合

$$
L(N)=\left(\frac{N_c}{N}\right)^{\alpha_N},\quad
L(D)=\left(\frac{D_c}{D}\right)^{\alpha_D},\quad
L(C_{\min})=\left(\frac{C_c}{C_{\min}}\right)^{\alpha_C}.
$$

其中 $\alpha_N\approx0.076$、$\alpha_D\approx0.095$、$\alpha_C\approx0.050$；$N_c\approx8.8\times10^{13}$、$D_c\approx5.4\times10^{13}$，计算拟合常数 $C_c\approx3.1\times10^{8}$ PF-days。**补充推导：** 固定其他约束不成为瓶颈，$N$ 乘以 $r$ 时 $L(rN)/L(N)=r^{-\alpha_N}$；如 $r=2$，比值约 $2^{-0.076}=0.949$。这表示**交叉熵损失的相对变化约 5%**，不能改写为准确率提高 5%。这些比例受适用范围、tokenizer 与损失基准影响；作者 §1.2 明说尺度常数无独立物理意义。

### 2. 联合模型与数据：极限、过拟合和补充推导

**原文依据：** Equation (1.5)/(4.1)、Figure 4、Figure 9、Table 2。作者的联合拟合形式为

$$
L(N,D)=\left[\left(\frac{N_c}{N}\right)^{\alpha_N/\alpha_D}
             +\frac{D_c}{D}\right]^{\alpha_D}.
$$

**补充推导：** 令 $D\to\infty$，第二项趋零，得到 $(N_c/N)^{\alpha_N}$；令 $N\to\infty$，得 $(D_c/D)^{\alpha_D}$，所以联合式与两个单边界相容。令 $A=(N_c/N)^{\alpha_N/\alpha_D}$，大 $D$ 时二项式展开 $L=A^{\alpha_D}[1+\alpha_D D_c/(AD)+O(D^{-2})]$，首个有限数据修正按 $1/D$ 缩小。固定数据瓶颈与模型瓶颈的相对比例，需要 $D\propto N^{\alpha_N/\alpha_D}$。联合 Table 2 的重新拟合参数是 $\alpha_N=0.076,\alpha_D=0.103,N_c=6.4\times10^{13},D_c=1.8\times10^{13}$，指数比约 **0.74**；它与上面的单变量 $\alpha_D=0.095$ 不是同一组拟合，不能交叉代入后声称严格等于 0.74。Figure 9 最小数据集约 $2\times10^7$ tokens 的点偏离明显，作者也特别标出这一失败。

### 3. 计算分配指数为什么重模型

**原文依据：** Equations (1.6)–(1.8)、§6.1、Figure 14。论文拟合有限步学习曲线 $L(N,S_{\min})=(N_c/N)^{\alpha_N}+(S_c/S_{\min})^{\alpha_S}$，其中 $\alpha_S\approx0.76$；临界批量随目标损失变化。**补充推导（采用作者的幂律模型与近似 $C_{\min}\propto NBS$）：** 固定目标损失缩放时，各项资源弹性相加，可得计算最优损失指数

$$
\alpha_C^{\min}=\left(\frac1{\alpha_N}+\frac1{\alpha_B}
                         +\frac1{\alpha_S}\right)^{-1},
\qquad
p_N=\frac{\alpha_C^{\min}}{\alpha_N}.
$$

直接代入文中显示的 $\alpha_N=0.076,\alpha_B=0.21,\alpha_S=0.76$，算得 $\alpha_C^{\min}\approx0.052$、$p_N\approx0.68$。原文 §6.2 的 Equations (6.4)–(6.5) 却分别写约 **0.054、0.71**；附录 Equation (B.7) 写约 **0.052**。这组印刷数值有内部差异，不能把 0.054、0.71 当成由上述显示参数精确代入的结果。Figure 14 的经验最优模型尺寸拟合约 $N_{\rm opt}\propto C_{\min}^{0.73}$；文中相应 $B\propto C_{\min}^{0.24}$、$S\propto C_{\min}^{0.03}$、处理的数据量约 $D\propto C_{\min}^{0.27}$（§1.2、§6.1）。由此得“在该模型和损失定义下固定训练计算应优先加大模型并较早停止”的建议；它是特定拟合的最优解，不是所有时代、数据分布和推理成本下的通则。

## 实验分析

### 可见的证据

Figure 1 分别展示计算、数据和非 embedding 参数横轴上的近似直线；作者明确要求另两类资源不能先成为瓶颈。Figure 4 左与 Figure 9 对不同 $N,D$ 的提前停止测试 loss 做联合比较，展示固定小 $D$ 时大模型收益趋缓。Figure 5–6 检查模型形状与参数统计口径；Figure 7 的 LSTM 对比说明长 context 后段有体系差异，故“形状不重要”也不适用于随意跨架构。Figure 8 显示在 Books、Common Crawl、Wikipedia 等其他文本分布上，模型增大时测试 loss 同向改善，但这些评估样本仍来自相近的文本任务，不能外推到非文本能力。

Figure 10 / §5 估算临界批量；Figure 11–14 再用训练曲线与 batch 校正推最优计算分配。作者提醒早期学习曲线有 transient 区间，Figure 12 的极大模型区域不可无条件相信。论文 Figure 14 左的约 0.73 是用 $C_{\min}$ 轴的经验拟合；不要把它当成任意固定 batch 实验中的指数。它用 loss 而非下游任务评价“最佳”。

### 数字的口径

本文量的是模型数 $N$（排除 embedding）、训练集 tokens $D$、非 embedding 训练 FLOPs、每 token nats。约 22.9B tokens 是 WebText2 总量，训练与测试已分开；“数据需求增长缓慢”指该论文最优训练中**处理**的 tokens 随计算增长约 $C^{0.27}$，并非所有扩大模型的任务只需极少独立数据。Figure 3 是将拟合指数用于十亿倍计算增长的**示意**，不是实测千亿倍训练。对模型质量、吞吐、推理成本、能力涌现均不能用这张示意图直接作证。

## 局限性

### 作者自述

§6.3、Figure 15 给出最重要的内部警示：为避免过拟合，联合模型要求 $D\propto N^{0.74}\propto C_{\min}^{0.54}$，而按他们的最优训练轨迹、即使不重复数据，实际可处理的独立 tokens 只约随 $C_{\min}^{0.26}$ 增长。两条损失预测在远处相交；作者明确说幂律必须在交点之前某处失效。Equation (6.8) 的约 $10^{12}$ 参数等交点数高度依赖微小指数误差，不能当可靠的技术时间表或自然语言熵测量。

附录 C 再列出：无扎实理论解释；临界批量 $B_{\rm crit}(L)$ 的区间外预测不确定；极小数据集拟合差；未系统测试正则化/数据增强；$C\approx6NBS$ 忽略长 context 项；未穷尽学习率、初始化等超参，短跑可能使用不同最佳学习率。§8 明问 loss 的平滑改善能否对应具体任务能力改善，以及规律能否泛化到图像、音频、视频。这些是作者自述的不确定性，不应删去。

### 后续资料与我的判断

后来的 [Hoffmann 等，2022，《Training Compute-Optimal Large Language Models》](https://arxiv.org/abs/2203.15556) 在另一组大规模实验中报告固定训练计算下模型参数与训练 tokens 宜近似同比增长，给出不同于本文偏重模型规模的最优分配。它是**后续论文的实验结论**，不能倒写成 Kaplan 等人的结果，也不意味着 2020 年观测的受控拟合曲线在自己的范围内“全错”。在我看来，这个差异恰好说明数据质量、实验规模、训练计划和“最优”所计成本如何决定资源配置建议。

## 后续影响

本文把“规模与测试 loss 的关系”整理成可拟合、可外推、也可被新数据检验的方程，使讨论模型大小、数据量与训练计算时能明确指标和约束。Hoffmann 等 2022 对 compute-optimal 路径重新估计，是这一研究脉络中的关键修正。更广的应用影响不能从本文 loss 曲线单独推断。本文未取得可核查的同日引用数快照，因此不填未核实引用量；后续论述只引用具体一手论文。

## 个人笔记

我原先最容易记住 Figure 1 的三条直线；真正让我停下的是 Figure 15。作者把自己前面两组看似顺滑的公式放到同一张图，主动展示它们在远处相撞。这样读回 Equation (1.5) 和 Table 2，指数不再像自然常数，而像某个数据区间中暂时稳定的测量。尤其 $\alpha_D$ 从单变量的 0.095 到联合拟合的 0.103，微小差异经过长距离外推便会放大。本文可贵之处既在拟合，也在写出何处必须重新测量。

另一个细节是作者刻意把 embedding 从 $N$ 中剔除。Figure 6 表明横轴定义就能影响规律的清晰度；定义一变，指数与尺度常数就不能直接搬用。我会先把坐标、损失、数据和停止规则抄下来，再讨论“模型该大还是数据该多”。

## 小红书写作备忘

### Hook 素材

- 同一篇论文既给出幂律，也在 Figure 15 展示两条外推曲线的矛盾。
- 2020 年拟合建议固定训练计算时大幅加模型、较早停止；2022 年后续实验重新估计数据的份额。
- Figure 6 提醒：参数是否包含 embedding，会改变横轴与拟合质量。

### 核心 Insight

缩放定律是带着模型、数据、损失、训练规则和适用区间的经验关系；资源最优配比须随新证据重估。

### 自查重点

- $N$ 为非 embedding 参数，$D$ 为 tokens，$L$ 为 nats/token 交叉熵，$C$ 为近似训练 FLOPs。
- 单变量 $\alpha_D=0.095$ 与联合拟合 $\alpha_D=0.103$ 分开；0.73 是 $C_{\min}$ 口径经验指数。
- 不把 2022 后续数据分配结论、Figure 3 示意、Figure 15 高度不确定的交点写成 2020 年已证明的定律。

### 动态 Hashtags

#缩放定律 #语言模型 #计算最优训练

## 来源

- [Kaplan 等，2020，arXiv:2001.08361 v1](https://arxiv.org/abs/2001.08361)：本篇公式、图表、附录 C 与贡献脚注。
- [Hoffmann 等，2022，arXiv:2203.15556](https://arxiv.org/abs/2203.15556)：后续计算最优分配的独立实验证据。
