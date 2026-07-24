# 精读报告 #32：Neocognitron — A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position（Fukushima, 1980）

## 元信息

- 标题：Neocognitron: A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position
- 作者：Kunihiko Fukushima（福岛邦彦，NHK Broadcasting Science Research Laboratories, Kinuta, Setagaya, Tokyo, Japan）
- 发表：Biological Cybernetics, Vol. 36, No. 4, pp. 193–202 (1980), Springer-Verlag
- 收稿：1979-10-28
- 原文链接：<https://www.cs.princeton.edu/courses/archive/spr08/cos598B/Readings/Fukushima1980.pdf>
- 精读日期：2026-07-24
- 对应小红书期号：#32

## 作者背景

### 福岛邦彦 Kunihiko Fukushima（1936– ）
- **发表时身份**：日本放送协会（NHK）广播科学研究所（Broadcasting Science Research Laboratories）高级研究员，工作地址位于东京世田谷区砧町。
- **教育背景**：1958 年京都大学电子工学专业本科毕业。此后长期在 NHK 研究所任职，从事神经科学与视觉信息处理的工程模型研究。
- **此前工作**：
  - 1969 年，福岛引入了「analog threshold element」——即今天的 ReLU 激活函数原型（这一点鲜为人知）。
  - **Cognitron（1975）**：福岛的第一个自组织多层神经网络。发表于 Biological Cybernetics，能识别模式但对位置敏感——这是本文 Neocognitron 的直接前身。
- **后续轨迹**：
  - 1988 年 Neural Networks 期刊上发表 Neocognitron 的改进版，加入了反向学习。
  - 1989 年离开 NHK，加入大阪大学基础工学部；后转任电气通信大学（1999）、东京工科大学（2001）。
  - 主要奖项：**Bower Award and Prize for Achievement in Science（2020, Franklin Institute）**——颁奖词直接肯定 Neocognitron 是「convolutional neural networks 的基础」；IEEE Neural Networks Pioneer Award；INNS Helmholtz Award。
  - 是日本神经网络学会（JNNS）创会会长，国际神经网络学会（INNS）创始理事。

## 历史语境

### 当时的学术主流
1970s–1980s 之交的视觉识别研究呈现两条脉络：
- **神经生理学一侧**：Hubel & Wiesel（1959–1968）在猫视觉皮层的著名实验，揭示了 simple cell → complex cell → lower-order hypercomplex cell → higher-order hypercomplex cell 的层级结构，并因此获 1981 年诺贝尔生理学或医学奖。
- **工程一侧**：Rosenblatt 的 Perceptron（1957–1962）被 Minsky & Papert 1969 年的《Perceptrons》证明单层无法处理 XOR 问题后，神经网络研究整体陷入低谷——史称第一次「AI 冬」。
- **模式识别工程**：OCR（光学字符识别）已可实用，但都需要预先「归一化位置」，无法处理平移、噪声、形变。

### 待解决的核心问题
福岛在引言中直接列出：
1. **位置不变性（Position Invariance）**：如何让同一个模式在输入图像的任何位置都被识别？
2. **形变鲁棒性**：如何容忍小的形状扭曲和大小变化？
3. **无教师学习**：能否在没有类别标签的情况下自组织地学到有意义的特征？

这三点在 1980 年时几乎是「工程界的诅咒」——文中原话：「it has long been desired to find out an algorithm of pattern recognition which can cope with the shift in position of the input pattern.」

### 同时期的相关工作
- **Rosenblatt (1962)** 的 Perceptron：单层线性分类器，不能处理平移。
- **Kabrisky (1966)**：另一个视觉处理模型。
- **Giebel (1971)**：手写字符识别的分层模型。
- **Cognitron（Fukushima 1975）**：本文的直接前驱，多层自组织网络但对位置敏感。
- **Marr（1980 前后）**：正在建立 computational vision 的宏大理论，与 Neocognitron 走的是完全不同的方向（自上而下的表征论 vs 自下而上的生物启发架构）。

### 直接前驱
- Hubel, D.H. & Wiesel, T.N. "Receptive fields, binocular interaction and functional architecture in cat's visual cortex" (J. Physiol. 1962)
- Fukushima, K. "Cognitron: a self-organizing multilayered neural network" (Biol. Cybernetics 20, 1975)
- Gross, C.G., Rocha-Miranda, C.E., Bender, D.B. "Visual properties of neurons in inferotemporal cortex of the macaque" (J. Neurophysiol. 35, 1972)——启示了「grandmother cells」的猜想。

## 问题形式化

### 问题定义
给定一组模式 $\mathcal{P} = \{P_1, \ldots, P_M\}$（如手写数字 0–4），构造一个前馈神经网络 $f_\theta: \mathbb{R}^{16 \times 16} \to \mathbb{R}^{K}$，使得：

1. **平移不变**：对任意模式 $P_i$ 和其平移版本 $T(P_i)$，$f_\theta(P_i) = f_\theta(T(P_i))$
2. **类内一致**：对 $P_i$ 的小形变、大小变化，$f_\theta$ 输出稳定
3. **类间分离**：$f_\theta(P_i)$ 只对一个「grandmother cell」产生大输出

### 输入与输出
- **输入**：$16 \times 16$ 的光感受阵列 $U_0$，每个像素取非负实数
- **输出**：最后一层 $U_{C3}$ 的 $K=24$ 个 C-cell 输出——理想情况下每个模式激活恰好一个 C-cell

### 目标 / 评价准则
无监督自组织：只需重复呈现刺激模式（不提供标签），网络应能自动学到区分性表示。

## 核心方法

### 直觉
福岛的核心思路是把 Hubel-Wiesel 的层级结构完整地搬到人工神经网络中：
- **S-cell（simple cell 类比）**：负责特征检测，感受野小、位置敏感、有可塑突触。
- **C-cell（complex cell 类比）**：负责位置不变性，从一小片 S-cells 中取「或」，对平移容忍。
- **级联堆叠**：S/C 层交替堆叠，每一层的感受野扩大、位置不变性容忍范围扩大，直至最深层的单个 C-cell 覆盖整个输入。

这三个设计——**局部感受野、共享权重、空间池化**——正是三十年后 LeCun CNN 的三大支柱。福岛在 1980 年就把它们组合到了一起。

### 形式化描述

网络结构：$U_0 \to U_{S1} \to U_{C1} \to U_{S2} \to U_{C2} \to U_{S3} \to U_{C3}$

**Cell-plane（细胞平面）的关键约束**：同一 cell-plane 内所有 S-cells 共享同一组突触权重，只是感受野位置不同。**这正是今天所说的 "weight sharing"**——权重在空间上被 tied。

**S-cell 输出公式**（论文式 1，做了记号简化）：

$$u_{Sl}(k_l, \mathbf{n}) = r_l \cdot \varphi\left[\frac{1 + \sum_{k_{l-1}}\sum_{\mathbf{v} \in S_l} a_l(k_{l-1}, \mathbf{v}, k_l) \cdot u_{C,l-1}(k_{l-1}, \mathbf{n}+\mathbf{v})}{1 + \frac{2 r_l}{1+r_l} b_l(k_l) \cdot v_{C,l-1}(\mathbf{n})} - 1\right]$$

其中 $\varphi[x] = \max(x, 0)$——**这就是 ReLU**。

- $a_l(k_{l-1}, \mathbf{v}, k_l)$：可塑的兴奋性权重（会学习）
- $b_l(k_l)$：可塑的抑制性权重
- $v_{C,l-1}(\mathbf{n})$：一个额外的抑制细胞（r.m.s. 型输出）：$v_{C,l-1}(\mathbf{n}) = \sqrt{\sum_{k,\mathbf{v}} c_{l-1}(\mathbf{v}) \cdot u_{C,l-1}^2(k, \mathbf{n}+\mathbf{v})}$
- $r_l$：抑制强度参数（论文中取 $r_1 = 4.0, r_2 = r_3 = 1.5$）

**C-cell 输出公式**（式 4）：

$$u_{Cl}(k_l, \mathbf{n}) = \psi\left[\frac{1 + \sum_{\mathbf{v} \in D_l} d_l(\mathbf{v}) \cdot u_{Sl}(k_l, \mathbf{n}+\mathbf{v})}{1 + v_{Sl}(\mathbf{n})} - 1\right]$$

其中 $\psi[x] = \varphi[x/(\alpha + x)]$ 是带饱和的非线性——**这近似 max-pooling 的柔性版本**。

### 关键定理与证明思路

论文不含形式化定理，但作者用一段严谨的构造性论证证明了「移位不变性」的实现机理：

**引理（Fukushima 1980, §2 阐述）**：若同一 cell-plane 内的所有 S-cells 共享突触权重（仅感受野位置不同），且 C-cell 从对应 S-plane 的一个局部邻域中取「饱和 OR」，则：
- 当刺激模式 $P$ 平移量小于 C-cell 感受野半径时，最深层 C-cell 输出不变；
- 平移量较大时，通过多层级联，容忍度累加至覆盖整个输入。

**证明骨架**：设 $P$ 平移 $\Delta$ 单位。由于 S-plane 内权重共享，会有另一个 S-cell 在位置 $\mathbf{n} + \Delta$ 上给出相同的响应值。C-cell 在其感受野内取「饱和 OR」，故只要 $\Delta$ 在感受野半径以内，C-cell 输出保持不变。级联 $L$ 层后，总容忍度约为 $\prod_l r_l^{\text{pool}}$。

### 与前人方法的本质区别

| 方法 | 位置不变 | 无监督 | 学习信号 |
|------|---------|--------|---------|
| Perceptron (1957) | ✗ | ✗ | 监督 |
| Cognitron (1975) | ✗ | ✓ | 竞争学习 |
| **Neocognitron (1980)** | **✓** | **✓** | **代表元竞争学习 + weight tying** |
| LeCun CNN (1989) | ✓ | ✗ | 监督 + 反向传播 |

## 关键公式推导

### 公式 1：无监督学习规则（representative selection）

**原文表述**（式 7）：若细胞 $u_{Sl}(\hat{k}, \hat{\mathbf{n}})$ 被选为「代表元」，则其可塑权重更新为：

$$\Delta a_l(k_{l-1}, \mathbf{v}, \hat{k}) = q_l \cdot c_{l-1}(\mathbf{v}) \cdot u_{C,l-1}(k_{l-1}, \hat{\mathbf{n}} + \mathbf{v})$$

$$\Delta b_l(\hat{k}) = (q_l / 2) \cdot v_{C,l-1}(\hat{\mathbf{n}})$$

**逐步推导**：

Step 1：目标是让代表元 $u_{Sl}(\hat{k}, \hat{\mathbf{n}})$ 变得更能选择性响应当前呈现的模式。
— 依据：Hebbian 学习「fire together, wire together」。

Step 2：更新兴奋性权重时，只在代表元的**感受野内**、按 $c_{l-1}(\mathbf{v})$（关于距离单调递减）加权，把当前的 C-cell 输出记入。
— 依据：Hebbian 规则的空间加权版本。

Step 3：由于 cell-plane 内所有 S-cells 权重共享，代表元学到的权重被拷贝到 plane 内所有其他 S-cells——这实现了 **weight tying**。
— 依据：论文对 cell-plane 的约束条件（§2 中反复强调）。

Step 4：抑制性权重 $b_l(\hat{k})$ 同步增长，其大小与代表元感受野内输入的 r.m.s. 强度成正比。这抑制了细胞对无关刺激的响应，起到「负样本压制」的作用。
— 依据：cognitron 中已论证 r.m.s. 抑制的稳定性。

**直觉理解**：每次呈现刺激模式时，网络挑选出反应最强的 S-cell 作为「代表」，强化它的权重，同时把这份强化传播到整个 cell-plane。这实现了：
- 一个 cell-plane 只学到一个特征（防止冗余）；
- 该特征在整个空间上都能被检测到（因为权重共享）。

### 公式 2：Cell-plane 内的 weight tying

**约束条件**：$a_l(k_{l-1}, \mathbf{v}, k_l)$ 与感受野**位置** $\mathbf{n}$ **无关**。

**直觉理解**：这一约束等价于：若把 cell-plane 内的所有 S-cells 排列成二维数组，则该数组与输入 $u_{C,l-1}$ 的关系是**卷积**（离散互相关）。福岛在文中虽然没用「卷积」一词，但已构造出等价的运算：

$$u_{Sl}(k_l, \mathbf{n}) \approx \text{ReLU}\left[\sum_{k_{l-1}} (a_l \star u_{C,l-1})(\mathbf{n}) - \text{inhibition}\right]$$

其中 $\star$ 就是二维卷积。**Neocognitron 的每一个 S-layer 本质上就是一个卷积层加 ReLU；每一个 C-layer 本质上就是一个池化层。** 这一发现直到 1998 年 LeCun 的 LeNet 才被工程界主流吸收。

## 实验分析

### 实验设置
- **数据集**：手写数字 "0", "1", "2", "3", "4" 的样式（每次随机平移位置）；后又扩展到 "X", "Y", "T", "Z"（相似模式）；再扩展到 "0"–"9"（10 类）。
- **网络架构**：7 层（$U_0 \to U_{S1} \to U_{C1} \to U_{S2} \to U_{C2} \to U_{S3} \to U_{C3}$），每层 24 个 cell-planes，$U_0$ 为 $16\times16$。
- **基线方法**：无（论文是首个宣示的方案）。
- **评价指标**：分类正确率（每个模式是否只激活唯一的一个 $U_{C3}$ 细胞）。

### 主要结果
- **5 类实验**：每个模式呈现 20 次即完成自组织，所有测试模式（含平移、噪声、形变、大小变化）均被正确识别，且响应最后一层的同一 C-cell。
- **相似模式实验（XYTZ）**：即使模式共享大量视觉特征，网络仍能区分。
- **10 类实验**：能自组织成功，但参数调整极为敏感——福岛坦承 24 个 cell-planes/层不够。

### 关键发现
- **早期特征共享**：同一 S-plane 会同时参与多个类别的识别——**早期特征通用性**这个 CNN 时代的常识，福岛已经明确指出。
- **参数敏感性**：类别数增加时对超参数敏感，暗示了后来 CNN 依赖大量参数、大数据、反向传播的必然性。

### 实验设计评价
- **优点**：证明了架构本身的可行性——**位置不变性、形变鲁棒性、无监督学习**三者能同时实现。
- **不足**：
  - 无量化基准（错误率、混淆矩阵），今天看来实验规模极小。
  - 学习规则是启发式的「代表元竞争」，无梯度下降的收敛性保证。
  - 只做了 5–10 类的字符识别，未在真实图像上验证。

## 论文的局限性

### 作者自述
- **与生物学的偏离**：福岛在结论中明确写道「The author does not advocate that the neocognitron is a complete model for the mechanism of pattern recognition in the brain」——它只是一个工作假设。
- **Hubel-Wiesel 层级不完全正确**：作者引用了 monosynaptic 连接从 LGB 直达 complex cell 的证据，承认原始层级模型并不严格成立。
- **类别数受限**：24 个 cell-planes 不足以支持 10 类以上模式，需要更多计算资源。

### 后续批评
- **学习规则的可扩展性**：竞争学习难以扩展到大规模；这一点直到 1989 年 LeCun 用反向传播才解决。
- **缺少任务导向**：无监督学习到的特征未必对分类最优；有监督的梯度下降能针对任务定制特征。
- **没有池化的严格数学化**：C-cell 的「饱和 OR」是 max-pooling 的柔性版本，但对训练稳定性的影响直到 2010 年代才被完全理解。

### 假设检验
- **权重共享的生物合理性**：福岛承认「同一 cell-plane 的所有细胞共享突触分布」这一假设在真实大脑中是否成立并不清楚，只是作为工作假设。
- **r.m.s. 抑制的作用**：作者用直觉论证抑制的必要性，但没有形式化证明。

## 后续影响

### 直接后继
- **Fukushima (1988) "Neocognitron: A Hierarchical Neural Network Capable of Visual Pattern Recognition"**（Neural Networks）：加入反向学习。
- **LeCun et al. (1989) "Backpropagation Applied to Handwritten Zip Code Recognition"**（Neural Computation）：把反向传播嫁接到局部感受野 + 权重共享的架构上——CNN 由此诞生。LeCun 本人多次公开承认 Neocognitron 是 CNN 的直接前身。
- **LeNet-5 (1998)**：应用于 MNIST，位置不变 + 卷积 + 池化的组合彻底定型。
- **AlexNet (2012)**：8 层深、ReLU 激活、GPU 加速——同一架构在 ImageNet 上引爆深度学习热潮。
- **ResNet (2015)**：152 层，同样是 Neocognitron 血脉的延续。

### 开创的方向
Neocognitron 是**卷积神经网络（Convolutional Neural Networks）**的开山之作。它奠定的三个核心原则贯穿今日：
1. **局部感受野（Local Receptive Field）**
2. **权重共享（Weight Sharing）**
3. **空间池化（Spatial Pooling）**

以及一个被长期忽视的贡献：福岛 1969 年已经在其著作中使用 ReLU 型的「analog threshold element」。ReLU 作为现代深度学习的默认激活函数，其源头可溯至此。

### 当代回响
今天所有计算机视觉的骨干网络——从 ResNet、EfficientNet 到 ConvNeXt——都源自 Neocognitron 的架构基因。即便 Transformer 兴起后，Vision Transformer (ViT) 也不得不用 patch embedding 引入类似的局部性；Swin Transformer 更是重新引入了层级 + 局部窗口结构，与 Neocognitron 的直觉高度吻合。

### 引用统计
- Google Scholar 引用数：约 6,700（截至 2025），是深度学习历史文献中被反复回溯的经典。
- 2020 年 Bower Award 颁奖词直言：福岛的 Neocognitron 是「现代深度卷积神经网络架构的起源」。

## 个人笔记

读完这篇论文，最强烈的感受是**孤独感**。1980 年的福岛，一个在 NHK 广播研究所工作的日本工程师，独自把 Hubel-Wiesel 的神经生理学发现、Rosenblatt 的感知机、以及自己 1975 年的 Cognitron 拼接成一个完整的架构——这个架构此后引导了整个计算机视觉的方向。而当时的世界并没有意识到。1980 年代主流 AI 仍在符号主义与专家系统的辉煌中，第二次「AI 冬」正在酝酿。福岛写这篇论文时几乎是在自说自话。

论文第五节末尾有一句话让我久久不能忘：

> 「the computer simulation for the case of more than 24 cell-planes in each layer, however, has not been made yet, because of the lack of memory capacity of our computer.」

——「无法做超过 24 个 cell-planes 的实验，因为我们的计算机内存不够」。1980 年的 NHK 研究所，福岛的机器扛不起更大的网络。他已经写出了 CNN 的全部要素，但要等三十年，硬件才追得上他的想象力。

再读这篇论文的第 4 节「Rough Sketches of the Working of the Network」，福岛用几段散文式的语言描述了 A 字识别的完整过程：Layer S1 检测 A 的斜线，Layer C1 允许斜线位置轻微游移，Layer S2 检测斜线的组合，Layer C2 再次允许游移……层层递进，直到最深处一个 grandmother cell 独立响应 A。这段描述几乎可以逐字放进任何一本 CNN 教科书的第一章。

第三个让我停下来的地方：福岛在结尾写道，同样的原理也可以用于**语音识别**——把耳蜗基底膜上的振动包络当作输入。这是 1980 年。三十六年后，WaveNet、Deep Speech 等系统都验证了这个预言。

## 小红书写作备忘

### Hook 素材
1. 「1980 年，日本 NHK 广播研究所的一位工程师，用 $16\times16$ 的光感受阵列和 24 个『cell-plane』，写下了 CNN 全部要素的第一份蓝图。」
2. 「三十年后 AlexNet 让世界震惊，而它的祖父在 1980 年就诞生了——只是它的作者当时孤身一人。」
3. 「Neocognitron 论文的最后一段有一句话：『无法做超过 24 层每层的实验，因为我们的计算机内存不够』——1980 年的算力还追不上福岛的想象。」

### 核心 Insight（一句话）
福岛借鉴 Hubel-Wiesel 的视觉皮层层级，用 **S-cell（特征检测）+ C-cell（位置容忍）+ 权重共享 + 级联加深** 四件套，在 1980 年就把「局部感受野—卷积—池化—深层堆叠」这四条 CNN 支柱一次性写全。

### 自查重点
1. **不夸大发明归属**：福岛提出了 CNN 的架构原型，但**反向传播的引入是 LeCun 1989 的功劳**。福岛用的是竞争式的「代表元」无监督学习，不是 SGD。
2. **ReLU 归属**：福岛在文中直接使用了 $\varphi[x] = \max(x, 0)$，这一函数他 1969 年就用过（作为「analog threshold element」）。但「ReLU」这个名字是 2010 年后由 Nair & Hinton 等人推广的。表述要谨慎：「使用了与 ReLU 等价的非线性」而非「首次提出 ReLU」。
3. **与 Hubel-Wiesel 关系**：文中明确说明是「suggested by」而非「严格实现」，福岛承认原始 H-W 层级并不完全正确。
4. **准确的机构名**：NHK Broadcasting Science Research Laboratories（不能简写为「NHK 电视台」）。
5. **准确年份**：Cognitron 1975，Neocognitron 首次发表 1979（IJCAI），期刊版本 1980（Biological Cybernetics）——这三个日期不能混淆。
6. **不误用「卷积」一词**：福岛在 1980 年论文中**没有用 "convolution" 这个术语**，只用了 "cell-plane" 与 "weight sharing" 等价的表述。可以说「这在数学上等价于卷积运算」，而不是「福岛提出了卷积」。
7. **Bower Award 年份**：2020 年颁发（不是 2021）。

### 动态 Hashtags
- #卷积神经网络 #深度学习 #视觉皮层
