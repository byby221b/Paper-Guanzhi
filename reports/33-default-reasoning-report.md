# 精读报告 #33：A Logic for Default Reasoning（Reiter, 1980）

## 元信息

- 标题：A Logic for Default Reasoning
- 作者：Raymond Reiter（雷蒙德·赖特，Department of Computer Science, University of British Columbia, Vancouver, B.C., Canada）
- 发表：Artificial Intelligence, Vol. 13, No. 1–2, pp. 81–132 (1980), North-Holland Publishing Company
- 荐稿人（Recommended by）：Patrick J. Hayes
- 原文链接：<http://www.horty.umiacs.io/courses/readings/reiter-default-1980.pdf>（DOI: 10.1016/0004-3702(80)90014-4）
- 精读日期：2026-07-31
- 对应小红书期号：#33

## 作者背景

### Raymond Reiter 雷蒙德·赖特（1939–2002）

- **发表时身份**：论文署名机构为不列颠哥伦比亚大学（University of British Columbia, UBC）计算机科学系。赖特 1970 年代在 UBC 任教，本文属于他 UBC 时期的代表作。此后（1980 年代初）他转往多伦多大学（University of Toronto），并在那里度过其学术生涯的主要阶段。
- **教育背景**：1967 年获密歇根大学（University of Michigan）博士学位，博士论文题为《A Study of a Model for Parallel Computations》，导师为 Harvey Garner 与 Richard M. Karp（Karp 后来因计算复杂性理论获图灵奖）。
- **此前工作**：
  - **Closed-World Databases（1978）**：赖特提出「封闭世界假设」（Closed-World Assumption, CWA）的形式化——数据库中未被证明为真的事实默认为假。这一思想是本文 default logic 的直接思想来源之一（论文第 1.1.3 节把 CWA 表述为一条 default 规则）。
  - **On Reasoning by Default（1978, TINLAP）**：本文多数例子（鸟会飞、frame 问题等）「adapted from those in Reiter (1978a)」，即脱胎于这篇更早的会议论文。
- **后续轨迹**：
  - **Model-based Diagnosis（1987）**：《A Theory of Diagnosis from First Principles》，把基于一致性的诊断建立在逻辑之上，是故障诊断领域的奠基文献之一。
  - **Situation Calculus 与 Knowledge in Action（2001）**：晚年系统重建了 McCarthy-Hayes 的情境演算，处理 frame problem，出版专著《Knowledge in Action》。
  - **主要荣誉**：IJCAI Award for Research Excellence（1993）；ACM Fellow；AAAI Fellow；加拿大皇家学会院士（FRSC）。他被广泛视为**非单调推理（non-monotonic reasoning）领域的奠基人之一**。
  - **师承延续**：其博士生包括 Sheila McIlraith（1997 年博士，诊断与语义 Web 服务方向）。

## 历史语境

### 当时的学术主流

1970 年代末的知识表示（Knowledge Representation, KR）研究由两股力量主导：

- **一阶逻辑（First-Order Logic, FOL）作为表示语言**：McCarthy 自 1958 年起倡导用逻辑表示常识知识，情境演算（McCarthy & Hayes, 1969）是其代表。但 FOL 是**单调的**——加入新前提不会使原有定理失效，这与人类常识推理中「随时可被推翻」的信念格格不入。
- **框架/程序化表示**：Minsky 的 Frames（1975）、以及受其启发的知识表示语言 FRL（Roberts & Goldstein, 1977）与 KRL（Bobrow & Winograd, 1977），都内建了「默认槽值」（default slot value）机制。但这些机制停留在程序实现层面，缺乏清晰的逻辑语义。

### 待解决的核心问题

如何用严格的形式系统刻画「在没有相反信息时，就假设……」这一类推理？具体困难在于：

1. **非单调性（Non-monotonicity）**：FOL 无法表达「除非有反证，否则相信 P」，因为一旦相信 P，再多的前提也不能推翻它。
2. **一致性检验的循环性**：默认规则「若 α 成立且 β 与已知一致，则相信 w」中的「一致」到底是「与什么一致」？答案里包含了「其他默认规则推出的信念」，从而形成自指。
3. **信念修正（Belief Revision）**：一旦默认假设被后续观察推翻，依赖它的信念该如何撤回？

### 同时期的相关工作（本文明确对话的对象）

- **McDermott & Doyle (1978)《Non-Monotonic Logic I》**：用模态算子 M（「可假设」）构造非单调逻辑。赖特在第 2 节详细对比：default logic 的 extension 对应 NML 的 fixed point，但二者算子定义不同，会给出不同结果（并存在 default 有 extension 而对应 NML 无 fixed point 的例子）。**关系：竞争/平行**。
- **Doyle (1978) Truth Maintenance System（TMS）**：信念修正的启发式实现。赖特把 TMS 视为其第 6 节信念修正理论的工程对应物。**关系：互补**。
- **Sandewall (1972)**：提出 UNLESS 模态算子处理 frame 问题，是本文 frame default 的直接前身。
- **McCarthy 的 Circumscription（限定，1980 同年）**：另一条非单调推理路线（后续本系列 #37）。**关系：竞争/平行**。

### 直接前驱

- Reiter, R. "On closed world data bases" (1978)——CWA。
- Reiter, R. "On reasoning by default" (TINLAP-2, 1978)——本文例子的来源。
- Minsky, M. "A framework for representing knowledge" (1975)——frames 与默认值（本系列 #24）。
- McDermott, D. & Doyle, J. "Non-monotonic logic I" (1978, 预印本)——本文的主要对话对象。
- Sandewall, E. "An approach to the frame problem, and its implementation" (1972)。

## 问题形式化

### 问题定义

经典一阶逻辑对一组前提 $S$ 只能推出被逻辑蕴含的公式 $\mathrm{Th}_L(S)$。default logic 要在此之上补充「可默认成立」的信念。

一条**默认规则（default）**写作：

$$\frac{\alpha(\mathbf{x}) : M\beta_1(\mathbf{x}), \ldots, M\beta_m(\mathbf{x})}{w(\mathbf{x})}$$

- $\alpha(\mathbf{x})$：**前提（prerequisite）**——须先被相信；
- $\beta_1, \ldots, \beta_m$：**辩护（justifications）**——$M\beta_i$ 读作「$\beta_i$ 可一致地假设」（即 $\neg\beta_i$ 不在当前信念集里）；
- $w(\mathbf{x})$：**结论（consequent）**——满足条件后可加入的信念。

平文本记法：$\alpha(\mathbf{x}) : M\beta_1, \ldots, M\beta_m / w(\mathbf{x})$。

一个**默认理论（default theory）**是二元组 $\Delta = (D, W)$，其中 $D$ 是默认规则集，$W$ 是一阶闭公式集（硬事实）。若 $D$ 中每条规则都不含自由变量，则称 $\Delta$ 为**闭的（closed）**。

### 输入与输出

- **输入**：默认理论 $(D, W)$，以及一个待判定的闭公式 $\beta$。
- **输出**：判定「$\beta$ 是否可被相信」——即是否存在一个由 $D$ 诱导出的 **extension（扩张）** $E$ 使得 $\beta \in E$。

### 目标 / 评价准则

关键概念是 **extension**：对不完全的一阶理论 $W$，用默认规则「填补空白」后得到的一个自洽信念集。一个理论可能有 0 个、1 个或多个 extension，每个 extension 代表一种融贯的世界观。赖特的立场是：默认推理的目的是**确定并停留在某一个 extension 中推理**，直到证据迫使切换，而非（像 McDermott-Doyle 那样）取所有 fixed point 的交集作为「定理」。

## 核心方法

### 直觉

赖特用三句话可概括其核心洞见：

1. **默认规则是元规则（meta-rules）**：它们不是关于世界的断言，而是「如何扩充这个不完全一阶理论」的指令。
2. **「一致地假设」用不动点刻画**：信念集 $E$ 必须满足——若 $\alpha \in E$ 且诸 $\neg\beta_i \notin E$，则 $w \in E$。这里 $E$ 出现在自身的定义两侧，故用**不动点**而非直接构造来定义。
3. **正规默认（normal defaults）驯服了理论**：把辩护限制为与结论相同（$\alpha : Mw / w$），就能保证 extension 恒存在，并获得一套「关于默认局部化」的证明论。

### 形式化描述

**扩张的定义（Definition 1，算子 $\Gamma$）**：给定闭默认理论 $\Delta = (D, W)$，对任意闭公式集 $S$，令 $\Gamma(S)$ 是满足以下三条的**最小**集合：

- **D1**：$W \subseteq \Gamma(S)$；
- **D2**：$\mathrm{Th}_L(\Gamma(S)) = \Gamma(S)$（演绎封闭）；
- **D3**：若 $(\alpha : M\beta_1, \ldots, M\beta_m / w) \in D$，$\alpha \in \Gamma(S)$，且 $\neg\beta_1, \ldots, \neg\beta_m \notin S$，则 $w \in \Gamma(S)$。

$E$ 是 $\Delta$ 的 **extension**，当且仅当 $\Gamma(E) = E$（即 $E$ 是 $\Gamma$ 的不动点）。

注意 **D3 的一致性检验用 $S$，而结论的加入用 $\Gamma(S)$**——这种「检验用外层、生成用内层」的错位，正是非单调性的技术根源。

### 关键定理与证明思路

- **Theorem 2.1（迭代刻画）**：$E$ 是 extension 当且仅当 $E = \bigcup_{i=0}^{\infty} E_i$，其中 $E_0 = W$，
$$E_{i+1} = \mathrm{Th}_L(E_i) \cup \{ w \mid (\alpha : M\beta_1,\ldots,M\beta_m / w)\in D,\ \alpha \in E_i,\ \neg\beta_1,\ldots,\neg\beta_m \notin E \}.$$
  **关键微妙处**：生成 $E_{i+1}$ 时，一致性检验用的是**最终的 $E$**（而非 $E_i$）。所以这不是一个可直接执行的构造算法，而是一个「先猜 $E$ 再验证」的定义。证明用双向归纳：$(\Leftarrow)$ 证 $E_i \subseteq E$；$(\Rightarrow)$ 证 $E_i \subseteq \Gamma(E)$。
- **Theorem 2.4（极小性）**：若 extension $E \subseteq F$，则 $E = F$——extension 之间不存在真包含，各自极小。
- **Corollary 2.2**：闭默认理论有不一致 extension 当且仅当 $W$ 不一致。
- **Theorem 3.1（正规理论存在性）**：**每个闭正规默认理论都有 extension。** 证明是构造性的：取 $E_0 = W$，逐层取「与当前一致的、由已触发默认结论构成的极大集」$T_i$，令 $E_{i+1} = \mathrm{Th}_L(E_i)\cup T_i$。这是本文最重要的正面结果。
- **Theorem 3.2（半单调性 Semi-monotonicity）**：正规默认理论中，若 $D' \subseteq D$，$E'$ 是 $(D', W)$ 的 extension，则 $(D, W)$ 有 extension $E \supseteq E'$ 且 $GD(E',A') \subseteq GD(E,A)$。**意义**：添加正规默认不会摧毁已有 extension，这使得**关于默认的局部证明论**成为可能（证明某信念时无需检查全部默认）。
- **Theorem 3.3（正交性 Orthogonality）**：正规理论的两个不同 extension $E, F$，其并 $E \cup F$ 必不一致——不能同时持有两个 extension 的信念。
- **Theorem 4.9（不可半判定）**：闭正规默认理论的「extension 成员问题」**不是半可判定的**（信念集不是递归可枚举的）。这是本文最重要的负面结果。

### 与前人方法的本质区别

| 方法 | 语义基础 | 是否非单调 | 表达机制 | 与本文关系 |
|------|---------|-----------|---------|-----------|
| 一阶逻辑（FOL） | Tarski 语义 | 否 | 蕴含 | 被扩充的底座 |
| Frames / KRL (1975–77) | 程序过程 | 是（隐式） | 默认槽值 | 缺形式语义，本文为其提供理论 |
| McDermott-Doyle NML (1978) | 模态不动点 | 是 | 模态算子 M | 竞争：extension ≠ fixed point |
| McCarthy Circumscription (1980) | 极小模型 | 是 | 二阶极小化 | 竞争/平行 |
| **Default Logic (1980)** | **不动点 extension** | **是** | **元规则 default** | 本文 |

## 关键公式推导

### 公式 1：默认规则的触发条件（Definition 1 的 D3）

**原文表述**：若 $(\alpha : M\beta_1, \ldots, M\beta_m / w) \in D$，$\alpha \in E$，且 $\neg\beta_1, \ldots, \neg\beta_m \notin E$，则 $w \in E$。

**逐步推导（以「Tweety 会飞」为例）**：

Step 1：把「大多数鸟会飞」写成正规默认 $\dfrac{\mathrm{BIRD}(x) : M\,\mathrm{FLY}(x)}{\mathrm{FLY}(x)}$。
— 依据：论文式 (1.1)，「若 $x$ 是鸟，且可一致地假设 $x$ 会飞，则相信 $x$ 会飞」。

Step 2：设 $W = \{\mathrm{BIRD}(\text{tweety})\}$，$D$ 含上面这条 default 的实例化 $\mathrm{BIRD}(\text{tweety}) : M\,\mathrm{FLY}(\text{tweety}) / \mathrm{FLY}(\text{tweety})$。
— 依据：把默认按常量代入（开默认的实例化）。

Step 3：检验 D3。前提 $\alpha = \mathrm{BIRD}(\text{tweety}) \in E$（因 $W \subseteq E$）；辩护 $\beta = \mathrm{FLY}(\text{tweety})$，需 $\neg\mathrm{FLY}(\text{tweety}) \notin E$。由于 $W$ 中无任何信息蕴含 tweety 不会飞，$\neg\mathrm{FLY}(\text{tweety})$ 不在 $E$ 中，检验通过。
— 依据：一致性检验 $\neg\beta \notin E$。

Step 4：故 $\mathrm{FLY}(\text{tweety}) \in E$。此时唯一 extension 为 $E = \mathrm{Th}_L(\{\mathrm{BIRD}(\text{tweety}), \mathrm{FLY}(\text{tweety})\})$。
— 依据：Theorem 3.1（正规理论存在 extension）+ Corollary 3.4（唯一性）。

Step 5（非单调性显现）：若追加 $W' = W \cup \{\mathrm{PENGUIN}(\text{tweety}),\ \forall x.\,\mathrm{PENGUIN}(x)\Rightarrow\neg\mathrm{FLY}(x)\}$，则 $\neg\mathrm{FLY}(\text{tweety}) \in E$，D3 的一致性检验失败，$\mathrm{FLY}(\text{tweety})$ **不再**被相信。
— 依据：新事实使辩护 $M\,\mathrm{FLY}$ 落空——这正是「加前提反而失结论」的非单调现象。

**直觉理解**：D3 说的是「先信前提、再看结论的否定会不会已经在信念里；若没有，就大胆相信结论」。默认规则因此像一个**谨慎的赌注**：只要没被反驳，就下注；一旦被反驳，就撤注。

### 公式 2：封闭世界假设作为一条默认（第 1.1.3 节）

**原文表述**：对 $n$ 元关系 $R$，闭世界默认为
$$\frac{\ :\ M\,\neg R(x_1, \ldots, x_n)}{\neg R(x_1, \ldots, x_n)}.$$

**逐步推导**：

Step 1：这是一条**前提为空（$\alpha = \text{true}$）**的正规默认，辩护与结论都是 $\neg R(\bar x)$。
— 依据：正规默认形式 $\alpha : Mw / w$，此处 $\alpha$ 恒真。

Step 2：触发条件退化为「只要 $\neg R(\bar x)$ 与当前信念一致（即 $R(\bar x)$ 不可证），就相信 $\neg R(\bar x)$」。
— 依据：D3，$\neg\beta = R(\bar x) \notin E$。

Step 3：对数据库中所有关系 $R$ 施加此默认，即得「凡不能证明为真者，皆假」的 CWA。
— 依据：论文指出这正是 Reiter (1978b) 封闭世界数据库的默认逻辑重述。

**直觉理解**：数据库回答「AC113 航班是否连接温哥华与纽约？」时，若证不出连接，就答「否」。这一「查不到即为假」的日常直觉，被 default logic 精确地写成了一条空前提默认。论文还给出一个反直觉例子：对 $W = \{p \lor q\}$ 施加 $:\!M\neg p/\neg p$ 与 $:\!M\neg q/\neg q$，得到**两个** extension $\{p, \neg q\}$ 与 $\{\neg p, q\}$——CWA 在析取事实上会产生多重世界观。

## 实验分析

本文是**纯理论论文**，没有数据集、基线或量化实验。其「实证」以一系列**形式化例子**呈现，用于检验定义的合理性：

### 例子作为「实验」

- **Example 2.1–2.6**：展示 extension 的多样性——有唯一 extension（2.2）、两个（2.1, 2.3）、三个（2.4）、乃至**无 extension**（2.6：$D = \{\ :M A / \neg A\}$，$W = \emptyset$）。后者说明一般默认理论未必有解，直接催生了对 normal defaults 的研究。
- **Example 2.5**：不同 extension 可承诺**不同的本体（ontology）**——一个 extension 相信「存在某个个体」，另一个不信。这揭示默认推理可影响存在性判断。
- **Example 4.1 / 5.1–5.3**：演示 default proof 与 top-down（线性归结）default proof 的构造，以及证明如何「被阻塞」（P4 满足性检验失败）。

### 关键发现

- **正规默认几乎无所不包**：赖特直言「我不知道有任何自然出现的默认无法写成 $\alpha : Mw / w$ 的形式」——第 1 节所有例子皆是正规的。
- **可判定子类**：虽然一般 extension 成员问题不可半判定（Thm 4.9），但**命题情形**与**一元谓词（monadic）情形**是可判定的。

### 实验设计评价（对「例子驱动」方法论的评价）

- **优点**：每个定义都用极简例子立即检验其边界（尤其是「无 extension」「多 extension」的反例），使抽象定义可触可感；与线性归结定理证明器的接口（第 5 节）给出了可实现路径。
- **不足**：没有真实规模的知识库验证；正规默认虽覆盖面广，但**多个正规默认交互**时仍可能产生反直觉结果（后来 Reiter & Criscuolo 1981 专门研究「interacting defaults」）；满足性检验（P4）在实践中不可行，只能靠启发式近似。

## 论文的局限性

### 作者自述

- **一般理论可能无 extension**（Example 2.6），这「对一个通用的默认推理理论不太令人鼓舞」——正是这句自我批评促成了向 normal defaults 的收缩。
- **不可半判定性**（Theorem 4.9）：任何默认证明论都必须诉诸某种「本质上不可半判定的过程」（满足性检验）。赖特坦承这「迫使我们承认：任何默认的计算处理都必须带有启发式成分，并会偶尔导致错误信念」。
- **信念修正未完全解决**：第 6 节只给出「何时需要修正」的判据（共同 extension 的一致性条件，Theorem 6.1），而「如何修正」（该撤回哪些信念）被明确留给 Doyle 的 TMS，本文「不予处理」。

### 后续批评

- **正规默认仍不够**：Reiter & Criscuolo (1981)《On Interacting Defaults》指出，多个正规默认交互时会得到不该有的结论，需引入**半正规默认（semi-normal defaults）** $\alpha : M(\gamma \wedge w) / w$ 加以约束。
- **辩护的语义争议**：Łukaszewicz、Delgrande 等后来提出多种变体（constrained / justified default logic），因为原始定义在某些情形下会「遗忘」辩护，导致 extension 不含支撑它的辩护。
- **计算复杂性**：即便在命题情形可判定，Gottlob (1992) 证明其判定问题位于多项式层级第二层（$\Sigma_2^p$ / $\Pi_2^p$），实践代价高昂。

### 假设检验

- **「一个 extension 即一种世界观」的立场**：赖特选择在单一 extension 内推理，而非取交集。这一取舍在需要「skeptical（怀疑主义）」推理（只信所有 extension 共有的结论）时并不合适——后来的文献两种立场并存。
- **正交性（Thm 3.3）的代价**：不同 extension 互斥，意味着系统必须**承诺**某一世界观；一旦承诺错误，就要付出信念修正的代价。

## 后续影响

### 直接后继

- **Reiter & Criscuolo (1981)《On Interacting Defaults》**（IJCAI）：半正规默认。
- **McCarthy (1980, 1986) Circumscription**：与 default logic 并列的另一大非单调形式系统（本系列 #37）。
- **Reiter (1987)《A Theory of Diagnosis from First Principles》**：把默认/一致性思想用于故障诊断，赖特本人的重要延续。
- **Answer Set Programming（ASP）**：Gelfond & Lifschitz 的 stable model 语义（1988）与 default logic 有深刻对应，正规默认对应逻辑程序的规则——这是 default logic 在今日最活跃的技术后裔。

### 开创的方向

本文与 McCarthy 的 circumscription、McDermott-Doyle 的 NML 一道，**奠定了非单调推理（non-monotonic reasoning）这一子领域**。default logic 因其接近自然语言「除非……否则……」的形式、以及正规默认的良好性质，成为其中被教学和引用最多的形式系统之一。1980 年《Artificial Intelligence》第 13 卷是非单调逻辑的里程碑专号，本文是其中的核心篇章。

### 当代回响

- **知识表示教材的标准章节**：default logic 是几乎所有 KR 课程的必讲内容。
- **逻辑程序与 ASP**：现代 answer set 求解器（clingo、DLV）背后的稳定模型语义，可视为正规默认在逻辑程序上的落地；「否定即失败」（negation as failure）与 CWA-as-default 一脉相承。
- **常识推理与大模型时代的再审视**：在 LLM 主导常识推理的今天，default logic 提醒人们「可废止推理（defeasible reasoning）」有其严格的形式刻画；神经-符号（neuro-symbolic）研究重新关注如何让可学习系统具备「可被推翻的信念」这一结构。

### 引用统计

- Semantic Scholar 引用数：约 4,556（截至 2026-07，DOI 10.1016/0004-3702(80)90014-4）。
- Google Scholar 因收录口径不同，数字通常更高；两者不宜直接对齐比较。本文长期位居人工智能与知识表示领域被引最多的理论论文之列。

## 个人笔记

读这篇论文，最触动我的是它的**克制**。赖特本可以像 McDermott 和 Doyle 那样，构造一个尽可能一般的模态非单调逻辑；但他反其道而行，先给出一般定义（$\Gamma$ 不动点），随即在第 2.6 例中亲手举出「无 extension」的反例——等于承认这个一般框架并不好用。然后他做了一件更聪明的事：**收缩**。把辩护限制成与结论相同的「正规默认」，换来了三个漂亮的定理——存在性（3.1）、半单调性（3.2）、正交性（3.3）。这是一种「先承认理想框架的失败，再退守到可用的子类」的研究策略，非常值得学习。

第二个让我停下来的地方是 **Theorem 2.1 的自指**。定义 $E_{i+1}$ 时，一致性检验 $\neg\beta_i \notin E$ 用的是**最终的 $E$**，而不是当前的 $E_i$。第一次读会本能地想「这不是循环定义吗？」——但正是这个「向未来借答案」的结构，精确抓住了默认推理的本质：你现在敢不敢下注，取决于最终的信念全貌里有没有反证。赖特在脚注 3 特意提醒读者注意这个 $E$ 的出现，说明他自己也知道这是最容易被误读的一步。

第三处是 **Theorem 4.9 的哲学余味**。他证明了默认信念集不是递归可枚举的，然后写下一句近乎宿命论的话：「鉴于人类常识推理本就充满谬误，这或许已是我们所能期望的最好结果。」——把一个否定性的计算复杂性结果，翻转成对「人类式易错推理」的辩护。一个理论逻辑学家，在证明的尽头流露出对人类认知局限的体认，这种笔触在技术论文里很少见。

最后，开篇引用 Xenophanes（色诺芬尼）残篇的那段诗——「万物不过是一张猜测编织的网」——为整篇冷峻的形式化定下了一个谦卑的基调。赖特显然清楚：他形式化的不是「真理」，而是「在无知中如何合理地猜测」。

## 小红书写作备忘

### Hook 素材

1. 「一只叫 Tweety 的鸟——你只知道它是鸟，你会不会认为它能飞？经典逻辑无法回答，赖特在 1980 年为这个问题写下了一套逻辑。」
2. 「经典逻辑有个铁律：加入新前提，旧结论永不失效（单调性）。可人的常识恰恰相反——‘它会飞’这个念头，会被‘它是企鹅’一句推翻。赖特要形式化的，正是这种‘可被推翻’的推理。」
3. 「论文开篇引了两千五百年前色诺芬尼的诗：‘万物不过是一张猜测编织的网。’一篇满是定理的逻辑论文，却以对人类无知的谦卑起笔。」

### 核心 Insight（一句话）

默认逻辑把「在没有相反证据时就假设……」这一日常直觉，形式化为「元规则 + 不动点 extension」：默认规则不断言世界，而是指示如何**填补**不完全一阶理论的空白；一个 extension 就是一种自洽的、随时可能因新证据而被撤换的世界观。

### 自查重点

1. **发表机构**：本文署名是**不列颠哥伦比亚大学（UBC）**，不是多伦多大学。赖特是**之后**才去多伦多的。这一点极易写错。
2. **不夸大归属**：赖特是非单调推理的**奠基人之一**（与 McCarthy、McDermott、Doyle 并列），不宜写成「唯一创立者」。circumscription（McCarthy）与 NML（McDermott-Doyle）是同期并行的路线。
3. **正规默认的贡献边界**：存在性定理（Thm 3.1）只对 **normal / closed normal** 默认理论成立；一般默认理论可能**无 extension**（Example 2.6）。不能笼统说「默认逻辑保证有解」。
4. **不可半判定 ≠ 不可判定的日常混用**：Thm 4.9 说的是 extension 成员问题**不是半可判定**（信念不是 r.e.）；但命题与一元谓词情形**是可判定**的。表述要精确。
5. **M 算子的读法**：$M\beta$ 读作「$\beta$ 可一致地假设」（即 $\neg\beta$ 不在信念集内），不是模态逻辑里「可能」的标准语义——赖特刻意**回避了模态逻辑**，全程在一阶框架内工作。
6. **与 TMS / circumscription 的关系**：Doyle 的 TMS 是信念修正的工程实现（本文第 6 节的对应物）；circumscription 是**另一套**形式系统，别混为一谈。
7. **引用数口径**：只报 Semantic Scholar ≈ 4,556；不要把 Google Scholar 的更高数字与之硬性对齐。

### 动态 Hashtags

- #非单调推理 #知识表示 #逻辑与人工智能
