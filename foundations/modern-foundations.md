# Modern Foundations (1980–2009) — The Statistical Learning Revolution

> Emerging from the AI winter, probabilistic reasoning, neural network revival, statistical NLP, and reinforcement learning redefined AI's methodology.

## Probabilistic Reasoning & Uncertainty

| Year | Paper / Book | Authors | Venue | Significance |
|------|-------------|---------|-------|-------------|
| 1986 | **Fusion, Propagation, and Structuring in Belief Networks** | Judea Pearl | Artificial Intelligence, Vol. 29, pp. 241–288 | Algorithmic foundation of Bayesian networks — introduced the belief propagation algorithm, making probabilistic reasoning computationally tractable |
| 1988 | **Probabilistic Reasoning in Intelligent Systems: Networks of Plausible Inference** | Judea Pearl | San Francisco: Morgan Kaufmann | The "bible" of Bayesian networks — systematized graphical model representation and inference methods. Pearl received the 2011 Turing Award largely for this work |

## Non-Monotonic Reasoning

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|-------------|
| 1980 | **A Logic for Default Reasoning** | Raymond Reiter | Artificial Intelligence, Vol. 13, pp. 81–132 | Default logic — how to reason when information is incomplete? This is the core problem of commonsense reasoning, and Reiter's formalization remains the benchmark |
| 1980 | **Circumscription — A Form of Non-monotonic Reasoning** | John McCarthy | Artificial Intelligence, Vol. 13, pp. 27–39 | Circumscription — used minimization assumptions to address the frame problem, one of McCarthy's last major contributions to AI reasoning theory |

## Neural Network Revival

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|-------------|
| 1982 | **Neural Networks and Physical Systems with Emergent Collective Computational Abilities** | John Hopfield | Proc. National Academy of Sciences, Vol. 79, pp. 2554–2558 | Hopfield networks — brought energy functions from statistical physics into neural networks, reigniting interest in connectionism |
| 1986 | **Learning Representations by Back-Propagating Errors** | David Rumelhart, Geoffrey Hinton, Ronald Williams | Nature, Vol. 323, pp. 533–536 | The canonical statement of backpropagation — though not the first formulation (Werbos 1974), this concise Nature paper established backprop as the standard method for training multi-layer neural networks |
| 1986 | **Parallel Distributed Processing** (PDP, 2 vols.) | James McClelland, David Rumelhart et al. | Cambridge, MA: MIT Press | The connectionist manifesto — two volumes systematically demonstrated the power of distributed representations and parallel processing, directly seeding modern deep learning |
| 1980 | **Neocognitron: A Self-organizing Neural Network Model for a Mechanism of Pattern Recognition Unaffected by Shift in Position** | Kunihiko Fukushima | Biological Cybernetics, Vol. 36, pp. 193–202 | The Neocognitron — introduced hierarchical structure with convolution and pooling layers, the direct ancestor of convolutional neural networks (CNNs) |
| 2006 | **A Fast Learning Algorithm for Deep Belief Nets** | Geoffrey Hinton, Simon Osindero, Yee-Whye Teh | Neural Computation, Vol. 18, No. 7, pp. 1527–1554 | Deep Belief Networks — used layer-wise greedy pretraining to overcome the difficulty of training deep networks, ushering in the "deep learning" era |

## Machine Learning

| Year | Paper / Book | Authors | Venue | Significance |
|------|-------------|---------|-------|-------------|
| 1986 | **Induction of Decision Trees** | J. Ross Quinlan | Machine Learning, Vol. 1, pp. 81–106 | ID3/C4.5 — used information gain to automatically construct decision trees, becoming one of the most widely used algorithms in data mining |
| 1984 | **Classification and Regression Trees** (CART) | Leo Breiman, Jerome Friedman, Richard Olshen, Charles Stone | Pacific Grove, CA: Wadsworth | CART — developed in parallel with Quinlan's work, introduced the Gini index and pruning strategies, influencing ensemble methods like Random Forests |
| 1995 | **Support-Vector Networks** | Corinna Cortes, Vladimir Vapnik | Machine Learning, Vol. 20, No. 3, pp. 273–297 | Support Vector Machines — combined kernel methods with maximum-margin classification, dominating pattern recognition for over a decade before deep learning |
| 1997 | **A Decision-Theoretic Generalization of On-Line Learning and an Application to Boosting** (AdaBoost) | Yoav Freund, Robert Schapire | Journal of Computer and System Sciences, Vol. 55, No. 1, pp. 119–139 | AdaBoost — proved that weak learners can be "boosted" into a strong learner, the foundational work of ensemble learning theory |

## Reinforcement Learning

| Year | Paper / Book | Authors | Venue | Significance |
|------|-------------|---------|-------|-------------|
| 1989 | **Learning from Delayed Rewards** | Christopher Watkins | Ph.D. thesis, Cambridge University | Q-learning — proposed a model-free temporal-difference control algorithm, one of the most fundamental algorithms in reinforcement learning |
| 1995 | **Temporal Difference Learning and TD-GAMMON** | Gerald Tesauro | Communications of the ACM, Vol. 38, No. 3 | TD-Gammon — a backgammon program trained via temporal-difference learning that reached world-champion level, the first proof that RL can surpass human performance in complex games |
| 1998 | **Reinforcement Learning: An Introduction** | Richard Sutton, Andrew Barto | Cambridge, MA: MIT Press | The "bible" of reinforcement learning — systematized MDPs, TD learning, policy gradients, and related core frameworks; still the standard textbook for the field |

## Statistical NLP

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|-------------|
| 1993 | **Building a Large Annotated Corpus of English: The Penn Treebank** | Mitchell Marcus, Beatrice Santorini, Mary Ann Marcinkiewicz | Computational Linguistics, Vol. 19, pp. 313–330 | The Penn Treebank — provided a large-scale gold standard for English syntactic annotation, enabling statistical parsing and ushering in the era of data-driven NLP |
| 2003 | **Latent Dirichlet Allocation** | David Blei, Andrew Ng, Michael Jordan | Journal of Machine Learning Research, Vol. 3, pp. 993–1022 | LDA topic model — used a Bayesian generative model to automatically discover latent topics in document collections, becoming the standard tool for unsupervised text analysis |

## Game AI

| Year | Paper | Authors | Venue | Significance |
|------|-------|---------|-------|-------------|
| 1997 | **Deep Blue** | Murray Campbell et al. | Artificial Intelligence, Vol. 134, pp. 57–83 (2002) | Deep Blue defeats Kasparov — though relying more on brute-force search than "intelligence," this event brought global awareness to AI's potential and remains one of the most iconic moments in AI history |

---

*Papers on this page are primarily drawn from Nilsson's The Quest for AI, Chapters 25–32 and their endnotes. SVM (Cortes & Vapnik 1995) is briefly mentioned in Nilsson (Ch. 29, endnote 65) but is fully included here given its importance in ML history.*
