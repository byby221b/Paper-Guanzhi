# 精读报告 #41：Building a Large Annotated Corpus of English: The Penn Treebank

## 元信息

- 标题：*Building a Large Annotated Corpus of English: The Penn Treebank*
- 作者：Mitchell P. Marcus、Beatrice Santorini、Mary Ann Marcinkiewicz
- 发表：*Computational Linguistics*, Volume 19, Number 2, pp. 313–330，1993 年 6 月
- 出版者：MIT Press；论文版权页标注 Association for Computational Linguistics
- 原文：[ACL Anthology J93-2004](https://aclanthology.org/J93-2004/)
- 精读日期：2026-08-08
- 对应小红书期号：#41

**版本与页码说明**：本次使用 ACL Anthology 官方 PDF，共 18 个 PDF 页面，文件 1,222,093 字节，PDF 版本 1.3，未加密，含文本层；期刊印刷页码为 313–330。ACL Anthology 当前元数据没有列 DOI。下文的 p. 均指期刊印刷页码。文本提取用于定位，POS tagset、人工校正实验、bracketing 速度、语料组成表与作者局限均回到渲染页核对。

**时间截面**：论文总结的是 Penn Treebank 第一阶段（1989–1992）截至 1992 年 11 月的状态。后来的 Treebank-2、Treebank-3、predicate-argument annotation 以及基于 WSJ 的统计句法分析结果均标为“后续资料”，不倒写进 1993 年语料。

**数字口径**：正文开头写“over 4.5 million words”，表 4 的精确统计单位是 tokens：4,885,798 个 POS-tagged tokens、2,881,188 个 skeletally parsed tokens。报告引用精确值时沿用 tokens，不把 word 与 token 强行等同。

---

## 作者背景

### Mitchell P. Marcus

- **发表时身份**：论文首页列为 University of Pennsylvania Department of Computer and Information Sciences。项目由其担任第一作者并负责联系方式；致谢中的项目经费支持指向 Penn Treebank 团队。
- **可核验学术身份**：Penn 当前官方目录列其为 Professor Emeritus、RCA Professor of Artificial Intelligence，研究方向为 natural language processing、corpus-based 与 statistical models for NLP，1976 年获 MIT 博士学位。[来源：Penn Linguistics faculty directory](https://www.ling.upenn.edu/people/faculty)、[Penn Engineering directory](https://directory.seas.upenn.edu/mitch-marcus/)
- **师承边界**：官方目录未列博士导师，本报告不补写二手资料。

### Beatrice Santorini

- **发表时身份**：论文首页列为 Northwestern University Department of Linguistics。
- **与项目的关系**：Santorini 的 Penn 个人履历记载，她 1989–1991 年担任 Penn Treebank Project administrator，项目 PI 为 Mitchell Marcus；1991–1997 年任 Northwestern University Linguistics assistant professor。她 1989 年获 University of Pennsylvania 语言学博士学位，履历列出的 dissertation committee 为 Anthony Kroch、Ellen Prince 与 Jack Hoeksema。[来源：作者个人履历（Penn 域名）](https://www.ling.upenn.edu/~beatrice/cv.html)
- **后续轨迹**：履历显示她后来长期参与 Penn 的历史句法语料建设，并于 2010–2024 年任 Penn Linguistics senior fellow。这是后续背景，不能据此改变其 1993 年的 Northwestern 身份。

### Mary Ann Marcinkiewicz

- **发表时身份**：论文首页列为 University of Pennsylvania Department of Computer and Information Sciences。
- **可确认工作**：她与 Santorini 合作撰写 1991 年 *Bracketing Guidelines for the Penn Treebank Project*；1994 年又作为作者参与 predicate-argument annotation 的后续论文。ACL Anthology 作者页只收录这两篇相关论文，本次未找到足以确认其学位、具体职位或后续任职的官方资料，故不扩写身份。[来源：ACL Anthology 作者页](https://aclanthology.org/people/mary-ann-marcinkiewicz/)

### 团队贡献的边界

论文没有现代格式的 author contribution statement。致谢另列出多位 annotators、Robert MacIntyre 的软件与语料管理工作，以及资助机构。报告因此把三位署名作者视为共同作者，不把 tagset、工具或实验单独归给某一人。

---

## 历史语境

### 从手工规则转向可学习的语言模型

论文开篇给出的判断是：自然语言处理、语音识别和理论语言学开始需要大型、自然发生、较少约束的语料；标注语料可以用于建立统计语法、比较 parsing models、研究书面语与口语差异（p. 313）。

这一时期的关键瓶颈并不只是“缺少文本”。机器可读文本若只有字符串，监督式 POS tagging 与 parsing 缺少稳定目标，算法之间也难在同一材料、同一标签体系上比较。Penn Treebank 的任务是把文本、POS 标签、浅层短语结构、标注规范和分发机制共同做成可复用研究基础设施。

### 既有 tagset 的粒度问题

论文比较了当时的英语标注体系（pp. 314–316）：

- Brown Corpus：87 个 simple tags；连 compound tags 一共 187 个；
- Lancaster-Oslo/Bergen（LOB）：约 135 个；
- Lancaster UCREL：约 165 个；
- London-Lund Spoken English：197 个；
- Penn Treebank：36 个 POS tags，加 12 个标点与货币符号标签。

更细的 tagset 能表达更多语法区分，也会加剧数据稀疏与标注不一致。Penn Treebank 的 stochastic orientation 促使团队压缩可从词形或句法结构恢复的冗余标签，把有限样本集中到更稳定的类别上。

### 直接前驱与同时期方法

- **Brown Corpus**：提供既有英语语料、标签传统和本论文实验所用的样本；Penn 团队从未标注版本重新给 Brown 全量打标签。
- **PARTS（Church, 1988）**：早期自动 POS 预标注器，先提供候选标签，再由人工修正。
- **Fidditch（Hindle, 1983/1989）**：确定性 parser，为句法标注生成保守的树片段。
- **Lancaster Treebank**：提供 skeletal analysis 的可比参照。
- **早期统计 parsing 工作**：Magerman & Marcus、Brill、Pereira & Schabes 等已使用项目的阶段性产物，说明资源与算法在同步迭代（p. 328）。

---

## 问题形式化

### 标注对象

给定由多个来源组成的英语 token 序列

$$X=(x_1,x_2,\ldots,x_N),$$

项目希望构造两层输出：

1. **POS 序列** $Y=(y_1,\ldots,y_N)$，其中 $y_i$ 来自 36 个词类标签与 12 个标点/货币标签；
2. **skeletal parse** $T$，以 14 个 phrase/clause labels 和 4 类 null elements 表示主要成分结构。

论文允许少数 token 关联两个 POS tags，以表达文本本身的歧义或 annotator uncertainty（pp. 315–316）。这意味着 gold annotation 可以显式保留不确定性，而不强迫每个案例都产生单一断言。

### 设计目标

论文反复权衡四项指标：

- **规模**：在有限人力下处理数百万 tokens；
- **速度**：按每 1,000 words 所需分钟或 words/hour 衡量；
- **一致性**：annotators 两两之间的 disagreement rate；
- **准确性**：与精心构造的 benchmark version 之间的不一致率。

这里有一个术语陷阱：p. 319 写“Accuracy was computed as the rate of disagreement”，随后给出 5.4%、4.0% 等数字。数值实质是 **disagreement/error rate，越低越好**，报告不把它们转述为“准确率只有 5.4%”。

### 资源输出

- POS-tagged corpus：完整第一阶段语料；
- skeletally parsed corpus：其中超过一半；
- annotation guidelines、tagsets 与 correction interfaces；
- 通过 LDC、ACL/Data Collection Initiative 等渠道分发的研究资源。

论文脚注 1 还主动区分 corpus 与 collection：原始材料的选择带有机会性，从严格设计原则看更接近 collection。这一自我限定对后续的领域偏差分析很重要。

---

## 核心方法

### 方法总览：自动预标注，人工修正

POS 与 bracketing 共用同一生产思想：

$$\text{raw text}\rightarrow\text{automatic proposal}\rightarrow\text{human correction}\rightarrow\text{validated corpus}.$$

机器负责提供覆盖面广但含错的第一版，annotator 把劳动集中在检测与修正。团队用实验比较这一流程与完全手工从零标注，而非只凭直觉假设它更快。

### POS tagset 的四条取舍

#### 1. Recoverability

若细粒度标签能从词形或后续 parse 恢复，就不在 POS 层重复编码。例如 Brown 为 *be*、*have*、*do* 保留专门范式；Penn Treebank 统一到一般动词形态标签。主格/宾格代词的区分也交由句法位置恢复（pp. 314–315）。

#### 2. Consistency

合并边界模糊、使用不稳定的类别，减少相同语境下 annotators 选择不同标签的机会（p. 315）。

#### 3. Syntactic function

Penn Treebank 在有助于后续 bracketing 时让 POS 反映句法功能；例如区分 VB（infinitive/imperative）与 VBP（non-third-person singular present）（p. 315）。

#### 4. Indeterminacy

对于语境仍无法消歧的案例，允许 JJ|NN、JJ|VBG、JJ|VBN、NN|VBG、RB|RP 等少量双标签组合，避免强制随机裁决（pp. 315–316）。

### POS 标注的两阶段流程

**早期自动阶段**（p. 317）：

- PARTS 本身在其标签体系上的错误率为 3%–5%；
- 映射到 Penn tagset 另引入约 4% 错误；
- 合成后的样例输出错误率为 7%–9%；
- 后来改用面向 Penn tagset 的 stochastic + rule-driven tagger cascade，自动输出错误率降至 2%–6%。

**人工修正阶段**（pp. 317–318）：

- annotator 在 GNU Emacs Lisp 鼠标界面中定位错误词并输入合法新标签；
- 系统先保留“原标签*新标签”，使团队能提取 confusion patterns；
- 分发版再删除错误的自动标签，只保留校正结果；
- POS 任务学习期不足一个月（每周 15 小时），一个月后速度超过 3,000 words/hour。

这个界面设计把 corpus production 与 tagger improvement 连在一起：修正记录本身成为自动系统错误分析的数据。

### bracketing 的两阶段流程

Fidditch 先生成确定性的单一分析，但对无法确定 attachment 的成分保持保守，只输出未连接的 tree fragments（pp. 320–322）。Annotators 随后：

1. 把 fragments 移到合适父节点下；
2. 删除、创建或重标 constituent brackets；
3. 用 X 标记类别不确定但成分边界确定的片段；
4. 用 pseudo-attachment 表达全局歧义或无法唯一附着的 modifier。

自动输出还会被简化与 flatten：删除 POS tags、去掉部分内部结构，降低树的视觉复杂度。论文的工程准则很清楚：让机器先产生易修的候选，而非追求一棵必须完全正确的初始树。

### 为什么采用 skeletal representation

试验表明（p. 323）：

- 完整 Fidditch 结构在 3 周训练后约 375 words/hour，6 周后约 475；
- 简化为 skeletal representation 可再提高约 100–200 words/hour；
- 遇到不清楚的 argument/adjunct 时允许 annotator 不强制区分，可提高约 150–200 words/hour；
- 后续非正式检查发现，强制这类区分无法保持一致。

因此项目接受较平的 context-free 表示，换取规模与一致性；更丰富的 predicate-argument information 被留到下一阶段。

---

## 关键指标推导

### 指标 1：两两不一致率

**原文位置**：p. 319。

对于同一文本的 $n$ 位 annotators，论文先为每一对 annotators 计算 token-level disagreement ratio，再对所有配对汇总。配对数为

$$\binom n2=\frac{n(n-1)}2.$$

若第 $a$、$b$ 位 annotator 的标签为 $y_i^{(a)}$、$y_i^{(b)}$，可把单对不一致率形式化为（补充形式化）：

$$d_{ab}=\frac1N\sum_{i=1}^{N}\mathbf 1\!\left[y_i^{(a)}\ne y_i^{(b)}\right].$$

四位 annotators 对同一文本最多形成 $\binom42=6$ 个配对。该指标衡量一致性，不直接保证所有人都正确；因此论文另建 benchmark version 衡量 validity。

### 指标 2：人工校正的吞吐提升

**原文数字**：纯手工 tagging 平均 44 分钟/1,000 words，correction 平均 20 分钟/1,000 words（p. 319）。

换算为小时吞吐（补充推导）：

$$R=\frac{60\times1000}{t_{1000}}.$$

- 手工 tagging：$60,000/44\approx1,364$ words/hour；
- 校正：$60,000/20=3,000$ words/hour；
- 比值：$44/20=2.2$。

这与论文“more than twice as fast”的文字一致。该结果来自 4 位受过语言学研究生训练的 annotators 与 8 个各 2,000 words 的 Brown samples，不能直接外推到任意任务或任意 annotator 群体。

### 指标 3：句法覆盖率

**原文数字**：表 4 的 POS 总量 4,885,798 tokens，skeletal parsing 总量 2,881,188 tokens（p. 327）。

$$\text{parse coverage}=\frac{2,881,188}{4,885,798}\approx58.97\%.$$

这验证了正文“over half”的谨慎说法。1994 年后续论文出现约 two-thirds 的概述，属于不同时间点与口径，不能替换 1992-11 表 4 的精确截面。

---

## 实验与产出分析

### 完全手工与机器预标注后校正

**设置**（pp. 318–320）：

- 4 位具有语言学研究生训练的 annotators；
- 训练顺序为 15 小时 correction，再 6 小时 tagging；
- 8 个 Brown Corpus 样本，每个 2,000 words，覆盖 4 种体裁（2 fiction、2 nonfiction）；
- 每人先手工标 4 个样本，再修正 4 个自动标注样本；体裁顺序采用不同 permutation；
- benchmark 由 Brown tags 映射到 Penn tagset 后再仔细人工校正得到。

**速度**：correction 平均 20 分钟/1,000 words，manual tagging 为 44 分钟；annotation mode effect 在 repeated-measures analysis 中达到 $p=.05$，其他主效应和交互不显著。

**一致性**：

- manual tagging 的平均 inter-annotator disagreement 为 7.2%；
- correction 为 4.1%；
- 去掉一个含大量化学/公式占位符、当时缺少明确规范的文本后，correction 降为 3.5%。

这个异常样本很有价值：它显示标注分歧可以来自 guideline 缺口，团队随后可通过补规则减少系统性不一致。

**与 benchmark 的不一致率**：

- p. 319 报告 manual tagging 为 5.4%、correction 为 4.0%；去掉上述文本后，correction 为 3.4%；
- p. 320 随即又写 modified PARTS 为 9.6%、“corrected version”为 5.4%，并据此称 annotators 降低约 4.2 个百分点；脚注 10 特别说明，9.6% 还包含 PARTS 与 Penn tag usage 的体系差异，不能当作 PARTS 本身的纯错误率。

这两页对 corrected version 的 4.0% 与 5.4% 存在文本内冲突。稳妥结论只保留方向：机器预标注后校正优于从零手工标注，速度和一致性证据最清楚；精确的 benchmark error reduction 应注明原文冲突，不能擅自选一个数字消除矛盾。

### bracketing 生产率

采用 skeletal structure 且不强制困难的 argument/adjunct 区分后，经过 3–4 个月训练的 annotators 速度约 750 到超过 1,000 words/hour，个体差异明显；最快者短时超过 1,500 words/hour。论文按 750 words/hour 估算，5 位兼职 annotators 每天各 3 小时，一年可生产约 2.5 million words 的一次校正树（p. 323）。

对已有解析语料的 proofreading 可达约 4,000 words/hour；作者明确限定：这个速度只能发现 gross parsing errors，没有时间逐一核查 prepositional phrase attachment。这一说明阻止我们把“快速复核”误写成“完整二次精标”。

### 第一阶段语料组成

**表 4，as of 11/92**：

| 来源 | POS-tagged tokens | Skeletal parsing tokens |
|---|---:|---:|
| Department of Energy abstracts | 231,404 | 231,404 |
| Dow Jones Newswire | 3,065,776 | 1,061,166 |
| Department of Agriculture bulletins | 78,555 | 78,555 |
| Library of America texts | 105,652 | 105,652 |
| MUC-3 messages | 111,828 | 111,828 |
| IBM Manual sentences | 89,121 | 89,121 |
| WBUR radio transcripts | 11,589 | 11,589 |
| ATIS sentences | 19,832 | 19,832 |
| Brown Corpus, retagged | 1,172,041 | 1,172,041 |
| **总计** | **4,885,798** | **2,881,188** |

Dow Jones 占 POS tokens 约 62.7%，Brown 又占约 24.0%。虽然来源包括科学摘要、文学、农业说明、新闻、手册、广播与口语转写，整体分布仍由 newswire 与 Brown 主导；“American English 的大型语料”不等于均匀覆盖所有体裁。

表 4 没有给 sentence count 或 tree count。现代资料中常见的约 49,000 sentences 属于后续修订版或特定 WSJ 子集，不能回填为这个多来源的 1992-11 截面。

### 质量状态

- 整体 POS corpus 的估计 error rate 约 3%；
- Library of America 与 Agriculture 子集由两位 annotators 分别校正并 adjudicate，估计 error rate 低于 1%；
- 团队计划通过 retrained PARTS 与 preliminary corpus 之间的 adjudication，把最终整体错误率降到约 1%；论文时点仍是计划；
- skeletal parses 普遍只校正一次；Brown 额外快速 proofread，但只处理 gross errors（p. 328）。
- 1993 论文没有声称 double-blind annotation。双人分别校正再 adjudicate 只明确用于 Library of America 与 Agriculture 两个 POS 子集，不能推广为全语料流程。

---

## 局限性

### 作者明确承认的表示缺口

论文 pp. 328–329 列出多项限制：

1. 表示“essentially context-free”且较 flat，许多 argument/adjunct relations 没有标出；
2. sentential adverb 与 complement 的结构会产生 trapping problem；
3. 某些 syntactic categories 的规范随时间暴露出不一致，需要 regularize；
4. 用户需要更丰富的 noncontiguous structures、dependencies 与 predicate-argument structure；
5. 第一阶段的 skeletal analysis 是有限人力约束下的务实选择。
6. NP 的大量内部结构被省略，尤其从 NP 内部起点到 head，以及单词级 post-head modifier 的结构（p. 325）。

团队提出下一阶段先自动转换已有树，再人工补全更丰富结构；1994 年 predicate-argument annotation 论文是这一计划的后续实现证据。

### 语料与实验边界

- **体裁偏斜**：Dow Jones 和 Brown 合计约 86.7% 的 POS tokens；对口语、对话及其他领域的代表性有限。
- **英语与美国材料限定**：论文对象是 American English，不能直接代表跨语言句法。
- **标注准确率不均一**：不同子集经历一次校正、双人校正与 adjudication、或快速 gross-error proofreading，质量口径不同。
- **实验样本较小**：标注模式比较只有 4 位 annotators、16,000 words，并全部来自 Brown Corpus。
- **顺序效应未隔离**：每位 annotator 都先 tagging 后 correcting；体裁顺序有 permutation，但任务顺序没有对调。这是本报告的实验设计分析，论文没有讨论其影响。
- **自动建议可能产生 anchoring**：校正流程的高效率可能伴随对机器建议的依赖；论文用 benchmark 比较缓解了这一担忧，但两页 error numbers 又存在内部冲突。
- **许可与可访问性**：1993 年材料主要通过 LDC 等渠道分发；后续 LDC Treebank 版本也有用户许可。它不同于今天常见的完全开放 benchmark。

---

## 后续影响

### 数据版本的演进

- **1994 predicate-argument annotation**：Marcus 等人在 HLT 报告新的句法标注方案，增加 coindexed null elements、非 context-free 标记与部分 semantic roles。[ACL Anthology H94-1020](https://aclanthology.org/H94-1020/)
- **Treebank-2（1995）**：LDC 官方目录称其包含超过 1.6 million words 的 hand-parsed material，另有 1 million POS-only words，并含 fully parsed Brown Corpus。[LDC LDC95T7](https://catalog.ldc.upenn.edu/LDC95T7)
- **Treebank-3（1999）**：LDC 官方目录说明其中包含 Treebank-2 的 1 million words WSJ material，并进一步整合多个语料部分。[LDC LDC99T42](https://catalog.ldc.upenn.edu/LDC99T42)
- **PropBank（2005）**：在 Penn Treebank 句法树上继续叠加 predicate-argument 与 semantic role annotation，直接体现 Treebank 作为后续 annotation substrate 的作用。[ACL Anthology J05-1004](https://aclanthology.org/J05-1004/)

这些版本不能与表 4 相互替代：1993 论文记录的是第一阶段生产截面，后续版本在材料、结构和修订程度上均有变化。

### 统计 parsing benchmark

Penn Treebank 的 WSJ 部分后来成为英语 constituency parsing 的共同训练/测试资源。Collins 1997 年 *Three Generative, Lexicalised Models for Statistical Parsing* 等论文在同一资源上报告模型，使 parser 的方法差异可以落到可比较的 evaluation protocol。[ACL Anthology P97-1003](https://aclanthology.org/P97-1003/)

这种影响应表述为“资源促成可比较研究”，而不把所有后续 parsing 进展归为 1993 年论文直接提出的算法。

### treebank 方法的扩展

后来 treebanks 扩展到更多语言、依存句法和统一标注体系。Universal Dependencies v2 在 2020 年已汇集 90 种语言，2021 年综述称框架覆盖超过 100 种语言。[ACL Anthology 2020 LREC](https://aclanthology.org/2020.lrec-1.497/)、[Computational Linguistics 2021](https://aclanthology.org/2021.cl-2.11/)

UD 的 dependency-based、cross-lingual schema 与 Penn Treebank 的英语 constituency schema 不同；两者的连续性在于“以显式规范、人工校验和可分发资源支撑可比较 NLP”，不能写成标签体系原样沿用。

### 引用统计

- Semantic Scholar 页面显示 **9,136 Citations**（查询日期 2026-08-08，Corpus ID 252796；平台动态计数）。
- ACL Anthology 页面不提供引用数，本报告不把 Semantic Scholar 数字冒充 ACL 官方统计。

---

## 个人笔记

我最想保留的是 p. 323 的生产率试验。完整结构训练六周后约 475 words/hour；把树压成 skeletal representation，可再增加 100–200；允许 uncertain argument/adjunct 不强制裁决，又增加 150–200。更“丰富”的标签并没有免费出现，它会以标注时间、分歧和规模为代价。

紧接着的表 4 给出结果：2,881,188 tokens 已有 skeletal parsing。这个规模部分来自团队主动放弃一些无法稳定保持的区分。读到这里，我更愿意把 Treebank 看成一个明确的测量仪器：它先规定哪些语言现象能被可靠记录，再让算法在同一刻度上比较。

这也解释了作者为何在结尾毫不回避 flat context-free representation、trapping problem 和 category inconsistencies。资源的可信度并不来自宣称“标注就是语言真相”，而来自把规则、误差、未决问题和下一版修订路径一起公开。

---

## 小红书写作备忘

### Hook 素材

1. 1992 年 11 月，4,885,798 tokens 已完成 POS 标注，2,881,188 tokens 有 skeletal parsing；精确覆盖率约 58.97%。
2. 自动预标注后人工校正平均 20 分钟/1,000 words，纯手工为 44 分钟；速度差异有对照实验支持。
3. 项目通过减少强制的 argument/adjunct 区分提高 150–200 words/hour，并明确承认这种表示较 flat。

### 核心 Insight（一句话）

Penn Treebank 把自动预标注、人工校正、可执行规范与分发机制组织成一条可规模化的数据生产线，为自然语言模型提供了广泛复用、可比较的句法坐标系。

### 自查重点

1. 1993 截面是 4,885,798 POS tokens 与 2,881,188 parsed tokens；不混入 Treebank-2/3 数字。
2. POS tagset 是 36 个 POS + 12 个标点/货币标签；不能笼统写成“48 个词性”。
3. p. 319 的百分数是与 benchmark 的不一致/错误率，越低越好；不能写成低准确率。
4. p. 319 的 correction 4.0% 与 p. 320 的 corrected version 5.4% 存在原文冲突，需明确保留证据缺口。
5. skeletal parsing 没有完整编码 argument/adjunct 与 predicate-argument structure；作者将其列为下一阶段方向。
6. Treebank-2、Treebank-3、Collins parser 与 UD 均属于后续资料。
7. 论文没有给第一阶段 sentence/tree 总数，也没有声称全语料采用 double-blind adjudication。

### 动态 Hashtags

#PennTreebank #语料库 #自然语言处理

---

## 来源与页码索引

### 论文原文

- Marcus, Mitchell P.; Santorini, Beatrice; Marcinkiewicz, Mary Ann. 1993. *Building a Large Annotated Corpus of English: The Penn Treebank*. *Computational Linguistics* 19(2):313–330. [ACL Anthology J93-2004](https://aclanthology.org/J93-2004/)
- 项目定位与两阶段流程：pp. 313–314。
- tagset 原则与表 2：pp. 314–317。
- POS 自动预标注与人工修正：pp. 317–318。
- annotation mode experiment：pp. 318–320。
- bracketing 方法、tagset、工具与速度：pp. 320–326，表 3、图 3–5。
- 语料组成与质量：pp. 326–328，表 4。
- 作者自述局限与下一阶段：pp. 328–329。

### 官方背景与后续资料

- [Penn Linguistics faculty directory](https://www.ling.upenn.edu/people/faculty)
- [Beatrice Santorini 个人履历（Penn）](https://www.ling.upenn.edu/~beatrice/cv.html)
- [ACL Anthology：Mary Ann Marcinkiewicz](https://aclanthology.org/people/mary-ann-marcinkiewicz/)
- [LDC Treebank-2](https://catalog.ldc.upenn.edu/LDC95T7)
- [LDC Treebank-3](https://catalog.ldc.upenn.edu/LDC99T42)
- [ACL Anthology：The Penn Treebank: Annotating Predicate Argument Structure](https://aclanthology.org/H94-1020/)
- [ACL Anthology：PropBank](https://aclanthology.org/J05-1004/)
- [ACL Anthology：Three Generative, Lexicalised Models for Statistical Parsing](https://aclanthology.org/P97-1003/)
- [ACL Anthology：Universal Dependencies v2](https://aclanthology.org/2020.lrec-1.497/)
- [Semantic Scholar citation page](https://www.semanticscholar.org/paper/Building-a-Large-Annotated-Corpus-of-English%3A-The-Marcus-Santorini/0b44fcbeea9415d400c5f5789d6b892b6f98daff)
