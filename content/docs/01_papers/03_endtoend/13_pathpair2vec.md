---
title: PathPair2Vec
math: true
url: pathpair2vec
weight: 3
---

# PathPair2Vec

## Pipeline Overview

The model takes as input a code snippet. This code snippet is converted into an AST, from which are extracted path-pairs. These are limited, by span, length and number. Each path is represented as a tuple in the form \(\langle t_s, p_s, t_e, p_e\rangle\). The terminal nodes are converted into a sequence of embedded subtokens, which are aggregated via a BiLSTM. The paths are converted into a sequence of embedded nodes, which are also aggregated via a BiLSTM. The set of 4 vectors is merged into one single vector by a fully connected layer. This procedure is done for all the extracted paths, and finally these are merged into one single vector representing the whole code snippet by attention mechanisms.

## Data

To train the model, the authors used the software defect dataset in the PROMISE repository.

## Method Description

### Path Pair Extraction

Code2vec considered paths to be a sequence of nodes connecting two terminals. In this method, we call that a long path. A short path is defined as a triplet \(\langle t_s, p_s, n_s\rangle\) where \(t_s\) is a terminal node, \(n_s\) is an internal node with at least two children and \(p_s\) is the node sequence connecting them. If there are two short paths sharing the same internal node, then \(\langle t_s,p_s,n_{top}\rangle\) and \(\langle t_e,p_e,n_{top}\rangle\) are called a path pair. The authors found that \(n_{top}\) has little contribution to the representation of the path pair, and so they use the alternative representation \(\langle t_s, p_s, t_e, p_e\rangle\). The number of path pairs in an AST can grow factorially. So, the search space is reduced by span and length. Span is defined as the distance between siblings in the AST, and length is defined as the number of nodes in the internal node sequence. Limiting paths by length and span allows to significantly reduce the number of paths without compromising semantic information.
\subsubsection{Encoding Nodes}
To encode the terminal nodes, we use subtoken splitting. Additionally, the type name of the node also contains information that can be better extracted from subtokens, so these nodes are processed in a similar manner. Each of the subtokens can be embedded into a representation vector via a learned table, and this leaves us with two sequences of vectors. To join all of it into one single vector, we harness BiLSTMs, as
$$
\left\{
\begin{array}{ll}
    \operatorname{embed}(t_s)=[\operatorname{BiLSTM}\_{type}(\operatorname{typeof}(t_s)); \operatorname{BiLSTM}\_{name}(\operatorname{nameof}(t_s))] \\
    \operatorname{embed}(t_e)=[\operatorname{BiLSTM}\_{type}(\operatorname{typeof}(t_e)); \operatorname{BiLSTM}\_{name}(\operatorname{nameof}(t_e))]
\end{array}
\right.
$$
where \(\operatorname{typeof}\) and \(\operatorname{nameof}\) obtain the type and name subtoken sequence of the terminal node, respectively, and \(\operatorname{BiLSTM}\_{type}\) and \(\operatorname{BiLSTM}\_{name}\) represent the encoding results for the type and name Bi-LSTM.

In the code2vec model, the internal path \(p\) is the logical control process. However, this path is actually a composition of two paths, the path from terminal \(t_s\) up to node \(n_{top}\), and the path from node \(n_{top}\) down to terminal \(t_e\). Each path independently describes the logical operations for the two terminal nodes. The PathPair2Vec model builds an embedded vector for each of the subpaths, using as well an embedded vector table to compute the sequence of vector representations, and a BiLSTM to get the final vector embedding.

### Attention-Based Vector Generation

Via the procedure described above, we get a sequence of embedded vectors \(\langle \operatorname{embed}(t_s), \operatorname{embed}(p_s), \operatorname{embed}(t_e), \operatorname{embed}(p_e)\rangle\). These are then merged into a single vector representation by a fully connected layer
$$
\operatorname{embed}(\langle t_s, p_s, t_e, p_e\rangle)=\tanh(W[\operatorname{embed}(t_s);\operatorname{embed}(p_s);\operatorname{embed}(t_e);\operatorname{embed}(p_e)]+b)
$$
where \(W\) is the weight of the fully connected layer and \(b\) is the bias, both learnable.

For a given source code, we get many such path embeddings. These are merged into one single vector by attention mechanisms. Using a learned attention vector \(a\), the weight of the path embedding \(c_i\) for the final vector is
$$
\alpha_i = \frac{\exp(c_i^Ta)}{\sum_j\exp(c_j^Ta}
$$
which means the final vector is given by
$$
C=\sum_i^n\alpha_ic_i
$$

This vector can then be used for downstream tasks like, for example, classification with a softmax layer.

## Training

Like code2vec, there is no distinction between pre-training and fine-tuning. Instead, this model needs to be retrained for each downstream task. The authors tested the model on software defect prediction, which is a classification problem.

## References

- Original Paper: ["PathPair2Vec: An AST path pair-based code representation method for defect prediction"](https://www.researchgate.net/publication/341724634_PathPair2Vec_An_AST_path_pair-based_code_representation_method_for_defect_prediction)
- [PROMISE dataset](http://openscience.us/repo/defect)