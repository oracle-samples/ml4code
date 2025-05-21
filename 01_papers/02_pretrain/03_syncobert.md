# SynCoBERT

SynCoBERT is a pre-trained encoder model that uses ASTs for better understanding of programming language. It introduces two tasks, Identifier Prediction (IP) and AST Edge Prediction (TEP). To exploit complementary information in equivalent modalities (code, comments, AST), the authors propose a multi-modal contrastive learning strategy to use this mutual information.

## Pipeline Overview

SynCoBERT takes as input a natural language comment $w$, the corresponding source code $c$, and its AST sequence $a$. The AST is built from the source code using tree-sitter, and then it is converted into a token sequence via depth-first traversal. The multi-modal representations are concatenated into a single vector $x=\{[CLS],w,[SEP],c,[SEP],a,[SEP]\}$ and sent through the model. Like in CodeBERT, the output is the contextual vector representation of each token, as well as the representation of the $[CLS]$ token.

## Data

For pre-training, the model used the CodeSearchNet Challenge dataset, which contains 2.1 million bimodal datapoints and 6.4 unimodal codes across six programming languages. For fine-tuning they consider several datasets. For natural language code search, they use the AdvTest dataset (Python), as well as the CodeSearch dataset (Ruby, JavaScript, Go, Python, Java, PHP). For code clone detection, they use the BigCloneBench dataset (Java). For code defect detection, they use the Defects4J dataset (C). For program translation, they use the CodeTrans dataset (translations of  C\# and Java).

## Architecture

SynCoBERT is built on a multi-layer Transformer similar to BERT. This model uses 12 layers with 768 hidden sizes and 12 attention heads to encode the input vectors into contextual representations.

## Input Representation

The AST of the code is taken to be part of the input, and it is  flattened into an AST token sequence via depth-first traversal. Then, given a natural language comment $w=\{w_1,w_2,...,w_{|w|}\}$, the corresponding source code $c=\{c_1,c_2,...,c_{|c|}\}$, and the AST sequence $a=\{a_1,a_2,...,a_{|a|}\}$, SynCoBERT takes the concatenation of multiple modalities as input
```math
    x=\{[CLS],w,[SEP],c,[SEP],a,[SEP]\}
```

## Pre-Training

### Multi-Modal Masked Language Modelling (MMLM)

Given a datapoint of NL-PL-AST triplet $\{w,c,a\}$ as input, we randomly select 15\% of the tokens from the concatenated input. Like in the original BERT, 80\% of these tokens are replace with $[MASK]$, 10\% with random tokens and 10\% are left unchanged. Then, the MMLM objective is a cross-entropy loss on predicting the masked tokens as:
```math
    \mathcal{L}_{MMLM}=-\sum_i^M\log p^{MMLM}(x_i|w^m,c^m,a^m)
```
where $M=w^m\cup c^m\cup a^m$ is the set of masked tokens for NL, PL and AST, $V$ represents the vocabulary size.

### Identifier Prediction (IP)

The identifier plays an important role in source codes. However, as it can be replaced with any other string without affecting the logic of the source code, it is prohibitive to exhaustively predict a large number of code token types in source code. So, the authors divide the code token types into $\textit{identifier}$ and $\textit{non-identifier}$. As opposed to MMLM, where we only consider 15\% of the tokens, on this pre-training task the objective is applied to all code tokens. For each token in the source code, a label 1 is given if it is an identifier and 0 if it is not. So, we get the IP loss function as a binary classification loss defined as
```math
    \mathcal{L}\_{IP}=-\sum_{i\in c}\left[y_i^{IP}\log p^{IP}(i) + (1-y_i^{IP})\log(1-p^{IP}(i))\right]
```

### AST Edge Prediction (TEP)

Flattening an AST may lead to loss of some structural information. TEP encodes the tree structure information into the model to alleviate this effect.  This is done by masking edges in the AST and asking the model to predict them. So, the loss function is defined as
```math
    \mathcal{L}\_{TEP}=-\sum_{(i,j)\in N_a}\left[y_{(i,j)}^{TEP}\log p^{TEP}(i,j)+(1-y_{(i,j)}^{TEP})\log(1-p^{TEP}(i,j))\right]
```
where $N_a$ represents the set of all AST node pairs, and $p^{TEP}(i,j)$ represents the probability of there being an edge between the $i$-th and the $j$-th nodes. It is computed by taking the dot product between the representation of these nodes and normalized to range $[0,1]$ via a sigmoid function.

### Multi-Modal Contrastive Learning (MCL)

MCL surfaces as an attempt to counter-act the representation bias introduced by token imbalance. For this purpose, the authors train SynCoBERT with paired data and unpaired data. Paired data refers to PL with associated NL comments, while unpaired data refers to standalone PL.

To construct positive samples for paired data, we pair NL with their corresponding code and AST, or by switching the order of AST and PL in the input (i.e. $\{w,c,a\}$ becomes $\{w,a,c\}$). For unpaired data, the positive pairs the scheme is the same as the last described (switching the order), but without considering the NL part.

To construct negative samples, the authors use in-batch and cross-batch samplsing during training. For a batch of training data $b_1=[x_1,...,x_N]$, we first get another positive data batch $b_2=[x_1^+,....,x_N^+]$ with size $N$ using the schemes described before for positive matches. Then, for $\{x_i\}$, the in-batch and cross-batch negative samples are $\{x_j\},\forall j\neq i$. So, we can get a set of $2N-2$ samples for each $x_i$.

The objective of contrastive learning is to maximize the representation similarity between positive samples, while minimizing the representation similarity for negative pairs. So, the loss function for a positive pair $(x_i, x_i^+)$ with representations $(v_i, v_i^+)$ is given by
```math
    l(x_i,x_i^+)=-\log\frac{\exp(v_i\cdot v_i^+)}{\exp(v_i\cdot v_i^+)+\sum_{k=1}^{2N-2}\exp(v_i\cdot v_k^-)}
```

Finally, the overall MCL loss is defined as
```math
    \mathcal{L}_{MCL}=\sum_i^N\left[l(x_i,x_i^+)+l(x_i^+,x_i)\right]
```

So, in the end, the full loss function for SynCoBERT is the sum of all the previous losses (with $L_2$ regularization)
```math
    \mathcal{L}=\mathcal{L}_{MMLM}+\mathcal{L}_{IP}+\mathcal{L}_{TEP}+\mathcal{L}_{MCL}+\lambda ||\Theta||^2
```

## Fine-Tuning

SynCoBERT was fine-tuned for several downstream tasks, namely natural language code search, code clone detection, code defect detection, and program translation. 

## References

- Original Paper: ["SynCoBERT: Syntax-Guided Multi-Modal Contrastive Pre-Training for Code Representation"](https://arxiv.org/abs/2108.04556)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search"](https://arxiv.org/abs/1909.09436)
- AdvTest dataset: ["CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation"](https://arxiv.org/abs/2102.04664)
- BigCloneBench: ["Towards a Big Data Curated Benchmark of Inter-project Code Clones"](https://ieeexplore.ieee.org/document/6976121)
- Defects4J: ["Devign: Effective Vulnerability Identification by Learning Comprehensive Program Semantics via Graph Neural Networks"]({https://arxiv.org/abs/1909.03496)
- CodeTrans: ["Tree-to-tree Neural Networks for Program Translation"](https://arxiv.org/abs/1802.03691)