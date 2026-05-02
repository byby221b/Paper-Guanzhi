# Deep Learning Era (2010–Present)

> Nilsson's book covers AI history up to 2009. For the period after, selections are drawn from Ilya Sutskever's recommended reading list, major conference Test-of-Time Awards, and universally recognized milestones.

## Vision and Convolutional Networks

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2012 | **ImageNet Classification with Deep Convolutional Neural Networks** (AlexNet) | Alex Krizhevsky, Ilya Sutskever, Geoffrey Hinton | NeurIPS 2012 | AlexNet won ImageNet by a decisive margin — the day that marked the official start of the deep learning revolution. GPU training, ReLU, and Dropout became standard practice |
| 2015 | **Deep Residual Learning for Image Recognition** (ResNet) | Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun | CVPR 2016 (arXiv 2015) | Residual connections — skip connections solved the degradation problem in deep networks, making 100+ layer networks feasible. The last fundamental breakthrough in CNN architecture design |
| 2015 | **Multi-Scale Context Aggregation by Dilated Convolutions** | Fisher Yu, Vladlen Koltun | ICLR 2016 (arXiv 2015) | Dilated (atrous) convolutions — systematically expanded the receptive field without losing resolution or increasing parameters, becoming essential for semantic segmentation and dense prediction |

## Sequence Modeling and Attention

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2014 | **Sequence to Sequence Learning with Neural Networks** | Ilya Sutskever, Oriol Vinyals, Quoc V. Le | NeurIPS 2014 | Seq2Seq — established the encoder-decoder paradigm, becoming the foundational framework for machine translation, summarization, dialogue, and other sequence transduction tasks |
| 2014 | **Neural Machine Translation by Jointly Learning to Align and Translate** | Dzmitry Bahdanau, Kyunghyun Cho, Yoshua Bengio | ICLR 2015 (arXiv 2014) | Attention mechanism — enabled the decoder to "attend" to different positions in the input sequence, fundamentally resolving the long-sequence information bottleneck. The direct precursor to the Transformer |
| 2015 | **Pointer Networks** | Oriol Vinyals, Meire Fortunato, Navdeep Jaitly | NeurIPS 2015 | Pointer Networks — used attention as a pointer to select from the input, enabling neural networks to handle variable-size output dictionaries. Influenced copy mechanisms, summarization, and combinatorial optimization |
| 2017 | **Attention Is All You Need** | Ashish Vaswani et al. | NeurIPS 2017 | Transformer — replaced RNNs/CNNs with pure attention mechanisms, becoming the architectural foundation of GPT, BERT, and virtually all modern large language models |

## Generative Models

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2013 | **Auto-Encoding Variational Bayes** (VAE) | Diederik Kingma, Max Welling | ICLR 2014 (arXiv 2013) | VAE — combined variational inference with deep learning, providing a scalable framework for probabilistic generative modeling |
| 2014 | **Generative Adversarial Nets** | Ian Goodfellow et al. | NeurIPS 2014 | GAN — introduced adversarial training for generative models, creating a new paradigm for image generation, style transfer, and beyond |

## Representation Learning

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2013 | **Distributed Representations of Words and Phrases and their Compositionality** (Word2Vec) | Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg Corrado, Jeff Dean | NeurIPS 2013 | Word2Vec — efficient word vector training methods (Skip-gram + Negative Sampling) that made word embeddings a foundational building block of NLP |

## Neural Computation and Memory

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2014 | **Neural Turing Machines** | Alex Graves, Greg Wayne, Ivo Danihelka | arXiv:1410.5401 | Neural Turing Machines — augmented neural networks with differentiable external memory, pioneering the paradigm of memory-augmented networks capable of learning algorithms like copying, sorting, and recall |

## Graph Neural Networks

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2017 | **Neural Message Passing for Quantum Chemistry** | Justin Gilmer, Samuel S. Schoenholz, Patrick F. Riley, Oriol Vinyals, George E. Dahl | ICML 2017 | Message Passing Neural Networks (MPNN) — unified diverse graph neural network variants under a single message-passing framework, establishing the theoretical foundation for the GNN subfield |

## Speech Recognition

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2015 | **Deep Speech 2: End-to-End Speech Recognition in English and Mandarin** | Dario Amodei et al. | ICML 2016 (arXiv 2015) | Deep Speech 2 — demonstrated that end-to-end deep learning (RNNs + CTC) could match or surpass traditional speech pipelines across languages, marking the paradigm shift from hand-engineered features to learned representations in speech |

## Scaling Laws and Large Models

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|--------------|
| 2018 | **GPipe: Easy Scaling with Micro-Batch Pipeline Parallelism** | Yanping Huang et al. | NeurIPS 2019 (arXiv 2018) | GPipe — introduced micro-batch pipeline parallelism for training extremely large models across accelerators, a key enabler of the scaling paradigm that produced GPT-3, PaLM, and beyond |
| 2020 | **Scaling Laws for Neural Language Models** | Jared Kaplan et al. | arXiv:2001.08361 | Scaling laws — empirically discovered power-law relationships between model performance and parameter count, data size, and compute budget, providing the theoretical basis for the large model paradigm |

---

*Sources for this page: Ilya Sutskever's recommended reading list, NeurIPS/ICML/ICLR Test-of-Time Awards, and universally recognized milestones.*
*The selection criterion remains the same — only papers truly worth "stopping to admire." For example, BERT and GPT-3, while enormously influential, are successful applications of the Transformer paradigm rather than methodological breakthroughs, and are therefore not included for now. Future additions may be warranted as time passes.*
