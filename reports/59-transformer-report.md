# 《Attention Is All You Need》精读报告

## 元信息

- 标题：*Attention Is All You Need*
- 作者：Ashish Vaswani、Noam Shazeer、Niki Parmar、Jakob Uszkoreit、Llion Jones、Aidan N. Gomez、Łukasz Kaiser、Illia Polosukhin
- 发表时机构：Google Brain、Google Research；Aidan N. Gomez 首页列 University of Toronto，并注明工作在 Google Brain 完成；Illia Polosukhin 注明工作在 Google Research 完成
- 发表：31st Conference on Neural Information Processing Systems（NIPS 2017），*Advances in Neural Information Processing Systems 30*
- 原文：[NeurIPS 论文页](https://proceedings.neurips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html)；[NeurIPS proceedings PDF](https://proceedings.neurips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf)；[arXiv:1706.03762 v7](https://arxiv.org/abs/1706.03762)
- 精读日期：2026-09-11
- 对应小红书期号：#59

### 版本与数字口径

本次保存并逐页精读的是 arXiv v7（15 页、5 幅图）。NeurIPS 官方 proceedings PDF 是 11 页版本，对应会议页码 5998–6008，缺少 v7 的 §6.3 constituency parsing 与三页 attention visualizations。两者均为合法的一手版本；报告以扩展版覆盖完整内容，并标明只存在于扩展版的证据。

版本间还留有不能静默合并的数字。arXiv v7 摘要与 Table 2 报告 WMT 2014 English–French 41.8 BLEU，v7 §6.1 的叙述段仍写 41.0；11 页 proceedings PDF 的摘要、Table 2 与 §6.1 均为 41.0。NeurIPS HTML/Metadata 顶层摘要又保留更早的 27.5/41.1 与 165M parameters，而 proceedings PDF 的 Table 3 写 big model 213M。以下以具体版本、页码或表格陪伴数字，不把旧网页摘要当作最终实验表。

### 原文验证

arXiv 返回 HTTP 200、Content-Type application/pdf、文件名 1706.03762v7.pdf 与 2,215,244 字节 Content-Length；本地大小一致，文件头为 PDF 1.5，SHA-256 为 bdfaa68d8984f0dc02beaca527b76f207d99b666d31d1da728ee0728182df697。正文提取 59,599 字节，15 页均成功渲染并逐页核查：Equations (1)–(3)、Figures 1–5、Tables 1–4、机器翻译、成分句法分析、结论、参考文献和可视化附录均完整。渲染出现 Fontconfig 无可写缓存目录提示，但 15 个页面文件全部生成，属于非致命本地缓存警告。

## 作者与合作背景

### 八位 equal contributors

首页脚注明确写出八位作者 equal contribution，署名顺序随机，并给出罕见的逐人贡献说明：

- Jakob Uszkoreit 提议用 self-attention 替换 RNN，并启动这条思路的评估。
- Ashish Vaswani 与 Illia Polosukhin 设计、实现最早的 Transformer models。
- Noam Shazeer 提出 scaled dot-product attention、multi-head attention 与无参数 position representation，并参与几乎所有细节。
- Niki Parmar 设计、实现、调参与评估大量 model variants。
- Llion Jones 探索模型变体，负责早期代码库、高效 inference 与 visualizations。
- Łukasz Kaiser 与 Aidan Gomez 参与架构设计并实现 Tensor2Tensor，以新代码库加速研究。

报告只沿用原文的贡献声明，不从随机署名顺序推断主次。

论文首页把 Vaswani、Shazeer、Kaiser 列为 Google Brain，把 Parmar、Uszkoreit、Jones 列为 Google Research。Gomez 的栏位为 University of Toronto，脚注说明研究在 Google Brain 完成；Polosukhin 以个人邮箱署名，脚注说明研究在 Google Research 完成。Google Research 的 2017 年官方介绍由 Uszkoreit 以 Natural Language Understanding software engineer 身份发布，印证这是一项研究与系统实现交织的团队工作。

### 后续轨迹及证据边界

以下履历来自机构或公司官方页面，只说明后来的轨迹，不反推 2017 年未写明的分工：

- Essential AI 官方 About 页面在 2026 年把 Ashish Vaswani 列为 CEO。
- Cohere 官方 About 页面把 Aidan Gomez 列为 co-founder and CEO。
- Sakana AI 官方公司页把 Llion Jones 列为 2023 年共同创办人及 CTO。
- NEAR 官方历史页记录 Illia Polosukhin 与 Alexander Skidanov 于 2018 年共同创办 NEAR Protocol，并明确其此前为 Google machine-learning researcher。
- OpenAI 的 GPT-4 contributions 页面把 Łukasz Kaiser 列为 long-context lead；OpenAI Forum 官方简介另记录其早年研究 logic and automata theory。
- Character.AI 官方技术博客把 Noam Shazeer 称为 founder。

对 Niki Parmar、Jakob Uszkoreit 等作者，本报告不补写未由一手页面确认的博士导师或当时职务。“八人都离开 Google”等媒体叙述不是理解论文机制所必需，也不作为论文事实使用。

## 历史语境

### 2017 年的主流序列模型

神经机器翻译已形成 encoder–decoder 范式。Sutskever、Vinyals 与 Le（2014）的 sequence-to-sequence 模型用多层 LSTM 编码源序列，再自回归生成目标；Bahdanau、Cho 与 Bengio（2014）让 decoder 在每一步对 encoder states 做 attention，缓解固定长度瓶颈；Luong 等（2015）系统比较 global/local attention。注意力当时通常附着在 recurrent backbone 上。

RNN 的状态更新按 token 顺序形成依赖链。第 $t$ 步需等待第 $t-1$ 步；序列越长，单样本内并行越困难。CNN 路线如 ByteNet 与 ConvS2S 可以并行计算各位置，但相距很远的 token 要交换信息，仍需随距离增长的卷积层路径：普通卷积近似线性增长，dilated convolution 可降为对数。

### 直接前驱与组合创新

Transformer 沿用 encoder–decoder、attention、dot-product compatibility、residual connection、LayerNorm、position-wise nonlinear layers、weight tying、Adam、label smoothing 与 byte-pair encoding。论文在 §2 对“首个完全依靠 self-attention 的 sequence transduction model”使用 *to the best of our knowledge* 限定；原创性集中在架构组合、三种 attention placement、scaled dot product、multi-head construction，以及把组合训练到强翻译结果的工程 recipe。

NeurIPS 公开评审提供了同期外部视角。Reviewer 1 认为单个底层技术未必各自新奇，但把它们组合并做到优于 LSTM 是主要成就；Reviewer 2 要求更正式的 multi-head 定义、显著性检验与长句实验。评审意见不等同于作者自述，却帮助界定论文当时已知的证据缺口。

## 问题形式化

### 序列转导

给定输入符号序列 $x=(x_1,\ldots,x_n)$，encoder 将其映射为同长连续表示

$$
z=(z_1,\ldots,z_n),\qquad z_i\in\mathbb R^{d_{\text{model}}}.
$$

decoder 自回归分解输出 $y=(y_1,\ldots,y_m)$：

$$
p(y\mid x)=\prod_{t=1}^{m}p(y_t\mid y_{<t},z).
$$

训练时已知完整目标序列，causal mask 阻止位置 $t$ 读取未来 token，所有目标位置可并行计算；推理时 $y_t$ 尚未出现，仍需逐 token 生成。论文消除的是网络层内的 sequence-aligned recurrence，不是自回归解码的时间顺序。

### Attention 的输入输出

queries $Q\in\mathbb R^{n_q\times d_k}$、keys $K\in\mathbb R^{n_k\times d_k}$ 与 values $V\in\mathbb R^{n_k\times d_v}$ 定义从 query positions 到 key positions 的兼容度。每行 softmax 得到和为 1 的权重，再对 value 向量加权：

$$
\operatorname{Attention}(Q,K,V)
=\operatorname{softmax}\!\left(\frac{QK^\top}{\sqrt{d_k}}\right)V.
$$

encoder self-attention 的 $Q,K,V$ 均来自上一层 encoder；masked decoder self-attention 均来自 decoder 且遮住未来；encoder–decoder attention 的 $Q$ 来自 decoder，$K,V$ 来自 encoder output。三者共享算子，却承担不同的信息流约束。

### 训练与评价目标

模型以 teacher forcing 下的 token-level cross-entropy 训练，并用 $\epsilon_{ls}=0.1$ label smoothing。作者明确观察到 smoothing 会伤害 perplexity，却改善 accuracy 与 BLEU。翻译结果用当时 WMT 的 BLEU 约定；成分句法分析用 WSJ Section 23 F1。Table 3 的 perplexity 是 per-wordpiece，不能与 per-word perplexity 直接比较。

## 核心方法

### Encoder 与 decoder stacks

Figure 1 的 base model 取 $N=6$、$d_{\text{model}}=512$。每个 encoder layer 包含 multi-head self-attention 与 position-wise FFN；每个 decoder layer 多一个 encoder–decoder attention。原始结构是

$$
\operatorname{LayerNorm}\bigl(x+\operatorname{Sublayer}(x)\bigr),
$$

即后来常称的 post-LN。每个 sublayer 外有 residual connection，dropout 在 sublayer output 与 residual 相加前应用。

decoder 的 self-attention logits 对未来位置置为 $-\infty$，softmax 后相应权重为 0；output embeddings 右移一位。两项共同保证预测 $y_t$ 只依赖 $y_{<t}$。

### Scaled dot-product attention

Equation (1)，v7 PDF p. 4，核心是 $1/\sqrt{d_k}$。未缩放 dot product 能用优化的 matrix multiplication 实现，却会随维度增大而把 softmax 推入极端区域。论文脚注给出统计直觉：若 $q_i,k_i$ 独立、均值 0、方差 1，则 $q\cdot k$ 的方差是 $d_k$。缩放把 logit 的典型尺度维持在常数量级。

### Multi-head attention

每个 head 使用不同的 learned projections：

$$
\operatorname{head}_i
=\operatorname{Attention}(QW_i^Q,KW_i^K,VW_i^V),
$$

$$
\operatorname{MultiHead}(Q,K,V)
=\operatorname{Concat}(\operatorname{head}_1,\ldots,\operatorname{head}_h)W^O.
$$

base model 取 $h=8$，且 $d_k=d_v=d_{\text{model}}/h=64$。分头后每个 attention 的 channel dimension 变小，八头总算量与一个 full-dimensional head 同阶。多头给不同 representation subspaces 与位置关系各自的加权通道；“每一头必然学到一种可命名语法关系”只在附录中有案例，不是定理。

### Position-wise FFN、embedding 与位置

Equation (2)，v7 PDF p. 5：

$$
\operatorname{FFN}(x)=\max(0,xW_1+b_1)W_2+b_2.
$$

它对每个位置独立、共享地应用；base model 由 512 维升到 $d_{ff}=2048$，再投回 512 维。不同层使用不同 FFN 参数。论文还让输入 embedding、输出 embedding 与 pre-softmax transformation 共享 weight matrix，并在 embedding 端乘 $\sqrt{d_{\text{model}}}$。

无 recurrence/convolution 后，token order 需显式注入。论文使用多种几何频率的 sinusoidal positional encoding，与 token embedding 相加；Table 3 row (E) 的 learned positional embeddings 得到几乎相同结果。作者选择 sinusoid 的理由是“可能”外推到训练时未见的长度，本文没有相应长长度实验。

### 为什么 self-attention 适合并行

Table 1 比较长度 $n$、维度 $d$、kernel $k$ 与局部窗口 $r$：

| Layer type | 每层复杂度 | 最少串行操作 | 最大路径长度 |
|---|---:|---:|---:|
| Self-attention | $O(n^2d)$ | $O(1)$ | $O(1)$ |
| Recurrent | $O(nd^2)$ | $O(n)$ | $O(n)$ |
| Convolutional | $O(knd^2)$ | $O(1)$ | $O(\log_k n)$ |
| Restricted self-attention | $O(rnd)$ | $O(1)$ | $O(n/r)$ |

当 $n<d$ 时，full self-attention 的 $n^2d$ 小于 recurrent layer 的 $nd^2$，这在当时的 wordpiece sentence translation 中常见。这个比较不表示 self-attention 对所有长度都更省：当 $n$ 很长，二次 attention matrix 成为主要成本，论文已把 local/restricted attention 列为未来工作。Table 1 也没有计入 Transformer FFN 的 $O(nd^2)$ 或 attention-score memory。

## 关键公式推导

### 推导一：为什么除以 $\sqrt{d_k}$

**原文定位：** Equation (1) 与脚注 4，v7 PDF p. 4。以下展开原文统计说明。

令

$$
s=q^\top k=\sum_{i=1}^{d_k}q_i k_i,
$$

并假设各分量相互独立，$\mathbb E[q_i]=\mathbb E[k_i]=0$，$\operatorname{Var}(q_i)=\operatorname{Var}(k_i)=1$。

Step 1：独立性给出 $\mathbb E[q_i k_i]=0$，且

$$
\operatorname{Var}(q_i k_i)
=\mathbb E[q_i^2]\mathbb E[k_i^2]=1.
$$

Step 2：各项独立相加，因此

$$
\operatorname{Var}(s)
=\sum_{i=1}^{d_k}\operatorname{Var}(q_i k_i)
=d_k.
$$

Step 3：令 $\tilde s=s/\sqrt{d_k}$，便有

$$
\operatorname{Var}(\tilde s)
=\frac{1}{d_k}\operatorname{Var}(s)
=1.
$$

logits 的尺度因此不随 key dimension 线性膨胀，减轻 softmax 饱和及其极小梯度。这个推导依赖独立、零均值、单位方差假设，不是实际训练动态的普适定理。

### 推导二：多头为何不把 attention 主项乘上 $h$

**原文定位：** §3.2.2，v7 PDF pp. 4–5；以下为复杂度补充推导。

对长度约为 $n$ 的 self-attention，单头 score matrix $QK^\top$ 成本约 $O(n^2d_k)$，再乘 $V$ 约 $O(n^2d_v)$。若 $h$ 个 heads 均取

$$
d_k=d_v=d_{\text{model}}/h,
$$

则 attention 主项总成本为

$$
h\cdot O\!\left(n^2\frac{d_{\text{model}}}{h}\right)
=O(n^2d_{\text{model}}).
$$

Q/K/V 与 output projections 合计仍是 $O(nd_{\text{model}}^2)$ 量级。因此，多头用于拆分 representation subspaces，并非免费增加 $h$ 倍通道容量；heads 过多会让每头维度过小。Table 3 的 32-head row 从 base 的 25.8 降至 25.4 dev BLEU，给出一项具体边界。

### 推导三：正弦位置为何支持固定偏移的线性变换

**原文定位：** §3.5，v7 PDF p. 6。论文只给出 hypothesis；以下为补充推导。

对某一频率 $\omega$，位置 $p$ 的二维分量写为

$$
u(p)=
\begin{bmatrix}
\sin(\omega p)\\
\cos(\omega p)
\end{bmatrix}.
$$

利用和角公式：

$$
u(p+k)=
\begin{bmatrix}
\cos(\omega k) & \sin(\omega k)\\
-\sin(\omega k) & \cos(\omega k)
\end{bmatrix}
u(p).
$$

固定 offset $k$ 对应一个只依赖 $k$ 与频率的 $2\times2$ 旋转矩阵，因此每对 sin/cos channels 都能以线性方式从 $p$ 映射到 $p+k$。多频率拼接后仍是 block-diagonal linear map。这解释“relative offset 可线性表示”的数学来源；它不保证有限深度网络一定学会任意相对位置规律，也不单独证明长度外推。

## 训练与实验分析

### 数据、硬件与优化

WMT 2014 English–German 使用约 450 万 sentence pairs 与约 37,000 shared BPE vocabulary；English–French 使用约 3,600 万 pairs 与 32,000 word-piece vocabulary。batch 按近似长度组合，每批约 25,000 source tokens 和 25,000 target tokens。

模型在单机 8 张 NVIDIA P100 GPU 上训练。base 每 step 约 0.4 秒、100,000 steps、12 小时；big 每 step 约 1.0 秒、300,000 steps、3.5 天。base 约 65M parameters，big 约 213M（Table 3 v7）。Adam 参数为 $\beta_1=0.9,\beta_2=0.98,\epsilon=10^{-9}$，warmup 4,000 steps，之后按 inverse square root decay：

$$
\operatorname{lrate}
=d_{\text{model}}^{-1/2}
\min\!\left(
\operatorname{step}^{-1/2},
\operatorname{step}\cdot\operatorname{warmup}^{-3/2}
\right).
$$

这套 schedule 是复现 recipe 的重要部分。NeurIPS Reviewer 1 的反馈称普通 SGD 对其实现失败，进一步提示“attention-only”并不意味着优化细节可忽略。

### 翻译主结果

Table 2（arXiv v7 PDF p. 8）：

| Model | EN–DE BLEU | EN–FR BLEU | 估算训练 FLOPs |
|---|---:|---:|---:|
| GNMT + RL | 24.6 | 39.92 | $2.3\times10^{19}$ / $1.4\times10^{20}$ |
| ConvS2S | 25.16 | 40.46 | $9.6\times10^{18}$ / $1.5\times10^{20}$ |
| MoE | 26.03 | 40.56 | $2.0\times10^{19}$ / $1.2\times10^{20}$ |
| ConvS2S ensemble | 26.36 | 41.29 | $7.7\times10^{19}$ / $1.2\times10^{21}$ |
| Transformer base | 27.3 | 38.1 | $3.3\times10^{18}$ / 未报 |
| Transformer big | 28.4 | 41.8（v7 Table 2） | $2.3\times10^{19}$ / 未报 |

EN–DE 的 28.4 比表中最佳 ensemble 26.36 高 2.04 BLEU。EN–FR 的 41.8 在 v7 表中比 single-model MoE 40.56 高 1.24，也略高于 ConvS2S ensemble 41.29；但 v7 §6.1 与会议版写 41.0，版本冲突必须随数字出现。

训练成本列由作者用 training time × GPU count × 各 GPU 估算 sustained single-precision capacity 得出。它支持“在该估算口径下成本更低”，不等同于同软件栈、同 GPU、同实现的严格 wall-clock benchmark；也不能从训练 FLOPs 推导 serving latency、memory 或 token/s。

### Model variations

Table 3 全部在 EN–DE development set newstest2013 上评估，未做 checkpoint averaging：

- base：PPL 4.92、BLEU 25.8、65M parameters。
- 1/4/16/32 heads：BLEU 24.9/25.5/25.8/25.4；单头较差，heads 过多也下降。
- $d_k=16/32$：BLEU 25.1/25.4，低于 base 的 $d_k=64$。
- $N=2/4/8$：23.7/25.3/25.5；加深到 8 层没有超过 base 的 25.8。
- $d_{\text{model}}=256/1024$：24.5/26.0；$d_{ff}=1024/4096$：25.4/26.2。
- dropout 0.0/0.2：BLEU 24.6/25.5；label smoothing 0.0/0.2：PPL 4.67/5.47、BLEU 25.3/25.7，体现 PPL 与 BLEU 的方向张力。
- learned positional embedding：PPL 4.92、BLEU 25.7，与 sinusoid base 的 25.8 几乎相同。
- big configuration：PPL 4.33、BLEU 26.4、213M parameters。

这些是一次训练 recipe 下的局部变化；同时改变 parameter count 的 rows 不能解释为纯组件因果。公开评审还指出表中没有 significance tests，0.1–0.3 BLEU 的微小差距不宜写成普适排序。

### English constituency parsing

只存在于 arXiv 扩展版的 §6.3 与 Table 4 使用 4-layer Transformer、$d_{\text{model}}=1024$。WSJ-only setting 约 40K sentences、16K vocabulary，Section 23 F1 为 91.3；semi-supervised setting 加入约 17M high-confidence 与 BerkeleyParser sentences、使用 32K vocabulary，F1 为 92.7。beam size 为 21、length penalty $\alpha=0.3$、maximum output length 为 input length + 300。

这个实验支持架构迁移到另一种 sequence transduction 任务；它不支持“无需任务适配”，也不代表 image/audio/video 已在本文验证。该节不能写成 11 页 proceedings 版的内容。

### Attention visualizations

Figures 3–5 展示某些 encoder heads 对 long-distance dependency、anaphora 与类似句法结构的 sharp patterns。它们是少量定性案例，足以说明不同 heads 可能形成差异化模式；论文没有系统量化 head semantics，更没有证明 attention weight 等同于因果解释。

## 局限性

### 作者自述

1. **长序列成本。** §4 明确建议对非常长输入采用 restricted neighborhood attention；full attention 的 $O(n^2d)$ 是已知代价。
2. **任务范围。** §7 把 image、audio、video 作为未来扩展，本文证据只覆盖翻译和扩展版 parsing。
3. **生成仍串行。** 结论把 making generation less sequential 列为未来目标；自回归 inference 没被标题中的 “all you need” 消除。
4. **位置外推是 hypothesis。** 作者说 sinusoid *may allow* 超出训练长度的 extrapolation，未提供对应实验。
5. **局部 attention 未验证。** restricted self-attention 只出现在复杂度讨论与 future work。

### 当时评审指出的证据缺口

NeurIPS Reviewer 2 指出 multi-head 数学定义仍可更清楚、Table 3 缺 significance tests、最大路径长度优势没有用 long-sentence slice 直接检验。Reviewer 1 强调 hyperparameters 与 learning-rate schedule 对复现很关键。这些意见不否定主结果，却限制了可从主表推出的机制结论。

### 后续研究揭示的结构边界

- Jain 与 Wallace（NAACL 2019）表明 attention distributions 往往与其他 feature-importance measures 不一致，且可构造差异很大的 attention 而预测近似不变；附录图应视为模型内部相关性线索。
- Dong、Cordonnier 与 Loukas（ICML 2021）分析 pure self-attention 在无 skip connection/MLP 时向 token uniformity 退化。原始 Transformer 恰有 residual 与 FFN，成功来自完整 block。
- Xiong 等（ICML 2020）分析 LayerNorm placement，解释原始 post-LN Transformer 的 warmup/optimization difficulty，并研究 pre-LN 变体。今天的“Transformer”包含许多后续结构选择，不能全部回写为 2017 原式。
- 高效 attention、sparse/local attention 与 kernel work 后来持续处理 $n^2$ memory/compute。算法复杂度、硬件利用率与端到端吞吐属于不同层级，需要分别测量。

## 后续影响

### 直接后继

- BERT（Devlin et al., NAACL 2019）采用双向 Transformer encoder 预训练，再面向下游任务微调。
- GPT 路线沿用 causal Transformer decoder 与自回归 next-token objective，也保留推理串行边界。
- Vision Transformer（Dosovitskiy et al., ICLR 2021）把 image patches 当作 token sequence，验证论文结论中设想的视觉扩展。
- speech、music、multimodal models 与 AlphaFold 2 等系统吸收了 Transformer 的全局交互或变体；这些系统还包含大量领域专用模块，不能缩写为原论文直接实现。

### 引用与奖项

Google Scholar 合并记录在 2026-09-11 显示 280,310 citations，其中页面所列主记录为 268,518；Semantic Scholar 同日显示 169,904。两库的合并、去重与覆盖规则不同，数字不能横向相减解释。引用量也不表示每篇后续论文都验证了原始机制。

2024 年，C&C Foundation 官方将 C&C Prize 授予八位作者组成的 Transformer team，表彰这项工作作为 generative AI 基础之一的贡献。这是论文发表七年后的外部奖项，不是 NeurIPS 2017 best-paper award；NeurIPS Metadata 的 award 字段为空。

### 历史地位的准确表述

这篇论文把 self-attention 从 recurrent encoder–decoder 的辅助通道提升为主干层，给出能在当时机器翻译基准上同时取得质量与训练并行优势的完整 recipe。其后“Transformer”成为跨模态架构家族的名称。影响来自可堆叠的 token-mixing block、硬件友好的矩阵运算和可迁移接口共同作用；标题不表示 FFN、residual、normalization、position、optimizer 或 decoder 约束不重要。

## 个人笔记

### 最值得反复看的不是 Equation (1)，而是 Table 1

初读很容易把注意力停在 $QK^\top/\sqrt{d_k}$。真正改变研究方向的判断藏在 Table 1：作者把“两个 token 之间要走几步”与“这些步骤能否并行”并列成设计指标。RNN 的问题被重新表达成计算图的路径长度，模型创新由此与硬件结构连接。

我会给这张表加一条脚注：$O(1)$ sequential operations 指同一层内各位置可并行，不是整个模型只有一步，更不是 autoregressive generation 一步完成。把抽象复杂度写成系统速度之前，需要给出长度、维度、batch、kernel 与硬件利用率。

### 一张表内部的 41.8/41.0 分歧

v7 摘要和 Table 2 已更新到 41.8，§6.1 却保留 41.0；会议 PDF 又一致为 41.0。这种微小的不整齐很有教育意义：经典论文也有版本历史，引用数字必须落到具体页与版本。阅读“名作”仍要像读普通实验记录一样核对。

### “all you need” 的真正组成

论文标题很锋利，Figure 1 却很诚实：attention 周围还有 residual、LayerNorm、FFN、positional encoding、mask、embedding sharing、label smoothing、Adam 与 warmup。准确的简写是“无需 sequence-aligned recurrence/convolution 的主干”，不是“模型只剩 attention”。

## 小红书写作备忘

### Hook 素材

1. 2017 年，八位 equal contributors 把 RNN 的顺序依赖改写成一张所有 token 两两相望的矩阵。
2. Equation (1) 最著名，Table 1 才把算法结构、路径长度与硬件并行放进同一论证。
3. arXiv v7 与 proceedings 对 EN–FR BLEU 保留 41.8/41.0 的版本差异，适合作为具体精读手记。

### 核心 Insight（一句话）

Transformer 用可并行的 self-attention 路径承担序列内部的信息传递，同时用 mask、position、residual、FFN 与 normalization 保住顺序和表达能力。

### 自查重点

1. 不把“无 recurrence”写成“generation 完全并行”。
2. 不把 41.8 与 41.0 混成无版本的唯一数字。
3. 不把 $O(n^2d)$ 写成对任意长度都更省；原文只在 $n<d$ 时与 RNN 比较。
4. 不把 qualitative attention visualization 写成 faithful explanation。
5. 不把后来的 BERT、GPT、ViT 或现代 pre-LN block 全部归为原论文直接提出。

### 动态 Hashtags

#Transformer #SelfAttention #机器翻译 #深度学习

## 来源

### 原始论文与同期材料

- Vaswani et al., 2017, [arXiv v7](https://arxiv.org/abs/1706.03762) 与 [15-page PDF](https://arxiv.org/pdf/1706.03762)
- NeurIPS, [official paper page](https://proceedings.neurips.cc/paper_files/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html)、[11-page proceedings PDF](https://proceedings.neurips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Paper.pdf)、[Metadata JSON](https://proceedings.neurips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Metadata.json) 与 [public reviews](https://proceedings.neurips.cc/paper_files/paper/2017/file/3f5ee243547dee91fbd053c1c4a845aa-Reviews.html)
- Google Research, [publication record](https://research.google/pubs/attention-is-all-you-need/) 与 Uszkoreit, 2017, [Transformer: A Novel Neural Network Architecture for Language Understanding](https://research.google/blog/transformer-a-novel-neural-network-architecture-for-language-understanding/)

### 作者、后续研究与统计

- [Essential AI — About](https://www.essential.ai/about)
- [Cohere — About](https://cohere.com/about)
- [Sakana AI — Corporate Information](https://sakana.ai/company-info/)
- [NEAR — Roadmap & History](https://www.near.org/roadmap-history)
- [OpenAI — GPT-4 Contributions](https://openai.com/contributions/gpt-4/)
- [Character.AI — Inside Kaiju](https://blog.character.ai/inside-kaiju-building-conversational-models-at-scale/)
- [C&C Foundation — 2024 C&C Prize](https://www.nec.com/en/press/202410/global_20241015_01.html)
- Jain & Wallace, 2019, [Attention is not Explanation](https://aclanthology.org/N19-1357/)
- Xiong et al., 2020, [On Layer Normalization in the Transformer Architecture](https://proceedings.mlr.press/v119/xiong20b.html)
- Dong et al., 2021, [Attention is Not All You Need](https://proceedings.mlr.press/v139/dong21a.html)
- [Google Scholar merged record](https://scholar.google.com/citations?view_op=view_citation&hl=en&user=mOG0bwsAAAAJ&citation_for_view=mOG0bwsAAAAJ:_FxGoFyzp5QC)（citation count，2026-09-11）
- [Semantic Scholar record](https://www.semanticscholar.org/paper/Attention-Is-All-You-Need-Vaswani-Shazeer/204e3073870fae3d05bcbc2f6a8e263d9b72e776)（citation count，2026-09-11）
