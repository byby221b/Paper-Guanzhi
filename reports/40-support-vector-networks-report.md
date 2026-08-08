# 精读报告 #40：Support-Vector Networks

## 元信息

- 标题：*Support-Vector Networks*
- 作者：Corinna Cortes、Vladimir Vapnik
- 发表：*Machine Learning*, 20, 273–297，1995 年 9 月
- 发表时机构：AT&T Bell Laboratories, Holmdel, New Jersey, USA
- DOI：[10.1007/BF00994018](https://doi.org/10.1007/BF00994018)
- 作者机构页：[Google Research 收录页](https://research.google/pubs/support-vector-networks/)
- 精读日期：2026-08-08
- 对应小红书期号：#40

**版本与页码说明**：本次精读使用 Springer DOI 页面提供的 25 页期刊 PDF，文件 1,502,380 字节，PDF 版本 1.3，未加密，含可提取文本；页眉印刷页码为 273–297。下文的 p. 均指期刊印刷页码。文本提取只用于定位，soft margin 的式 (21)–(30)、附录式 (49)–(67)、表 2、表 3 和图 8–9 均回到渲染页面核对。

**证据边界**：论文内容、公式、实验和作者判断以 1995 年原文为准。作者生平、1992 年前驱、后续优化软件与奖项分别标为“官方背景资料”或“后续资料”。本文采用的惩罚形式、现代教材常见的线性松弛惩罚，以及后来的 squared hinge loss 彼此相关但不完全相同，下文分别说明。

---

## 作者背景

### Corinna Cortes

- **发表时身份**：论文首页列出的单位是 AT&T Bell Labs。Google Research 官方简介称，她 1989 年加入 AT&T Bell Labs 任研究员，1993 年获 University of Rochester 计算机科学博士学位；因此本文投稿（1993-05-15）与发表（1995）期间，她已经在 Bell Labs 从事机器学习研究。
- **此前基础**：1992 年 Boser、Guyon 与 Vapnik 已在 COLT 发表可分数据的 optimal margin classifier；Cortes 与 Vapnik 在本文中共同把该路线扩展到不可分数据，并报告手写数字实验。论文没有作者贡献声明，不再细分两人的具体分工。
- **后续轨迹**：Google Research 官方简介现称其为 VP，研究理论与大规模应用机器学习；她与 Vapnik 因支持向量机获得 2008 ACM Paris Kanellakis Theory and Practice Award。[来源：Google Research 作者页](https://research.google/people/author121/)、[ACM Awards Committee 年报](https://www.acm.org/binaries/content/assets/about/annual-reports/awards-fy09.pdf)
- **师承边界**：可核验官方简介给出博士学校与年份，但本次来源未可靠确认其博士导师，故不写具体师承。

### Vladimir Vapnik

- **发表时身份**：论文首页同列 AT&T Bell Labs。NEC 的 2013 C&C Prize 官方获奖人资料记载，Vapnik 于 1990–1996 年任 AT&T Bell Labs 研究科学家，时间与论文完全重合。
- **此前基础**：论文把 optimal hyperplane 的概念追溯到 1965 年，并引用 Vapnik 1982 年著作；其理论背景包括 VC dimension、结构风险最小化与统计学习理论。本文的 p. 285–286 直接用训练误差与容量两项来解释 generalization control。
- **后续轨迹**：NEC 官方资料列出其 1964 年获 Institute of Control Sciences 博士学位，后任 Royal Holloway 教授、NEC Laboratories Fellow、Columbia University 教授；Columbia 当前目录仍收录其 Computer Science 教授身份。[来源：NEC C&C Prize 官方资料](https://www.nec.com/en/press/201310/images/2401-01-02.pdf)、[Columbia Engineering 作者页](https://www.engineering.columbia.edu/faculty-staff/directory/vladimir-vapnik)
- **师承边界**：上述官方资料没有列博士导师，本报告不据二手百科补写。

### 合作关系

两位作者在论文发表时是 Bell Labs 同事。可核验材料支持“同事与共同研究者”，没有证据支持将二人写成师生。2008 年 ACM 奖项把两人并列为获奖者，授奖理由是发展了用于分类及相关机器学习问题的支持向量机。

---

## 历史语境

### 线性判别、感知机与反向传播

论文第 1 节自己搭出一条六十年的路径：

1. Fisher（1936）在样本相对参数不足时主张采用更受约束的线性判别；
2. Rosenblatt（1962）的感知机把输入非线性变换到隐层表示，再构造线性输出；
3. 1980 年代中期的 back-propagation 可以联合调整网络权重，但目标函数通常非凸，只保证局部优化；
4. optimal hyperplane 以最大几何间隔选择分离面，解由少量 support vectors 决定；
5. Boser、Guyon 与 Vapnik（1992）展示：若算法只依赖特征空间内积，就能用输入空间中的核函数计算它，无须显式展开高维特征。

这条历史线说明论文面对两个不同问题：怎样选一个更能泛化的分离面，以及怎样在极高维特征空间中计算它。

### 1992 年方法留下的边界

1992 年 optimal margin classifier 面向可完全分离的训练集。现实数据常有噪声、类间重叠或错误标注；此时硬间隔约束没有可行解。1995 年论文在摘要和 p. 276 明确把自身新增内容定位为：把 support-vector network 扩展到训练数据无法无错分离的情形。

因此，严谨的贡献归属应当拆成三层：

- 最大间隔思想与统计学习理论有更早基础；
- 隐式计算高维内积的支持向量学习方案已见于 1992 年工作；
- 本文系统给出 soft margin 扩展、相应优化形式，并在两个手写数字数据库上展示效果。

### 当时的核心矛盾

把 256 维像素映射成 7 次多项式特征，显式空间可达约 $10^{16}$ 维。直接创建特征向量几乎不可行；仅追求训练集零错又容易被噪声牵制。论文要让以下三点同时成立：

- 在高维特征空间保留非线性表达能力；
- 训练仍可化为有全局解的凸优化；
- 允许少量训练误差，并用一个参数调节间隔与误差的权衡。

---

## 问题形式化

### 输入与输出

给定二分类训练集

$$\mathcal D=\{(x_i,y_i)\}_{i=1}^{\ell},\qquad x_i\in\mathbb R^n,\quad y_i\in\{-1,+1\}.$$

- **输入**：带二元标签的有限训练样本，以及预先选定的核函数 $K$；
- **输出**：特征空间中的分离超平面，等价地表示为一组支持向量、系数 $\alpha_i$ 与偏置 $b$；
- **预测**：按 $\operatorname{sign}(f(x))$ 分类，其中 $f$ 由支持向量与核函数之和给出。

论文的实验是十分类数字识别，但算法主体是 two-group classification。实验为每个数字各训练一个“该类对其余类”的二分类器，最终取十个输出中的最大者（p. 287）。

### 硬间隔目标

可分情形要求

$$y_i(w^\top x_i+b)\ge 1,\qquad i=1,\ldots,\ell.$$

在这个归一化下，两侧支撑超平面的距离是 $2/\lVert w\rVert$，所以最大化间隔等价于最小化 $\frac12\lVert w\rVert^2$。恰好满足等号的样本定义为支持向量（pp. 277–279，式 (10)–(14)）。

### 不可分数据与 slack variables

论文引入 $\xi_i\ge0$，把约束改成

$$y_i(w^\top x_i+b)\ge 1-\xi_i.$$

它给出的语义是：

- $\xi_i=0$：样本在正确一侧且达到单位函数间隔；
- $0<\xi_i\le1$：分类仍可能正确，但进入 margin；
- $\xi_i>1$：样本被误分类。

论文先用 $\sum_i\xi_i^\sigma$ 在 $\sigma\to0^+$ 时逼近错误数量，但指出直接最小化误分类数一般是 NP-complete；随后取 $\sigma=1$，用偏差之和与间隔共同构造可有效求解的问题（pp. 280–281，式 (21)–(25)）。

### 目标与评价准则

优化层面同时考虑：

1. 小的 $\lVert w\rVert$，即大的几何间隔；
2. 小的 slack 惩罚，即较少或较轻的 margin violation；
3. 核函数对应的合法特征空间内积。

实验层面报告 raw classification error、每个二分类器的平均支持向量数、训练错误数，并与同一 OCR benchmark 中的若干分类器作横向比较。

---

## 核心方法

### 方法步骤

1. 选择满足 Mercer 条件的核函数 $K(u,v)$；
2. 用 $D_{ij}=y_i y_j K(x_i,x_j)$ 构造 Gram 型矩阵；
3. 解带线性等式与非负/盒约束的凸二次规划；
4. 保留 $\alpha_i>0$ 的训练点作为支持向量；
5. 预测时只计算测试点与支持向量之间的核函数。

### 最大间隔为何产生稀疏表示

硬间隔问题的 Lagrangian 是

$$L(w,b,\alpha)=\frac12\lVert w\rVert^2-\sum_i\alpha_i\bigl[y_i(w^\top x_i+b)-1\bigr],\qquad \alpha_i\ge0.$$

驻点条件给出

$$w=\sum_i\alpha_i y_i x_i,\qquad \sum_i\alpha_i y_i=0.$$

互补松弛又给出

$$\alpha_i\bigl[y_i(w^\top x_i+b)-1\bigr]=0.$$

离边界足够远的样本约束严格成立，只能取 $\alpha_i=0$；决策面因此由落在边界上的少量样本决定。论文将这种“解向量能展开在支持向量上”的性质视为命名核心（附录 A.1，pp. 291–293）。

### soft margin 的一般形式与常见特例

论文式 (25) 写成

$$\min_{w,b,\xi}\ \frac12\lVert w\rVert^2+C\,F\!\left(\sum_i\xi_i\right),$$

约束仍为 $y_i(w^\top x_i+b)\ge1-\xi_i$、$\xi_i\ge0$。正文为了简化推导取 $F(u)=u^2$；附录 A.2 更一般地分析 $F(u)=u^k$，并说明本文实验取 $k=2$（p. 295）。

附录随后单列 $F(u)=u$ 的情形。它对应今天最常见的线性 slack 惩罚：

$$\min_{w,b,\xi}\ \frac12\lVert w\rVert^2+C\sum_i\xi_i.$$

其对偶为

$$\max_\alpha\ \sum_i\alpha_i-\frac12\sum_{i,j}\alpha_i\alpha_j y_i y_j K(x_i,x_j),$$

满足

$$0\le\alpha_i\le C,\qquad \sum_i\alpha_i y_i=0.$$

这里的盒上界来自 slack 的 KKT 条件。需要保留的版本细节是：现代常见公式确实在论文附录中出现，论文实验使用的则是 $k=2$ 形式，二者不能混写成同一个设定。

### 核函数把高维几何压缩成内积计算

设 $\phi(x)$ 是从输入空间到特征空间的映射。由对偶表示可得

$$f(x)=\sum_i\alpha_i y_i\,\phi(x_i)^\top\phi(x)+b.$$

只要有

$$K(u,v)=\phi(u)^\top\phi(v),$$

训练和预测就只需要核值。论文以

$$K(u,v)=(u^\top v+1)^d$$

构造 $d$ 次多项式分类器（pp. 283–284，式 (34)–(37)），并讨论 potential/RBF 类核。它援引 Mercer 条件保证核对应合法内积；任意“相似度”并不自动满足这一条件。

### 参数 $C$ 的作用

论文 p. 286 的解释是：$C$ 控制决策规则复杂度与训练误差频率之间的权衡。

- $C$ 较大时，slack 更昂贵，优化更重视训练违约；
- $C$ 较小时，允许更多违约以换取较小的 $\lVert w\rVert$；
- 即使训练集可分，允许少量训练误差也可能获得更好的泛化。

这已经包含现代正则化路径的核心直觉，但论文没有给出系统的 validation protocol 或完整的 $C$ 扫描结果。

---

## 关键公式推导

### 公式 1：从几何间隔到硬间隔对偶

**原文位置**：式 (10)–(18)，pp. 277–279；附录式 (41)–(48)，pp. 291–292。

**Step 1：固定尺度。** 超平面 $(w,b)$ 同比缩放不改变决策面，因此把最近样本的函数间隔归一为 1：

$$y_i(w^\top x_i+b)\ge1.$$

此时两条支撑平面到决策面的距离各为 $1/\lVert w\rVert$，总间隔为 $2/\lVert w\rVert$。

**Step 2：写出 primal。** 最大化 $2/\lVert w\rVert$ 等价于

$$\min_{w,b}\frac12\lVert w\rVert^2\quad\text{s.t.}\quad y_i(w^\top x_i+b)\ge1.$$

**Step 3：引入乘子。** 对每个约束设 $\alpha_i\ge0$，得到 Lagrangian。对 $w$、$b$ 求偏导并令零：

$$w=\sum_i\alpha_i y_i x_i,\qquad \sum_i\alpha_i y_i=0.$$

**Step 4：消去 primal 变量。** 代回后得到

$$W(\alpha)=\sum_i\alpha_i-\frac12\sum_{i,j}\alpha_i\alpha_j y_i y_j x_i^\top x_j.$$

**Step 5：读出支持向量。** KKT 互补松弛说明 $\alpha_i>0$ 只可能出现在约束取等号的点；这些点决定 $w$ 与边界。

**直觉**：最大间隔把整个训练集压缩成边界样本。稀疏性来自最优性条件，并非训练前手工删点。

### 公式 2：soft margin 的盒约束从何而来

**原文位置**：式 (22)–(30)，pp. 280–282；$F(u)=u$ 特例见 p. 295。

下面是论文线性惩罚特例的补充推导。

**Step 1：写出 primal。**

$$\min_{w,b,\xi}\frac12\lVert w\rVert^2+C\sum_i\xi_i$$

满足 $1-\xi_i-y_i(w^\top x_i+b)\le0$ 与 $-\xi_i\le0$。

**Step 2：引入两组非负乘子。** 令 $\alpha_i$ 对应 margin 约束、$r_i$ 对应 $\xi_i\ge0$：

$$L=\frac12\lVert w\rVert^2+C\sum_i\xi_i+\sum_i\alpha_i[1-\xi_i-y_i(w^\top x_i+b)]-\sum_i r_i\xi_i.$$

**Step 3：对 $\xi_i$ 求驻点。**

$$\frac{\partial L}{\partial\xi_i}=C-\alpha_i-r_i=0.$$

因为 $r_i\ge0$，所以 $\alpha_i\le C$；结合 $\alpha_i\ge0$，得到盒约束 $0\le\alpha_i\le C$。

**Step 4：对 $w,b$ 求驻点并代回。** 仍有 $w=\sum_i\alpha_i y_i x_i$ 与 $\sum_i\alpha_i y_i=0$，因此得到上一节的 soft-margin 对偶。

**直觉**：硬间隔只有 $\alpha_i\ge0$；soft margin 为每个样本的“推动边界之力”加上上限 $C$，防止单个异常点无限支配超平面。

### 公式 3：核替换为何成立

**原文位置**：式 (31)–(37)，pp. 282–284。

**Step 1：在特征空间训练。** 把 $x$ 映射为 $\phi(x)$，最优解仍满足

$$w=\sum_i\alpha_i y_i\phi(x_i).$$

**Step 2：代入预测函数。**

$$w^\top\phi(x)+b=\sum_i\alpha_i y_i\phi(x_i)^\top\phi(x)+b.$$

**Step 3：定义核。** 若 $K(x_i,x)=\phi(x_i)^\top\phi(x)$，则

$$f(x)=\sum_i\alpha_i y_iK(x_i,x)+b.$$

**Step 4：训练矩阵同步替换。** 对偶目标中的 $x_i^\top x_j$ 全部替换为 $K(x_i,x_j)$。显式特征维数从计算图中消失。

**直觉**：算法仍在高维空间做线性分割，但只访问样本两两之间的内积。计算成本转而受样本数、支持向量数、核计算与优化器影响；“不显式依赖特征维数”不等于训练成本与问题规模无关。

---

## 实验分析

### 实验一：平面上的二次决策面

论文先用 $K(u,v)=(u^\top v+1)^2$ 在二维人工数据上展示 soft-margin 决策边界。图 5 用双圈标支持点、叉号标错误点；作者称所得二次多项式在相应训练集上达到最少错误（pp. 286–287）。这一部分是几何演示，没有重复试验、置信区间或与其他算法的数值比较。

### USPS 手写数字数据库

**设置**（pp. 287–289）：

- 7,300 个训练样本，2,000 个测试样本，分辨率 $16\times16$；
- 预处理包括居中、去倾斜与平滑，Gaussian 平滑标准差为 0.75；
- 十个 one-versus-rest 分类器，最终取最大输出；
- 使用式 (39) 的多项式核，次数从 1 到 7；
- 指标为 raw error 与每个二分类器的平均支持向量数。

**表 2 原始结果**：

| 多项式次数 | Raw error | 平均支持向量数 | 文中给出的特征空间维数 |
|---:|---:|---:|---:|
| 1 | 12.0% | 200 | 256 |
| 2 | 4.7% | 127 | 约 33,000 |
| 3 | 4.4% | 148 | 约 $10^6$ |
| 4 | 4.3% | 165 | 约 $10^9$ |
| 5 | 4.3% | 175 | 约 $10^{12}$ |
| 6 | 4.2% | 185 | 约 $10^{14}$ |
| 7 | 4.3% | 190 | 约 $10^{16}$ |

从 2 次到 7 次，测试误差只在 4.7%–4.2% 之间小幅变化；7 次核的平均支持向量数仅比 3 次核高约 30%，而显式特征空间维数相差约 $10^{10}$ 倍。论文据此观察到高维特征没有在该实验中造成明显过拟合。这个结论限于当前数据、预处理与参数设置，不能外推为任意高阶核都不会过拟合。

线性分类器的“200 个支持向量”还包含 slack 非零的训练点；每个二分类器平均约有 34 个线性训练误分类。二次分类器在整个训练集上只剩 4 个误分类，图 7 展示了这四个样本（p. 288–289）。

### NIST benchmark

**设置**（pp. 287、289–290）：

- 60,000 个训练样本、10,000 个测试样本；论文称其为 NIST training/test sets 的 50–50 mixture；
- 分辨率 $28\times28$，输入维数 784；
- 只训练一种 4 次多项式分类器，不做预处理；
- benchmark 周期只有两周，其他分类器结果来自同一 benchmark，本文作者只提交 SVN 结果。

十个二分类器平均训练错误率约 0.02%，即每类约 12 个；表 3 给出每类支持向量数 989–2,765，以及逐类训练/测试错误。组合十分类测试错误率为 1.1%。图 9 报告：

| 分类器 | 测试错误率 |
|---|---:|
| Linear classifier | 8.4% |
| 3-nearest neighbor | 2.4% |
| LeNet1 | 1.7% |
| LeNet4 | 1.1% |
| SVN | 1.1% |

因此，论文中的 SVN 与 LeNet4 在该 benchmark 上同为 1.1%，不能写成 SVN 单独取得最低错误率。作者引用 benchmark 论文指出，SVN 没有显式编码图像几何先验；随后将构造反映问题先验的核函数列为改进方向（p. 290）。

### 支持向量比例与泛化界的边界

论文式 (5) 给出一个期望意义结果：在可分训练集的 optimal hyperplane 情形，期望测试错误概率受期望支持向量数与训练样本数之比控制。作者在实验中观察到，把实际支持向量数代入后，该比例仍覆盖单个分类器的实测错误，并报告上界不超过 3%、单个分类器实测错误不超过 1.5%（p. 288）。

这里应区分“定理”与“实验观察”：式 (5) 的条件和期望算子不能直接删去，有限样本 soft-margin 结果也没有因一次观察就获得同等强度的证明。

### 实验设计评价

**优点**：

- 同时给出可视化人工例子、小型真实数据库与较大 benchmark；
- 报告支持向量数与隐式特征维数，使 kernel trick 的计算意义可被量化；
- NIST 对比引用共同 benchmark，且明确说明各结果的贡献者；
- 在只有两周的条件下，用单一 4 次核取得与专用卷积网络 LeNet4 相同的 1.1%。

**不足**：

- 没有报告 $C$ 的选择流程、验证集协议或超参数敏感性；
- 没有多个随机运行、方差与显著性检验；
- 训练速度只有定性声称，没有表格化时间、硬件和内存数据；
- USPS 对比混合了“文献结果”和“为本文专门运行的结果”，公平性依赖各自设置；
- NIST 只试一种 4 次核，无法判断次数选择是否稳健；
- 多分类是十个二分类器的工程组合，论文没有推导联合多分类目标。

---

## 局限性

### 作者自述与论文内边界

1. 方法主体针对 two-group classification；数字十分类依赖 one-versus-rest 组合。
2. 核函数预先选定。论文 p. 290 明确把“让 $K(u,v)$ 反映问题先验”作为进一步改进方向。
3. NIST 实验受两周 benchmark 时限约束，只完成一个 4 次多项式模型。
4. 精确最少误分类一般是 NP-complete，论文用凸的 slack 惩罚替代（p. 281）。
5. 论文虽称优化可高效进行，但训练仍涉及由样本构成的二次/凸规划；大样本扩展性并未由本文充分解决。

### 理论适用条件

- 核必须满足文中所述 Mercer 条件；非法核可能失去对应的 Hilbert 空间内积与凸性保证。
- support-vector ratio 的式 (5) 是特定可分情形下的期望界，不应泛化成任意 soft-margin 模型的逐次确定性界。
- 结构风险最小化的讨论给出容量控制直觉，论文没有为所有核、所有 $C$ 与所有数据分布建立统一有限样本最优性结论。
- 决策函数稀疏程度依赖数据与参数；支持向量可能很多，预测成本随其数量增长。

### 与现代常见表述的版本差异

现代教材常把 SVM 写成逐样本线性 slack 惩罚 $C\sum_i\xi_i$ 或 squared hinge loss $C\sum_i\xi_i^2$。本文正文的 $F(\sum_i\xi_i)$ 与附录实验采用的 $F(u)=u^2$ 是“slack 总和的平方”；它不等同于“每个 slack 平方后求和”。论文附录也推导了 $F(u)=u$ 的盒约束特例。报告引用现代公式时必须注明所选特例，避免把三种惩罚互换。

---

## 后续影响

### 直接后继与工程化

- **Sequential Minimal Optimization（SMO）**：Platt 在 1998 年提出把大型 SVM 二次规划分解为可解析更新的最小子问题，推动训练效率；这是后续优化工作，1995 年本文没有包含 SMO。
- **LIBSVM**：Chang 与 Lin 的 LIBSVM 把分类、回归等支持向量方法做成长期维护的软件；官方页面称 2011 年实现论文发表于 *ACM TIST*。[来源：LIBSVM 官方页](https://www.csie.ntu.edu.tw/~cjlin/libsvm/)
- **kernel methods**：核 PCA、支持向量回归、one-class SVM 等后续路线共同形成更广泛的 kernel methods。它们继承“只通过核访问特征空间”的计算范式，但各自目标与本文二分类器不同。

### 学术认可

ACM 的 2008 Awards Committee 年报将 Corinna Cortes 与 Vladimir Vapnik 列为 2008 Paris Kanellakis Theory and Practice Award 获得者，理由是发展了支持向量机这一用于分类及相关机器学习问题的有效算法。这项授奖材料能支持“理论产生了显著实践影响”，但不意味着全部 SVM 思想只来自 1995 年单篇论文。

### 引用统计

- Springer 的 DOI `10.1007/BF00994018` 页面显示 **43k Citations**、284k Accesses（动态页面，查询日期 2026-08-08；计数为平台显示的四舍五入值，不解释为跨 DOI 合并后的唯一总计）。
- 本次未获得可稳定复核的 Google Scholar 精确数字，故不填伪精确值。

### 当代回响

SVM 在深度学习兴起后不再独占视觉基准前沿，但最大间隔、核表示、稀疏对偶解与正则化权衡仍是现代机器学习课程和工具链的基础。对于中小规模、高维稀疏特征或需要明确凸目标的任务，线性与核 SVM 仍是可解释、可复现的基线。这里是工程分析，具体效果仍取决于数据规模、特征、核与校准需求。

---

## 个人笔记

最让我停下来的一处是表 2。多项式次数从 3 增到 7，文中估算的显式特征空间从约 $10^6$ 膨胀到约 $10^{16}$，测试错误率却只在 4.4% 与 4.3% 之间变化，支持向量数也只从 148 增到 190。高维在这里没有以“展开后的坐标表”出现；训练真正看到的是样本之间的核矩阵，以及间隔约束挑出的边界点。

这组数字也提醒我克制：论文展示的是一次特定 OCR 实验中没有出现明显过拟合，理论保障依赖 margin、核与样本等条件。把它写成“维数再高也不会过拟合”会抹掉作者在 p. 285–286 讨论的容量—训练误差权衡。

写作上，论文的结论很短，只列出三块结构：optimal hyperplane 的解法、dot-product convolution、soft margin。这个三分法比把 SVM 概括成一个孤立公式更有解释力：稀疏表示解决“由谁决定边界”，核解决“怎样进入高维”，soft margin 解决“怎样容纳现实数据的违约”。

---

## 小红书写作备忘

### Hook 素材

1. 256 维像素经 7 次多项式映射，对应约 $10^{16}$ 维显式特征；算法从未把这个向量真正展开。
2. NIST benchmark 中，单一 4 次多项式 SVN 与专用 LeNet4 同为 1.1% 测试错误；比较范围限定在论文图 9。
3. “支持向量”是 KKT 条件筛出的边界样本，模型名称来自解可展开在这些样本上。

### 核心 Insight（一句话）

支持向量网络把最大间隔、合法核内积与 soft margin 组合成一个凸优化框架，使高维非线性分类可以由边界样本和两两核值来表达。

### 自查重点

1. 1992 年已有可分数据的 optimal margin classifier；1995 年论文新增重点是不可分数据的 soft margin 扩展与实证。
2. 论文实验采用 $k=2$ 的 $F(\sum_i\xi_i)$ 形式；现代常见 $C\sum_i\xi_i$ 是附录明确推导的特例。
3. NIST 上 SVN 与 LeNet4 同为 1.1%，不能写成单独领先。
4. 式 (5) 是带期望且有可分条件的界；论文对实际支持向量数的替换是实验观察。
5. 核函数需要满足 Mercer 条件，不能把任意相似度都称为合法核。

### 动态 Hashtags

#支持向量机 #核方法 #最大间隔

---

## 来源与页码索引

### 论文原文

- Cortes, C. & Vapnik, V. (1995). *Support-vector networks*. *Machine Learning*, 20, 273–297. [Springer DOI 页面](https://doi.org/10.1007/BF00994018)
- 摘要与贡献定位：p. 273、p. 276、pp. 290–291。
- 硬间隔与支持向量：pp. 277–280，式 (8)–(20)；附录 pp. 291–293，式 (40)–(48)。
- soft margin：pp. 280–282，式 (21)–(30)；附录 pp. 293–296，式 (49)–(67)。
- 核与 Mercer 条件：pp. 282–284，式 (31)–(37)。
- 泛化讨论：pp. 285–286，式 (38)。
- USPS 与 NIST 实验：pp. 286–290，表 1–3、图 5–9。

### 官方背景与后续资料

- [Google Research：Corinna Cortes](https://research.google/people/author121/)
- [Google Research：Support-Vector Networks 收录页](https://research.google/pubs/support-vector-networks/)
- [NEC：2013 C&C Prize 获奖人 Vladimir Vapnik 资料](https://www.nec.com/en/press/201310/images/2401-01-02.pdf)
- [Columbia Engineering：Vladimir Vapnik](https://www.engineering.columbia.edu/faculty-staff/directory/vladimir-vapnik)
- [ACM Awards Committee：2008 Kanellakis Award 记录](https://www.acm.org/binaries/content/assets/about/annual-reports/awards-fy09.pdf)
- [LIBSVM 官方项目页](https://www.csie.ntu.edu.tw/~cjlin/libsvm/)
