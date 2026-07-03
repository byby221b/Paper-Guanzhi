# 精读报告 #21: STRIPS — A New Approach to the Application of Theorem Proving to Problem Solving

## 元信息

- 标题：STRIPS: A New Approach to the Application of Theorem Proving to Problem Solving
- 作者：Richard E. Fikes、Nils J. Nilsson（Stanford Research Institute, Menlo Park, California）
- 发表：*Artificial Intelligence* 2 (1971), 189–208；预印本发表于第 2 届 IJCAI, Imperial College, London, 1971 年 9 月 1–3 日
- 推荐人：Bertram Raphael
- 原文链接：https://ai.stanford.edu/~nilsson/OnlinePubs-Nils/PublishedPapers/strips.pdf
- 精读日期：2026-07-03
- 对应小红书期号：#21

## 作者背景

### Richard E. Fikes（1942–）

- 发表时身份：SRI Artificial Intelligence Group 研究员
- 师承：博士毕业于 Carnegie Institute of Technology（Carnegie Mellon 前身）计算机科学系，博士导师为 Allen Newell（GPS 与 Logic Theorist 的作者之一）
- 此前工作：博士论文探索用「过程式」方法（而非 QA3 的一阶逻辑演绎）求解问题，此思路直接催生了 STRIPS 的 add/delete list 表示
- 后续轨迹：先后任教 Rutgers 与 Stanford；1980 年代加入 Xerox PARC，参与知识表示与 KL-ONE 家族的开发；1991 年至今为 Stanford 计算机系教授、Knowledge Systems, AI Laboratory 主任；主持 W3C RDF 语义 web 相关工作；ACM Fellow、AAAI Fellow

### Nils J. Nilsson（1933–2019）

- 发表时身份：SRI Artificial Intelligence Group 高级研究员
- 师承：1958 年获 Stanford 电气工程博士，导师为 Willis Adcock（模式识别方向）
- 此前工作：
  · 1965 年出版 *Learning Machines: Foundations of Trainable Pattern-Classifying Systems*（早期机器学习教科书）
  · 1968 年与 Peter Hart、Bertram Raphael 合作发表 A* 算法（见本系列 #15）
  · 1971 年出版 *Problem-Solving Methods in Artificial Intelligence*
- 后续轨迹：主持 Shakey 机器人项目（1966–1972）；1985 年任 Stanford 计算机系主任，1990 年获 Kumagai Professor 冠名讲席；著有 *Principles of AI*（1980）、*Artificial Intelligence: A New Synthesis*（1998）、*The Quest for AI*（2010）；IJCAI Award for Research Excellence (1991)、AAAI Fellow

### 合著关系

Fikes 1969 年博士毕业后加入 SRI，恰好赶上 Shakey 机器人项目遇到「用一阶逻辑做规划无法扩展」的瓶颈。Nilsson 早已在思考如何绕开框架问题；Fikes 从其博士研究中带来过程式操作符的思路。两人在 1969–1970 年间合作，Fikes 主要负责 LISP 实现，Nilsson 在概念设计上贡献甚多。这是典型的「资深研究员 + 新进博士」组合，也是 SRI 传统作者顺序（贡献大者列前）的体现。

## 历史语境

### 当时的学术主流

1971 年，AI 规划领域的主流路线是 McCarthy 情景演算（1963）与 Green 定理证明规划器（1969）。这条路线的核心思想是：用一阶逻辑描述世界状态与行动，用通用定理证明器（如 Robinson 归结原理）作为推理引擎，通过 constructive proof 提取行动序列。

Cordell Green 在 1969 年 IJCAI 上展示了用 QA3 定理证明器求解规划问题，被视为「AI 规划 = 定理证明」范式的示范。SRI 内部也一度沿用此路线：Shakey 机器人的初版规划器就是 QA3 加情景演算。

### 待解决的核心问题

问题一：**框架问题（Frame Problem）**。情景演算中，每个行动不仅要写出「改变了什么」，还必须显式地写出「不改变的东西」——这被称作 frame axioms。对每个行动、每个未受影响的谓词，都要写一条 axiom。对于 Shakey 那样有数百个事实的世界模型，frame axioms 的数量呈组合爆炸。

问题二：**搜索空间爆炸**。即便克服了框架问题，纯粹的定理证明器缺少「哪个行动值得下一步执行」的启发式指导。Green 的系统只能解决两三步的规划任务。

问题三：**多世界模型的存储代价**。搜索规划涉及大量假想的中间世界模型。全量复制每个世界的所有 wffs（well-formed formulas，合式公式）在 PDP-10 的内存下不可行。

### 同时期的相关工作

- Green (1969)：QA3 + 情景演算（前驱，被 STRIPS 直接对标）
- Hewitt (1970)：PLANNER 语言——一种过程式表示与推理，思想与 add/delete list 相近，但作为编程语言而非规划算法（同期发展，STRIPS 论文脚注引用）
- Ernst & Newell (1969)：GPS 的正式总结（STRIPS 搜索策略的直接来源）
- Robinson (1965)：归结原理（STRIPS 的理论证明模块 QA3.5 基于此）

### 直接前驱

1. Green (1969), "Application of theorem proving to problem solving"——STRIPS 明确要「绕过」的对象
2. McCarthy & Hayes (1969), "Some philosophical problems from the standpoint of AI"——正式命名并讨论框架问题
3. Ernst & Newell (1969), *GPS: A Case Study in Generality and Problem Solving*——STRIPS 借用其 means–ends analysis 策略
4. Robinson (1965), 归结原理——STRIPS 内嵌的 QA3.5 定理证明器基于此
5. Fikes (1969), 博士论文——过程式操作符与状态更新的雏形

## 问题形式化

### 问题定义

给定：
- 初始世界模型 $M_0$：一阶谓词逻辑合式公式（wffs）的有限集合
- 操作符集 $\mathcal{O} = \{O_1(\vec{p}_1), \ldots, O_k(\vec{p}_k)\}$：每个操作符是一个模式（schema），带参数向量 $\vec{p}$
- 目标 wff $G_0$

每个操作符 $O_i(\vec{p}_i)$ 由三部分定义：
- Precondition：wff schema $P_i(\vec{p}_i)$
- Delete list：一组 wffs $D_i(\vec{p}_i)$
- Add list：一组 wffs $A_i(\vec{p}_i)$

### 输入与输出

- 输入：$(M_0, \mathcal{O}, G_0)$
- 输出：操作符序列 $\pi = (O_{i_1}(\vec{c}_1), O_{i_2}(\vec{c}_2), \ldots, O_{i_n}(\vec{c}_n))$（所有参数已实例化为常量），使得依次应用 $\pi$ 后得到 $M_n$，满足 $M_n \vdash G_0$

### 应用规则

若 $M_j \vdash P_{i_{j+1}}(\vec{c}_{j+1})$（当前世界满足前提），则：

$$M_{j+1} = (M_j \setminus D_{i_{j+1}}(\vec{c}_{j+1})) \cup A_{i_{j+1}}(\vec{c}_{j+1})$$

### 目标 / 评价准则

作者未给出严格的最优性判据（如最短路径），而是使用启发式评估函数选择下一个待展开的节点。评估函数考虑：剩余子目标数、剩余目标公式的谓词类型、附加于节点的「差异」的复杂度。

## 核心方法

### 直觉

STRIPS 的核心洞见分三层：

1. **表示层**：不用 frame axioms 描述「什么不变」，改用 add/delete list 描述「什么改变」——其余默认不变。这一「STRIPS 假设」（closed-world assumption 的一种）从根本上绕开了框架问题的爆炸。

2. **计算层**：将「定理证明」与「状态搜索」分离。定理证明器只在单个世界模型内工作（判断前提是否满足、目标是否达成），不再承担跨状态的搜索职责。跨状态的搜索交由 GPS 式的 means–ends analysis 处理。

3. **存储层**：以 initial model 为基础，用 ADDITIONS / DELETIONS 差量描述后继世界，通过 visibility flag 让同一个 wff 池服务于多个假想世界。

### 形式化描述（伪代码）

```
STRIPS(M_0, O_set, G_0):
    root ← Node(M_0, goal_list=[G_0])
    open ← {root}
    while open ≠ ∅:
        n ← select-node-by-heuristic(open)
        M, [G_1, G_2, ..., G_k] ← n
        # 尝试证明当前最上层子目标
        result ← QA3.5.prove(M, G_1)
        if result.success:
            if k == 1: return trace of operators
            n' ← Node(M', [G_2, ..., G_k])      # M' 由 result 对应的操作符应用得到
            open.add(n')
        else:
            diff ← result.unfinished-proof     # 「差异」= 未完成的归结证明
            candidates ← operators whose add-list matches predicates in diff
            for O(p) in candidates:
                # 通过归结检查该操作符能否推进未完成证明
                relevant-instances ← unify diff with add-list of O(p)
                for O(c) in relevant-instances:
                    subgoal ← precondition of O(c)
                    n'' ← Node(M, [subgoal, G_1, G_2, ..., G_k])
                    open.add(n'')
```

### 关键定理与证明

STRIPS 论文并未给出正式的完备性或最优性定理——这与 A* (1968) 形成鲜明对比。作者更关注「工程可行性」而非「理论最优」。真正的形式化定理化要等到 1980 年代 Lifschitz 对 STRIPS 语义的重构工作。

不过论文有一个隐含的**正确性论证**：若操作符的 add/delete list 完整刻画了行动的所有影响（STRIPS 假设成立），则搜索输出的操作序列在任一叶节点上能证明目标 $G_0$，就意味着该序列在真实世界中执行也能达到目标。这一论证的前提——完整刻画——正是后来 Reiter 后继状态公理试图形式化的东西。

### 与前人方法的本质区别

| 维度 | Green (1969) | STRIPS (1971) |
|------|--------------|---------------|
| 行动表示 | 情景演算 + frame axioms | Precondition + add-list + delete-list |
| 推理机制 | 单一定理证明器 | 定理证明（局部）+ means-ends 搜索（全局）|
| 框架问题 | 需显式写出 frame axioms | 通过默认假设隐式解决 |
| 多状态存储 | 每个 situation 独立表示 | 差量 + visibility flag |
| 可扩展规模 | 2–3 步规划 | 6+ 步规划（Shakey 实际使用）|

## 关键公式推导

### 公式 1：世界模型的差量更新

**原文表述：**

给定当前世界模型 $M_j$、待应用的操作符实例 $O(\vec{c})$、其 delete list $D$ 与 add list $A$，新的世界模型为：

$$M_{j+1} = (M_j - D) \cup A$$

**逐步推导：**

Step 1: 定义 $M_j$ 为 wffs 的集合——依据论文 §2.1 的世界模型定义。

Step 2: 论文假设每个操作符 $O(\vec{c})$ 描述了一个原子的状态转移，其对世界的**全部影响**由 $(D, A)$ 刻画——这是 STRIPS 假设（The STRIPS Assumption）。

Step 3: 由 STRIPS 假设，$M_j$ 中未在 $D$ 中的每一条 wff 在新世界仍然为真。因此：

$$M_{j+1} \supseteq M_j \setminus D$$

Step 4: $A$ 中的每条 wff 是新世界中新增的事实：

$$M_{j+1} \supseteq A$$

Step 5: 由 STRIPS 假设的极小性——**除此之外无其他改变**：

$$M_{j+1} = (M_j \setminus D) \cup A$$

**直觉理解：**

这个公式的深刻之处不在于它的数学复杂度（几乎没有），而在于它把「框架问题」从**表示**层面转移到了**假设**层面。McCarthy 情景演算中，若某状态有 $n$ 个谓词、$m$ 个动作，则 frame axioms 的数量为 $O(nm)$——每个动作要显式声明它不改变的 $n-1$ 个谓词。而 STRIPS 只需为每个动作写出 $|D| + |A|$ 条 wffs，通常远小于 $n$。

代价是：**闭世界假设**——凡未在 add list 中出现的谓词都不为真，凡未在 delete list 中出现的原有事实都仍为真。这一假设在大多数机器人任务中合理，但在开放域（如 web 上的知识推理）中就是严重局限。

### 公式 2：STRIPS 差异的计算

**原文表述：**

若在世界模型 $M$ 上试图证明目标 $G_j$ 而未成功，则「差异」（difference）定义为：

$$P = \{\text{negation of } G_j\} \cup \{\text{descendants under resolution}\} \setminus \{\text{clauses eliminated by editing}\}$$

**逐步推导：**

Step 1: 归结定理证明的标准做法——证明 $M \vdash G_j$ 等价于证明 $M \cup \{\neg G_j\}$ 不可满足。

Step 2: 若在给定计算预算内未能推出空子句，则归结栈中留下一组「未消解」的子句——它们是 $\neg G_j$ 的归结子代。

Step 3: 减去编辑策略（如 subsumption、predicate evaluation）已消除的子句，剩余部分即差异 $P$。

Step 4: $P$ 中每一条 clause 都是**证明尚缺什么**的具体表达。例如，若 $P$ 中有 $\neg \text{AT}(BOX2, m) \lor \neg \text{ATR}(m)$，这意味着「要完成证明，我们需要一个位置 $m$，让 $BOX2$ 和 robot 都在那里」。

**直觉理解：**

Green 的 QA3 只告诉你「未能证明」；STRIPS 更进一步：**用未完成的证明本身作为对当前差异的诊断**。哪些谓词还需成立？哪些操作符的 add list 恰好能填补这些谓词？后者就是「相关操作符」。

这是一个精巧的双重利用：定理证明器不仅回答「目标是否成立」，其失败模式还携带了「下一步该做什么」的信息。means–ends analysis 从 GPS 里借来的「difference」概念，在这里被赋予了严格的逻辑形式。

## 实验分析

### 实验设置

- 硬件：PDP-10（DEC 大型分时机）
- 语言：LISP
- 定理证明器：QA3.5（Garvey & Kling 1969 版本，Robinson 归结算法的扩展）
- 世界：走廊 + 4 个房间 + 3 个箱子 + 1 个电灯开关（约 24 条 axioms 描述初始世界）
- 操作符：`gotol`（去坐标点）、`goto2`（去物品旁）、`pushto`（推物）、`turnonlight`、`climbonbox`、`climboffbox`、`gothrudoor`

### 主要结果（Table 2）

| 任务 | 总时间 (s) | 定理证明时间 (s) | 解路径节点数 | 搜索树节点数 | 解操作符数 |
|------|-----------|-----------------|-------------|-------------|-----------|
| 开灯（Monkey-and-Bananas 变体）| 113.1 | 83.0 | 13 | 21 | 6 |
| 推三箱聚合 | 66.0 | 49.6 | 9 | 9 | 4 |
| 跨房间抵达指定坐标 | 123.0 | 104.9 | 11 | 12 | 5 |

**自主解读：**

三个数据点足以支撑作者的核心主张：搜索树几乎不「浪费」节点——解路径节点数与搜索树总节点数比值接近 1，说明 means–ends 启发式**极其精确地**指向了解。但代价明显：约 70–85% 的时间花在定理证明本身。这暴露了 STRIPS 的性能瓶颈——归结定理证明的常数因子太大，一个 4 步计划要花 66 秒。这个瓶颈在后续 NOAH、GRAPHPLAN 等系统中被通过放弃通用归结、使用更专门化的匹配算法所解决。

作者坦诚地指出「Turn on the lightswitch」任务得到的解并非最优（6 步，深度优先偏好导致），这是启发式搜索的典型现象。

### 关键发现

搜索树几乎「零冗余」——但这是因为任务被精心挑选来展示 means–ends analysis 的威力。三个任务都有清晰的层次结构（先去某处 → 再操作），启发式恰好匹配这种结构。若换成需要「先破坏再重建」的任务（如 Sussman anomaly，1973 由 Sussman 提出），STRIPS 的贪婪 means–ends 会陷入交互子目标的困境。

### 实验设计评价

- **优点**：任务多样（导航、推物、开关），共用一套世界模型和操作符库，展示了框架的通用性。
- **不足**：
  · 未与 Green 的 QA3 系统做定量对比（只是文中说「Green 的系统无法解决 3-Box 问题的完整版」）
  · 未涉及计划失败或计划修复的场景
  · 未讨论 Sussman anomaly 这类目标交互问题（合理，因为它 1973 年才被提出）

## 局限性

### 作者自述（论文 §5「Future Plans and Problems」）

1. **搜索启发式仍不够精细**：评估函数需要进一步实验
2. **只能生成线性操作序列**：不支持迭代、递归、条件分支——即无法生成「程序」而只能生成「路径」
3. **不能自我学习新操作符**：作者提到希望通过 Hart & Nilsson (1971) 的 macro operator 思路和 Hewitt 的 procedural abstraction 来解决

### 后续批评

- **Sussman anomaly（1973）**：Sussman 展示了一个简单的积木世界目标（Goal: `on(A,B) ∧ on(B,C)`），STRIPS 的线性规划器会陷入循环——先满足其中一个子目标会破坏另一个的前提。这暴露了 STRIPS 的**目标独立性假设**过强。
- **框架问题未真正解决，只是被回避**：Hayes、McCarthy 等人后来指出，STRIPS 假设本质上是**闭世界假设 + 完备操作符描述**——这在需要不完全信息推理的场景中失效。Reiter 1991 的**后继状态公理**才给出情景演算下的正式解决。
- **表达力有限**：STRIPS 无法表达条件效果（"如果 X 则 Y 也变"）、量化效果（"所有 X 都变成 Y"）、时间/资源约束。这些缺陷在 ADL (Pednault 1989)、PDDL 2.x (Fox & Long 2003) 中逐步补齐。

### 假设检验

STRIPS 假设成立的条件：世界模型使用**原子命题**（primitive predicates），且每个操作符的效果仅涉及少量 primitive predicates 的直接改变。当引入派生谓词（NEXTTO 由 AT 派生）时，作者已经开始感到不安——论文 §3.2 讨论了「非原始子句」的复杂处理。这是「STRIPS 假设」开始破裂的征兆。

## 后续影响

### 直接后继（1970s–1980s）

1. **ABSTRIPS**（Sacerdoti 1974）——分层规划，操作符前提按抽象层次排序
2. **NOAH**（Sacerdoti 1975）——非线性规划器，用 partial order 表示部分完成的计划，正面攻击 Sussman anomaly
3. **NONLIN**（Tate 1977）——引入 goal-protection 机制
4. **MOLGEN**（Stefik 1980）——用元规划思想扩展 STRIPS
5. **TWEAK**（Chapman 1987）——第一个可证明正确、完备的部分排序 STRIPS 规划器

### 开创的方向

- **经典规划**（classical planning）作为 AI 子领域整体形成
- **操作符-状态**表示范式主导规划研究 40 余年
- 派生出「domain description language」的整条谱系

### 当代回响

- **PDDL**（Planning Domain Definition Language, McDermott 1998）——国际规划竞赛（IPC）的标准语言，其 1.x 版本几乎完全对应 STRIPS：`(:action ... :precondition ... :effect (and ... (not ...) ...))`。整整一代规划学者的第一次编程作业就是「写一个 STRIPS 域文件」
- **GRAPHPLAN**（Blum & Furst 1995）、**FF**（Hoffmann & Nebel 2001）、**LAMA**（Richter & Westphal 2010）等现代规划器都以 STRIPS/PDDL 域为输入
- **强化学习与 LLM 结合**：2023 年以来，多篇工作（如 LLM+P、SayCan）用大模型将自然语言目标编译为 PDDL/STRIPS 域，交由经典规划器求解——STRIPS 的 add/delete 表示在 LLM 时代获得新生

### 引用统计

- Semantic Scholar：约 3,500+ 次引用（截至 2025 年）；标注「高影响力引用」约 320 次
- Google Scholar：约 6,200+ 次引用
- 2011 年获 AAAI Classic Paper Award

## 个人笔记

读这篇论文，几个印象最深的地方：

**第一，工程直觉的胜利**。1971 年的 AI 领域正陷入「一切用逻辑」的教条中。Green 的 QA3 象征着「AI 应当是数理逻辑的应用」的理想。Fikes 与 Nilsson 做的事情，本质上是承认：**通用性有时是效率的敌人**。分离定理证明与搜索，牺牲一部分数学优雅，换来数量级的效率提升。这个决断在当时并非显然。

**第二，STRIPS 假设的政治性**。「凡未在 add/delete list 中提及的都不改变」——这是一种**默认推理**（default reasoning），逻辑上并非有效。它工作是因为**领域被约束得足够好**：Shakey 的世界是一个封闭的、静态背景 + 有限的可动实体。当我们把 STRIPS 应用到复杂开放世界时，这一假设就是各种失败的根源。1980 年代的非单调推理研究——Reiter 的 default logic、McCarthy 的 circumscription——某种意义上都是在为 STRIPS 假设「补票」。

**第三，作者顺序的意味**。这是我第一次注意到 SRI 传统的作者顺序。Fikes 是主要程序员和第一作者，但 Nilsson 在概念设计上贡献甚多。在他 2010 年的回忆录 *The Quest for AI* 中，Nilsson 极坦率地叙述了自己「反对情景演算 + frame axioms」的直觉如何催生了 add/delete list 表示，同时把 means–ends analysis 的想法归功于 Fikes。这种「谁贡献大谁列前」的传统与今日 AI 论文中盛行的「按字母序」「顾问最后」形成有趣对比。

**第四，一个未被作者点破的洞察**。论文 §3.3 讨论「difference 计算」时，其实隐含地把归结定理证明当成了一个**可微诊断器**——不仅告诉你「证明失败」，还告诉你「因为哪些子句尚不能被消去」。这个思路在今天的 LLM + symbolic 混合系统中重新出现：用 constraint solver 的失败模式来引导 LLM 生成下一步。半个世纪前的洞察，穿透了范式的更迭。

## 小红书写作备忘

### Hook 素材

1. **五页纸 vs. 二十页纸**：McCarthy 1963 五页备忘录（#20）提出情景演算，八年后 Fikes & Nilsson 二十页论文给出实用的绕过方案——两代规划学者对话的开端
2. **一个默认假设的力量**：STRIPS 用一句「凡未提及的都不变」，绕开了 AI 十年的框架问题公案
3. **PDDL 的祖父**：至今每一届国际规划竞赛（IPC）的域文件，都在使用 STRIPS 1971 年发明的三段式（precondition/add-list/delete-list）

### 核心 Insight（一句话）

**用「表示」的巧妙，来置换「推理」的爆炸——STRIPS 假设让规划器不必显式描述「什么不变」，从而让 AI 规划第一次从玩具例子走向真实机器人。**

### 自查重点

1. **框架问题**不是 STRIPS 首次提出的（1969 McCarthy & Hayes 已命名），也不是 STRIPS 彻底解决的——只是**回避**
2. Fikes 的博士导师是 **Allen Newell**（不是 Simon 也不是 McCarthy）
3. 1971 年发表在 *Artificial Intelligence*（不是 IJCAI Proceedings——虽然同名论文在 IJCAI 上做过报告）
4. STRIPS 假设的正式名称是「closed-world assumption 的一种」——严格来说 STRIPS assumption 是关于**操作符描述完备性**的假设，与一般的 CWA 有细微差别
5. Sussman anomaly 是 1973 年提出的，不能说是「STRIPS 论文中就存在的问题」——它是 STRIPS 的**后续被指出**的局限
6. Nilsson 是 A* 论文（1968，#15）的三位作者之一，也是 STRIPS 的第二作者——这是同一个人在两个基础工作上都留下印记

### 动态 Hashtags

- #AI规划 
- #自动规划
- #知识表示
