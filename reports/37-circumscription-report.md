# 精读报告 #37：Circumscription—A Form of Non-Monotonic Reasoning

## 元信息

- 标题：Circumscription—A Form of Non-Monotonic Reasoning
- 作者：John McCarthy（Computer Science Department, Stanford University）
- 发表：*Artificial Intelligence*, Volume 13, Issues 1–2（Special Issue on Non-Monotonic Logic）, April 1980, pp. 27–39
- DOI：10.1016/0004-3702(80)90011-9
- 原文链接：http://www-formal.stanford.edu/jmc/circumscription.pdf ｜ 作者说明页 http://www-formal.stanford.edu/jmc/circumscription.html
- 精读日期：2026-08-07
- 对应小红书期号：#37

**版本说明（重要）**：本次精读所用 PDF 为 McCarthy 本人网站上的重新排版本（页眉标注 1986，为 LaTeX 重排年份，正文内容即 1980 年论文）。该 PDF 末尾附有一篇《ADDENDUM: Circumscription and Other Nonmonotonic Formalisms》。据 DBLP 与 OpenAlex 的期刊条目，该附录作为独立条目刊于同一期的 pp. 171–172，即与正文 pp. 27–39 分列。McCarthy 自己的网页则称附录"不在已发表的论文中"——两说存在出入，本报告采用"附录与正文同期刊出但分列"的读法，并标记为存疑。

**作者本人的重要提示**（引自其网页，需要在任何引用中一并考虑）：

> "The formalism of this paper is substantially superseded by that of *Applications of circumscription to formalizing common sense* published in 1986. In particular the latter paper properly treats variables that are not minimized."

即：McCarthy 认为 1980 年的形式系统已在很大程度上被 1986 年那篇取代，尤其在"不被最小化的变元"的处理上。这一点对评价本文的历史地位至关重要——它是一个被作者本人明确标记为过渡形态的开创性工作。

---

## 作者背景

### John McCarthy（1927–2011）

- **发表时身份**：斯坦福大学计算机科学系教授（1962 年起为正教授）；斯坦福人工智能实验室（SAIL）主任，直至 1980 年该实验室并入计算机科学系。
- **学术出身**：1948 年加州理工学院数学学士，1951 年普林斯顿大学数学博士，学位论文《Projection Operators and Partial Differential Equations》（偏微分方程方向）。

  *考据说明*：数学谱系项目（Mathematics Genealogy Project, id 22145）记录其导师为 **Donald Clayton Spencer**。但 Lefschetz 的相关条目中又将 McCarthy 列为其学生，两处记载冲突。论文选题（投影算子与偏微分方程）与 Spencer 的研究方向吻合，与 Lefschetz 的代数拓扑不符。本报告采信 Spencer 为形式上的论文导师，并在小红书稿中回避这一细节。

- **此前的关键工作**：
  - 1955 年与 Minsky、Rochester、Shannon 共同起草《A Proposal for the Dartmouth Summer Research Project on Artificial Intelligence》（1955-08-31），其中"人工智能"一词首次公开使用（对应本系列 #10）。
  - 1958 年发明 LISP。
  - 1959 年《Programs with Common Sense》（Teddington 会议），提出用逻辑语句表示常识、由演绎决定行动的"Advice Taker"设想。本文第 1 节开篇即直接回溯到这篇。
  - 1969 年与 Patrick Hayes 合著《Some Philosophical Problems from the Standpoint of Artificial Intelligence》（*Machine Intelligence* 4），**框架问题（frame problem）在此文中被命名**。
  - 1977 年《Epistemological Problems of Artificial Intelligence》（IJCAI-5, pp. 1038–1044），**限制条件问题（qualification problem）在此文第 9 条中被命名**，并已用散文形式给出了极小模型（minimal model）与非单调性的核心直觉。

- **本文之后**：
  - 1986 年《Applications of Circumscription to Formalizing Common-Sense Knowledge》（*AI* 28(1):89–116），提出公式限定（formula circumscription）与优先限定（prioritized circumscription）。
  - 获奖：1971 年图灵奖；1985 年首届 IJCAI 卓越研究奖；1988 年京都奖；1990 年美国国家科学奖章；2003 年富兰克林奖章。
  - 其个人网页自述："He invented the circumscription method of non-monotonic reasoning in **1978** and refined it during the 1980s." ——即想法比发表早约两年。

### 论文中致谢到的人

Remark 3 明确记载：限定模式左端的那个附加合取项 $\forall \bar{x}.(\Phi(\bar{x}) \supset P(\bar{x}))$ 来自 **Ashok Chandra（1979 年的私人交谈）** 与 **Patrick Hayes（1979 年的私人交谈）** 的建议。McCarthy 写道：没有这一项，对析取式做限定会导致矛盾。这是一个值得注意的细节——本文最关键的技术修补来自两次口头讨论。

第 6 节的模型论处理，McCarthy 注明"similar to Davis's (1980) treatment of domain circumscription"，并说"Pat Hayes (1979) pointed out that the same ideas would work"。

资助来源（Remark 8）：ARPA 合同 MDA-903-76-C-0206（ARPA Order 2494）；NSF Grant MCS 78-00524；IBM 1979 年 T. J. Watson 研究中心杰出教授计划；行为科学高级研究中心。

---

## 历史语境

### 1980 年那一期《Artificial Intelligence》

本文所在的是 *Artificial Intelligence* 第 13 卷第 1–2 期（1980 年 4 月）的**非单调逻辑专辑**。完整目录（据 OpenAlex 与 DBLP 交叉核对）：

| 页码 | 篇目 |
|------|------|
| 1–4 | Daniel G. Bobrow, "Editor's preface" |
| 5–26 | Terry Winograd, "Extended inference modes in reasoning by computer systems" |
| **27–39** | **John McCarthy, "Circumscription—A form of non-monotonic reasoning"** |
| 41–72 | Drew McDermott & Jon Doyle, "Non-monotonic logic I" |
| 73–80 | Martin Davis, "The mathematics of non-monotonic reasoning" |
| 81–132 | Raymond Reiter, "A logic for default reasoning" |
| 133–170 | Richard W. Weyhrauch, "Prolegomena to a theory of mechanized formal reasoning" |
| 171–172 | John McCarthy, "Addendum: Circumscription and other non-monotonic formalisms" |

这一期是非单调推理作为一个子领域的**奠基性出版事件**：至今定义该领域的三套形式系统——限定（McCarthy）、带 M 算子的非单调逻辑（McDermott & Doyle）、默认逻辑（Reiter）——在同一期同时问世，另有 Davis 的模型论分析作为技术支撑。本系列 #33 精读的 Reiter 默认逻辑，正是同一期的第 81–132 页。

*考据说明*：Davis 那篇的**已发表标题是 "The mathematics of non-monotonic reasoning"**，不含"Notes on"。"Notes on the Mathematics of..."这一形式出现在 McCarthy 本文的参考文献表中，并由此在二手文献中广泛流传。此处以 OpenAlex/DBLP 的期刊记录为准。

Bobrow 当时是该刊主编，前言由他撰写。是否另有"客座编辑"的正式安排，未能核实（ScienceDirect 对自动访问返回 403）。

### 当时的学术主流与待解问题

1970 年代末的知识表示有两条路线：

**逻辑路线**：McCarthy 自 1959 年起主张的、用一阶逻辑语句表示常识的纲领。它严谨，但撞上了一堵墙——一阶逻辑是**单调的**。

**过程路线**：Minsky 的框架理论（本系列 #24）、PLANNER/MICROPLANNER 中的 THNOT 算子、各类专家系统中的默认值机制。它们在工程上有效，却缺少清晰的语义。

McCarthy 在本文第 2 节明确点名了这两者：Reiter 1980 的默认推理与 Sussman 等人 1971 年 MICROPLANNER 中的 THNOT，都是非单调推理的例子，"but possibly of a different kind from those discussed in this paper"。

### 直接前驱

1. **McCarthy (1959), Programs with Common Sense** —— 用逻辑表示常识、由演绎驱动行动的原始纲领。本文第一句话就是它。
2. **McCarthy & Hayes (1969)** —— 框架问题的命名，情境演算（situation calculus）的建立。本文第 7 节的 `on(x,y,s)` / `result(move(x,y),s)` 记法直接来自这里。
3. **McCarthy (1977), Epistemological Problems of AI** —— 限制条件问题的命名，以及极小模型思想的散文版。该文已写下："Circumscription is not deduction in disguise, because every form of deduction has two properties that circumscription lacks — transitivity and what we may call monotonicity."
4. **Davis (1980)** —— 域限定（domain circumscription）的数学处理，本文第 5、6 节的模型论直接建立在其上。
5. **Amarel (1971), On Representation of Problems of Reasoning about Actions** —— 传教士与食人族问题的状态空间表示。本文第 3 节整节都在追问：从英文题面到 Amarel 表示，这一步推理的合法性从何而来。

---

## 问题形式化

### 限制条件问题（the qualification problem）

McCarthy 在第 1 节给出的表述：为了完整表示一个行动成功执行的条件，似乎需要写下"数量上不切实际、看上去也不合情理"的大量限定条款。他的例子是划船过河：

> "the successful use of a boat to cross a river requires, if the boat is a rowboat, that the oars and rowlocks be present and unbroken, and that they fit each other. Many other qualifications can be added, making the rules for using a rowboat almost impossible to apply, and yet **anyone will still be able to think of additional requirements not yet stated**."

关键在最后一句：**限定条款的列表原则上无法穷尽**。这不是勤奋程度的问题，而是结构性的。

### 输入与输出

- **输入**：一阶逻辑句子 $A$（包含谓词符号 $P$），以及待最小化的谓词 $P$。
- **输出**：一个句子模式（sentence schema），断言"满足 $P$ 的元组只有那些从 $A$ 出发不得不满足 $P$ 的元组"。
- **推理关系**：记 $A \vdash_P q$，若 $q$ 可从"对 $A$ 中的 $P$ 作限定"的结果演绎得到。McCarthy 称之为**限定式推理（circumscriptive inference）**。

### 目标

不是修改逻辑，而是在一阶逻辑之上加一条**猜想规则（rule of conjecture）**。Remark 1 的表述极为明确，也是二手文献中最常被误述的一句：

> "Circumscription is **not** a 'nonmonotonic logic'. It is a form of nonmonotonic reasoning augmenting ordinary first order logic."

McCarthy 的理由（同一条 Remark）：修改逻辑的诱惑太多，很难保持它们彼此兼容。宁可保留一阶逻辑不动，把非单调性放在"如何使用逻辑"这一层。

---

## 核心方法

### 直觉

三句话：

1. 常识推理经常要"跳到结论"——可被证明具有性质 $P$ 的那些对象，**就是全部**具有 $P$ 的对象。
2. 这个跳跃不能靠增加公理或增加推理规则来实现，因为经典逻辑是单调的。
3. 所以把它做成一条外在的猜想规则：给定事实集 $A$，生成一个断言"$P$ 的外延已经最小"的模式，然后照常用一阶逻辑演绎。

论文第 3 节用传教士与食人族问题做了一段极精彩的叙事来说明第 1 点。给某人这道题，他提议去上游半英里走桥过河。你说题面没提桥。他说"题面也没说没有桥"。你排除了桥，他提议直升机；排除直升机，他提议飞马，或者让其余人挂在船外。你无奈告诉他答案，他又攻击说船可能漏水或者没有桨；补上之后，他提出可能有海怪游上来把船吞了。

McCarthy 的判断很关键：把"除了船没有别的过河方式、船不会出任何问题"写进题面是**作弊**——

> "A human doesn't need such an ad hoc narrowing of the problem, and indeed the only watertight way to do it might amount to specifying the Amarel representation in English."

也就是说，要想把所有例外堵死，你实际上要用英文把 Amarel 的状态空间表示重写一遍。这在方法论上是循环的。

### 形式化描述：限定模式

**定义**（论文 Definition，式 (1)）。设 $A$ 是含谓词符号 $P(x_1,\dots,x_n)$ 的一阶句子，$A(\Phi)$ 表示把 $A$ 中所有 $P$ 替换为谓词表达式 $\Phi$ 的结果（$\Phi$ 可以是谓词符号，也可以是 $\lambda$ 表达式）。则 **$P$ 在 $A(P)$ 中的限定**是如下句子模式：

$$A(\Phi) \wedge \forall \bar{x}.(\Phi(\bar{x}) \supset P(\bar{x})) \;\supset\; \forall \bar{x}.(P(\bar{x}) \supset \Phi(\bar{x}))$$

这里 $\Phi$ 是一个**谓词参数**，可以代入任意谓词表达式。McCarthy 补了一句括号说明：若用二阶逻辑，前面就会有一个 $\forall \Phi$。

**联合限定**（同时限定 $P$ 和 $Q$）：

$$A(\Phi,\Psi) \wedge \forall\bar{x}.(\Phi(\bar{x}) \supset P(\bar{x})) \wedge \forall\bar{y}.(\Psi(\bar{y}) \supset Q(\bar{y})) \supset \forall\bar{x}.(P(\bar{x}) \supset \Phi(\bar{x})) \wedge \forall\bar{y}.(Q(\bar{y}) \supset \Psi(\bar{y}))$$

McCarthy 说本文不给联合限定的例子，但相信它在某些 AI 应用中会很重要。

### 模型论：极小蕴涵

第 6 节给出语义对应物。

**定义**。设 $M(A)$、$N(A)$ 是 $A$ 的模型。称 $M$ 是 $N$ 在 $P$ 上的**子模型**，记 $M \leq_P N$，若 $M$ 与 $N$ 有相同的论域，$A$ 中除 $P$ 外的所有谓词符号在 $M$ 与 $N$ 中有相同的外延，而 $P$ 在 $M$ 中的外延**包含于**其在 $N$ 中的外延。

**定义**。$A$ 的模型 $M$ 称为**在 $P$ 上极小的**，若 $M' \leq_P M$ 蕴涵 $M' = M$。（McCarthy 引 Davis 1980 指出：极小模型不总是存在。）

**定义**。$A$ 在 $P$ 上**极小蕴涵** $q$，记 $A \models_P q$，若 $q$ 在 $A$ 的所有在 $P$ 上极小的模型中为真。

**定理**。$P$ 在 $A$ 中的限定的任一实例，在 $A$ 的所有在 $P$ 上极小的模型中为真。

**推论**。若 $A \vdash_P q$，则 $A \models_P q$。

注意这只是**可靠性（soundness）**——完备性不成立。这个缺口是此后二十年大量技术工作的起点。

### 与前人方法的本质区别

| | 经典一阶逻辑 | THNOT / 框架默认值 | 限定 |
|---|---|---|---|
| 语义地位 | 有 | 无（停留在实现层） | 有（极小模型） |
| 单调性 | 单调 | 非单调 | 非单调 |
| 是否修改逻辑 | — | 不适用 | **不修改**，外加猜想规则 |
| 表达能力 | — | 逐对象默认 | 可断言"没有任何对象满足 $P$" |

第 8 节 Remark 2 给出了限定强于逐条默认的精确论证：假设默认规则是"块 $x$ 在块 $y$ 上，仅当被显式陈述"。那么对每个具体的块 $x$，我们能推出它不在块 $A$ 上；但**推不出"$A$ 上什么都没有"**。后者需要另写一条独立的默认（"块是空闲的，除非有东西被陈述在它上面"）。而限定一次就能给出这个全称结论。

这是限定相对于默认逻辑的核心优势：它作用于**外延整体**，而不是逐个实例。

---

## 关键公式推导

### 公式 1：限定模式为什么长这样

**原文表述**（式 (1)）：

$$A(\Phi) \wedge \forall \bar{x}.(\Phi(\bar{x}) \supset P(\bar{x})) \;\supset\; \forall \bar{x}.(P(\bar{x}) \supset \Phi(\bar{x}))$$

**逐步解读**：

- **Step 1**：$A(\Phi)$ ——依据：把 $A$ 中的 $P$ 整体换成 $\Phi$。含义是"$\Phi$ 满足 $P$ 所满足的一切条件"，即 $\Phi$ 也是这批事实的一个合法解释。
- **Step 2**：$\forall\bar{x}.(\Phi(\bar{x}) \supset P(\bar{x}))$ ——依据：Chandra 与 Hayes 1979 年的建议。含义是"$\Phi$ 的外延是 $P$ 的外延的子集"。
- **Step 3**：由 Step 1 与 Step 2 推出 $\forall\bar{x}.(P(\bar{x}) \supset \Phi(\bar{x}))$ ——这是 Step 2 的**逆**，两者合起来即 $\Phi$ 与 $P$ **外延相等**。

**整体在说什么**：如果你能找到一个比 $P$ 更小（或相等）的 $\Phi$，而它仍然满足全部已知事实，那么 $P$ 本来就不该比 $\Phi$ 大。换言之：**$P$ 不能比事实所强制的更大**。

**Step 2 为什么不可省**：McCarthy 在 Remark 3 说，没有这一项，对析取式做限定会导致矛盾。用第 4 节的 Example 2 可以看清：限定 $\text{isblock } A \vee \text{isblock } B$。若无 Step 2，代入 $\Phi(x) \equiv (x = A)$ 时，左端只剩 $\Phi(A) \vee \Phi(B)$，即 $(A=A) \vee (B=A)$，恒真；于是无条件得到 $\forall x.(\text{isblock } x \supset x = A)$。同理代入 $\Phi(x) \equiv (x = B)$ 得到 $\forall x.(\text{isblock } x \supset x = B)$。两者合取，配上 $A \neq B$ 即矛盾。

有了 Step 2，两次代入分别只能得到**条件式**：

$$\text{isblock } A \supset \forall x.(\text{isblock } x \supset x = A) \tag{9}$$
$$\text{isblock } B \supset \forall x.(\text{isblock } x \supset x = B) \tag{11}$$

与前提 (6) 合起来，得到的是**析取**：

$$\forall x.(\text{isblock } x \supset x = A) \;\vee\; \forall x.(\text{isblock } x \supset x = B) \tag{12}$$

即"要么 $A$ 是唯一的块，要么 $B$ 是唯一的块"。这正是应有的结论——极小模型有两个，限定只能给出它们的公共部分。**这个例子是理解整个模式设计的钥匙。**

### 公式 2：限定如何生成归纳公理模式

这是全文最漂亮的一段（第 4 节 Example 3）。取自然数的代数公理：

$$\text{isnatnum } 0 \wedge \forall x.(\text{isnatnum } x \supset \text{isnatnum succ } x) \tag{13}$$

对 $\text{isnatnum}$ 作限定：

$$\Phi(0) \wedge \forall x.(\Phi(x) \supset \Phi(\text{succ } x)) \wedge \forall x.(\Phi(x) \supset \text{isnatnum } x) \supset \forall x.(\text{isnatnum } x \supset \Phi(x)) \tag{14}$$

**化简**：代入 $\Phi(x) \equiv \Psi(x) \wedge \text{isnatnum } x$。

- 第三个合取项 $\forall x.(\Phi(x) \supset \text{isnatnum } x)$ 变成 $\forall x.(\Psi(x) \wedge \text{isnatnum } x \supset \text{isnatnum } x)$，恒真，**脱落**。
- 结合 (13)，前两项化为 $\Psi(0)$ 与 $\forall x.(\Psi(x) \supset \Psi(\text{succ } x))$。

得到：

$$\Psi(0) \wedge \forall x.(\Psi(x) \supset \Psi(\text{succ } x)) \supset \forall x.(\text{isnatnum } x \supset \Psi(x)) \tag{15}$$

这就是**数学归纳法公理模式**。

**这一结果的意义**：数学归纳法在直觉上说的正是"自然数只有那些被 0 和后继运算强制生成的对象"——这是一个最小化断言。限定把这个直觉变成了机械操作。McCarthy 自己在这两个例子后加了一句关键的限定：

> "In the preceding two examples, the schemas produced by circumscription play the role of **axiom schemas rather than being just conjectures**."

也就是说，在数学这类"我们确知论域封闭"的场合，限定的产物是公理；在常识推理这类开放场合，它只是猜想。同一个形式装置，认识论地位随语境而变。第 3 节末尾有一句对应的话，是全文最好的一句：

> "In puzzles, circumscription seems to be a **rule of inference**, while in life it is a **rule of conjecture**."

### 公式 3：块世界中"没有东西阻止"的推导

第 7 节展示了限定在规划中的实际用法。核心公理：

$$\forall xys.(\forall z.\neg\text{prevents}(z, \text{move}(x,y), s) \supset \text{on}(x, y, \text{result}(\text{move}(x,y), s))) \tag{21}$$

读作：除非有东西阻止，否则移动之后 $x$ 就在 $y$ 上。

然后列举可能的阻碍：非块 (22)、被覆盖 (23)、太重 (24)。

程序想把 $A$ 移到 $C$ 上，必须建立 $\forall z.\neg\text{prevents}(z, \text{move}(A,C), s_0)$。做法是对 $\lambda z.\text{prevents}(z, \text{move}(A,C), s_0)$ 作限定，得到 (25)，然后**令 $\forall z.(\Phi(z) \equiv \text{false})$**，(25) 化简为：

$$\text{isblock } A \wedge \text{isblock } B \wedge \text{clear}(A, s_0) \wedge \text{clear}(B, s_0) \wedge \neg\text{tooheavy } A \supset \forall z.\neg\text{prevents}(z, \text{move}(A,C), s_0) \tag{26}$$

**这一步的技巧值得记住**：把 $\Phi$ 取为恒假，等于断言"阻碍者的集合是空的"。限定模式此时退化为一个纯粹的充分条件——只要这几条前提成立，就没有任何东西阻止移动。

其中 $\text{clear}$ 的两个前提又是通过对 $\lambda xy.\text{on}(x,y,s_0)$ 作限定得到的：已知只有 $\text{on}(A,B,s_0)$ 被显式给出，限定后代入 $\Phi(x,y) \equiv (x = A \wedge y = B)$，得

$$\forall xy.(\text{on}(x,y,s_0) \supset x = A \wedge y = B) \tag{28}$$

再配上 $\text{clear}$ 的定义 $\forall xs.(\text{clear}(x,s) \equiv \forall y.\neg\text{on}(y,x,s))$ (29)，即得。

**注意 McCarthy 的诚实**：他在 (25) 之后立刻写道——

> "Whether (25) is true depends on how good the program was in finding all the relevant statements."

限定的正确性最终依赖于"是否把所有相关事实都纳入了考虑"，而这一点在形式系统内部无法保证。

---

## 实验分析

本文是纯理论工作，无实验。论文以四个逐步加深的形式化例子代替实验：

| 例子 | 内容 | 展示了什么 |
|------|------|-----------|
| Example 1 | 三个块 | 限定的基本运作；非单调性（加入 $\text{isblock } D$ 后 (5) 不再可推） |
| Example 2 | 析取 $\text{isblock } A \vee \text{isblock } B$ | 多个极小模型时限定只给出析取；Step 2 合取项的必要性 |
| Example 3 | 自然数 | 限定生成归纳公理模式——最深刻的一个 |
| Example 4 | $\text{on}$ 与 $\text{above}$ | 限定生成**传递闭包**：$\text{above}$ 是 $\text{on}$ 的传递闭包 |

**我的解读**：Example 3 和 Example 4 的共同点是，限定在这两处生成的不是"关于世界的猜测"，而是"关于概念的定义"。传递闭包和数学归纳法都是"最小不动点"式的构造，而限定本质上就是一个最小化算子。McCarthy 大概是先注意到了这个数学上的巧合，才敢把同一个装置推广到常识推理上去——如果一个形式装置能自动生成归纳公理，它就不太可能是临时拼凑的。

**实验设计（例子选择）的不足**：四个例子全部是"论域封闭、答案显然"的情形。真正困难的场合——时序推理、行动的间接效应——在本文中只有第 7 节一个初步的块世界示例，而恰恰是在这类场合，限定六年后遭遇了它最著名的反例。

---

## 局限性

### 作者自述

McCarthy 在第 8 节的八条 Remarks 中相当坦率：

1. **可计算性**：限定产生的是**句子模式**，"sentence schemata are not properly handled by most present general purpose resolution theorem provers"。连固定的数学归纳模式在程序验证中都常需人工干预，而这里程序还得处理由限定新生成的模式。
2. **启发式负担**（Remark 4）："Clearly the program will have to include **domain dependent heuristics** for deciding what circumscriptions to make and when to take them back." ——决定"限定什么、何时撤回"需要领域相关的启发式，形式系统本身不提供。
3. **表示依赖性**（Remark 7，最深刻的一条）：限定的结果依赖于用哪组谓词来表达事实。同样的块世界事实，可以用 $\text{on}$、用 $\text{above}$、或者用高度与水平位置来公理化，**限定的结果会不同**。McCarthy 的类比是：选择谓词集合"就像在物理学或地理学中选择坐标系"。他进一步说，这意味着"如果承认限定是一条猜想规则，那么表示的选择就具有认识论后果（epistemological consequences）"。

   这一条实际上承认了：限定没有把常识推理完全形式化，它把一部分负担转移到了本体论设计上。

4. **本体论代价**：为了写下"除非有什么阻止它"，必须把"船的毛病"这类东西作为**实体**引入本体。McCarthy 承认这"在技术上很可能困难，在哲学上也成问题（philosophically problematical）"，但坚持"we must try"，并向反对者下了一个挑战：用你偏好的形式系统表达"除了漏水，这条船还有别的毛病"。

### 后续批评（一）：耶鲁枪击问题

这是限定所遭遇的最著名反例。

**出处**：Steve Hanks & Drew McDermott, "Default Reasoning, Nonmonotonic Logics, and the Frame Problem", AAAI-86, pp. 328–333；扩充版 "Nonmonotonic logic and temporal projection", *Artificial Intelligence* 33(3):379–412, 1987.

**公理**（情境演算）：

1. $T(\text{ALIVE}, S_0)$
2. $\forall s.\, T(\text{LOADED}, \text{RESULT}(\text{LOAD}, s))$
3. $\forall s.\, T(\text{LOADED}, s) \supset \text{AB}(\text{ALIVE}, \text{SHOOT}, s) \wedge T(\text{DEAD}, \text{RESULT}(\text{SHOOT}, s))$
4. $\forall f,e,s.\, T(f, s) \wedge \neg\text{AB}(f, e, s) \supset T(f, \text{RESULT}(e, s))$ ——惯性（框架）公理

令 $S_1 = \text{RESULT}(\text{LOAD}, S_0)$，$S_2 = \text{RESULT}(\text{WAIT}, S_1)$，$S_3 = \text{RESULT}(\text{SHOOT}, S_2)$。

**意图中的模型**：唯一的反常是 $\text{AB}(\text{ALIVE}, \text{SHOOT}, S_2)$。枪装弹后一直保持装弹，射击杀死 Fred。

**反常模型**：从 $S_3$ 倒推。假设 $\neg\text{AB}(\text{ALIVE}, \text{SHOOT}, S_2)$。由公理 3 的逆否，得 $\neg T(\text{LOADED}, S_2)$。但 $T(\text{LOADED}, S_1)$（公理 2）。于是公理 4 强制 $\text{AB}(\text{LOADED}, \text{WAIT}, S_1)$——**枪在等待期间自己卸了弹**，Fred 活了下来。

**为什么这击中了限定**：两个模型各含**恰好一个**反常，而两个反常集合 $\{\text{AB}(\text{ALIVE},\text{SHOOT},S_2)\}$ 与 $\{\text{AB}(\text{LOADED},\text{WAIT},S_1)\}$ 在**集合包含序下不可比**。因此两者都是极小的。限定只能给出二者的交集，结论弱到只剩 $T(\text{ALIVE}, S_3) \vee T(\text{DEAD}, S_3)$。

Hanks & McDermott 的原话：

> "What we can deduce from Circum(A, AB) is therefore considerably weaker than what we had intended."

**关键的公允之处**：他们明确指出这不是限定独有的毛病——

> "So circumscription is not the culprit here—Reiter's proof-theoretic default logic has the same problem."

**诊断**：我们想要的不是集合包含意义上的极小模型，而是"**按时序极小**（chronologically minimal）"的模型——反常尽可能晚发生。这个术语归于 Yoav Shoham。而"chronological minimality cannot be expressed in those terms"，因为限定的极小性与集合包含密不可分。

Hanks & McDermott 还有一句更尖锐的判断，值得记在心里：

> "the original idea behind circumscription, that a simple, problem-independent extension to a first-order theory would 'minimize' predicates in just the right way, has been lost along the way."

### 后续批评（二）：闭世界推理的充分性

**Etherington, Mercer & Reiter (1985)**, "On the Adequacy of Predicate Circumscription for Closed-World Reasoning", *Computational Intelligence* 1(1):11–15.

指出的局限：在 1980 年的原始定义下（不允许其他谓词随之变化），限定**只能增加关于被最小化谓词的否定信息，不能导出新的肯定事实**。因此在某些理论上，限定给出的结论弱于闭世界假设（CWA），而后者才是直觉上正确的答案。

这正是 McCarthy 1986 年论文所修补的缺陷——他在自己网页上说 1986 年那篇"properly treats variables that are not minimized"，指的就是这件事。

*核实说明*：Wiley 对自动访问返回 403，上述限制的表述由引用该文的二手文献（Handbook of KR 相关章节、Etherington IJCAI-87）重构而来，未能读到原文。

### 后续批评（三）：不可计算性

据 Brewka, Niemelä & Truszczyński, "Nonmonotonic Reasoning"（*Handbook of Knowledge Representation*, Elsevier 2008, Ch. 6, §6.4.4）：

> "general first order circumscription is **highly uncomputable**. This is not surprising as circumscription transforms a first order sentence into a second order formula and it is well known that second order logic is not even semi-decidable. This means that in order to compute circumscription we cannot just use our favorite second order prover — such a prover simply cannot exist."

即：一阶情形下的限定式蕴涵**不是半可判定的**。

相关工作：J. S. Schlipf, "How uncomputable is general circumscription?" (LICS 1986, pp. 92–95) 与 "Decidability and definability with circumscription" (*Annals of Pure and Applied Logic* 35:173–191, 1987)。

**命题情形的复杂度**：Thomas Eiter & Georg Gottlob, "Propositional Circumscription and Extended Closed-World Reasoning are $\Pi^p_2$-Complete", *Theoretical Computer Science* 114(2):231–245, 1993。即使退到命题逻辑，限定式蕴涵仍是 $\Pi^p_2$-完全的——比 coNP 还高一层。

**对照 #33 的 Reiter**：Reiter 1980 证明的是"一般默认理论的信念集不是递归可枚举的"。两个坏消息形状不同（一个来自二阶量词，一个来自扩张的不动点结构），但指向同一件事：**可废止推理的代价，在两条路线上都无法回避**。

### 假设检验

本文最脆弱的假设，是**"极小 = 正常"**这个等式。

在块世界和自然数上它成立，因为"更少的块""更少的自然数"确实对应着更简单的世界。但在时序推理中它失效了：耶鲁枪击问题证明，"反常最少"并不等于"符合直觉"——因为反常可以在时间轴上"搬家"，搬家前后个数不变。

更一般地说，**集合包含序是一个太贫乏的偏好结构**。现实中的常识偏好是有方向、有优先级、有时序的。此后二十年的技术发展（优先限定、逐点限定、时序极小化、System Z）几乎都在做同一件事：给这个偏好序加上更多结构。

---

## 后续影响

### 直接后继

1. **Vladimir Lifschitz, "Computing Circumscription"**（IJCAI-85, pp. 121–127）。摘要开门见山："Circumscription is difficult to implement because its definition involves a second-order quantifier. This paper presents metamathematical results that allow us in some cases to replace circumscription by an equivalent first-order formula."

   技术核心：定义关于 $P$ 的 **solitary（孤立）** 与 **separable（可分离）** 公式。定理 1 给出可分离公式的限定的一阶等价式。定理 2："any prioritized circumscription can be written as a conjunction of parallel circumscriptions"。

   Lifschitz 在致谢中写："I am indebted to John McCarthy for introducing me to problems of non-monotonic reasoning and circumscription." 他后来成为限定理论的主要推进者，也是回答集程序（ASP）的共同奠基人。

2. **McCarthy (1986), Applications of Circumscription to Formalizing Common-Sense Knowledge**（*AI* 28(1):89–116）。两处关键改动：
   - 被最小化的对象从谓词 $P(x)$ 推广到任意合式公式 $E(P,x)$，即**公式限定**；
   - 用显式的二阶量词 $\forall P'$ 替换 1980 年的**模式**。

   §12 定义**优先限定**：给反常谓词加上优先级序，按字典序组合。动机是鸟/鸵鸟的例子——平凡限定只能给出一个无用的析取。McCarthy 写道："Lifschitz (1985) further develops the idea of prioritized circumscription. I expect that prioritized circumscription will turn out to be the most natural and powerful variant."

3. **Lifschitz, 逐点限定（pointwise circumscription）**：一次最小化一个点，最小化的次序由对象语言中的公式指定——从而可以表达"时序上更早/更晚"。Hanks & McDermott 把它列为耶鲁枪击问题的候选解药之一，并指出"Pointwise circumscription contains predicate circumscription as a special case."

4. **David Etherington, "Relating Default Logic and Circumscription"**（IJCAI-87, pp. 489–494）。摘要："We show that there are interesting cases in which the two formalisms do not correspond, as well as cases where default logic subsumes circumscription."

   其中对差异原因的解释是我读到的最精炼的一句：

   > "default logic's semantics must be based on **sets of models**. Circumscription's submodel relation, however, only considers **pairs of models**."

   其他结论：（Imielinski）限定只能对**无前提默认**给出模块化的翻译；默认逻辑可以做出广义限定（无可变项）做不到的猜想（定理 1）；在带域封闭公理与确定等式理论的受限情形下，对应关系成立（定理 2 / 推论 4）。Etherington 的概括是"default logic is a 'brave' reasoner while circumscription is 'cautious'"——默认逻辑承诺于某一个扩张，限定取所有极小模型的交。

   反方向的不对称也值得记：限定可以让一些谓词固定、另一些可变，而"In default logic, there is no way to restrict the repercussions of the defaults to some particular set of predicates."

### 开创的方向

限定与同期的默认逻辑、非单调逻辑一道，开创了**非单调推理（nonmonotonic reasoning）**这一子领域。在这三者中，限定是唯一从**模型极小化**出发的，也因此与后来的稳定模型语义有了意想不到的汇合。

### 当代回响

**（一）与回答集程序的汇合。** Paolo Ferraris, Joohyung Lee & Vladimir Lifschitz, "Stable Models and Circumscription", *Artificial Intelligence* 175(1):236–263, 2011：

> "we propose a new definition of that concept [stable model]… It is based on a syntactic transformation **similar to parallel circumscription**."

以及从学术史角度最有意思的一句：

> "We can distinguish between 'fixpoint' nonmonotonic formalisms, such as default logic and autoepistemic logic, and 'translational' formalisms, such as program completion and circumscription. In the past, stable models were seen as part of the 'fixpoint tradition.' The **remarkable similarity** between the new definition of a stable model and the definition of circumscription is curious from this point of view."

这个结果给出了 SM 算子，支撑着现代一阶 ASP 语义。实践后果是：限定与 ASP 这两条自 1980 与 1988 年起分道扬镳的路线，最终被证明是同一个构造的两种呈现方式——而 ASP 的求解器，正是今天让限定式推理真正可执行的东西。

**（二）事件演算。** Erik T. Mueller 在 *Handbook of Knowledge Representation* 第 17 章中，§17.4.1 与 §17.4.2 分别题为 "Circumscription" 与 "Computing Circumscription"。事件演算对 `Happens` 与 `Initiates`/`Terminates`/`Releases` 作限定来处理框架问题。实践中的求解路径有两条：约化为谓词完备化（predicate completion），或者转成 ASP——Kim, Lee & Palla, "Circumscriptive Event Calculus as Answer Set Programming" (IJCAI-09) 走的是后者。

**（三）描述逻辑。** 近年最活跃的一条线是把限定加进描述逻辑，以支持本体中的可废止/原型推理。IJCAI 2023 有 "Description Logics with Pointwise Circumscription"——Lifschitz 的逐点限定在四十年后被直接复活。

**（四）求解器化。** 2025–2026 年仍有 "A MaxSAT-based framework for computing circumscription"（*Annals of Mathematics and AI*, 2026-01）、"Minimal reduct for propositional circumscription"（*Frontiers in AI*, 2025-12）等工作。1985 年 Lifschitz 提出的"计算限定"议程，如今用现代 SAT/MaxSAT/ASP 求解器继续推进。

据 OpenAlex，2023 年以来仍有 **59 篇**引用本文的新论文。

### 引用统计

| 来源 | 计数 | 说明 |
|------|------|------|
| Google Scholar | **3,729** | 截至 2026-08-07 |
| OpenAlex | 2,023 | 同上 |
| Semantic Scholar | 434 | 明显低估（S2 对 1990 年前 AI 文献覆盖不全，且记录分裂） |

同期对照（Google Scholar，同一日期）：

- Reiter 1980《A logic for default reasoning》：**6,459**
- McDermott & Doyle 1980《Non-monotonic logic I》：1,711
- McCarthy 1986《Applications of circumscription...》：1,823
- Hanks & McDermott 1987：746
- Lifschitz 1985《Computing circumscription》：692

**值得注意**：Reiter 的默认逻辑被引约为限定的 1.7 倍。这个数字差距是有意义的——默认逻辑的语法形式更接近自然语言的"除非……否则……"，更容易教学与实现；而限定的二阶本性使它长期缺少可执行的求解器。但通过 Ferraris–Lee–Lifschitz 的结果，两条谱系在 2011 年之后部分合流了。

---

## 个人笔记

**一、这篇论文最好的部分不是形式系统，是第 3 节那个"愚人"。**

McCarthy 用了整整两页写一个虚构的对话：你出题，对方一次次提出桥、直升机、飞马、漏水的船、海怪。你越排除，他越发明。McCarthy 给他的评语是 "You now see that while a dunce, he is an **inventive** dunce."

这个段落的作用不是修辞。它是在论证一件很难论证的事：限制条款的列表**原则上无法穷尽**。你没法靠证明来说明这一点，只能靠演示。让读者亲自体验一次"排除—又被提出新例外"的循环，比任何定理都更有说服力。

我读到最后一句才明白他为什么要花这么多篇幅："the only watertight way to do it might amount to specifying the Amarel representation in English." 也就是说，如果你真的把所有例外堵死，你写下的东西就已经是答案本身了。这是一个循环——**你需要用答案来提问**。整篇论文都是为了打破这个循环。

**二、真正让我停下来的，是第 4 节的 Example 3。**

限定一个说"0 是自然数、后继还是自然数"的公理，居然直接吐出了数学归纳法公理模式。

第一次读的时候我以为是巧合。想了一会儿才反应过来这是必然的：数学归纳法在直觉上说的就是"自然数只有那些被 0 和 succ 强制生成的对象"——这本来就是一个最小化断言。我们从小学到大的归纳法，原来一直是一条伪装成推理规则的限定。

而 Example 4 又给出了传递闭包。两个例子放在一起，指向同一件事：数学里那些"最小不动点"式的构造——归纳、闭包、递归定义——本质上全是限定。

我猜 McCarthy 是先注意到了这个，才敢把同一个装置推到常识推理上去。因为如果一个形式装置能自动生成归纳公理，它就不太可能是临时拼凑出来的。这是一个方法论上的信号：**当你的新工具意外地重现了一个古老的、被公认为正确的东西，说明你大概摸到了某个真实的结构。**

**三、Remark 7 是全文最诚实、也最容易被略过的一段。**

McCarthy 承认：限定的结果依赖于你用哪组谓词来描述世界。同样的块世界，用 `on` 描述、用 `above` 描述、或者用坐标描述，限定出来的东西不一样。

他的类比是"就像在物理学中选择坐标系"。但这个类比其实是自谦了——坐标系的选择不改变物理定律，而谓词集合的选择**改变限定的结论**。他自己也这么说了：这意味着表示的选择具有"认识论后果"。

换句话说：限定并没有把常识推理完全形式化。它把一部分负担从"写下所有例外"转移到了"选对本体"。这是一次转移，不是一次消除。

我觉得这一点在今天格外值得琢磨。我们现在训练模型时争论的"表示学习""归纳偏置"，在结构上就是同一个问题：**你选择用什么来描述世界，决定了你能从中最小化出什么。** 1980 年 McCarthy 用一条 Remark 就把这件事说清楚了，而且他知道自己没解决它。

**四、关于耶鲁枪击问题，我想给限定说一句公道话。**

通常的叙事是"限定被耶鲁枪击问题击败了"。但 Hanks & McDermott 自己写得很清楚："So circumscription is not the culprit here—Reiter's proof-theoretic default logic has the same problem."

真正被击败的不是限定这个装置，而是**"集合包含序足以刻画常识偏好"这个假设**。这是一个更深的教训，而且是整个非单调推理领域共有的。反常可以在时间轴上"搬家"，搬家前后个数不变——这说明"数得少"和"合乎情理"是两回事。

此后所有的修补——优先限定、逐点限定、时序极小化——做的都是同一件事：给偏好序加结构。从这个角度看，1980 年的这篇论文提出的其实是一个**问题模板**："在什么偏好序下取极小？"McCarthy 给出的第一个答案（集合包含）是错的，但问题本身立住了。

**五、我没有完全理解的地方。**

Remark 6 提到限定可以用于一阶逻辑之外的形式系统，给出了集合论版本 $\forall x.(A(x) \wedge (x \subset a) \supset (a \subset x))$，然后说"如果 $a$ 在 $A(a)$ 中只以 $z \in a$ 的形式出现，其数学性质应该类似于谓词限定"，接着补了一句"We have not explored what happens if formulas like $a \in z$ occur."

我不确定 $a \in z$ 这种出现方式会带来什么麻烦。直觉上，$z \in a$ 是把 $a$ 当作"外延"来用（与谓词的用法同构），而 $a \in z$ 是把 $a$ 当作"个体"塞进别的集合——这时候缩小 $a$ 会改变它作为个体的身份，而不只是缩小它的外延。所以极小化的方向可能不再良定义。但这只是我的猜测，论文没说，我也没找到后续文献处理这一点。

**六、写作上值得学的。**

这篇论文只有 13 页，但结构极为清楚：先讲问题（限制条件问题），再论证为什么现有工具不够（单调性），再用一个长故事让你相信问题的严重性（传教士与食人族），然后才给形式定义，接着四个由浅入深的例子，最后是模型论、一个实际应用、八条坦率的 Remarks。

最值得学的是那八条 Remarks。它们不是补充说明，而是**主动暴露弱点**：这东西定理证明器处理不了；需要领域启发式；结果依赖表示的选择。一个开创性工作的作者，在提出它的同一篇文章里就把它的边界画了出来。

对照之下，"Circumscription is not a 'nonmonotonic logic'"这句澄清也很有意思。McCarthy 显然预见到了会被误读，所以放在 Remarks 的第一条。四十多年后，这仍然是二手文献中最常见的误述——他的预见是对的，但预防没有奏效。

---

## 小红书写作备忘

### Hook 素材

1. **"一个爱抬杠的愚人"**：给他一道过河的谜题，他提议走桥、坐直升机、骑飞马；你排除了，他说船可能漏水、没有桨；再排除，他说可能有海怪。McCarthy 的评语是"虽然是个蠢人，却是个有创造力的蠢人"。这两页篇幅不是玩笑，而是在演示一件无法用定理说明的事：例外的列表原则上写不完。

2. **"数学归纳法其实是一条限定"**：把"0 是自然数、后继还是自然数"这条公理拿去做限定，吐出来的就是数学归纳法公理模式。我们从小用到大的归纳法，原来一直是一条伪装成推理规则的最小化断言。

3. **"作者自己说这篇过时了"**：McCarthy 在个人网页上写，本文的形式系统"已在很大程度上被 1986 年那篇取代"，建议引用者一并引用后者。一篇被引近四千次的奠基论文，被作者本人标记为过渡形态。

4. **"两个同样极小的世界"**：耶鲁枪击问题——枪装了弹，等一会儿，开枪。常识说人死了。而限定给出的答案是：要么人死了，要么枪在等待期间自己卸了弹。因为这两个世界的"反常"各有一个，谁也不比谁少。

推荐用 1（叙事性最强，且是论文原文的核心段落）作为 Page 3 的开场，用 2 作为 Page 5 的技术高潮。

### 核心 Insight（一句话）

限定不是给逻辑打补丁，而是在逻辑之外加一条猜想规则：**"能被证明满足 $P$ 的东西，就是全部满足 $P$ 的东西"**——它把"无法穷尽例外"这个困境，转化为"在极小模型中取真"这个可操作的语义条件；代价是，这个猜想的正确性依赖于你是否选对了描述世界的谓词，而这一点形式系统本身无法保证。

### 自查重点

1. **绝不能写"限定是一种非单调逻辑"**。McCarthy 在 Remark 1 明确否认："Circumscription is not a 'nonmonotonic logic'. It is a form of nonmonotonic reasoning augmenting ordinary first order logic." 这是全文最常被误引的一句。

2. **不能写"McCarthy 首创了非单调推理"**。同一期还有 McDermott & Doyle 与 Reiter，是三条并行路线同时问世。用"奠基者之一""与……一道开创"。本系列 #33 已按此口径处理过 Reiter，两篇须一致。

3. **框架问题与限制条件问题的命名出处不同**：框架问题出自 McCarthy & Hayes 1969；限制条件问题出自 McCarthy 1977（IJCAI-5）。本文是 1977 年那条的直接续篇（第 1 节标题就是 "INTRODUCTION. THE QUALIFICATION PROBLEM"）。不要混为一谈。

4. **耶鲁枪击问题不是只打限定**。Hanks & McDermott 明说默认逻辑有同样的毛病。若写成"限定被证伪而默认逻辑幸存"是错的。

5. **不可计算性的表述要准确**：一阶限定式蕴涵**不是半可判定的**（因为涉及二阶量词）；命题情形是 $\Pi^p_2$-完全的（Eiter & Gottlob 1993）。不要笼统写成"不可判定"。注意与 #33 中 Reiter 的"信念集非递归可枚举"区分——两个坏消息来源不同。

6. **PhD 导师有争议**，数学谱系项目记为 Donald C. Spencer，与部分资料冲突。小红书稿中回避此细节，只写"1951 年普林斯顿数学博士"。

7. **引用数只报 Google Scholar 的 3,729**，不与 Semantic Scholar 的 434 并列（后者明显低估），避免"不同指标硬对齐"。

8. **谨慎描述与 ASP 的关系**：Ferraris–Lee–Lifschitz 2011 证明的是稳定模型的新定义与并行限定"相似"，可互相刻画。不要写成"限定就是 ASP"或"ASP 源于限定"。

### 动态 Hashtags

#非单调推理 #知识表示 #常识推理
