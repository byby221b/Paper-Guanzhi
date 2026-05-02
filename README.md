# Paper Guanzhi 📜

> *A curated anthology of the most influential papers in computer science.*

**"Guanzhi"** comes from the Chinese classic *"古文观止"*, meaning "the finest writings — see these and look no further." This repository collects two kinds of papers: **Foundations** — papers whose significance is self-evident and needs no award to prove it; and **Test-of-Time Awards** — papers honored years after publication for their lasting impact.

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
![License](https://img.shields.io/badge/license-CC--BY--4.0-blue)
![Last Updated](https://img.shields.io/badge/updated-May%202026-brightgreen)

---

## Why This List?

Thousands of papers are published at top venues each year. Some are recognized as classics only decades later. This repository approaches the problem from two directions: **Foundations** traces the intellectual DNA of AI from Turing and Shannon to Transformers, curated from authoritative histories like Nilsson's *The Quest for AI*; **Test-of-Time Awards** gathers papers formally honored by major conferences for proven lasting impact. Quality over quantity — every entry must earn its place.

---

## Contents

- **[Foundations](#foundations)** — Selected for their landmark status, independent of any award
  - [Pre-AI (1936–1956)](foundations/pre-ai.md) — Turing Machine, Information Theory, Dartmouth
  - [Classical AI (1956–1979)](foundations/classical-ai.md) — Search, Planning, Knowledge Representation
  - [Modern Foundations (1980–2009)](foundations/modern-foundations.md) — Bayesian Networks, Neural Network Revival, Reinforcement Learning
  - [Deep Learning Era (2010–)](foundations/deep-learning-era.md) — CNN, Transformer, Scaling Laws
- **[Test-of-Time Awards](#test-of-time-awards)** — Papers honored years later for proven lasting impact
  - [AI & Machine Learning](#ai--machine-learning)
  - [Computer Vision](#computer-vision)
  - [Natural Language Processing](#natural-language-processing)
  - [Information Retrieval & Data Mining](#information-retrieval--data-mining)
- [Award Quick Reference](#award-quick-reference)
- [Contributing](#contributing)

---

## Foundations

> Foundational works — selected for their irreplaceable role in the history of AI, independent of any award.
> Primary references: Nils Nilsson [*The Quest for AI*](https://ai.stanford.edu/~nilsson/QAI/qai.pdf) (Stanford, 2010) and [Ilya Sutskever's recommended reading list](https://github.com/dzyim/ilya-sutskever-recommended-reading), cross-validated against each other.

| Period | Page | Papers | Keywords |
|------|------|--------|--------|
| 1936–1956 | **[Pre-AI](foundations/pre-ai.md)** | 12 | Turing Machine, Information Theory, Dartmouth, Logic Theorist |
| 1956–1979 | **[Classical AI](foundations/classical-ai.md)** | 18 | Perceptron, A\*, STRIPS, Resolution, Frames, SHRDLU |
| 1980–2009 | **[Modern Foundations](foundations/modern-foundations.md)** | 20 | Bayesian Networks, Backprop, SVM, Q-learning, LDA, Deep Blue |
| 2010–Present | **[Deep Learning Era](foundations/deep-learning-era.md)** | 15 | AlexNet, ResNet, Transformer, GAN, GNN, Scaling Laws |

---

## Test-of-Time Awards

### AI & Machine Learning

#### NeurIPS — Test of Time Award

> Established in **2017**. Honors papers from ~10 years prior.

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2025 | **Faster R-CNN: Towards Real-Time Object Detection with Region Proposal Networks** | Shaoqing Ren, Kaiming He, Ross Girshick, Jian Sun | NeurIPS 2015 | [🔗](https://blog.neurips.cc/2025/11/26/announcing-the-test-of-time-paper-award-for-neurips-2025/) |
| 2024 | **Generative Adversarial Nets** | Ian Goodfellow et al. | NeurIPS 2014 | [🔗](https://blog.neurips.cc/2024/11/27/announcing-the-neurips-2024-test-of-time-paper-awards/) |
| 2024 | **Sequence to Sequence Learning with Neural Networks** | Ilya Sutskever, Oriol Vinyals, Quoc V. Le | NeurIPS 2014 | [🔗](https://blog.neurips.cc/2024/11/27/announcing-the-neurips-2024-test-of-time-paper-awards/) |
| 2023 | **Distributed Representations of Words and Phrases and their Compositionality** | Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg Corrado, Jeff Dean | NeurIPS 2013 | [🔗](https://blog.neurips.cc/2023/12/11/announcing-the-neurips-2023-paper-awards/) |
| 2022 | **ImageNet Classification with Deep Convolutional Neural Networks** | Alex Krizhevsky, Ilya Sutskever, Geoffrey Hinton | NeurIPS 2012 | [🔗](https://blog.neurips.cc/2022/11/21/announcing-the-neurips-2022-awards/) |
| 2021 | **Online Learning for Latent Dirichlet Allocation** | Matthew Hoffman, Francis Bach, David Blei | NeurIPS 2010 | [🔗](https://blog.neurips.cc/2021/11/30/announcing-the-neurips-2021-award-recipients/) |
| 2020 | **HOGWILD!: A Lock-Free Approach to Parallelizing Stochastic Gradient Descent** | Benjamin Recht, Christopher Re, Stephen Wright, Feng Niu | NeurIPS 2011 | [🔗](https://blog.neurips.cc/2020/12/07/announcing-the-neurips-2020-award-recipients/) |
| 2019 | **Dual Averaging Method for Regularized Stochastic Learning and Online Optimization** | Lin Xiao | NeurIPS 2009 | [🔗](https://neurips.cc/virtual/2019/awards_detail) |
| 2018 | **The Tradeoffs of Large Scale Learning** | Leon Bottou, Olivier Bousquet | NeurIPS 2007 | [🔗](https://neurips.cc/virtual/2018/awards_detail) |
| 2017 | **Random Features for Large-Scale Kernel Machines** | Ali Rahimi, Benjamin Recht | NeurIPS 2007 | [🔗](https://neurips.cc/virtual/2017/awards_detail) |

<details>
<summary>📖 What each paper contributed</summary>

- **Faster R-CNN** — Introduced Region Proposal Networks (RPN), enabling a fully learnable two-stage object detection pipeline.
- **GAN** — Proposed the generative adversarial framework: two networks competing to generate realistic data.
- **Seq2Seq** — Established the encoder-decoder paradigm for sequence transduction, laying groundwork for Transformers and LLMs.
- **Word2Vec** — Introduced efficient word embedding methods (Skip-gram, Negative Sampling), launching the word embedding era.
- **AlexNet** — Won ImageNet 2012 with deep CNNs, igniting the modern deep learning revolution.
- **Online LDA** — Enabled topic modeling at web scale via stochastic variational inference.
- **HOGWILD!** — Proved that lock-free parallel SGD converges, enabling scalable distributed training.
- **Dual Averaging** — Provided a principled framework for regularized online/stochastic optimization.
- **Tradeoffs of Large Scale Learning** — Analyzed the fundamental trade-off between approximation, estimation, and optimization error.
- **Random Features** — Approximated kernel methods with random Fourier features, making kernel machines scalable.

</details>

---

#### ICML — Test of Time Award

> Full list with Honorable Mentions: [test-of-time/icml.md](test-of-time/icml.md)

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2025 | **Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift** | Sergey Ioffe, Christian Szegedy | ICML 2015 | [🔗](https://icml.cc/virtual/2025/awards_detail) |
| 2024 | **DeCAF: A Deep Convolutional Activation Feature for Generic Visual Recognition** | Jeff Donahue et al. | ICML 2014 | [🔗](https://icml.cc/virtual/2024/awards_detail) |
| 2023 | **Learning Fair Representations** | Rich Zemel, Yu Wu, Kevin Swersky, Toni Pitassi, Cynthia Dwork | ICML 2013 | [🔗](https://icml.cc/virtual/2023/awards_detail) |
| 2020 | **Gaussian Process Optimization in the Bandit Setting: No Regret and Experimental Design** | Niranjan Srinivas, Andreas Krause, Sham Kakade, Matthias Seeger | ICML 2010 | [🔗](https://icml.cc/virtual/2020/awards_detail) |

---

#### ICLR — Test of Time Award

> Established in **2024** (inaugural). Honors papers from 10 years prior.

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2025 | **Adam: A Method for Stochastic Optimization** | Diederik P. Kingma, Jimmy Ba | ICLR 2015 | [🔗](https://blog.iclr.cc/2025/04/14/announcing-the-test-of-time-award-winners-from-iclr-2015/) |
| 2025 *(runner-up)* | **Neural Machine Translation by Jointly Learning to Align and Translate** | Dzmitry Bahdanau, Kyunghyun Cho, Yoshua Bengio | ICLR 2015 | [🔗](https://blog.iclr.cc/2025/04/14/announcing-the-test-of-time-award-winners-from-iclr-2015/) |
| 2024 | **Auto-Encoding Variational Bayes** | Diederik P. Kingma, Max Welling | ICLR 2014 | [🔗](https://blog.iclr.cc/2024/05/07/iclr-2024-test-of-time-award/) |
| 2024 *(runner-up)* | **Intriguing Properties of Neural Networks** | Christian Szegedy et al. | ICLR 2014 | [🔗](https://blog.iclr.cc/2024/05/07/iclr-2024-test-of-time-award/) |

---

#### AAAI — Classic Paper Award

> Established in **1999**. Full historical list: [test-of-time/aaai.md](test-of-time/aaai.md)

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2026 | **Learning Structured Embeddings of Knowledge Bases** | Antoine Bordes, Jason Weston, Ronan Collobert, Yoshua Bengio | AAAI 2011 | [🔗](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |
| 2026 | **Understanding Natural Language Commands for Robotic Navigation and Mobile Manipulation** | Stefanie Tellex et al. | AAAI 2011 | [🔗](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |
| 2023 | **Theta\*: Any-Angle Path Planning on Grids** | Kenny Daniel, Alex Nash, Sven Koenig, Ariel Felner | AAAI 2007 | [🔗](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |
| 2017 | **Monte Carlo Localization: Efficient Position Estimation for Mobile Robots** | Frank Dellaert, Dieter Fox, Wolfram Burgard, Sebastian Thrun | AAAI 1999 | [🔗](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |
| 2000 | **Reverend Bayes on Inference Engines: A Distributed Hierarchical Approach** | Judea Pearl | AAAI 1982 | [🔗](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |

---

### Computer Vision

#### CVPR — Longuet-Higgins Prize

> Established in **2005**. Full historical list: [test-of-time/cvpr.md](test-of-time/cvpr.md)

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2025 | **Going Deeper with Convolutions** | Christian Szegedy et al. | CVPR 2015 | [🔗](https://cvpr.thecvf.com/Conferences/2025/News/TCPAMI) |
| 2025 | **Fully Convolutional Networks for Semantic Segmentation** | Jonathan Long, Evan Shelhamer, Trevor Darrell | CVPR 2015 | [🔗](https://cvpr.thecvf.com/Conferences/2025/News/TCPAMI) |
| 2024 | **Rich Feature Hierarchies for Accurate Object Detection and Semantic Segmentation** | Ross Girshick et al. | CVPR 2014 | [🔗](https://cvpr.thecvf.com/Conferences/2024/News/Awards) |
| 2015 | **Histograms of Oriented Gradients for Human Detection** | Navneet Dalal, Bill Triggs | CVPR 2005 | [🔗](https://cvpr2015.thecvf.com/awards.php) |
| 2011 | **Rapid Object Detection using a Boosted Cascade of Simple Features** | Paul Viola, Michael Jones | CVPR 2001 | [🔗](https://www.thecvf.com/?page_id=534) |
| 2007 | **Normalized Cuts and Image Segmentation** | Jianbo Shi, Jitendra Malik | CVPR 1997 | [🔗](https://www.thecvf.com/?page_id=534) |

> **[→ See all 30+ CVPR winners](test-of-time/cvpr.md)**

---

#### ICCV — Helmholtz Prize

> Established as Helmholtz Prize in **2013** (with a retrospective covering 1987–1999). Full list: [test-of-time/iccv.md](test-of-time/iccv.md)

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2021 | **ORB: An Efficient Alternative to SIFT or SURF** | Ethan Rublee et al. | ICCV 2011 | [🔗](https://iccv2021.thecvf.com/iccv-2021-paper-awards) |
| 2017 | **Video Google: A Text Retrieval Approach to Object Matching in Videos** | Josef Sivic, Andrew Zisserman | ICCV 2003 | [🔗](https://www.thecvf.com/?page_id=537) |
| 2013 | **Snakes: Active Contour Models** | Michael Kass, Andrew Witkin, Demetri Terzopoulos | ICCV 1987 | [🔗](https://www.thecvf.com/?p=84) |
| 2011 | **Object Recognition from Local Scale-Invariant Features** | David Lowe | ICCV 1999 | [🔗](https://www.thecvf.com/?page_id=537) |

> **[→ See all 29+ ICCV winners](test-of-time/iccv.md)**

---

### Natural Language Processing

#### ACL — Test-of-Time Paper Award

> Established in **2020**. Dual track: 25-year award + 10-year award. Full list: [test-of-time/acl.md](test-of-time/acl.md)

| Year | Track | Paper | Authors | Venue | Source |
|------|-------|-------|---------|-------|--------|
| 2025 | 25-yr | **Automatic Labeling of Semantic Roles** | Daniel Gildea, Daniel Jurafsky | ACL 2000 | [🔗](https://www.aclweb.org/portal/content/announcement-2025-acl-test-time-paper-award) |
| 2025 | 10-yr | **Effective Approaches to Attention-based Neural Machine Translation** | Minh-Thang Luong, Hieu Pham, Christopher Manning | EMNLP 2015 | [🔗](https://www.aclweb.org/portal/content/announcement-2025-acl-test-time-paper-award) |
| 2024 | 10-yr | **GloVe: Global Vectors for Word Representation** | Jeffrey Pennington, Richard Socher, Christopher Manning | EMNLP 2014 | [🔗](https://www.aclweb.org/portal/content/announcement-2024-acl-test-time-paper-award) |
| 2020 | 25-yr | **Unsupervised Word Sense Disambiguation Rivaling Supervised Methods** | David Yarowsky | ACL 1995 | [🔗](https://www.aclweb.org/portal/content/announcement-2020-acl-test-time-awards-tot) |

> **[→ See all ACL/NAACL winners](test-of-time/acl.md)**

---

### Information Retrieval & Data Mining

#### KDD — Test of Time Award

> Dual track: Research + Applied Data Science. Full list: [test-of-time/kdd.md](test-of-time/kdd.md)

| Year | Paper | Authors | Venue | Source |
|------|-------|---------|-------|--------|
| 2025 | **Collaborative Deep Learning for Recommender Systems** | Hao Wang, Naiyan Wang, Dit-Yan Yeung | KDD 2015 | [🔗](https://kdd.org/awards/view/2025-sigkdd-test-of-time-award-winners) |
| 2024 | **DeepWalk: Online Learning of Social Representations** | Bryan Perozzi, Rami Al-Rfou, Steven Skiena | KDD 2014 | [🔗](https://kdd.org/awards/view/2024-sigkdd-test-of-time-award-winners) |
| 2023 | **Auto-WEKA: Combined Selection and Hyperparameter Optimization** | Chris Thornton, Frank Hutter, Holger Hoos, Kevin Leyton-Brown | KDD 2013 | [🔗](https://kdd.org/kdd2023/awards/index.html) |

> **[→ See all KDD/SIGIR/WWW winners](test-of-time/kdd.md)**

---

## Award Quick Reference

| Conference | Award Name | Est. | Lookback | Official Page |
|-----------|-----------|------|----------|---------------|
| NeurIPS | Test of Time Award | 2017 | ~10 years | [neurips.cc](https://blog.neurips.cc/) |
| ICML | Test of Time Award | 2014 | 10 years | [icml.cc](https://icml.cc/) |
| ICLR | Test of Time Award | 2024 | 10 years | [blog.iclr.cc](https://blog.iclr.cc/) |
| AAAI | Classic Paper Award | 1999 | ~15 years | [aaai.org](https://aaai.org/about-aaai/aaai-awards/aaai-classic-paper-award/) |
| CVPR | Longuet-Higgins Prize | 2005 | 10 years | [thecvf.com](https://www.thecvf.com/?page_id=534) |
| ICCV | Helmholtz Prize | 2013 | 10 years | [thecvf.com](https://www.thecvf.com/?page_id=537) |
| ACL | Test-of-Time Paper Award | 2020 | 10yr + 25yr | [aclweb.org](https://www.aclweb.org/adminwiki/index.php/ACL_Test-of-Time_Papers_Award_Recipients) |
| NAACL | Test of Time Award | 2018 | ~16 years | [naacl](https://naacl2018.wordpress.com/2018/03/22/test-of-time-award-papers/) |
| SIGIR | Test of Time Award | ~2012 | ~10 years | [sigir.org](https://sigir.org/test-of-time-award-winners/) |
| KDD | Test of Time Award | ~2014 | 10 years | [kdd.org](https://kdd.org/awards/kdd-test-of-time-award) |
| WWW | Seoul Test of Time Award | 2015 | ~15 years | [iw3c2.org](https://archives.iw3c2.org/iw3c2/ToT/) |

---

## Contributing

Contributions are welcome! If you notice a missing award, an error, or want to add a new category:

1. Fork this repository
2. Create a branch (`git checkout -b add-missing-award`)
3. Make your changes and ensure data accuracy with official sources
4. Submit a pull request

Please include **official source links** for any new entries. See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

---

## License

This work is licensed under [CC BY 4.0](LICENSE). You are free to share and adapt this material with attribution.

---

<p align="center">
  <i>"Guanzhi" — See these, and look no further.</i>
</p>
