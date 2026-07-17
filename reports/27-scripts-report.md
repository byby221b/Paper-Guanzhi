# 精读报告 #27: Scripts, Plans, Goals, and Understanding

## 元信息

- 标题：Scripts, Plans, Goals, and Understanding: An Inquiry into Human Knowledge Structures
- 作者：Roger C. Schank（耶鲁大学计算机科学与心理学）、Robert P. Abelson（耶鲁大学心理学）
- 发表：Lawrence Erlbaum Associates, Hillsdale, New Jersey, 1977（专著，约 248 页）
- ISBN：0-470-99033-3 / 978-0-203-78103-6（后续重印版）
- 原文链接（预览本，27 页，含 Preface + Ch.1）：https://api.pageplace.de/preview/DT0400.9781134919666_A23788687/preview-9781134919666_A23788687.pdf
- 精读日期：2026-07-17
- 对应小红书期号：#27
- 备注：本书不是传统论文，而是认知科学（Cognitive Science）初创期的一部理论专著。作者在 Preface 中坦承本书「提出的问题多于回答的问题」，四个核心概念（scripts, plans, goals, themes）按确定性递减排列——「我们真正理解 script，对 plan 相当有把握，对 goal 稍不确定，对 theme 则较模糊」。本报告基于可获取的预览本（Preface、目录、第 1 章全文）加二手文献（Jim Davies 章节摘要、作者传记、后续研究综述）撰写。

## 作者背景

### Roger C. Schank（1946–2023）

- 出生：1946 年 3 月 12 日，纽约市；2023 年 1 月 29 日卒于佛蒙特州 Shelburne，享年 76 岁
- 本科：Carnegie Mellon University，数学
- 博士：University of Texas at Austin，语言学博士，1969 年，导师 Jacob L. Mey；博士论文即 *A Conceptual Dependency Representation for a Computer-Oriented Semantics*
- 1974 年赴耶鲁，任计算机科学与心理学教授；1981 年任耶鲁计算机科学系主任兼耶鲁 AI 项目主任
- 核心贡献：
  · **Conceptual Dependency（CD，概念依存）理论**（1969）——一种与具体自然语言无关的意义表示，将动词分解为有限组「原语行为」（primitive acts，如 PTRANS、ATRANS、MTRANS 等）
  · **Script 理论**（本书，与 Abelson 合著）
  · **Dynamic Memory**（1982）——提出记忆组织包（MOP, Memory Organization Packet），修正了早期 script 过于僵硬的问题
  · **Case-Based Reasoning（CBR，基于案例的推理）**——由其动态记忆模型催生，直接影响了 Kolodner 的 CYRUS、Lebowitz 的 IPP 等系统
- 后期转向学习科学：1989 年获 Andersen Consulting 资助创立西北大学 Institute for the Learning Sciences

### Robert P. Abelson（1928–2005）

- 美国社会心理学家，长期任教于耶鲁大学
- 研究横跨态度与信念系统、认知一致性、社会认知，以及统计方法论（其名著 *Statistics as Principled Argument*, 1995 影响深远）
- 在本书中，Abelson 的社会心理学视角为「信念系统」「主题」（themes）等偏向人类意图与人际关系的高层结构提供了支撑。Schank 在 Preface 中戏称需要不断「拦住 Abelson，防止他滑入超出本书范围的抽象心理学思辨」
- Schank 与 Abelson 的合作始于 1971 年夏一场跨学科工作坊（心理学 × AI × 语言学），二人自陈那次会议上产生了「也许我们正在定义一个新学科」的自觉——后来这个学科被称为 Cognitive Science

### 合作者与学术群落

本书的研究是耶鲁两年间大量 seminar 的产物。参与者名单堪称一份日后 AI/认知科学的花名册：Jaime Carbonell、Gerald DeJong、Wendy Lehnert、Christopher Riesbeck、Robert Wilensky、Richard Cullingford、James Meehan 等。书中程序分工明确：Cullingford 主导 SAM 的 script applier；Wilensky 负责 PAM（plans/goals）；Meehan 负责 TALESPIN（故事生成）；DeJong 负责 FRUMP（新闻略读）。

## 历史语境

### 当时的学术主流

1977 年，符号主义 AI 正处在从「玩具问题」向「真实世界知识」艰难过渡的阶段。作者在第 1 章开宗明义地批评了两条路线：

1. **逻辑主义**：以 McCarthy 的情景演算、Robinson 的归结原理为代表。作者直言「基于严密逻辑系统的机械式方法，一旦扩展到真实世界任务就力不从心——真实世界是杂乱而常常不合逻辑的」。

2. **语言学（转换生成语法）**：作者批评 Chomsky 传统「从 S（句子）节点出发」，而人「从一个已经成形的想法出发」。语言学「几乎完全忽略了人类理解是如何运作的」。

3. **实验认知心理学**：作者认为其材料过于人为（如让被试听到音素 /b/ 就按键），「太不自然、太慢」，无法支撑一个关于连贯语篇理解的大理论。他们因此选择「远超通常实验验证程度地去构造理论」。

### 待解决的核心问题

一个看似简单却极难的问题：**机器（或人）如何理解一小段故事？** 例如「John 走进餐馆，点了汉堡，付账后离开」——读者能毫不费力地推断：John 坐下了、有服务员、他吃了汉堡、他给的是钱而非别的。这些**未被言明的信息从何而来？** 传统逐句语义分析无法解释这种「填补空白」的能力。

### 直接前驱

- **Schank 的 Conceptual Dependency 理论**（1972, 1975）——本书的直接基座，提供事件层面的意义表示；本书则跃升到事件之间的**意图与语境连接**，即作者命名的 Knowledge Structure（KS）层
- **Minsky 的 Frame 理论**（1974，本系列 #24）——平行的知识表示思潮，script 可视为 frame 思想在「时序事件序列」上的具体化
- **Abelson 的信念系统研究**（1973）——themes 一章的思想来源
- **Bartlett 的 Schema**（1932）与 **Heider 的「朴素心理学」（naive psychology）**（1958）——认知结构与常识归因的心理学渊源

## 问题形式化

### 问题定义

给定一段自然语言文本（通常是几句话到一个短故事），要求系统：
1. 建立文本的意义表示（在 CD 层）；
2. 补全文本未明说、但为理解所必需的**因果链**（causal chain）与**推断**；
3. 据此回答关于该文本的问题、做摘要、或复述。

### 输入与输出

- 输入：自然语言故事（英文句子序列）
- 输出：一个连贯的概念表示（可回答问题、可生成摘要）；对 TALESPIN 而言则反向——由 goals/plans 生成故事

### 目标 / 评价准则

作者的评价标准是**行为主义式的功能验证**：程序能否正确回答关于故事的问题、能否填补合理的隐含事件、能否生成合乎常理的摘要或故事。作者明言不追求形式化证明，而追求「可运行且看起来合理」。

## 核心方法

### 直觉

理解一段日常叙事，靠的不是从公理出发的逻辑推导，而是**调取预存的知识结构**。作者的核心命题极为凝练：「理解一个情境，意味着此前曾身处该情境。」（understanding a situation means having been in that situation before）人之所以能瞬间读懂「餐馆故事」，是因为脑中早有一份「餐馆脚本」。

作者区分了两类知识：
- **specific knowledge（特定知识）**：高度程式化的情境，用 **script** 处理；
- **general knowledge（一般知识）**：非程式化情境，需借助对行动者 **goal（目标）** 的建模及可用 **plan（计划）** 的推理。

四个概念构成一个从「熟悉」到「新颖」、确定性递减的谱系：

**Script → Plan → Goal → Theme**

### 形式化描述：Script（脚本）

Script 被定义为「定义某个熟知情境的、程式化的动作序列」（a stereotyped sequence of actions that defines a well-known situation）。以经典的**餐馆脚本（Restaurant Script）**为例，其结构包含：

- **Roles（角色）**：不同视角的行动者——顾客、服务员、厨师、老板
- **Props（道具）**：桌子、菜单、食物、账单、钱
- **Entry conditions（进入条件）**：顾客饿了、顾客有钱
- **Results（结果）**：顾客不饿了、顾客钱变少、老板钱变多
- **Scenes（场景）**：顺序展开的阶段——Entering（进入）、Ordering（点餐）、Eating（用餐）、Exiting（离开）
- **Tracks（轨道）**：同一情境的变体——如正式餐厅 track vs. 快餐 track

每个 scene 含一个 **MAINCON（main conceptualization，主概念）**——只要该 scene 被实例化，就「必然发生过」的核心事件。此外 script 允许 **branches（分支）** 与 **loops（循环）**，以处理「菜没了」等偏离。

Script 有两种运作机制：
- **Script retrieval（脚本提取）**：当文本提及某个前置状态（顾客饿、有钱）加上对 MAINCON 或 prop 的直接指涉（「点了汉堡」）时，脚本被激活；
- **Script application（脚本应用）**：一旦脚本激活，系统便可推断未言明的动作、填充角色空位——这正是脚本的**预测力**来源。

Schank 与 Abelson 还区分了三类脚本：
- **Situational scripts（情境脚本）**：特定场所的标准社会情境（如餐馆）
- **Personal scripts（个人脚本）**：如顾客暗中与服务员调情
- **Instrumental scripts（工具脚本）**：如点烟这类高度固定的操作序列

多脚本并存时会产生 **interference（干扰）**；当意外结果中断脚本、或递归调用另一脚本时，原脚本进入 **abeyance（暂挂）** 状态。

### 从 Script 到 Plan、Goal、Theme

- **Plan（计划）**：当情境不落入任何现成脚本时，理解者须借助**通用规划知识**。Plan 由达成目标的一般方法构成，含 **planboxes**（如「若想拥有某物，可 ASK/BARGAIN/STEAL」）与 **named plans（命名计划）**。脚本可视为「被反复使用而固化下来的计划」。
- **Goal（目标）**：驱动 plan 的意图。作者提出 **goal fate graph（目标命运图）**、**goal substitution（目标替换）** 等，并归类若干目标形式（如 satisfaction goals、achievement goals、preservation goals）。
- **Theme（主题）**：产生目标的更高层背景，含 **role themes（角色主题）**、**interpersonal themes（人际主题）**、**life themes（人生主题）**。主题回答「这个人为什么会有这个目标」。

### 关键定理与证明

本书不含数学定理或证明。其「论证」形式是：提出一个知识结构 → 用它解释某段故事的理解过程 → 用一个可运行程序（SAM/PAM/TALESPIN）作为「可行性的仲裁者」。作者在 Preface 中明言，之所以坚持用计算机，是因为「不借助计算机，你根本无法知道你所理论化的东西究竟能否运作，遑论正确与否」。

### 与前人方法的本质区别

| 维度 | 逻辑主义 / 语言学 | Script 理论 |
|------|------------------|-------------|
| 理解的基础 | 从公理/语法规则推导 | 从记忆调取预存结构 |
| 知识粒度 | 原子命题、句法规则 | 大块的、程式化的事件序列 |
| 隐含信息 | 难以生成 | 由脚本默认填充 |
| 对「例外」的处理 | 组合爆炸 | 分支、循环、脚本暂挂 |
| 验证方式 | 形式证明 | 可运行程序的功能表现 |

## 关键公式推导

本书为理论专著，无数学公式。此处代之以**餐馆脚本推断链的形式化重构**，展示脚本如何补全隐含事件——这正是全书方法论的技术内核。

### 推断链：从「John 点了汉堡并付账离开」到完整理解

**原文（输入）：**
> John went to a restaurant. He ordered a hamburger. He paid and left.

**逐步推断（脚本应用）：**

Step 1: 检测到 prop「restaurant」+ 状态暗示 → **激活 RESTAURANT script（situational track）** —— 依据：script retrieval 机制。

Step 2: 「ordered a hamburger」匹配 **Scene: Ordering** 的 MAINCON（MTRANS 顾客→服务员：欲要食物）→ 实例化 Ordering 场景 —— 依据：MAINCON 一旦被指涉，则该 scene「必已发生」。

Step 3: 由脚本的 scene 顺序（Entering→Ordering→Eating→Exiting）**回填缺失场景**：
- Entering：John 进门、被引至座位、拿到菜单（未言明，默认填充）
- Eating：厨师做汉堡、服务员上菜、**John 吃了汉堡**（未言明，但 Eating 场景的 MAINCON 强制成立）
—— 依据：script application 的默认推断。

Step 4: 「paid」匹配 **Scene: Exiting** 的一部分（ATRANS 顾客→老板：钱）→ 实例化 Exiting；据 entry/result 条件，推断 **John 此前有钱、此后钱变少、老板钱变多** —— 依据：script 的 entry conditions 与 results。

Step 5: 据 result「顾客不再饿」→ 补全 John 的目标满足状态 —— 依据：script 的 results 字段。

**直觉理解：**
输入只有 3 句话、约 3 个显式事件，但读者「理解」到的是一个包含十余个事件的完整因果链。脚本理论的洞见在于：**这些多出来的信息不是推导出来的，而是被「期望」出来的**——它们预装在脚本里，除非文本提供反证（如「汉堡卖光了」触发分支），否则默认成立。这与 Minsky frame 的「default assignment（默认赋值）」是同一思想在时序事件上的体现。

## 实验分析

本书的「实验」是四个可运行的计算机程序，作为理论的存在性证明。

### 系统设置

- **SAM（Script Applier Mechanism）**：由 Cullingford 主导。读入新闻式短故事，套用脚本，能做**摘要、复述（含跨语言）、问答**。是脚本理论最直接的验证。
- **PAM（Plan Applier Mechanism）**：由 Wilensky 主导。当故事不落入现成脚本时，用 plan/goal 知识理解人物意图。
- **TALESPIN**：由 Meehan 主导。**反向**运行——给定人物目标，自动**生成**故事（如「饥饿的乌鸦想要奶酪」这类伊索寓言式叙事）。
- **FRUMP**：由 DeJong 主导。对新闻做「略读式理解」（skimming），只提取脚本相关要点。

### 主要结果与解读

在受限的题材内，SAM 能对餐馆、地震等有明确脚本的新闻做出像样的摘要与问答；TALESPIN 能生成结构完整的小故事。这些程序**证明了脚本/计划机制在原理上可运作**——在 1977 年，这本身就是有力的存在性论证。

但我的解读是：这些系统的成功高度依赖**手工编码的脚本库**。SAM 之所以「懂」餐馆，是因为研究者预先把餐馆脚本写进了系统。一旦故事偏离已编码的脚本，系统便束手无策——这与 SHRDLU（#23）暴露的「微世界」局限如出一辙。程序验证的是「若有脚本，则能理解」，而非「系统能自行获得脚本」。作者对此显然自知，故第 9 章专门讨论「脚本如何习得」，但那一章的确定性也最低。

### 实验设计评价

- 优点：以可运行程序作为理论仲裁者，方法论上极为超前——「不能运行的理论不算数」这一立场，至今仍是 AI 的健康准则。
- 不足：无量化基准、无对照、无可复现的数据集（这在 1977 年是常态）；系统的「理解」范围由人工脚本库的边界决定，泛化能力未经检验。

## 局限性

### 作者自述

作者在 Preface 中异常坦诚：本书「不是对已证明或已知为真之事的总结」，「松散的线头（loose ends）比比皆是」；四个概念的确定性递减，themes 之外「完全不知道还有什么」。他们选择在「差不多的地方」停笔，因为「多数研究其实从未真正完成」。

### 后续批评

- **脚本过于僵硬（brittleness）**：真实世界的情境很少精确匹配预存脚本，脚本间的组合与偏离处理成为难题。Schank 本人在 *Dynamic Memory*（1982）中以 **MOP（Memory Organization Packet，记忆组织包）** 取代僵硬的整体脚本——将脚本拆成可跨情境复用的更小场景单元，并引入基于失败的学习。这实际上是作者对自己 1977 年方案的重要修正。
- **知识获取瓶颈（knowledge acquisition bottleneck）**：脚本须人工编写，无法规模化——这是 1980 年代专家系统运动共同的死结。
- **仅限可言语化的知识**：作者主动划界，明言视觉、动觉等「无法以言语形式表示」的知识不在讨论范围（如打壁球、揉面团的「手感」）。

### 假设检验

核心假设——「理解 = 调取曾经历的结构」——在高度程式化的日常情境中相当有力，但在**新颖、创造性、跨领域**的理解任务上并不成立：人显然能理解从未身处的情境。脚本理论解释了「熟练」，却未解释「创新」。

## 后续影响

### 直接后继

- Schank, *Dynamic Memory*（1982）——MOP 与动态记忆，脚本理论的自我扬弃
- Kolodner 的 **CYRUS**、Lebowitz 的 **IPP**——基于案例的记忆系统
- **Case-Based Reasoning（CBR）** 整个子领域——「用过去的案例解决当前问题」，是脚本/动态记忆思想的工程化后裔

### 开创的方向

- 为 **Cognitive Science（认知科学）** 这一交叉学科提供了奠基性文本之一（作者自陈 1971 年工作坊上「Quissett 宣言」的自我实现）
- 确立了 AI 中 **knowledge structures（知识结构）/ 常识推理** 的研究纲领
- **叙事理解与叙事生成**（story understanding / generation）——TALESPIN 是计算叙事学的早期里程碑

### 当代回响

脚本理论描述的「情境的默认期望」在今天以全新形态复现：大型语言模型无需显式脚本，仅凭海量语料的统计学习，就在「餐馆」「机场」等情境上表现出类似脚本的默认补全能力——你说「他走进餐馆」，模型已隐含了点餐、用餐、付账。可以说，Schank 与 Abelson 在 1977 年**精确地描述了这项功能需求**，只是假定它必须依赖显式的符号脚本；而 LLM 提供了一条截然不同的、分布式的实现路径。近年亦有研究（如认知神经科学中的「事件脚本」建模）重新以变分/预测编码框架形式化脚本概念。

### 引用统计

- 该书是 AI 与认知科学领域被引最高的著作之一，Google Scholar 显示引用数在数万量级（不同版本合并统计，具体数字随时间变动）。
- 作为对比，其前身论文 Schank & Abelson, "Scripts, Plans, and Knowledge"（IJCAI 1975）本身亦被大量引用。

## 个人笔记

最打动我的，是 Preface 里那句近乎「反学术」的自白——四个概念按确定性递减排列：「我们真正理解 script，对 plan 相当有把握，对 goal 稍不确定，对 theme 则较模糊，再往外就完全不知道了。」在一个人人追求「完备理论」的年代，两位作者把自己认知的**边界坐标**明明白白标在书的开头。这种诚实本身就是一种学术品格。

第二个触动点是那句「understanding a situation means having been in that situation before」。它表面朴素，却暗含一个至今未解的张力：如果理解全靠调取旧经验，那么人类面对全新情境时的理解能力从何而来？脚本理论精彩地解释了「熟练」，却在「创新」面前沉默。今天的 LLM 某种意义上把这句话推向极端——它「见过」几乎所有情境，于是几乎总能「理解」；但这究竟印证了脚本理论，还是消解了它对「显式符号结构」的坚持？我倾向认为：Schank 对了一半——他抓住了「期望驱动理解」的功能本质，却押错了实现的赌注。

第三，作为方法论范本，本书那句「不能运行的理论不算数」值得反复咀嚼。Schank 让心理学理论必须通过一个能跑的程序来「仲裁」，这种严苛，恰是今天许多空谈概念的研究所缺乏的。

## 小红书写作备忘

### Hook 素材

1. 「John 走进餐馆，点了汉堡，付账离开」——3 句话，你却「读到」了十几件没写出来的事。这些信息从哪来？
2. 两位作者在书的开头老实交代：script 我们真懂，plan 挺有把握，goal 有点心虚，theme 就模糊了——一份罕见的「认知边界自白」。
3. 1977 年他们说「理解一个情境，意味着你曾身处其中」；半个世纪后，LLM 把这句话推到了极端。

### 核心 Insight（一句话）

理解日常叙事，靠的不是从公理出发的逻辑推导，而是从记忆中调取一份「预制脚本」，让未言明的细节由期望自动填充。

### 自查重点

1. 「首次/发明」类措辞——script 是 frame 思想在时序事件上的具体化，与 Minsky（#24）平行；用「系统阐述」「提出」而非「首创常识推理」。
2. 餐馆脚本的结构术语（scenes/roles/props/tracks/MAINCON）须忠于原文，不臆造。
3. MOP 是 Schank **后来**（1982）对脚本僵硬性的修正，不可写成 1977 年本书内容。
4. LLM 类比须降级——用「以不同方式实现了类似功能」而非「验证/实现了脚本理论」。
5. Abelson 身份：社会心理学家（1928–2005），不要写成计算机科学家。

### 动态 Hashtags

#认知科学 #自然语言理解 #知识表示
