# TreeBERT

TreeBERT is a pre-trained generative model for programming language-tasks, that expands on CodeBERT or CuBERT by harnessing the structure of Abstract Syntax Trees (ASTs). To incorporate the tree structure into the pre-training, the authors developed two tasks: tree masked language modelling (TMLM) and node order prediction (NOP). TMLM helps the model understand the AST and infer the missing semantics of the AST, and NOP teaches TreeBERT the order constraint of nodes in AST.

## Pipeline Overview

The model takes as input a snippet of code. This code snippet is turned into two semantically similar representations: a sequence of tokens and an AST.

The sequence of tokens is of the form $C=\{[LT],x_1,x_2,...,x_M,[CLS]\}$. This sequence of tokens is used as input for the decoder part of the model.

The AST is represented as a set of paths $A=\{p_1,p_2,...,p_N\}$. These paths are all the paths from the root node to the terminal nodes (leaves). So $N$ is at the same order of magnitude as $M$ (number of tokens), which prevents path explosion. Each path is represented as a concatenated sequence of nodes, and each node is represented as a vector. The intermediate nodes have a fixed number of values they can take, and so they are encoded as vector embeddings. The terminal codes are embedded via bytes-pair-encoding, representing each token as a concatenation of frequent subtokens. Additionally, node position embeddings are added to the node embeddings.

The AST representation is sent through the transformer encoder to encode the AST into a single vector. Then this vector is used by attention mechanisms to attend to the code tokens on the decoder side. The output of the decoder is a sequence of NL tokens.

## Data

For pre-training, they used the Python and Java corpus published by CuBERT. The Python dataset contains 7.2 million files with 2 billion tokens, which were turned into ASTs with 500 million paths and 7 billion nodes. The Java dataset contains 14.1 million files with 4.5 billion tokens,  which were turned into ASTs with 1.1 billion paths and 16.5 billion nodes.

For fine-tuning, they considered several datasets. For code summarization they used the ETH Py150, and the Java-small, Java-med and Java-large datasets. For code documentation they used the DeepCom dataset. For generalization tests they used the CodeNN's C\# dataset.

## Architecture

### Input Representation

In the pre-training phase, the input is a set of constituent paths in the corresponding AST given a code snippet. The node embeddings (as well as node position embeddings) are concatenated to represent the path. On the decoder side, the input is the sequence of tokens obtained from the code snippet after tokenization.

So, this model uses both the AST and the actual code snippet. The authors chose to use both because when converting to AST some semantic information is hidden. So, if the decoder were designed to predict ASTs, this information would be ignored. For this reason, the decoder is designed to predict the code snippet to encourage the model to learn this semantic information, adding to its generalizability.

### AST Representation 

Each code snippet is turned into an AST. AST nodes are divided into two categories: AST type nodes $v$ and AST value nodes $x$. Value nodes are almost always terminal nodes. The AST is thus represented as the set of paths from the root node to the terminal nodes, $\mathbf{A}=\{\mathbf{p_1},\mathbf{p_2},...,\mathbf{p_N}\}$, where $N$ is the number of paths in the tree.

### Code Representation

The snippet is split into a sequence of tokens, and $[LT]$ and $[CLS]$ tokens are added to the beginning and the end of the sequence, respectively. So we get $\mathbf{C}=\mathbf{[LT]},\mathbf{x_1},...,\mathbf{x_M},\mathbf{[CLS]}$, where $M$ is the length of the sequence. $\mathbf{C}$ is used as the input for the decoder. The $[LT]$ token has a dual purpose: it serves as a start-of-sentence token, but it also represents the target programming language (e.g. $[PLT]$ for Python, $[JLT]$ for Java). As the language-specific details are hidden when the snippet is converted to AST, this allows the model to learn the differences between different programming languages.

### Path Representation

Each path is a sequence of nodes, $p_i=v_1^iv_2^i...v_{L-1}^ix_t^i$, where $L$ is the length of the path. The vector of nodes is then concatenated to represent the path. There is no ordering relationship within the set of paths, so the model does not need to add position encoding to the path vectors. However, position information is added to the nodes in the tree via node position embeddings when forming the node representations.

### Token Representation

To prevent out-of-vocabulary problems, the authors used byte-pair-encoding (BPE) to learn the most frequent subtokens from value nodes in the AST and slice the tokens. Then, the token is represented by the vector sum of all the subtokens that constitute it.

### Node Position Embedding

Adding node position embeddings to the node embedding on the encoder side allows the model to get information about the relative position of the nodes in the AST. For that, define a node's position embedding to be a linear combination of its parent node position embedding and its corresponding level embedding. Namely, if there is a node on level $j$ whose position embedding is $W^{parent}$ and has $c$ child nodes, then the position embedding of its $i$-th child node is
```math
E_i^{position}=\frac{c-i+1}{c+1}W^{parent}+\frac{i}{c+1}W^{level}_{j+1}, \quad c \geqslant 1\text{ and } 1 \leqslant i \leqslant c
```
where $W^{parent}$ and $W^{level}$ are learnable weight matrices.

## Pre-training

The model was pre-trained with two tasks, tree masked language modelling and node order prediction. The data used for pre-training was the dataset published by CuBERT, a corpus of Java and Python code. In TMLM, each path randomly masked 15\% of the nodes, and in NOP, each AST had a 50\% chance of exchanging a pair of nodes.

### Tree Masked Language Modelling

This pre-training tasks requires two components, one on the encoder side and one on the decoder side.

From the encoder side, we start by sampling the nodes in path $p_i$ according to the distribution
```math
    q_n^i=\frac{\exp^{(l-L)}}{\sum_{j=1}^L\exp^(j-L)}
```
which gives a higher probability to the nodes closer to the terminal. This is because, the closer a node is to the root, the more frequent it is in the paths, and because terminal nodes usually refer to user-defined values, which forces the model to learn their representation. Then we select the nodes to mask using $m_i^A=mathrm{TOPK}(p_i, k, \{q_n^i\})$ and replace these nodes with the $[MASK]$ token.

From the decoder side, the input $C^{masked}$ of the decoder is obtained by masking the tokens in the code snippet given by $m^C=\{x|x\in C\cap x\notin m^A\}$, where $m^A=m_1^A\cup m_2^A\cup...\cup m_N^A$. This way, we keep the tokens corresponding to the value nodes in $m^A$ and hide all the others in the snippet, which forces the decoder to rely on the feature representation of the AST instead of the previous tokens in the code snippet.

With this task, the objective function of the TMLM is given by
```math
\mathcal{L}_{TMLM}=-\frac{1}{M}\sum_{x \in C} \log \prod_{t=1}^M p(x_t | x_{< t},A^{masked})
```

where $M$ is the length of the code snippet and $x$ is the tokens in the code snippet representation.

### Node Order Prediction

There are some implicit constraints in the node ordering of an AST. For example, an 'if' node must always be followed by 'body' node. To capture this structural information, the authors randomly exchanged the positions of some nodes in the path, and then the model's task was to predict whether the order of the nodes in the AST was correct or not. The model's output is obtained by compressing the $[CLS]$ vector to 1 dimension via a fully connected layer, and then the probability $p^{NOP}(\cdot)$ of the existence of out-of-order nodes in the path is given by a sigmoid function. So, the objective function is

```math
\mathcal{L}_{NOP}(\theta)=-\sum_i^P(y_i^{NOP}\log p^{NOP}(i) + (1-y_i^{NOP})\log(1-p^{NOP}(i)))
```
where $P$ is the number of paths considered.

Finally, the authors use a hyperparameter $\alpha$ to weight the two tasks and the final loss function becomes
```math
\mathcal{L}=\alpha\mathcal{L}_{TMLM}+(1-\alpha)\mathcal{L}_{NOP}
```

## References

- Original Paper: ["TreeBERT: A Tree-Based Pre-Trained Model for Programming Language"](https://arxiv.org/abs/2105.12485)
- [ETH Py150 dataset](https://www.sri.inf.ethz.ch/py150)
- [Java-small dataset](https://s3.amazonaws.com/code2seq/datasets/java-small.tar.gz)
- [Java-med dataset](https://s3.amazonaws.com/code2seq/datasets/java-med.tar.gz)
- [Java-large dataset](https://s3.amazonaws.com/code2seq/datasets/java-large.tar.gz)
- [DeepCom dataset](https://github.com/xing-hu/DeepCom/blob/master/data.7z)
- [CodeNN's C\# dataset](https://github.com/sriniiyer/codenn/tree/master/data/stackoverflow/csharp)