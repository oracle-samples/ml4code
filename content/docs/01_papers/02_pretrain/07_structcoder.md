---
title: StructCoder
math: true
url: structcoder
weight: 7
---

# StructCoder

StructCoder consists of a Transformer encoder with a strucutre-aware self-attention framework to incorporate source syntax and data flow when given a code input. From the decoder side, StructCoder uses a structure-aware Transformer decoder to model both the target syntax and the data flow. The base architecture for this model is not BERT, but rather the T5 architecture.

## Pipeline Overview

This model takes as input a code snippet, and from the encoder we can get the contextual vector representations, as well as the aggregated representation in the  \([CLS]\) token. From the input, an AST and a DFG representations are computed. The encoder receives the code tokens, the AST leaves and the DFG variables. Additionally, its attention layers are modified to take into account the structure of the code. Namely, code-code interactions receive relative position encodings, leaf-leaf interactions take into account the root-leaf path similarity, variable-variable interactions are only allowed if the variables are connected in the DFG and code-leaf/variable interactions are only allowed if the leaf/variable results from that code token.

## Data

For pre-training, the authors used the CodeSearchNet corpus, specifically a randomly chosen subset of 300K samples belonging to four languages. For fine-tuning, they used the CodeXGLUE benchmark dataset.

## Notation

A code can be a function or a program, represented as a sequence of tokens \(S=(s_1,...,s_{|S|})\). A code has an AST representation as \(\mathcal{T}=(N,N_{leaf},r,p(\cdot),L^{ast})\), where \(N\) is the set of nodes in the AST, \(N_{leaf}=\{l_1,...,l_{|N_{leaf}|}\}\subset N\) is the subset of lead nodes, \(r\in N\) is the root node, \(p:N-r\rightarrow N\) is a mapping such that \(p(n)\) denotes the parent of node \(n\), and \(L^{ast}\in\{0,1\}^{|S|\times|N_{leaf}|}\) is a linking matrix such that \(L^{ast}_{ij}=1\) iff token \(s_i\) is part of leaf \(l_j\). Each node \(n\in N\) has a type denoted by \(n.type\).

A code \(S\) also has a corresponding DFG represented as \(\mathcal{G}=(V,D,L^{dfg})\), where \(V=\{v_1,v_2,...,v_{|V|}\}\) is the set of variables extracted from \(S\), \(D\in\{0,1\}^{|V|\times|V|}\) is the adjacency matrix where \(D_{ij}=1\) iff the value of \(v_i\) is obtained from \(v_j\), and \(L^{dfg}\in\{0,1\}^{|S|\times|V|}\) is a linking matrix such that \(L_{ij}^{dfg}=1\) iff variable \(v_j\) is derived from token \(s_i\).

## Architecture

### Encoder

The input sequence to the encoder is 
$$
[CLS],s_1,...,s_{|S|},[SEP],l_1,...,l_{|N_{leaf}|},v_1,...,v_{|V|}
$$
consisting of code tokens, special tokens \([CLS]\) and \([SEP]\), AST leaves and DFG variables. 

The code tokens and the special tokens are embedded via a look-up table. All DFG variables are encoded with a default embedding (the DFG information is only used in the structure-aware self-attention). The embedding of a leaf \(l\) in an AST is embedded as a function of the path from the root to the leaf \(l\) by
$$
E(l) = \sum_i^{|l|}E_{type}(r_i.type)\odot E_{height}(|l|-i)
$$
where \(r_i\) are the nodes on the path from the root to \(l\), \(E_{type}\) is a type embedding to encode a node's semantics and \(E_{height}\) encodes the order of nodes in the path.

Since the input contains DFG and AST which consist of structural information, the relative encodings used by standard Transformers are insufficient. So, the authors introduced structure-aware self-attention mechanisms. These mechanisms are dependent on what kind of tokens are involved in the attention computation.

#### Code-Code

Attention scores between code tokens are computed in a standard manner, with a lookup embedding \(\phi\) to consider the relative positions. Denoting embedding of \(x\) by \(E_x\) we have
$$
A(s_i,s_j)=E_{s_i}^TW_q^TW_kE_{s_j}+\phi(|i-j|)
$$

#### Leaf-Leaf

To compute attention scores between leaves, we introduce a similarity-based transformation to replace the relative positional embedding. Let \((r_1^i,...,r_{|l_i|}^i)\) be the nodes on the path from root to leaf \(l_i\). Then the similarity between two leaves is defined as
$$
\operatorname{sim}(l_i,l_j)=\log\left[\frac{\left(\sum_k^{\min(|l_i|,|l_j|)}\mathbb{1}(r_k^i=r_k^j)\right)^2}{|l_i||l_j|}\right]=\\\
2\log\left(\sum_k^{\min(|l_i||l_j|)}\mathbb{1}(r_k^i=r_k^j)\right)-\log|l_i|-\log|l_j|
$$

The attention score between two leaves is then
$$
A(l_i,l_j)=E_{l_i}^TW_q^TW_kE_{l_j}+(w_a\operatorname{sim}(l_i,l_j)+w_b)
$$

#### Variable-Variable

The attention score between DFG nodes is simply masked if they are not connected, and computed using the query-key dot product otherwise
$$
A(v_i,v_j)=\left\{
\begin{array}{ll}
    E_{v_i}^TW_q^TW_kE_{v_j}\quad \text{if }D_{ij}=1 \\
    -\infty \quad\quad\quad\quad\quad\text{otherwise}
\end{array}
\right.
$$

\noindent

#### Code-Leaf/Variable

For interaction between code tokens and AST leaves (or DFG variables), we only compute the query-key dot product and do not use any positional information. However, we only let them attend each other if the code token is linked to the leaf (or variable). So the attention becomes
$$
A(s_i,l_j)=
\left\{
\begin{array}{ll}
    E_{s_i}^TW_q^TW_kE_{l_j}\quad\text{if }L_{ij}^{ast}=1 \\
    -\infty\quad\quad\quad\quad\quad\text{ otherwise}
\end{array}
\right.
$$

The \([CLS]\) and \([SEP]\) are treated like code tokens linked to all leaves and variables.

### Decoder

The decoder is the same as in the T5 model, but with some additional layers for the auxiliary tasks of AST path prediction. For AST path prediction, to predict the type of node, a linear layer followed by a softmax transformation are used.

## Pre-Training

### AST Path Prediction

In this task, the decoder is tasked with predicting the type of each ancestor, on each root-leaf path. To predict the type of node \(r_k^i\) (at height \(|l_i|-k\)), we use a linear layer with weights \(W_{ast(|l_i|-k)}\) followed by a softmax layer to predict a probability distribution on the set of node types \(\mathcal{Y}\)
$$
p_{ik}^{ast}=\operatorname{softmax}(W_{ast(|l_i|-k)}h_i)
$$
where \(h_i,i\in\{1,...,|T|\}\) is the hidden state generated by the decoder. Then, the loss function is given by
$$
\mathcal{L}\_{APP}=-\sum_i^{|T|}\sum_k^{|l_i|}\log p_{ik}^{ast}(r_k^i.type)
$$

### Data flow prediction

In this task, the decoder is tasked with predicting the data flow edges in the data flow. The probability \(p_{ij}^{dfg}\) of data flow from \(j\)-th to \(i\)-th position in the target code sequence is computed using an asymmetric transformation since data flow is directed
$$
p\_{ij}^{dfg}=\sigma(h\_i^TU\_{dfg}^TV\_{dfg}h_j + u\_{dfg}^Th\_i + v\_{dfg}^Th\_j + w\_{dfg})
$$
In the original paper, none of the new variables are defined, so I am left to assume that they are learned parameters for the transformation.

Then, the loss function is given by
$$
\mathcal{L}\_{DFP}=-\sum_i^{|T|}\sum_j^{|T|}\left(\mathbb{1}(\text{cond})\log p_{ij}^{dfg}+\mathbb{1}(~\text{cond})\log(1-p_{ij}^{dfg})\right)
$$
s
$$
\text{cond}=(\exists v_i',v_j'\in V \text{ such that } D_{i'j'}=L={ii'}^{dfg}=L_{jj'}^{dfg}=1)
$$

## Fine-Tuning

The model is fine-tuned for the following tasks: code translation and text-to-code generation.

## References

- Original Paper: ["StructCoder: Structure-Aware Transformer for Code Generation"](https://arxiv.org/abs/2206.05239)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search"](https://arxiv.org/abs/1909.09436)
- AdvTest dataset: ["CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation"](https://arxiv.org/abs/2102.04664)