# 《Distributed Representations of Words and Phrases and their Compositionality》精读报告

## 元信息

- 标题：*Distributed Representations of Words and Phrases and their Compositionality*
- 通称：Word2Vec / Skip-gram with Negative Sampling（SGNS）；后者只指本文的一种训练目标，不能代表整个 word2vec 工具包
- 作者：Tomas Mikolov、Ilya Sutskever、Kai Chen、Greg S. Corrado、Jeffrey Dean
- 发表：*Advances in Neural Information Processing Systems 26*（NIPS 2013）
- 官方论文页：[NeurIPS Proceedings](https://proceedings.neurips.cc/paper/2013/hash/9aa42b31882ec039965f3c4923ce901b-Abstract.html)
- 官方 PDF：[NeurIPS PDF](https://proceedings.neurips.cc/paper_files/paper/2013/file/9aa42b31882ec039965f3c4923ce901b-Paper.pdf)
- 后续奖项：NeurIPS 2023 Test of Time Award
- 精读日期：2026-08-21
- 对应小红书期号：#49

### 原文验证

NeurIPS 官方 PDF 返回 HTTP 200 与 `application/pdf`，111,997 字节，与服务器 `Content-Length` 一致；PDF 1.3，共 9 页，正文提取 37,787 字节。已结合页面核查标题与作者机构、图 1–2、公式 (1)–(6)、表 1–6、§3 的十亿词实验、§4.1 的 330 亿词短语实验、§5 的加法解释、§7 的结论与作者自述边界。报告页码采用 PDF 页脚 1–9。

需要先澄清版本关系：本文参考文献 [8] 是同年较早的 *Efficient Estimation of Word Representations in Vector Space*，它提出 CBOW 与连续 Skip-gram 两种架构。本文开头也写的是“recently introduced continuous Skip-gram”，贡献是在该架构上加入频词下采样、Negative Sampling、短语发现和组合性实验。把 CBOW、Skip-gram、负采样与短语算法全部说成“本文首次提出”，会抹掉两篇论文之间的分工。

## 作者背景

### Tomas Mikolov

- 发表时身份：论文首页列为 Google Inc.，第一作者。其博士论文 *Statistical Language Models Based on Neural Networks* 被本文参考文献 [7] 引用，显示这项工作来自其持续的神经语言模型研究。
- 可确认贡献边界：本文没有作者贡献声明。第一作者身份、公式与代码脚注能确认其核心参与，不能据此把每项设计排他地归于一人。
- 可靠来源：[NeurIPS 论文](https://proceedings.neurips.cc/paper/2013/hash/9aa42b31882ec039965f3c4923ce901b-Abstract.html)；[Google Research 论文页](https://research.google/pubs/distributed-representations-of-words-and-phrases-and-their-compositionality/)

### Ilya Sutskever

- 发表时身份：论文首页列为 Google Inc.。其个人主页记载，加入 Google Brain 前曾在 Stanford 的 Andrew Ng 团队做博士后，更早在 Toronto 与 Geoffrey Hinton 学习，并参与创办 DNNresearch。
- 学术语境：他在循环网络、序列建模与深度学习上的背景，与本文从局部上下文学习分布式表示的路线相接；这是研究语境，不是论文所列的个人贡献分工。
- 可靠来源：[Sutskever 个人主页](https://www.cs.toronto.edu/~ilya/)

### Kai Chen

- 发表时身份：论文首页列为 Google Inc.。Google Research 官方简介记载其 2004 年获 UIUC 计算机博士，研究与工作覆盖自然语言处理、在线广告和应用机器学习。
- 可靠来源：[Google Research 个人页](https://research.google/people/kaichen/)

### Greg S. Corrado

- 发表时身份：论文首页列为 Google Inc.。Google Research 官方简介把他列为 Google 大规模深度神经网络项目的创始成员与联合技术负责人之一，研究跨神经科学、AI 与可扩展机器学习。
- 可靠来源：[Google Research 个人页](https://research.google/people/gregcorrado/)

### Jeffrey Dean

- 发表时身份：论文首页列为 Google Inc.。Google 官方资料记录他长期负责大规模分布式系统与 AI 研究；这解释了团队能把训练扩到数百亿词的工程环境，但本文没有把具体系统工作逐人拆分。
- 可靠来源：[Google 官方作者页](https://blog.google/authors/jeff-dean/)；[Google Research ACM Fellow 回顾](https://research.google/blog/four-googlers-elected-acm-fellows/)

### 合作关系边界

论文首页与 Google Research 论文页只能确认五位作者当时同属 Google。本文没有作者贡献声明，也没有可核验的师承关系说明；报告不从作者顺序推断算法、工程和写作的精确分工。

## 历史语境

### 分布式表示并非从本文开始

把词映射为稠密向量的思想至少可追溯到神经网络中的分布式表示。Bengio 等人在 2003 年神经概率语言模型中联合学习词向量与语言模型；Collobert、Weston 等又把共享词表示用于多任务 NLP。本文 §1 也明确回溯 Rumelhart、Hinton、Williams 1986，并列出语音识别、机器翻译和情感分析等前序应用。

### 当时真正的计算瓶颈是大词表

标准 softmax 需要对词表中每个词计算归一化项。词表达到 $10^5$–$10^7$ 时，每个中心词—上下文词训练对都做全词表更新，代价不可接受。层次 softmax 把一次输出从 $O(W)$ 降到约 $O(\log W)$，但树结构和路径仍影响速度与表示质量。本文追问的是：如果目标主要是学好向量，能否用更直接的二分类任务替代规范化语言模型？

### 同年两篇论文的分工

同年伴随论文 *Efficient Estimation of Word Representations in Vector Space* 提出 CBOW 与 Skip-gram，并报告在 16 亿词上不到一天的训练。本文承接其中的 Skip-gram：

1. 用 Negative Sampling 取代全 softmax 或层次 softmax；
2. 用频词下采样减少信息量低的训练对；
3. 把高关联多词短语合成单 token；
4. 用类比和向量加法观察线性结构。

“word2vec”后来成为工具与方法家族的总称，精读时应把架构、训练目标和短语预处理分别归位。

## 问题形式化

### 从中心词预测邻近词

给定词序列 $w_1,\ldots,w_T$，中心位置 $t$ 的上下文半径为 $c$。Skip-gram 最大化平均上下文对数概率（原文公式 1，p. 2）：

$$
\frac{1}{T}\sum_{t=1}^{T}\sum_{-c\le j\le c,\,j\ne0}
\log p(w_{t+j}\mid w_t).
$$

窗口越大，一个中心词产生的训练对越多，能覆盖更宽语境，但训练时间也随之增加。论文只说明 $c$ 可以是中心词 $w_t$ 的函数；正文没有进一步规定具体的窗口采样分布。

### 两套向量

标准 softmax 为每个词保留 input vector $v_w$ 与 output vector $v'_w$（公式 2，p. 3）：

$$
p(w_O\mid w_I)=
\frac{\exp({v'_{w_O}}^\top v_{w_I})}
{\sum_{w=1}^{W}\exp({v'_w}^\top v_{w_I})}.
$$

$v_{w_I}$ 表示中心词，$v'_{w_O}$ 表示作为上下文输出的同一词。常见的“一个词一个向量”是训练完成后的使用选择；训练阶段实际有两套参数，不能把二者提前混为同一张表。

## 核心方法

### Hierarchical Softmax：沿树走一条路径

原文 §2.1 用二叉树表示输出层，词位于叶子。到目标词 $w$ 的路径包含 $L(w)-1$ 个内部节点，每一步做一次 sigmoid 二分类（公式 3，p. 3）。使用 Huffman tree 时，高频词路径较短，平均计算约 $O(\log W)$。它仍定义归一化概率分布，但表示质量会受树结构影响。

### Negative Sampling：把预测改成真假配对

对真实中心词—上下文词对 $(w_I,w_O)$，从噪声分布 $P_n$ 抽 $k$ 个负词。公式 (4) 的目标是：提高真实对内积，压低噪声对内积（p. 3）：

$$
\log \sigma({v'_{w_O}}^\top v_{w_I})
+\sum_{i=1}^{k}\mathbb E_{w_i\sim P_n}
\left[\log \sigma(-{v'_{w_i}}^\top v_{w_I})\right].
$$

原文写成期望；一次 SGD 更新实际以 $k$ 个抽样词估计该项。小数据集作者建议 $k=5$–$20$，大数据集可用 $k=2$–$5$。噪声分布经验上取

$$
P_n(w)=\frac{U(w)^{3/4}}{\sum_{u}U(u)^{3/4}},
$$

在作者尝试的任务上优于原始 unigram 与 uniform。$3/4$ 次幂压平词频：相对原 unigram，它降低极高频词占比，同时仍比均匀采样更常抽到高频词。

### NEG 与 NCE 的边界

NCE 通过区分数据与噪声来估计未归一化概率模型，理论目标近似最大化 softmax 对数概率；计算时需要噪声样本及其数值概率。本文明确说，SGNS 只关心表示质量，于是进一步简化：只使用抽到的噪声词，不追求恢复规范化语言模型概率。

因此 SGNS 的分数适合做相似度、类比和下游特征，不应直接解释为校准过的 $p(w_O\mid w_I)$。这是本文主动换取效率的目标边界。

### 频词下采样：少看“the”，多看有效搭配

每次遇到词 $w_i$，按公式 (5) 丢弃（p. 4）：

$$
P_{\mathrm{discard}}(w_i)=1-\sqrt{\frac{t}{f(w_i)}}.
$$

$f(w_i)$ 是词频，$t$ 通常约 $10^{-5}$。频率远高于阈值的词大概率被丢弃；低频词保留。作者明确说该式是 heuristic，不是由最优性定理推出。它同时减少训练对，并避免常见虚词支配每个内容词的上下文。

### 短语发现：先合并，再当作一个 token

本文没有让网络自动解析语法树，而是先按 unigram/bigram counts 给候选二元组打分（公式 6，p. 6）：

$$
\operatorname{score}(w_i,w_j)=
\frac{\operatorname{count}(w_iw_j)-\delta}
{\operatorname{count}(w_i)\operatorname{count}(w_j)}.
$$

$\delta$ 折扣抑制偶然出现的稀有组合；超过阈值的 bigram 合并为一个 token。训练数据通常迭代 2–4 次并逐轮降低阈值，从而形成更长短语。`New_York_Times` 之后与普通词一样进入 Skip-gram。

这能表示 “Air Canada” 这类不可由单词简单相加得到的专名，却不是一般句法组合模型：阈值、计数与语料决定哪些短语进入词表，未登录短语仍无独立向量。

### 向量加法：上下文交集的启发式解释

§5 观察到 `Russia + river` 接近 `Volga River`。作者的解释是：词向量与 softmax 输入呈线性关系，而这些输入与上下文概率的对数相关；两个词向量相加近似对应两组上下文分布相乘，像一个“AND”操作。

这是解释线性结构的直觉，不是本文证明的普遍定理。向量维度、训练目标、负采样分布和语料都会改变几何结构；“能做若干类比”也不等于模型理解了实体、关系或句法。

## 关键公式推导

### 推导一：为什么负采样每步只更新少量词

**原文定位：** 公式 (4)，§2.2；以下梯度为补充推导。

记正样本分数 $s_+= {v'_{w_O}}^\top v_{w_I}$，负样本分数 $s_i={v'_{w_i}}^\top v_{w_I}$。单次采样目标为

$$
J=\log\sigma(s_+)+\sum_{i=1}^{k}\log\sigma(-s_i).
$$

利用 $\frac{d}{dx}\log\sigma(x)=1-\sigma(x)$，得到

$$
\frac{\partial J}{\partial s_+}=1-\sigma(s_+),
\qquad
\frac{\partial J}{\partial s_i}=-\sigma(s_i).
$$

于是

$$
\frac{\partial J}{\partial v_{w_I}}
=(1-\sigma(s_+))v'_{w_O}
-\sum_{i=1}^{k}\sigma(s_i)v'_{w_i}.
$$

每个正对只触及中心词、一个正上下文词和 $k$ 个负词，复杂度约 $O((k+1)d)$，不再随词表 $W$ 线性增长。正对分数很低时更新大；容易的正对与负对梯度自然缩小。

### 推导二：$3/4$ 次幂如何重分配噪声

**原文定位：** §2.2，p. 4；以下比值为补充推导。

若两个词原始概率为 $U(a)>U(b)$，原 unigram 抽样比为 $U(a)/U(b)$；采用 $3/4$ 次幂后变为

$$
\frac{P_n(a)}{P_n(b)}=
\left(\frac{U(a)}{U(b)}\right)^{3/4}.
$$

当原频率相差 $10^4$ 倍，负采样概率只相差 $10^3$ 倍。极高频词仍更常成为“负例”，但中低频词获得更多采样机会。这是平滑效果的数学含义；指数 $3/4$ 本身来自本文实验经验，并无普适最优保证。

### 推导三：类比检索实际比较什么

**原文定位：** §3、§4，pp. 5–6；下式为补充形式化。

对类比 $a:b::c: ?$，查询向量为

$$
q=v_b-v_a+v_c,
$$

候选答案按 cosine similarity 排序：

$$
\hat d=\arg\max_{d\notin\{a,b,c\}}
\frac{q^\top v_d}{\lVert q\rVert\lVert v_d\rVert}.
$$

原文明确从搜索中排除输入词。这个指标要求唯一目标正好是最近邻；同义答案、拼写变体或合理的第二答案都算错。它测量特定线性关系能否在词表中被最近邻恢复，不是通用语言理解分数。

### 推导四：短语得分为何偏爱“共同出现且各自不泛滥”

**原文定位：** 公式 (6)，§4；以下为补充解释。

忽略折扣时，分子是联合计数，分母是两个词边缘计数乘积。若两个词各自常见却并不特别相邻，分母很大而得分低；若相邻次数相对各自频率异常高，得分上升。$\delta$ 再把只出现一两次的偶然组合压低。

它与点互信息都在比较联合与边缘频率，但本文公式不是对数 PMI，也未除以语料总量；阈值依赖计数尺度。报告不把它改名为“PMI 短语检测”。

## 实验分析

### 十亿词受控比较

§3 使用 Google 内部新闻语料，约 10 亿词；出现少于 5 次的词被删去，词表为 692K。模型 300 维，使用同一词类比任务。表 1 的主要结果为：

| 训练方法 | 下采样 | 时间（分钟） | 句法 | 语义 | 总准确率 |
|---|---|---:|---:|---:|---:|
| NEG-5 | 无 | 38 | 63% | 54% | 59% |
| NEG-15 | 无 | 97 | 63% | 58% | 61% |
| HS-Huffman | 无 | 41 | 53% | 40% | 47% |
| NCE-5 | 无 | 38 | 60% | 45% | 53% |
| NEG-5 | $10^{-5}$ | 14 | 61% | 58% | 60% |
| NEG-15 | $10^{-5}$ | 36 | 61% | 61% | 61% |
| HS-Huffman | $10^{-5}$ | 21 | 52% | 59% | 55% |

同一 NEG-5 配置加入下采样，时间从 38 降到 14 分钟，总准确率从 59% 到 60%；语义从 54% 到 58%，句法从 63% 到 61%。这支持“该数据与指标上更快且总分略升”，不能概括为所有子任务都提升。

NEG-15 与 NEG-5 总准确率都可到 61%，但耗时分别 36 与 14 分钟；多负样本主要把语义提高到 61%。最优 $k$ 取决于数据规模和任务，论文结论也明确如此。

### 短语类比：算法与数据规模交织

表 2 的短语类比集有 3,218 个问题，含报纸、球队、航空公司和高管关系。约 10 亿词、300 维、window 5 时（表 3）：

| 方法 | 无下采样 | $10^{-5}$ 下采样 |
|---|---:|---:|
| NEG-5 | 24% | 27% |
| NEG-15 | 27% | 42% |
| HS-Huffman | 19% | 47% |

这里下采样后的 HS 反而最好，说明“NEG 总优于 HS”不是跨任务定律。随后作者改用约 330 亿词、1,000 维、整句上下文与 HS，准确率达到 72%；把数据减到 60 亿词时为 66%。72% 同时改变了数据规模、维度和上下文，不能单独归因于某一个因素。

### 与已发布词向量的比较

表 6 把 1,000 维 Skip-Phrase 与 Collobert、Turian、Mnih 的向量做稀有词最近邻定性比较。本文模型训练约 300 亿词、一天；对照模型只用小两到三个数量级的数据，训练从 7 天到两个月不等。作者自己指出提升部分来自更多数据。

这张表展示“高效架构允许吃下更多数据后能得到更好的稀有词邻居”，不是相同语料、相同维度、相同硬件预算下的纯算法对照；而且只列少量人工挑选样例，没有盲评或统计检验。

### 实验设计评价

**优点：**

- 同时报告训练时间、句法/语义分项与总准确率，能看见速度—质量权衡。
- 对 NEG、NCE、HS、负样本数和下采样作局部对照。
- 明确给出语料规模、词表截断、维度、window 与短语测试集大小。
- 代码和类比数据集在发表时公开，官方 proceedings 还保留 reviews 与 author feedback。

**不足：**

- 新闻语料是 Google 内部数据，无法按论文描述完整复现。
- 没有多随机种子均值、方差或显著性检验。
- 类比集覆盖有限，且要求单一最近邻答案；没有直接评估解析、翻译、检索等下游任务。
- 330 亿词的 72% 同时改变数据、维度与窗口，缺少完整 factorized ablation。
- 表 6 是数据规模不匹配的定性对比。官方 author feedback 也承认，下游 NLP 任务才是词向量质量的最终检验。

## 局限性

### 作者自述

- 摘要直接指出：单词向量忽略词序，不能表示许多 idiomatic phrases。
- §4 说短语识别方法很多，比较这些方法超出本文范围；所用计数算法只是简单选择。
- §7 强调最优训练算法和超参数依任务而定，关键因素包括架构、向量维度、下采样率与窗口大小。
- 加法组合只被描述为 “somewhat meaningfully combined”，作者没有宣称所有语义都服从线性代数。

### 方法边界

- **一词一向量：** 同形异义词共享同一静态向量，无法随句子消歧。
- **顺序丢失：** 固定窗口共现主要编码局部邻近，`dog bites man` 与 `man bites dog` 的区别不能靠词向量相加保留。
- **未登录词：** 低频截断与短语词表使新词、拼写变体和新短语没有独立向量；后续 subword 方法才缓解这一点。
- **语料偏差：** 向量忠实吸收新闻语料的关联，也会吸收社会刻板印象。后续研究在性别类比中系统展示了这一风险；本文没有公平性分析。
- **概率解释：** SGNS 目标不恢复规范化 softmax 概率，内积与 cosine 不能直接当作可信概率。

### 结论范围

本文证明的是：在给定新闻语料和类比指标上，Skip-gram 配合负采样与频词下采样能以很低成本学到有用的静态词/短语向量，并展现可测的线性结构。它没有证明向量运算等于语言理解，也没有证明某组超参数、负采样或短语阈值对所有语言与任务最优。

## 后续影响

### 从预测模型到矩阵分解解释

Levy 与 Goldberg（NeurIPS 2014）证明 SGNS 隐式分解一个 shifted PMI matrix，把本文的经验目标连接到经典共现矩阵方法。这个结果是后续理论解释，不是本文公式 (4) 自带的定理。

### 静态词向量谱系

- GloVe（EMNLP 2014）直接使用全局词—词共现统计，把计数矩阵与预测式表示联系起来。
- fastText（TACL 2017）把词表示为字符 n-gram 向量之和，改善稀有词和未登录词。
- ELMo、BERT 等上下文化模型让同一个词随句子产生不同表示，处理多义性与更长依赖。

这些方法并未让 word2vec 失效：低成本静态 embedding 仍适合检索、初始化、可解释邻域和资源受限系统；只是其适用边界更清楚。

### Test of Time

NeurIPS 2023 官方 press release 与 awards blog 确认本文获 Test of Time Award。官方评价把它概括为引入 seminal word embedding technique word2vec，并称其推动 NLP 进入新阶段。结合两篇 2013 论文的版本关系，更精确的理解是：本文把高效训练、负采样、短语与线性结构组织成可规模化、可复用的方法体系；“word2vec”是奖项回顾采用的家族简称。

### 引用统计

OpenAlex work [W2153579005](https://openalex.org/W2153579005) 在 2026-08-21 查询时 `cited_by_count = 18,054`，记录以 arXiv:1310.4546 为主要位置，并聚合到同题作品。NeurIPS 2023 官方 press release 写“cited over 40,000 times”，但未标注数据库，显然采用不同索引口径。报告保留两种来源与日期，不把数字拼成一个“精确总引用数”。

## 个人笔记

最值得停下来的不是著名的 `king - man + woman`，而是表 1 的一组并不整齐的数字：NEG-5 加下采样后，总分 59% 到 60%，语义 54% 到 58%，句法却从 63% 到 61%。一项技巧可以同时减少计算、改变训练分布，并让不同子任务一升一降。“更好”只有在写清指标后才成立。

第二个细节是 72%。它来自约 330 亿词、1,000 维、整句窗口、层次 softmax；60 亿词时是 66%。如果只记住“Word2Vec 类比准确率 72%”，就会漏掉结果所依赖的数据、维度、上下文与训练算法。经典结果仍需要拆回条件。

第三个细节是本文自己的克制：短语算法只是一种简单计数方法，加法组合被写成 “somewhat meaningfully”，最佳超参数被明确说成 task specific。后来的叙事把词向量讲得近乎神奇，而原文其实一直把它放在经验边界里。精读不是替经典降温，而是恢复作者原本的温度。

## 小红书写作备忘

### Hook 素材

1. Word2Vec 最著名的公式，并不输出一个规范化的语言概率；它只训练模型分清真实邻居与噪声词。
2. 下采样让总分略升，却让句法分项下降：同一技巧对不同关系并不一致。
3. 短语类比的 72% 依赖 330 亿词、1,000 维、整句上下文与层次 softmax。

### 核心 Insight（一句话）

本文的力量在于把词表级预测压缩成少量真假配对，再用下采样和短语预处理把计算预算集中到信息更强的共现关系上。

### 自查重点

- CBOW 与基础 Skip-gram 来自同年伴随论文；本文重点是 NEG、下采样、短语与组合性。
- NEG 不等同 NCE，也不恢复规范化 softmax 概率。
- $U(w)^{3/4}$、$t\approx10^{-5}$ 与短语 score 都是经验设计，不写成理论最优。
- 表 1 的“更快更准”必须保留句法分项下降和具体配置。
- 72% 来自 330 亿词、1,000 维、整句上下文、HS；不是十亿词 300 维 NEG 的结果。
- OpenAlex 18,054 与 NeurIPS 2023 “over 40,000” 属不同索引口径。

### 动态 Hashtags

#Word2Vec #词向量 #负采样 #自然语言处理 #Paper观止

## 来源与证据分层

### 原文与官方资料

1. Mikolov et al. (2013), *Distributed Representations of Words and Phrases and their Compositionality*. [NeurIPS 页面](https://proceedings.neurips.cc/paper/2013/hash/9aa42b31882ec039965f3c4923ce901b-Abstract.html)；[PDF](https://proceedings.neurips.cc/paper_files/paper/2013/file/9aa42b31882ec039965f3c4923ce901b-Paper.pdf)；[official reviews and author feedback](https://proceedings.neurips.cc/paper_files/paper/2013/file/9aa42b31882ec039965f3c4923ce901b-Reviews.html)
2. Mikolov et al. (2013), *Efficient Estimation of Word Representations in Vector Space*. [Google Research](https://research.google/pubs/efficient-estimation-of-word-representations-in-vector-space/)；[arXiv](https://arxiv.org/abs/1301.3781)
3. NeurIPS (2023), official press release and awards blog. [Press release](https://media.neurips.cc/Conferences/NeurIPS2023/NeurIPS2023-Press_Release.pdf)；[Awards blog](https://blog.neurips.cc/tag/awards/)
4. Google Research author and publication pages. [论文页](https://research.google/pubs/distributed-representations-of-words-and-phrases-and-their-compositionality/)；[Kai Chen](https://research.google/people/kaichen/)；[Greg Corrado](https://research.google/people/gregcorrado/)；[Jeff Dean](https://blog.google/authors/jeff-dean/)
5. Ilya Sutskever academic homepage. [主页](https://www.cs.toronto.edu/~ilya/)
6. OpenAlex work W2153579005. [记录](https://openalex.org/W2153579005)

### 后继原始论文

- Levy & Goldberg, *Neural Word Embedding as Implicit Matrix Factorization*. [NeurIPS 2014](https://proceedings.neurips.cc/paper_files/paper/2014/hash/b78666971ceae55a8e87efb7cbfd9ad4-Abstract.html)
- Pennington, Socher & Manning, *GloVe: Global Vectors for Word Representation*. [ACL Anthology](https://aclanthology.org/D14-1162/)
- Bojanowski et al., *Enriching Word Vectors with Subword Information*. [ACL Anthology](https://aclanthology.org/Q17-1010/)
- Bolukbasi et al., *Man is to Computer Programmer as Woman is to Homemaker? Debiasing Word Embeddings*. [NeurIPS 2016](https://proceedings.neurips.cc/paper/2016/hash/a486cd07e4ac3d270571622f4f316ec5-Abstract.html)
- Devlin et al., *BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding*. [ACL Anthology](https://aclanthology.org/N19-1423/)

### 证据标记

- **论文事实**：公式、表格、数据规模、超参数与结论均以 9 页 NeurIPS 官方 PDF 为准。
- **后续资料**：作者轨迹、Test of Time、SGNS 的矩阵分解解释、后继模型和引用计数独立列源。
- **补充推导**：负采样梯度、$3/4$ 平滑比值、cosine 类比检索和短语分数解释按原文定义展开。
- **个人分析**：指标边界、配置拆解与经典叙事反思仅作为精读判断，不冒充作者结论。
