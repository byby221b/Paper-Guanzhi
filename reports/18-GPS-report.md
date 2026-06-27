# 精读报告 #18: GPS (General Problem Solver)

## 元信息

- 标题：Report on a General Problem-Solving Program
- 作者：Allen Newell, J. C. Shaw, Herbert A. Simon（RAND Corporation / Carnegie Institute of Technology）
- 发表：
  - RAND Corporation Report P-1584, February 1959（内部报告）
  - Proceedings of the International Conference on Information Processing (IFIP), UNESCO, Paris, 1960, pp. 256–264（正式出版）
- 原文链接：http://bitsavers.informatik.uni-stuttgart.de/pdf/rand/ipl/P-1584_Report_On_A_General_Problem-Solving_Program_Feb59.pdf
- 精读日期：2026-06-26
- 对应小红书期号：#18

## 作者背景

### Allen Newell (1927–1992)
- 发表时身份：RAND Corporation 研究员，同时在 Carnegie Institute of Technology（今 Carnegie Mellon University）任教
- 师承：Princeton（物理学本科），后转入 RAND 从事信息处理研究；未完成传统博士学位，但后来获 Carnegie Mellon 博士
- 此前工作：
  - Logic Theorist (1955-56)：与 Shaw、Simon 合作的第一个 AI 程序，能证明《数学原理》中的定理
  - IPL (Information Processing Language)：与 Shaw 合作开发的第一批列表处理语言
- 后续轨迹：
  - 1975 年与 Simon 共同获得图灵奖（ACM Turing Award）
  - 提出"物理符号系统假说"（Physical Symbol System Hypothesis, 1976）
  - 开发 SOAR 认知架构
  - 1992 年因癌症逝世

### J. C. "Cliff" Shaw (1922–1991)
- 发表时身份：RAND Corporation 系统程序员
- 背景：精通数学，担任 RAND 系统组负责人
- 核心贡献：
  - 设计并实现了 IPL 语言（Information Processing Language）——AI 领域最早的专用编程语言
  - Logic Theorist 和 GPS 的实际编程实现者
  - 被 Newell 称为团队中将理论变为运行程序的关键人物
- 后续：继续在 RAND 工作，后鲜为学界提及（程序员身份在学术出版中常被低估）

### Herbert A. Simon (1916–2001)
- 发表时身份：Carnegie Institute of Technology 教授（政治学/管理学/心理学跨学科）
- 师承：University of Chicago 政治学博士（1943）
- 此前工作：
  - 《Administrative Behavior》(1947)：有限理性（bounded rationality）理论
  - Logic Theorist (1955-56)
  - 对决策过程的开创性研究
- 后续轨迹：
  - 1978 年获诺贝尔经济学奖（有限理性与组织决策理论）
  - 1975 年与 Newell 共获图灵奖
  - 继续研究认知心理学、AI、复杂性科学
  - 2001 年逝世

### 合作关系
- Newell 和 Simon 是终身学术伙伴，从 1950 年代中期在 RAND 开始合作
- Shaw 是 RAND 的职业程序员，是将 Newell-Simon 的理论想法变为运行程序的工程纽带
- 三人合作产出了 Logic Theorist (1955-56)、GPS (1957-59)、IPL 语言，构成了早期 AI 的核心基石

## 历史语境

### 当时的学术主流
1957-1959 年，AI 尚处于"创始期"：
- Dartmouth 会议（1956）刚刚确立了"人工智能"这一名称
- Logic Theorist (1955-56) 刚刚展示了计算机可以"发现"数学证明
- Rosenblatt 的 Perceptron (1957) 代表了连接主义路线
- Samuel 的跳棋程序 (1959) 展示了机器学习
- 核心争论：AI 应该是符号推理还是模式识别？

### 待解决的核心问题
Logic Theorist 成功证明了计算机能解决特定领域（命题逻辑）的问题，但它的方法与领域紧密耦合。核心问题是：
1. 能否将问题求解方法从具体领域中抽离出来？
2. 存不存在"通用的"问题求解策略，适用于多种不同任务？
3. 人类解决问题时使用的核心认知机制是什么？

### 同时期的相关工作
- Rosenblatt (1957): Perceptron——数值方法、模式识别（竞争范式）
- Samuel (1959): 跳棋学习——任务特定的学习方法（平行）
- McCarthy (1958): Programs with Common Sense——逻辑方法（互补）
- Selfridge (1958): Pandemonium——并行竞争识别（竞争范式）

### 直接前驱
1. Newell, Shaw, Simon (1955-56): Logic Theorist——GPS 的直接前身
2. Polya (1945): 《How to Solve It》——启发式方法的系统论述
3. Duncker (1945): 格式塔心理学中的问题解决理论
4. Miller, Galanter, Pribram (1960): 《Plans and the Structure of Behavior》——TOTE 单元（同期平行工作）

## 问题形式化

### 问题定义
给定：
- 一组对象（objects）及其上的属性和关系
- 一组算子（operators），每个可在特定条件下将一个对象变换为另一个对象
- 一个初始对象 $A$（当前状态）
- 一个目标对象 $B$（目标状态）或目标条件

求：一个算子序列 $O_1, O_2, \ldots, O_k$，使得 $O_k(\ldots O_2(O_1(A))\ldots)$ 满足目标条件。

### 输入与输出
- 输入：
  - 任务环境描述（对象集合、算子集合、算子的适用条件和效果）
  - 差异表（table of connections / difference table）：差异类型 → 可能消除该差异的算子
  - 初始状态、目标状态
- 输出：达到目标的算子序列（求解路径）

### 目标 / 评价准则
找到一条从初始状态到目标状态的有效路径，以最少的搜索开销实现。GPS 不追求最优解，而追求"用有限资源找到某个解"。

## 核心方法

### 直觉
GPS 的核心策略是**手段-目的分析**（Means-Ends Analysis）：
1. 比较当前状态与目标状态，找出最重要的差异（difference）
2. 在差异表中查找能消除该差异的算子
3. 如果算子不能直接应用（前置条件不满足），则创建子目标：使算子可应用
4. 递归地解决子目标

这模拟了人类常见的问题解决策略："我想去 B，我在 A。A 和 B 之间最大的差距是什么？什么工具能缩小这个差距？我能用这个工具吗？如果不能，怎样才能用上它？"

### 形式化描述

**三个核心方法（Method）：**

1. **Method Transform(A, B)**：将对象 A 变换为对象 B
   - 比较 A 和 B，找出差异 D
   - 选择与 D 关联的算子 Q（查差异表）
   - 调用 Method Reduce(A, Q, B)

2. **Method Reduce(A, Q, B)**：通过应用算子 Q 来缩小 A 与 B 的差异
   - 检查 Q 对 A 的适用条件
   - 若条件不满足，调用 Method Apply(Q, A) 创建子目标
   - 若条件满足，应用 Q 得到 A'
   - 调用 Method Transform(A', B) 处理剩余差异

3. **Method Apply(Q, A)**：使算子 Q 对对象 A 可应用
   - 比较 A 的当前形式与 Q 的输入条件
   - 找出差异，递归调用 Transform 消除之

**差异表（Table of Connections）：**
一张二维表，行为差异类型，列为可用算子。表中标记每个算子对每种差异的关联程度。GPS 据此选择最可能有效的算子。

**目标栈（Goal Stack）：**
GPS 维护一个目标栈。每当创建子目标，就压入栈中。子目标解决后弹出，回到上级目标继续执行。

### 关键定理与证明
GPS 是工程性/实验性论文，不包含形式化定理。其"正确性"通过在多个领域的成功运行来验证，而非数学证明。

### 与前人方法的本质区别
| 方面 | Logic Theorist | GPS |
|------|---------------|-----|
| 适用范围 | 仅命题逻辑 | 多领域（逻辑、代数、规划等） |
| 问题表示 | 特定于逻辑 | 通用的对象-算子框架 |
| 核心策略 | 后向推理 + 相似性 | 手段-目的分析 |
| 领域知识 | 硬编码在程序中 | 编码在差异表中，与方法分离 |
| 人类模拟 | 部分 | 显式目标（与人类协议对照） |

GPS 的根本创新在于**内容与方法的分离**：通用的问题求解方法独立于具体领域知识。改变差异表和算子定义，就能将同一程序应用于不同任务。

## 关键公式推导

GPS 不是一篇形式化数学论文，核心"公式"是算法过程而非数学表达式。其核心可以用伪代码表示：

### 算法 1：Means-Ends Analysis 主循环

```
TRANSFORM(current, goal):
    diff = DETECT_DIFFERENCE(current, goal)
    if diff = nil: return SUCCESS
    op = SELECT_OPERATOR(diff, table_of_connections)
    if op = nil: return FAIL
    result = REDUCE(current, op, goal)
    return result

REDUCE(current, op, goal):
    if APPLICABLE(op, current):
        new_state = APPLY(op, current)
        return TRANSFORM(new_state, goal)
    else:
        subgoal = PRECONDITION(op)
        modified = TRANSFORM(current, subgoal)
        if modified = FAIL: return FAIL
        new_state = APPLY(op, modified)
        return TRANSFORM(new_state, goal)
```

**直觉理解：**
GPS 将一个大问题（A → B）递归分解为更小的子问题。每次分解的依据是"当前最大的差异"和"最可能消除该差异的操作"。这使得搜索不再是盲目的，而是有方向感的——始终朝着缩小差距的方向努力。

## 实验分析

### 实验设置
GPS 在以下四个领域被测试：

1. **命题逻辑定理证明**
   - 对象：命题逻辑表达式
   - 算子：12 条变换规则（如 A∨B → B∨A）
   - 差异：表达式之间的结构差异（变量类型、连接词类型、位置等）
   - 数据来源：人类被试的口语协议实验

2. **三角恒等式化简**
   - 对象：三角函数表达式
   - 算子：三角恒等式作为变换规则
   - 仅完成了初步测试

3. **不定积分**
   - 对象：数学表达式
   - 算子：积分表中的公式 + 代数变换
   - 思路：将"求积分"视为"将表达式变换为可查表的形式"

4. **国际象棋**
   - 对象：棋盘局面
   - 算子：合法走法
   - 仅完成了最初步的尝试

### 主要结果
- **命题逻辑**：GPS 能证明 Whitehead & Russell《数学原理》中的大部分定理；其搜索路径与人类被试的口语协议有显著相似性
- **领域独立性**：通过更换差异表和算子定义，同一程序框架确实能在不同领域工作
- **人类行为模拟**：GPS 的子目标分解顺序与人类被试的思考过程高度吻合

### 关键发现
- **方法与内容的分离可行**：GPS 证明了通用问题求解策略确实存在，且可以与领域知识分离
- **手段-目的分析的普遍性**：这一策略在人类问题解决行为中非常普遍

### 实验设计评价
- 优点：
  - 在多个领域展示了同一方法的适用性
  - 与人类行为协议的对照实验在方法论上具有开创性
- 不足：
  - 每个领域的测试都很初步，未做系统的性能对比
  - 差异表需要人工设计——"通用性"有多大程度依赖于设计者的领域知识？
  - 论文本身更像进度报告（report），而非最终的实验论文

## 局限性

### 作者自述
1. GPS 的通用性有限——它依赖于手工设计的差异表和算子形式化
2. 搜索空间的指数增长问题并未根本解决，只是通过启发式缓解
3. 作者在报告中明确承认，某些领域（如象棋）的测试尚不充分

### 后续批评
1. **虚假的通用性**：GPS 的"领域独立"实质上将领域知识转移到了差异表的设计中。真正困难的工作是构造差异表，而非运行 GPS [Dreyfus, 1972]
2. **框架问题**：GPS 假设所有对象状态可显式表达、所有差异可枚举——这在开放世界中不成立 [McCarthy & Hayes, 1969]
3. **组合爆炸未根本解决**：手段-目的分析虽然比盲目搜索好，但在深度复杂问题上仍然面临指数级子目标生成
4. **缺乏学习能力**：GPS 不能从经验中改进其差异表或策略选择
5. **表示限制**：对象必须以符号结构表达，难以处理连续值或感知输入

### 假设检验
- **物理符号系统假说**（隐含）：GPS 假设智能行为可以通过符号操作实现。这一假设后来受到连接主义和具身认知学派的挑战
- **差异表的完备性**：GPS 假设差异表能覆盖所有重要差异，但实际中差异类型的选择本身就需要智能

## 后续影响

### 直接后继
1. **STRIPS (Fikes & Nilsson, 1971)**：自动规划领域的奠基工作，直接继承了 GPS 的算子-状态框架
2. **Newell & Simon (1972)**: 《Human Problem Solving》——将 GPS 思想扩展为完整的认知心理学理论
3. **SOAR (Laird, Newell, Rosenbloom, 1987)**：GPS 思想的现代继承者，统一的认知架构
4. **Production Systems**：GPS 的目标-规则结构发展为生产系统（如 OPS5、ACT-R）

### 开创的方向
1. **自动规划**（Automated Planning）：GPS 的对象-算子-目标框架成为规划领域的基本范式
2. **认知建模**：GPS 开创了用计算机程序模拟人类认知过程的方法论
3. **启发式搜索**：手段-目的分析成为 AI 启发式搜索策略的经典范例
4. **知识表示**：领域知识与求解方法分离的思想影响了专家系统和知识工程

### 当代回响
- **HTN Planning**（Hierarchical Task Network）：分层任务规划继承了 GPS 的子目标递归分解思想
- **Goal-Oriented Behavior** in Games AI：游戏 AI 中的 GOAP（Goal-Oriented Action Planning）与 GPS 有直接的思想渊源
- **LLM Agent Planning**：当代大模型 agent 的"思考-行动-观察"循环，在结构上与 GPS 的 Transform-Reduce-Apply 有惊人相似
- **认知科学**：手段-目的分析仍是认知心理学教科书中解释人类问题解决的核心理论框架

### 引用统计
- Google Scholar 引用数：约 2,800+（截至 2026 年，包括不同版本引用的合计）
- 作为概念影响而非直接技术引用，GPS 的实际学术影响远超其引用数字所示
- Newell 与 Simon 共同获得 1975 年图灵奖，GPS 和 Logic Theorist 是主要贡献

## 个人笔记

读 GPS 这篇报告，最让我触动的不是技术方案本身，而是它的研究方法论。Newell、Shaw 和 Simon 做了一件在 1957 年极为超前的事：他们先让大学生"出声思考"（think aloud）解题，记录口语协议（verbal protocol），然后尝试写一个程序来精确复现被试的认知过程。GPS 不是从数学或工程出发的——它是从观察人类行为出发的。

这意味着 GPS 的成功标准不仅仅是"能否解出题目"，还包括"解题过程是否像人"。这在 1957 年的 AI 研究中是独特的视角。Logic Theorist 的目标是"证明定理"；GPS 的目标是"像人类那样解决问题"。从工程目标到认知模拟目标的跃迁，标志着 AI 研究开始分化为两条路线：一条追求性能，一条追求理解。

另一个让我印象深刻的细节是"内容与方法的分离"。GPS 将"怎么解决问题"（通用方法）和"解决什么问题"（领域知识）显式地分开。这个思想后来在专家系统中被称为"推理引擎与知识库分离"，在现代架构中则体现为"模型能力与 prompt 指令分离"。六十七年后的 LLM agent 框架——用 prompt 提供任务描述、让模型的通用推理能力来求解——与 GPS 的哲学有着惊人的结构对应。

Shaw 的角色也值得注意。三个作者中，Newell 提供理论框架，Simon 提供跨学科视野和心理学方法论，而 Shaw——一位 RAND 的系统程序员——完成了所有实际编程。在一个没有高级语言的年代（他们甚至要先发明 IPL 语言来编写 GPS），将抽象的认知理论变为运行程序本身就是非凡的智识成就。但 Shaw 在学术史中常常只是作为"第三作者"被一笔带过。

## 小红书写作备忘

### Hook 素材
1. 1959 年，三位研究者试图写出一个能解决一切问题的程序——他们的方法是先观察人类大学生怎么思考
2. GPS 的灵魂问题：当你不知道怎么做时，你其实在做什么？答案：比较现状与目标，找出差距，然后缩小它
3. 一位数学家、一位社会科学家、一位程序员走进 RAND——这不是一个笑话，而是 AI 诞生的一种方式

### 核心 Insight（一句话）
GPS 的核心贡献不是某个具体算法，而是一个哲学洞见：通用的问题求解策略可以与领域知识分离——"怎么解决问题"和"解决什么问题"是两个独立的维度。

### 自查重点
1. GPS 首次公开是 RAND Report P-1584（1959 年 2 月），正式出版于 1960 年 IFIP 会议论文集——不要写错年份
2. Newell 和 Simon 1975 年共获图灵奖，Simon 1978 年获诺贝尔经济学奖——两个奖不要混淆
3. GPS 不是第一个 AI 程序（Logic Theorist 更早），而是第一个显式追求"通用性"的问题求解程序
4. Shaw 不是学者而是 RAND 的职业程序员——不要用学术头衔描述他

### 动态 Hashtags
#问题求解 #认知科学 #符号AI
