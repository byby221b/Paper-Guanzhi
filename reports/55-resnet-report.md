# 《Deep Residual Learning for Image Recognition》精读报告

## 元信息

- 标题：*Deep Residual Learning for Image Recognition*
- 作者：Kaiming He（何恺明）、Xiangyu Zhang（张祥雨）、Shaoqing Ren（任少卿）、Jian Sun（孙剑）
- 发表时机构：Microsoft Research
- 公开版本：[arXiv:1512.03385](https://arxiv.org/abs/1512.03385)，v1 提交于 2015-12-10；[CVPR 2016 Open Access](https://openaccess.thecvf.com/content_cvpr_2016/html/He_Deep_Residual_Learning_CVPR_2016_paper.html)，正式会议论文页码 770–778
- DOI：[10.1109/CVPR.2016.90](https://doi.org/10.1109/CVPR.2016.90)
- 精读日期：2026-09-04
- 对应小红书期号：#55

### 年份口径

日程沿用 2015 年，即 arXiv 首次公开与 ILSVRC/COCO 竞赛发生的年份；CVF 与 DOI 元数据把论文列入 CVPR 2016。报告在涉及发表 venue、页码和奖项时采用“CVPR 2016”，不把两个口径混写。

### 原文验证

本次保存的是 Computer Vision Foundation 提供的 CVPR 2016 open-access PDF。服务器返回 HTTP 200、`Content-Type: application/pdf` 与 603,123 字节 `Content-Length`；本地文件大小一致，文件头为 PDF 1.3，共 9 页，正文提取 74,132 字节。已逐页核查标题页、Figures 1–7、公式 (1)–(2)、Tables 1–8、ImageNet/CIFAR-10/object detection 实验、脚注、局限与参考文献。引用定位使用正式页码 770–778。

## 作者与合作背景

### 论文能够确认的身份

原文首页把四位作者共同列为 Microsoft Research，未提供 author contribution statement。2015 年微软对同一四人团队的官方报道将孙剑称为 principal research manager、何恺明称为 researcher，并将张祥雨与任少卿称为 academic interns；这份报道早于 ResNet 正式公开，只用于说明团队当时的合作环境，不据此拆分本文创意或实验归属。

### 何恺明

何恺明个人主页记录：他 2007 年获清华大学学士学位，2011 年获香港中文大学博士学位，2011–2016 年任职微软亚洲研究院，之后在 FAIR 工作，2024 年加入 MIT。主页将 ResNet、Faster R-CNN、Mask R-CNN、MoCo 与 MAE 列为其主要研究脉络，也记录本文获得 CVPR 2016 Best Paper 与 2026 Longuet-Higgins Prize。报告不补写主页未给出的博士导师信息。

### 张祥雨、任少卿、孙剑

未来科学大奖官方获奖人资料给出三人的教育背景：张祥雨 2012 年本科毕业于西安交通大学、2017 年获该校与微软亚洲研究院联合培养博士学位；任少卿 2011 年本科毕业于中国科学技术大学、2016 年获该校与微软亚洲研究院联合培养博士学位；孙剑 1997 年本科毕业、2003 年博士毕业于西安交通大学。微软官方报道还记载孙剑早期曾随沈向洋参与 stereo reconstruction 与 belief propagation 研究。

2023 年未来科学大奖将数学与计算机科学奖授予四位作者，官方表述是奖励他们“提出深度残差学习”。获奖页明确说相关研究由团队于 2012–2016 年间在北京微软亚洲研究院完成。以上资料支持“共同团队”这一背景；原论文没有证据允许把 residual formulation、network design 或竞赛系统分别归到某一位作者。

## 历史语境

### 深度曾经既是资源，也是优化负担

AlexNet 在 2012 年展示大型卷积网络与 GPU 训练的潜力；VGG 进一步把 3×3 卷积堆叠到 16/19 个 weighted layers，GoogLeNet 则用 Inception 结构构建约 22 层网络。到 2015 年，深度与表征能力之间已有强烈经验联系，但简单继续堆层会遇到两类不同障碍。

第一类是 vanishing/exploding gradients。论文 §1 认为 normalized initialization 与 Batch Normalization 已使数十层网络能够开始收敛。第二类是本文集中处理的 degradation problem：在已经能传播非零激活与健康梯度的设置中，更深 plain network 的训练误差反而更高。由于训练误差也恶化，这个现象不能由测试集过拟合单独解释。

Figure 1 给出 CIFAR-10 的直接例子：56-layer plain net 从训练到测试都劣于 20-layer。Figure 4 在 ImageNet 上重复这一现象：34-layer plain net 的训练误差长期高于 18-layer。论文据此把问题定位为“现有 solver 难以在可行时间找到至少等同于浅层网络的解”。作者把深 plain net 可能具有极慢收敛率写成 conjecture，没有给出一般性优化定理。

### 构造性上存在的解，优化器却未必找到

给一个训练好的浅层网络，在后面新增若干恒等层，理论上可以构造一个函数完全相同的深层网络。因此深模型的函数空间至少包含浅模型对应的解，最优训练误差不应更差。现实训练却找不到这个显然存在的解，说明参数化方式会影响优化可达性。

这条观察把研究焦点从“更深网络能否表达”移到“同一个目标映射怎样更容易被求解”。作者令新增层学习相对于 identity 的修正量；当 identity 已经合适时，只需把 residual 压向零。

### Shortcut 早已有之，本文选择了无门恒等路径

§2 回顾了多层感知机中的线性 shortcut、deep supervision、Inception 分支以及若干中心化传播方法。与本文同期的 Highway Networks 使用带参数、依赖数据的 gates 控制 shortcut；gate 可以关闭。ResNet 的主要实现选择是 parameter-free identity shortcut，信息始终沿 shortcut 传递，残差分支只学习附加修正。

所以本文的历史贡献宜准确描述为：围绕 degradation problem 系统提出并验证 deep residual learning framework，并用 identity shortcuts 构建和训练显著更深的视觉网络。Shortcut connection 本身存在更早先例，论文也主动列出了这些来源。

## 问题形式化

### 目标映射与残差映射

设若干 stacked layers 的输入为 $x$，希望拟合的底层映射为 $H(x)$。Plain parameterization 直接用若干非线性层逼近 $H$。Residual parameterization 定义

$$
F(x)=H(x)-x,
$$

并重写为

$$
H(x)=F(x)+x.
$$

论文实际 residual block 的核心式为（公式 (1)，p. 772）

$$
y=F(x,\{W_i\})+x,
$$

其中 $F$ 是两层或三层卷积堆叠；原始架构在相加后再施加 ReLU。若输入、输出维度不一致，shortcut 使用线性投影（公式 (2)）

$$
y=F(x,\{W_i\})+W_sx.
$$

### 输入、输出与训练目标

- 输入：ImageNet/CIFAR-10 的 RGB 图像；迁移实验还使用 PASCAL VOC 与 MS COCO 图像。
- 输出：分类任务为类别概率；检测任务由 Faster R-CNN 产生类别与 bounding boxes。
- 训练：分类网络从 scratch 以 SGD 优化 softmax classification objective；论文没有把 residual learning 写成一个额外 loss。
- 评价：ImageNet 报告 top-1/top-5 error，CIFAR-10 报告 test error，检测报告 PASCAL mAP@.5 与 COCO mAP@[.5,.95]。

### 公平比较的关键约束

18/34-layer plain 与 residual baseline 共享 depth、width 和主要计算结构。Identity shortcuts 不增加参数，element-wise addition 的计算相对卷积可忽略。作者因此能把 Table 2 与 Figure 4 的差异主要归因于 parameterization，而非更宽网络或更多卷积参数。

## 核心方法

### Basic block：学习两层修正

ResNet-18/34 使用两层 3×3 卷积构成 $F$。论文的 Figure 2 可写为

$$
F(x)=W_2\sigma(W_1x),\qquad y=F(x)+x,
$$

随后再计算 $\sigma(y)$。卷积后接 Batch Normalization，bias 为简化记号而省略。当输入与输出 shape 相同，shortcut 直接复制 $x$；当 feature map 尺寸减半、channel 加倍，shortcut 以 stride 2 配合 zero padding（option A）或 1×1 projection（option B/C）匹配维度。

### 三种维度匹配策略

- **Option A**：尺寸变化时下采样 identity，并在 channel 维补零；所有 shortcuts 都无参数。
- **Option B**：只在维度变化处使用 projection，其余保持 identity。
- **Option C**：所有 shortcuts 都使用 projection。

Table 3 中三者都明显优于 plain-34；B 略优于 A，C 仅边际优于 B。作者选择 B 构建后续 ImageNet 深模型，理由是 projection 并非解决 degradation 的必要条件，全面投影还会增加 model size 与 time/memory complexity。

### Bottleneck block：把深度建立在可承受计算上

ResNet-50/101/152 使用 1×1、3×3、1×1 三层 bottleneck。第一层降低 channel，3×3 在低维空间计算，第三层恢复 channel。以 Figure 5 的 256-d 输入、64-d bottleneck 为例，每个空间位置的乘加量近似为

$$
256\times64+9\times64^2+64\times256=69{,}632,
$$

而 64-d basic block 的两次 3×3 卷积为

$$
2\times9\times64^2=73{,}728.
$$

两者量级相近，bottleneck 却能让 block 的外部表示达到 256 channels。论文报告 ResNet-152 为 11.3 billion FLOPs，低于其引用的 VGG-16/19 的 15.3/19.6 billion FLOPs。这里的 FLOPs 是论文架构复杂度口径，不是实测吞吐、显存或延迟。

### 深度配置

ImageNet 架构先以 7×7 stride-2 convolution 和 max pooling 将分辨率降到 56×56，再经过 conv2_x 至 conv5_x 四个 stage，channel 随空间分辨率下降而增加。ResNet-18/34 使用 basic blocks；50/101/152 分别堆叠 bottleneck blocks，其中 ResNet-152 的 conv4_x stage 包含 36 个三层 block。

CIFAR-10 使用更轻的 $6n+2$ weighted-layer 结构，在 32、16、8 三个空间尺度分别放置 $2n$ 个 3×3 layers，filters 为 16、32、64。所有 shortcut 采用 option A，因此 residual 与 plain counterpart 的 depth、width、parameter count 完全相同。

## 关键公式推导

### 推导一：残差重参数化保留目标函数集合

**原文定位：** §3.1，p. 772；以下为定义展开。

若 stacked layers 有能力逼近 $H(x)$，则令 $F(x)=H(x)-x$ 后，输出

$$
F(x)+x=H(x)-x+x=H(x).
$$

函数目标没有改变，改变的是待学习函数的参考点。若最佳映射接近 identity，可写成

$$
H(x)=x+\varepsilon(x),
$$

residual branch 只需学习幅度较小的 $\varepsilon$。Figure 7 观察到 ResNet layer responses 的标准差通常小于 plain counterpart，为“许多目标修正较小”提供经验支持；它不构成所有任务上 $F$ 必然更易优化的证明。

### 推导二：shortcut 在局部 Jacobian 中提供恒等项

**原文定位：** 公式 (1)；以下为补充推导。

对相加前输出 $y=x+F(x)$，有

$$
\frac{\partial y}{\partial x}=I+J_F(x),
$$

其中 $J_F$ 是 residual branch 的 Jacobian。若 loss 为 $L$，则

$$
\frac{\partial L}{\partial x}
=\frac{\partial L}{\partial y}\left(I+J_F(x)\right).
$$

展开后包含一项直接复制的 $\partial L/\partial y$，以及一项经 residual branch 变换的梯度。这个式子解释了 shortcut 为何能提供短路径，但原始 ResNet 还在 addition 后使用 ReLU；完整 block 的 Jacobian 是

$$
D_{\mathrm{ReLU}}(y)\left(I+J_F(x)\right),
$$

ReLU mask 仍会影响传播。作者随后在 *Identity Mappings in Deep Residual Networks* 中系统分析 identity pre-activation 路径并改进 residual unit。不能把这里的局部展开写成原论文已经证明了任意深网络梯度永不消失。

### 推导三：多个无后激活残差块的望远镜展开

**后续资料定位：** He et al., 2016 identity-mapping analysis；以下采用无 after-add activation 的简化条件。

令

$$
x_{l+1}=x_l+F_l(x_l),
$$

逐层代入得到

$$
x_L=x_l+\sum_{i=l}^{L-1}F_i(x_i).
$$

因此更深位置可以接收浅层 feature 的 additive direct path。对 $x_l$ 求导：

$$
\frac{\partial L}{\partial x_l}
=\frac{\partial L}{\partial x_L}
\left(I+\frac{\partial}{\partial x_l}
\sum_{i=l}^{L-1}F_i(x_i)\right).
$$

这展示 direct information/gradient path 的代数来源。它依赖恒等 shortcut 与相加结构；原始 post-activation block、dimension-changing projection、BN 的训练/推理状态都会改变具体 Jacobian。

## 实验设置

### ImageNet classification

- 数据：ImageNet 2012，1.28 million training images、50k validation images、100k test images，共 1000 classes。
- 训练增强：短边随机采样至 $[256,480]$，随机 224×224 crop、horizontal flip、per-pixel mean subtraction 与 color augmentation。
- 优化：Batch Normalization 位于 convolution 后、activation 前；SGD mini-batch 256，初始学习率 0.1，plateau 时除以 10，最多 $60\times10^4$ iterations；weight decay 0.0001，momentum 0.9，不用 dropout。
- 测试：受控比较使用 10-crop；best results 使用 fully convolutional multi-scale scoring，短边尺度为 224、256、384、480、640。

### CIFAR-10

- 数据：50k training images、10k test images、10 classes。
- 增强：四周各 pad 4 pixels，再随机 32×32 crop 或 horizontal flip；测试用原图 single view。
- 优化：batch 128、两块 GPU、weight decay 0.0001、momentum 0.9；学习率 0.1，在 32k/48k iterations 除以 10，64k 停止。
- ResNet-110 初始用约 400 iterations、学习率 0.01 warm-up，训练误差低于 80% 后恢复 0.1。

### Detection transfer

作者在 Faster R-CNN baseline 内只替换 backbone，比较 VGG-16 与 ResNet-101；PASCAL VOC 使用 mAP@.5，COCO 使用 mAP@.5 与更严格的 mAP@[.5,.95]。附录包含竞赛系统的更多细节，正文 Tables 7–8 是受控 baseline 比较。

## 实验结果

### 深 plain net 的训练退化，residual parameterization 逆转趋势

Table 2 的 ImageNet 10-crop top-1 error 如下：

| 深度 | Plain | ResNet |
|---|---:|---:|
| 18 layers | 27.94% | 27.88% |
| 34 layers | 28.54% | 25.03% |

Plain-34 比 plain-18 高 0.60 percentage point；ResNet-34 比 ResNet-18 低 2.85 points，也比同深度 plain-34 低 3.51 points。Figure 4 同时显示 training error 的顺序，因此结果直接支持“residual formulation 缓解本设置中的 optimization degradation”。作者额外把 plain training 延长三倍仍观察到退化，但没有对所有 optimizer、normalization 或任务给出一般保证。

### Identity 已承担主要作用，projection 带来较小增益

Table 3 中 ResNet-34 A/B/C 的 top-1 error 分别为 25.03%、24.52%、24.19%，top-5 error 分别为 7.76%、7.46%、7.40%。三种 shortcut 均优于 plain-34 的 28.54%/10.02%。C 比 B 多十三个 projection shortcuts，只改善 0.33 top-1 point；论文据此保留更经济的 option B。

### 更深 bottleneck ResNet 在 ImageNet 上持续改善

Table 3 的 10-crop 结果从 ResNet-50 的 22.85%/6.71%，改善到 ResNet-101 的 21.75%/6.05% 与 ResNet-152 的 21.43%/5.71%。Table 4 的 single-model multi-scale result 中，ResNet-152 达到 19.38% top-1、4.49% top-5 validation error。六个不同深度模型的 ensemble 在 ImageNet test server 上得到 3.57% top-5 error，并获 ILSVRC 2015 classification task 第一名。

“152 layers 比 VGG 深 8 倍但复杂度更低”使用的是 weighted-layer count 与论文 FLOPs 估算；它没有比较当时硬件上的 wall-clock throughput，也没有把 ensemble 成本与 single-model 成本混在一起。

### CIFAR-10 同时给出成功与边界

Table 6 中 ResNet-20/32/44/56/110 的 test error 依次为 8.75%、7.51%、7.17%、6.97%、6.43%（ResNet-110 五次 mean 为 $6.61\pm0.16\%$）。Plain nets 随深度增加出现更高训练误差，plain-110 的 error 高于 60%，未画入 Figure 6。

1202-layer ResNet 能把 training error 降到 0.1% 以下，说明此处没有重现 plain net 的 optimization failure；其 test error 却回升到 7.93%，差于 110-layer。作者将此归因于 19.4M-parameter model 对小数据集的 overfitting，并提出未来结合更强 regularization。这个结果限制了“越深越准”的通俗化表述。

### 检测迁移显示 backbone 表征收益

在相同 Faster R-CNN baseline 下，PASCAL VOC 2007 test mAP 从 VGG-16 的 73.2% 升到 ResNet-101 的 76.4%；VOC 2012 从 70.4% 升到 73.8%。COCO validation 的 mAP@.5 从 41.5% 升到 48.4%，mAP@[.5,.95] 从 21.2% 升到 27.2%，即 +6.0 absolute points、约 28% relative improvement。论文据此说明 residual representation 能迁移到检测；它没有在正文受控表中分离所有后续 detection-system tricks。

### 竞赛结果

论文记录 residual networks 支撑团队在 ILSVRC 2015 classification、detection、localization，以及 COCO 2015 detection、segmentation tracks 获得第一。竞赛排名是系统级结果；报告把它与 Tables 7–8 的 backbone controlled comparison 分开陈述。

## 实验设计评价

### 优点

- Plain/residual baseline 在 depth、width、parameter count 与主要卷积计算上严格对齐，直接测量重参数化效果。
- 同时报告 training 与 validation curves，能把 optimization degradation 与普通 test overfitting 区分开。
- ImageNet 与 CIFAR-10 重复深度趋势，并用 1202-layer case 主动展示 generalization boundary。
- Identity/partial projection/full projection 提供 shortcut ablation；basic/bottleneck 设计又分离可训练性与计算预算。
- Detection transfer 保持 Faster R-CNN implementation 一致，给出 backbone replacement 的相对清晰证据。

### 不足

- 没有 formal theorem 解释 residual parameterization 为何在一般条件下更易优化；“可能指数慢收敛”与“identity 是良好 preconditioner”属于作者假设。
- Plain 与 residual 都结合 BN、特定 initialization 和 SGD recipe，论文没有系统覆盖其他 optimizer、normalization 或 activation。
- ImageNet 只报告一次训练结果，没有随机种子方差、置信区间或显著性检验；CIFAR 仅 ResNet-110 给出五次统计。
- 论文报告 FLOPs、参数和竞赛 accuracy，没有训练时间、能耗、显存峰值、推理吞吐或延迟。
- 1202-layer network 的 overfitting 归因没有 regularization ablation；作者明确把它留作后续研究。

## 局限性与适用边界

### 作者明示的开放问题

作者把深 plain nets 的 optimization difficulty 原因留待未来研究；§3.1 也在脚注明确说多层非线性网络是否能渐近逼近任意复杂函数仍是开放问题。Figure 7 的 small response 是支持性观察，不能代替因果证明。

### 深度收益依赖架构与数据规模

ResNet-1202 在训练集拟合充分，却不如 ResNet-110 泛化。Residual connections 让更深模型更容易被优化，不自动选择合适 capacity，也不消除数据、regularization 与 compute constraints。

### Shortcut 的实现细节会改变信息路径

原始 block 在 addition 后使用 ReLU；projection shortcut 也不再是纯 identity。后续 *Identity Mappings in Deep Residual Networks* 通过 pre-activation 进一步清理 forward/backward direct path，并在 CIFAR-10 上报告 1001-layer network 4.62% error。现代实现常用“ResNet v1/v2”区分这些配置，不能把后续 pre-activation 结论无条件回写进 2015/2016 原架构。

### 计算复杂度与实际系统性能分开

论文的 bottleneck 和 identity shortcut 确实控制 parameter/FLOP growth，但更深网络仍增加 sequential layers、activation memory 与训练代价。文中没有 target hardware measurement，无法据此断言 ResNet-152 的实际速度快于 VGG，或 ensemble 适合特定 serving latency。

## 后续影响

### 从 residual unit 到更清晰的 identity path

四位作者在 2016 年的 *Identity Mappings in Deep Residual Networks* 分析 block 间 forward/backward propagation，提出 pre-activation residual unit，并以 1001-layer CIFAR 与 200-layer ImageNet 实验改善训练和泛化。它既延续原始洞见，也修正了 post-add activation 对直接路径的阻隔。

### 多路径解释与连接结构扩展

Veit、Wilber 与 Belongie 在 NeurIPS 2016 将 residual networks 展开为不同长度路径的集合，并通过 lesion study 提出 ensemble-like interpretation；这是后续解释，不是原论文自证。DenseNet 在 CVPR 2017 进一步把短连接扩展为每层接收所有前序 feature maps 的 concatenation，强调 feature propagation 与 reuse。两者都显示“缩短信息路径”成为深网络设计的一条重要路线，但连接运算和表示语义各不相同。

### 跨架构使用

Transformer 原文对每个 attention/feed-forward sublayer 使用 residual connection 与 layer normalization。这里能确认的是结构组件被采用；Transformer 的注意力机制、序列建模目标与训练体系并不能由 ResNet 单独推出。何恺明个人主页也将 residual connections 在 vision、Transformers、AlphaGo Zero 与 AlphaFold 等模型中的使用列为本文的长期影响，这是作者自述，应与原论文 2016 年证据分层阅读。

### 获奖记录

TCPAMI Longuet-Higgins Prize 官方页列出本文为 2026 年获奖论文之一，另一篇是 YOLO；该奖表彰十年前在 computer vision 产生重要影响的 CVPR papers。TCPAMI 的 CVPR paper-awards 页面也列出本文获得 CVPR 2016 Best Paper。2023 未来科学大奖另以“深度残差学习的基础性贡献”奖励四位作者。

### 引用统计

引用数据库口径不同，以下均为 2026-09-04 的独立快照，不相加：

- [Semantic Scholar paper 2c03df8b48bf3fa39054345bafabfeff15bfd11d](https://www.semanticscholar.org/paper/2c03df8b48bf3fa39054345bafabfeff15bfd11d)：`citationCount = 237,964`，`influentialCitationCount = 32,796`。
- [OpenAlex work W2194775991](https://openalex.org/W2194775991)：`cited_by_count = 228,018`，API 记录更新于 2026-09-03。

这些数字受版本归并、覆盖范围和更新周期影响，只表示查询时数据库状态。

## 个人笔记

我读完最在意的是 Table 2 的四个数字。18-layer plain 与 ResNet 几乎相同；加深到 34 层，plain 的训练与验证一起变差，ResNet 却把深度变成了收益。Residual connection 的力量由此显得很具体：它重画了 optimizer 前往已有好解的地形，而无需为 shortcut 增加卷积参数。

Figure 7 又给这个故事添了一层细节。更深 ResNet 的单层 residual response 往往更小，像是在一个已可用的状态上连续做细微校正。我愿意把它类比成数值积分中的小步更新，但这只是阅读联想；原文测的是 activation response standard deviation，没有建立微分方程解释。

最值得保留的反例来自 1202 layers。训练误差低于 0.1%，说明“能优化”这一关已经过去；test error 仍比 110-layer 差。它把 trainability、capacity 与 generalization 清楚地拆开。Residual path 解决的是怎样走到解附近，数据与正则化仍决定哪个解值得抵达。

工程上还需警惕 FLOPs 的诱惑。11.3 billion 低于 VGG-19 的 19.6 billion 是可信的架构计数，却没有告诉我们更深的 sequential graph 在具体硬件上的 latency、kernel efficiency 和 activation memory。经典论文给出结构原理；部署结论仍需要目标系统测量。

## 小红书写作备忘

### Hook 素材

1. 一个更深网络明明包含浅网络的恒等解，训练误差却更高；ResNet 从这个优化悖论出发。
2. Table 2 里 plain-34 比 plain-18 更差，ResNet-34 则比 ResNet-18 低 2.85 个 top-1 error points。
3. 1202-layer ResNet 能把训练误差压到 0.1% 以下，测试误差仍输给 110-layer，直接划出“可训练”与“可泛化”的边界。

### 核心 Insight（一句话）

Residual learning 把若干层的目标改写为相对于 identity 的修正，并以无参数 shortcut 提供直接信息路径，使显著增加深度在受控视觉实验中更容易优化。

### 自查重点

- 日程年份 2015 对应 arXiv/竞赛，正式发表为 CVPR 2016；不要混写。
- Degradation 指更深 plain net 的 training error 也上升，不等同于 overfitting 或经典 vanishing gradient。
- Shortcut connection 有更早先例；本文的贡献是 deep residual framework、identity implementation 与系统实证。
- 3.57% 是六模型 ensemble 的 ImageNet test top-5 error；ResNet-152 single model 为 4.49% validation top-5。
- COCO 的 28% 是 mAP@[.5,.95] 从 21.2 到 27.2 的相对提升，absolute gain 为 6.0 points。
- 1202-layer test error 7.93% 差于 ResNet-110；不能写成深度单调提升 accuracy。
- 原论文没有 formal optimization theorem，也没有实际训练/推理延迟或吞吐测量。

### 动态 Hashtags

#ResNet #残差学习 #计算机视觉 #深度学习 #Paper观止

## 来源与证据分层

### 原文与官方资料

1. He, Zhang, Ren & Sun, *Deep Residual Learning for Image Recognition*. [CVF 论文页](https://openaccess.thecvf.com/content_cvpr_2016/html/He_Deep_Residual_Learning_CVPR_2016_paper.html)；[PDF](https://openaccess.thecvf.com/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf)；[arXiv](https://arxiv.org/abs/1512.03385)
2. TCPAMI, *Longuet-Higgins Prize*. [官方获奖页](https://tc.computer.org/tcpami/awards/longuet-higgins-prize/)
3. TCPAMI, *CVPR Paper Awards*. [官方获奖页](https://tc.computer.org/tcpami/awards/cvpr-paper-awards/)
4. Kaiming He, personal academic homepage. [主页](https://people.csail.mit.edu/kaiming/)
5. Future Science Prize, 2023 Mathematics and Computer Science Prize laureates. [官方获奖人资料](https://www.futureprize.org/en/laureates/detail/75.html)
6. Microsoft, *Microsoft researchers win ImageNet computer vision challenge*. [官方报道](https://blogs.microsoft.com/ai/microsoft-researchers-win-imagenet-computer-vision-challenge/)
7. Semantic Scholar paper record. [记录](https://www.semanticscholar.org/paper/2c03df8b48bf3fa39054345bafabfeff15bfd11d)
8. OpenAlex work W2194775991. [记录](https://openalex.org/W2194775991)

### 后继原始资料

- He et al., *Identity Mappings in Deep Residual Networks*. [arXiv:1603.05027](https://arxiv.org/abs/1603.05027)
- Veit, Wilber & Belongie, *Residual Networks Behave Like Ensembles of Relatively Shallow Networks*. [NeurIPS 2016](https://proceedings.neurips.cc/paper_files/paper/2016/hash/37bc2f75bf1bcfe8450a1a41c200364c-Abstract.html)
- Huang et al., *Densely Connected Convolutional Networks*. [CVPR 2017](https://openaccess.thecvf.com/content_cvpr_2017/html/Huang_Densely_Connected_Convolutional_CVPR_2017_paper.html)
- Vaswani et al., *Attention Is All You Need*. [NeurIPS 2017](https://proceedings.neurips.cc/paper/2017/hash/3f5ee243547dee91fbd053c1c4a845aa-Abstract.html)

### 证据标记

- **论文事实**：问题定义、架构、公式、训练配置、Figures 1–7、Tables 1–8 与竞赛结果来自本次验证的 CVF 九页 PDF。
- **后续资料**：pre-activation、multi-path interpretation、DenseNet、Transformer 使用 residual connections、奖项与引用数独立列源。
- **补充推导**：Jacobian、望远镜展开与 bottleneck 乘加量均明确写出条件，未冒充原文定理。
- **个人分析**：对优化地形、小步更新和目标硬件性能的判断只作为精读笔记。
