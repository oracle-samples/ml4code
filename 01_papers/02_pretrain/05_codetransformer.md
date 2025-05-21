# CodeTransformer

CodeTransformer is a pre-trained model design to jointly learn on Context (code) and Structure (AST) of source code. This model uses only language-agnostic features (i.e. features and code that can be extracted directly from the AST).

## Pipeline Overview
This model takes as input a code snippet. It start by converting it into an AST and computing a number of pairwise relations between nodes. These are used as additional positional encodings in the attention mechanisms, to inject the tree structure into the model. Then the process is identical to the standard transformer, and in the end we get the contextual vector representations, as well as the representation of the CLS.

## Data

For pre-training, the authors used the Java-medium and Java-large datasets. For fine-tuning, the authors used the CodeSearchNet Challenge and the Java-small datasets.

## Relative Attention Mechanisms

The standard attention score (before the $mathrm{softmax}$ operator) is given by
```math
A_{ij} = Q_i^TK_j=E_i^TW_q^TW_kE_j
```
where $E_i,E_j\in\mathbb{R}^d$ are the $d$-dimensional embeddings of tokens $i$ and $j$, and $W_q,W_k\in\mathbb{R}^{d_k\times d}$ are the query and key projection matrices, respectively. Injecting positional encodings into the attention mechanisms modifies the equation above to
```math
A_{ij}=(E_i+\phi(p_i))^TW_q^TW_k(E_j+\phi(p_j))
```
which can be factorized into
```math
A_{ij}=E_i^TW_q^TW_kE_j+E_i^TW_q^TW_k\phi(p_j)+\phi(p_i)^TW_q^TW_kE_j+\phi(p_i)^TW_q^TW_k\phi(p_j)
```

In this model, the goal is to inject AST positional encodings into the attention layer. However, doing that it no longer makes sense to consider absolute positional encodings, as the sense of absolute positions doesn't make sense in an AST. So the authors use relative positional encodings. Under this formalism, the attention becomes
```math
A_{ij}^{rel}=E_i^TW_q^TW_kE_j+E_i^TW_q^TW_r\phi(r_{i\rightarrow j})+u^TW_kE_j+v^TW_r\phi(r_{i\rightarrow j})
```
where $r_{i\rightarrow j}$ is the relative distance from token $i$ to token $j$ in the sequence, $u,v\in\mathbb{R}^{d_k}$ are learnable bias vectors and $W_r$ is a key projection matrix for the relative distances. Now, integrating the distance relations from the AST into the attention layer is simply a matter of using one key projection matrix $W_r^s$ per relation $s$, and sum their contributions in the attention score.

The AST pairwise relations between nodes computed are
- shortest path length: number of hops required to reach node $i$ from node $j$
- ancestor distance: distance in vertical hops between two nodes, positive for descending and negative for ascending
- sibling distance: difference between index values of siblings
- personalized page rank

## Data Preprocessing
### Code Snippet Processing
Tokenize code snippets. Remove comments. Remove empty lines. Hard coded strings and numbers are replace with $[MASK_STRING]$ and $[MASK_NUMBER]$. Indentation is detected and white space characters at the beginning of a line are replaced with a single $[INDENT]$ or $[DEDENT]$ when the indentation changes. Tokens are split into subtokens.  Any remaining tokens that only consist of white spaces are removed. Any code snippets the tokenizer cannot parse are discarded. 

### AST Generation

Clean code snippets are used to generate language-specific ASTs. Snippets that lead to parse errors are discarded. Compute a mapping between tokens and nodes in the AST.

### Calculation of Distance Matrices

Tokens are vocabularized (any token occurring less than 100 times in the training corpus is replaced by an $\langle\texttt{unk}\rangle$ token. Multiple pair-wise relations between nodes in the AST are calculated. Distance values are binned into 32 bins using area-based exponential binning. Punctuation tokens are removed. Snippets longer than $\textit{MAX_NUM_TOKENS}$ are removed.


### References
- Original Paper: ["Language-Agnostic Representation Learning of Source Code from Structure and Context"](https://arxiv.org/abs/2103.11318)
- [Java-small dataset](https://s3.amazonaws.com/code2seq/datasets/java-small.tar.gz)
- [Java-med dataset](https://s3.amazonaws.com/code2seq/datasets/java-med.tar.gz)
- [Java-large dataset](\url{https://s3.amazonaws.com/code2seq/datasets/java-large.tar.gz)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search](https://arxiv.org/abs/1909.09436)