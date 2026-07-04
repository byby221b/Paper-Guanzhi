# 精读报告 #22: Semantic Memory

## 元信息

- 标题：Semantic Memory
- 作者：M. Ross Quillian（Carnegie Institute of Technology，现 Carnegie Mellon University）
- 发表：
  · 原始版本：博士论文，Carnegie Institute of Technology, October 1966（AFCRL-66-189 技术报告）
  · 广泛流传版：M. Minsky (ed.), *Semantic Information Processing*, pp. 216–270, MIT Press, 1968（部分重印）
- 原文链接：DTIC AD0641671（1966 版本）; 1968 书章节收录于 Minsky 编著
- 精读日期：2026-07-03
- 对应小红书期号：#22
- 备注：原始 1966 博士论文 PDF 因 DTIC 服务器不可达未能下载。本报告基于 Jim Davies 的详细摘要、Nilsson《The Quest for AI》§6.3 关于 Quillian 的讨论、以及 Collins & Quillian (1969) 等二手/后续文献撰写。

## 作者背景

### M. Ross Quillian（1931–?）

- 发表时身份：Carnegie Institute of Technology（后更名为 Carnegie Mellon University）博士研究生
- 师承：博士导师为 Herbert A. Simon（1978 年诺贝尔经济学奖、1975 年图灵奖得主），Quillian 与 Allen Newell、Simon 的其他学生（如 Edward Feigenbaum）同属 CMU 早期 AI 群体
- 此前工作：Quillian 的背景跨越心理学与计算机科学，对人类语义记忆的组织方式有浓厚兴趣
- 后续轨迹：
  · 1966 年博士毕业后加入 Bolt Beranek and Newman（BBN）
  · 1969 年发表 "The Teachable Language Comprehender"（TLC），展示语义网络在自然语言理解中的应用
  · 与 Allan M. Collins 合作发表 "Retrieval Time from Semantic Memory" (1969)——以心理实验验证语义网络的层次结构假说
  · Collins & Quillian 的合作催生了认知科学中「语义记忆」研究范式
  · 后来相对低调，但其 1966/1968 的语义网络框架影响深远

### 学术生态：Simon 的「认知建模」传统

Simon 和 Newell 在 Carnegie Mellon 建立了一种独特的研究风格：用计算机程序作为人类认知过程的理论模型。Logic Theorist (1956)、GPS (1957) 都是这一传统的产物。Quillian 的工作延续了这条脉络——他的「语义记忆」不只是一个 AI 系统，更是一个关于人类如何在大脑中存储和检索词义的认知理论。

## 历史语境

### 当时的学术主流

1966 年的 AI 知识表示方法主要有三种路径：

1. **逻辑方法**：McCarthy 的一阶逻辑 + 情景演算（处理事实性知识，但不太适合表示词义的关联网络）
2. **列表结构**：LISP 列表作为通用数据结构（如 Raphael 的 SIR 系统，1964），用属性列表存储知识
3. **过程式方法**：将知识嵌入程序流程中（如 GPS），不可言说

对「语义」的表示尚无统一框架。人们知道词与词之间有联系，但没有一个计算模型说清楚：「词义」在计算机中应当是什么数据结构？

### 待解决的核心问题

Quillian 问了一个极为根本的问题：

**What constitutes a reasonable view of how semantic information is organized within a person's memory?** （什么是语义信息在人类记忆中合理的组织方式？）

更具操作性地说：什么样的表示格式可以让词的「意义」被存储，使得类人的语义操作（比较、对比、消歧）成为可能？

### 同时期的相关工作

- Raphael (1964): SIR (Semantic Information Retrieval)——用属性-值对的列表处理简单语义关系（是 Quillian 的重要前驱，但 SIR 没有「spreading activation」的动态检索机制）
- Masterman (1961): Cambridge Language Research Unit 的语义网络在翻译中的应用（被 Quillian 引用为更早的网络形式）
- Newell & Simon 的 GPS 与 Logic Theorist（过程式 vs. 声明式的分歧）

### 直接前驱

1. Raphael (1964): *SIR*——最早的语义信息检索系统，用固定的关系类型
2. Masterman (1961): 语义网络在机器翻译中的雏形
3. Simon & Feigenbaum (1964): EPAM (Elementary Perceiver and Memorizer)——另一种记忆组织模型
4. 哲学/语言学传统：Wittgenstein「家族相似」、Carnap「意义外延」

## 问题形式化

### 问题定义

设计一种数据结构 $\mathcal{M}$ 和操作于其上的算法，使得：
- $\mathcal{M}$ 能表示英文单词的「意义」
- 给定两个词 $w_1, w_2 \in \mathcal{M}$，能自动产生一段比较两者关系的英文描述
- 给定一个歧义词 $w$ 和其上下文 $C$，能自动选择 $w$ 的正确义项

### 输入与输出

- 输入 1：两个英文单词 $w_1, w_2$ → 输出：英文语句描述两者的语义关系
- 输入 2：一个含歧义词的句子 → 输出：正确义项的选择

### 目标 / 评价准则

Quillian 以「心理学上的合理性」（psychological plausibility）为评价标准——模型的行为应当与人类在语义检索任务上的表现定性一致。没有严格的数值 metric。

## 核心方法

### 直觉

Quillian 的核心洞见：**词的意义不是一个「定义」，而是它在一个关联网络中的「位置」——它与其他词通过哪些关系相连、以及从它出发能「走到」哪些其他词。** 就像字典里每个词的释义都是用其他词写成的，整个语义记忆是一个自指的巨大网络。

具体来说：
1. 每个词概念是一个**节点（node）**
2. 节点之间由**带标签的有向链接（labeled directed links）**相连
3. 同一个词可能有多个义项，每个义项占据一个独立的**平面（plane）**
4. 词义的全部内容等于从该节点出发能到达的**整个子网络**

### 形式化描述

#### 节点类型

- **Type node（类型节点）**：一个词概念本身。它通过向外的链接指向构成其定义的其他词。
- **Token node（实例节点）**：指向某个 type node 的引用。多个 token 可指向同一个 type（例如 "water" 和 "agua" 指向同一概念）。

#### 链接分类（6 种）

1. 上位/下位（subclass-superclass）
2. 修饰（modification，形容词/副词对名词的修饰关系）
3. 析取（disjunction，如 earth/air/fire/water）
4. 合取（conjunction，组合多重修饰）
5. & 6. 两种开放式关系链接（通过一个「关系概念」连接两个词）

每条链接还可携带**强度标签（intensity tag）**——9 个等级的数值标注，对应 "a", "very", "not", "six" 等程度/数量词。

#### 平面（Planes）

一个词的每个义项占据一个独立的 plane。例如 "plant" 有三个平面：
- Plane 1：生物体（链接到 LIVE, LEAF, FOOD, AIR, WATER, EARTH）
- Plane 2：工厂（链接到 PEOPLE, PROCESS, INDUSTRY）
- Plane 3：动词「种植」

#### 参数符号（S, D, M）

用于句子理解时填充关系槽位：
- **S**（subject）：与当前词作主语相关的词
- **D**（direct object）：与当前词作宾语相关的词
- **M**（modifier）：当前词直接修饰的对象

### 关键算法：Spreading Activation（扩散激活）

给定两个词 $w_1, w_2$，比较其意义的算法：

```
Compare(w_1, w_2):
    从 w_1 和 w_2 各自的 type node 出发
    沿链接向外「广播」激活信号
    每个被激活的节点标记：
        (a) 激活源（w_1 或 w_2）
        (b) 将激活传递给它的前驱节点（用于回溯路径）
    当某个节点被 w_1 和 w_2 双方同时激活（intersection）时停止
    该交汇节点即为两词的「语义桥梁」
    沿回溯路径生成自然语言描述
```

例如：Compare("cry", "comfort") → 两条激活波在 "sad" 相交 → 输出："to cry is to make a sad sound, and to comfort is to make something less sad"

### 消歧算法

给定句子 "After the strike, the president sent him away"：
- "strike" 有多个 plane（劳资纠纷/棒球/军事打击）
- 从句中其他词（"president"）出发的激活，最先到达「劳资纠纷」plane
- 因此选择该义项

### 与前人方法的本质区别

| 维度 | SIR (Raphael 1964) | Quillian Semantic Memory (1966) |
|------|---------------------|--------------------------------|
| 关系类型 | 预定义、固定（is-a, has, belongs-to 等几种） | 六类通用链接 + 开放式关系 |
| 表示粒度 | 属性-值对 | 连通子图（plane） |
| 动态检索 | 模式匹配（pattern matching） | Spreading activation（扩散激活）|
| 歧义处理 | 不支持 | 多 plane + 上下文驱动消歧 |
| 心理学主张 | 无 | 明确宣称为人类记忆的模型 |

## 关键公式推导

### 概念 1：语义距离与层级检索时间

Quillian 本人未给出形式化的时间复杂度公式，但他的模型隐含一个核心预测：

**若两个概念之间的最短路径经过 $k$ 个中间节点，则检索（验证两者关系）所需的认知处理时间应与 $k$ 正相关。**

这一预测由 Collins & Quillian (1969) 实验验证：

- "A canary is a bird"（距离 1）→ 反应时约 1310 ms
- "A canary is an animal"（距离 2，canary → bird → animal）→ 反应时约 1380 ms
- "A canary has skin"（属性在 animal 节点，距离 2）→ 反应时约 1470 ms

**直觉理解：**

这不是一条数学定理，而是一个**可验证的心理学预测**——如果语义记忆真的如 Quillian 所描述那样以层次网络存储，则距离远的属性需要「传播」更多步才能到达，因此需要更多时间。这种「从表示结构直接推导可观测行为」的研究风格，是 Simon-Newell 传统的典型标志。

### 概念 2：经济原则（Cognitive Economy）

Quillian 的设计原则：**记忆中不存储任何可从更基本事实推导出的信息。**

例如：
- "bird → has skin" 不需要在 bird 节点存储，因为可从 "bird → is-a → animal" 和 "animal → has skin" 推导
- 只有 bird 特有的属性（"can fly", "has feathers"）存储在 bird 节点本身

这一原则减少了存储冗余，但后来被心理实验部分否证——人类确实会直接存储一些「可推导」的常用事实（如「鸟有翅膀」），因为它们被访问得足够频繁以至于形成了直接路径。

## 实验分析

### Quillian (1966/1968) 本身的实验

Quillian 实现了一个工作系统（在 IPL 和某种列表处理语言中），能：
1. 给定两个词，通过 spreading activation 找到交汇点，生成比较描述（简单英文输出）
2. 对 19 个歧义词进行消歧，**正确率 12/19（约 63%）**

### Collins & Quillian (1969) 的心理学验证

- 设计：被试判断「A canary is a bird」「A canary has skin」等语句真假
- 关键发现：反应时间随语义层级距离线性增加——直接支持了 Quillian 的层次化表示假说
- 意义：首次以严格的心理物理实验手段验证了一种 AI 知识表示的认知合理性

### 实验设计评价

- **优点**：Quillian 的系统虽小，却完整展示了「从表示到行为」的完整链路；Collins & Quillian 的后续实验提供了独立验证
- **不足**：
  · Quillian 系统的词库极小（文献中提及约 20 个词的手工编码网络）
  · 消歧实验只有 19 个用例，样本过小
  · 系统的英文输出相当粗糙

## 局限性

### 作者自述

1. **参数符号 S/D/M 过于粗糙**：例如 "swarm" 的主语（S）在 "Bees swarm" 中是施动者，但在 "The garden swarms with bees" 中 garden 更像处所——S 无法区分这些语义角色差异
2. **系统规模极小**：仅编码了约 20 个词的语义网络，尚无法扩展到大词表

### 后续批评

1. **认知经济原则过于极端**：Anderson (1983, ACT-R) 和其他研究者的实验表明，人类确实冗余存储高频推导结果（如「鸟有翅膀」）——纯层次化检索不能解释所有反应时数据
2. **层次化假设的反例**：Conrad (1972) 发现，当控制了属性的「联想频率」后，层级效应部分消失——提示反应时差异可能反映联想强度而非纯粹的网络距离
3. **缺乏学习机制**：Quillian 的网络是静态手工编码的，没有从文本中自动习得语义关系的方法
4. **缺乏量化推理**：无法处理程度、概率、不确定性
5. **Collins & Loftus (1975) 的扩展与修正**：放弃了严格的层次结构，改用更灵活的「加权连接」——spreading activation 被保留，但网络拓扑不再是树状的

### 假设检验

Quillian 模型的两个核心假设：
- **语义存储的非冗余性**：部分失效——高频事实会被冗余存储
- **层级结构**：过于刚性——真实的人类语义记忆更像「小世界网络」而非树

但「spreading activation as retrieval mechanism」这一核心机制被广泛保留下来。

## 后续影响

### 直接后继

1. **Collins & Quillian (1969)**: "Retrieval Time from Semantic Memory"——实验验证
2. **Quillian (1969)**: "The Teachable Language Comprehender (TLC)"——将语义网络应用于自然语言理解
3. **Collins & Loftus (1975)**: "A Spreading-Activation Theory of Semantic Processing"——放弃严格层次，改用加权网络
4. **Anderson (1976, 1983)**: ACT/ACT-R——将 spreading activation 吸收进更大的认知架构
5. **Brachman (1979)**: KL-ONE——形式化语义网络中的 is-a 关系与继承机制

### 开创的方向

- **语义网络**（Semantic Networks）作为 AI 知识表示的一大范式（与逻辑方法、框架并列）
- **Spreading activation** 成为认知心理学和信息检索中的基础机制
- 启发了后来的 **ontology**、**知识图谱**（Knowledge Graph）、**WordNet** 等大规模语义资源

### 当代回响

- **WordNet**（1985–）：Miller 的层次化词义网络，显然是 Quillian 思想的大规模工程实现
- **Google Knowledge Graph**（2012）："Things, not strings"——本质上是 Quillian 1966 年愿景在 web 规模上的重演
- **Word2Vec / GloVe / BERT embedding**：虽然技术实现完全不同（连续向量 vs. 符号网络），但核心直觉一致——「词义由其语境（邻居）决定」——这正是 Quillian 的分布式语义观
- **Graph Neural Networks + Knowledge Graphs**：2020 年代的 KG embedding 研究将 Quillian 的符号网络与深度学习融合

### 引用统计

- 1968 年 Minsky 编著 *Semantic Information Processing* 整本书在 Google Scholar 约 6,000 次引用
- Quillian 1968 章节单独被引用约 2,000–3,000 次（各来源估计有差异）
- Collins & Quillian 1969 实验论文约 3,800 次引用
- 1966 年博士论文/技术报告约 500–700 次引用

## 个人笔记

读 Quillian 这篇论文，我最大的感触是：**他在 1966 年就完整地想明白了「词义即位置」这件事。**

今天我们用 Word2Vec / BERT 把词映射到连续空间中的向量，然后说「语义相似的词，向量距离近」。Quillian 在没有梯度下降、没有大规模语料、没有 GPU 的年代，用纯粹的概念推理得出了同一个结论——**意义不是内禀的属性，而是关系的总和**。只不过他的关系是离散的符号链接，而今天的关系编码在高维连续空间中。

另一个让我印象深刻的设计决策是「平面」（plane）的引入。多义词的处理至今是 NLP 的难题（BERT 通过上下文向量给出了一种方案），而 Quillian 在 1966 年就给出了一个清晰的建模：每个义项一个独立子图，消歧等价于选择激活最强的那个子图。这种建模的清晰度——虽然工程上不可扩展——在概念上至今没有被超越。

最后，让我略感遗憾的是 Quillian 后来的相对沉寂。Collins 成了认知心理学的大人物，发展出了一整套 spreading activation 的理论体系；而 Quillian 似乎在 1970 年代之后逐渐退出了学术前沿。他的洞见通过 Collins-Quillian 的合作获得了实验验证和广泛传播，但他本人的贡献有时在引用中被 Collins 的名字所遮蔽。学术史上这种「创造者被后来的推广者遮蔽」的例子并不少见。

## 小红书写作备忘

### Hook 素材

1. **「字典即语义网络」的直觉**：字典里每个词的释义都是用其他词写成的——Quillian 说，人类的语义记忆也是这样工作的
2. **1966 年的 Word2Vec**：比 Mikolov 早了 47 年，Quillian 就提出了「词义 = 在网络中的位置」——分布式语义观的最早形式化
3. **一个 63% 正确率的消歧系统**：在 1966 年，没有训练数据、没有神经网络、只有 20 个手工编码的词——这已经是当时最先进的语义消歧

### 核心 Insight（一句话）

**词的意义不是一条定义，而是它在整个语义网络中与其他词的全部关系——Quillian 用这一洞见开创了语义网络、spreading activation 和知识图谱的整个谱系。**

### 自查重点

1. Quillian 的导师是 **Herbert Simon**（不是 Allen Newell——虽然两者密切合作）
2. 论文年份：**1966 年博士论文 / 1968 年 Minsky 编著中的章节重印**——通常引用 1968 版本，但工作本身完成于 1966
3. **不要夸大为「发明了语义网络」**——Masterman (1961) 有更早的语义网络形式；Quillian 的贡献是第一个**完整的语义记忆模型**，含 spreading activation 检索机制
4. Collins-Quillian 1969 的实验是**独立发表**的，不是本文的一部分——不要混淆
5. **认知经济原则**后来被证明并不完全正确——不要无条件赞美
6. Quillian 不是心理学家，而是 CMU 计算机系的博士生——但其工作对认知心理学影响深远

### 动态 Hashtags

- #语义网络
- #知识图谱
- #认知科学
