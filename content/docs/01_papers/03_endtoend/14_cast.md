---
title: Code summarization with hierarchical splitting and reconstruction of ASTs
math: true
url: cast
weight: 4
---

# Code summarization with hierarchical splitting and reconstruction of ASTs (CAST)
Processing directly an AST is an expensive, time-consuming process, while flattening it into sequences leads to a loss of information. The main idea behind CAST is to split an AST into a set of subtrees at a proper granularity and learn the representation of the complete AST by aggregating its subtrees' representations learned using tree-based neural models.

# Pipeline Overview
This model takes as input a code snippet, and it starts by getting the AST representation of that snippet. This AST is built into several hierarchical subtrees, and an RvNN is used to compute the vector representation of each subtree. These vectors are then encoded into one single vectors via another RvNN. The code tokens, on the other hand, are simply encoded using the standard Transformer architecture. For the decoder, the authors used a sequential strategy to use the two representation, where the attention is computed one-by-one for each input encoder.

## Data
The authors used the TL-CodeSum and Funcom datasets.

### AST Splitting
Given a code, we build its AST and visit it in preorder traversal. When a composite structure is encountered (\texttt{if}, \texttt{while}, \texttt{for},...), a placeholder node is inserted.The subtree rooted at this statement is split out to form the next level tree. So, a large AST is decomposed into a set of small subtrees with the composite structures retained. 

There are four kinds of subtrees: \(\texttt{OvT}\), \(\texttt{SigT}\), \(\texttt{StmtsT}\), \(\texttt{BlockT}\). \(\texttt{OvT}\) is the method overview tree, providing the big picture of the method and \(\texttt{SigT}\) gives the method signature information. \(\texttt{StmtsT}\) helps avoid too many scattered simple statements by combining sequential statements. Each terminal node in \(\texttt{OvT}\) creates an subtree \(T\), that is either a \(\texttt{StmtT}\) or a \(\texttt{BlockT}\), which provides detailed semantics of nodes in the overview tree. Subtrees corresponding to nested structures will be further split down, and this process carries on recursively until the tree is fully split.

### AST Encoding
AST encoding is done via a two-phase encoder module. In the first phase, a tree-based Recursive Neural Network (RvNN) followed by a max-pooling layer are applied to encode each subtree. In the second layer, we use another RvNN with different parameters to model the hierarchical relationship among the subtrees.

A subtree \(T_t\) is defined as \((V_t,E_t)\), where \(V_t\) is the node set and \(E_t\) is the edge set. The subtree is encoded as
$$
h_i^{(t)}=\tanh\left(W^Cc_i^{(t)}+\frac{1}{|Ch^t(v_i)|}\sum_{v_j\in Ch^t(v_i)}W^Ah_j^{(t)}\right)
$$
where \(W^C\) and \(W^A\) are learnable weight matrices, and \(h_i^{(t)}\), \(c_i^{(t)}\), \(Ch(v_i)\) are the hidden state, token embedding and child set of the node \(v_i\), respectively. This way, each node in the AST aggregates information from its children nodes.

Then, the hidden states of all nodes are aggregated to a vector \(s_t\), through dimension wise max-pooling operation, which will be used as the embedding for the whole subtree \(T_t\)
$$
s_t=\operatorname{maxpooling}\left(\cup h_i^{(t)}\right)
$$

Once we have the embeddings of all subtrees, we encode the descendant relationships among the subtrees. These relationships are represented in the structure tree \(T\), and so we apply another RvNN model on \(T\)
$$
h_t^{(a)}=\tanh\left(W^Ss_t+\frac{1}{|Ch(v_t)|}\sum_{v_k\in Ch(v_t)}W^Bh_k^{(a)}\right)
$$

## Decoder
For the decoder, the Transformer architecture is used. However, it is necessary to adapt to be able to integrate both the encodings from the AST encoders and the source code token encodings (encoded using a Transformer encoder). The authors used the serial strategy, which is to compute the encoder-decoder attention one by one for each input encoder. In each cross-attention layer, the encoding of ASTs (\(h^{(a)}=(h_1^{(a)},...,h_l^{(a)})\) flattened by preorder traversal) or codes (\(o=(o_1,...,o_n)\)) is queried by the output of the preceding summary self-attention \(s=(s_1,...,s_m)\). After several decoder layers, they add a softmax operator to obtain the generation probability of each summary token.

## Fine-Tuning
This model was used on the downstream task of code summarization.

## References
- Original Paper: ["CAST: Enhancing Code Summarization with Hierarchical Splitting and Reconstruction of Abstract Syntax Trees"](https://arxiv.org/abs/2108.12987)
- TL-CodeSum: ["On the evaluation of neural code summarization"](https://arxiv.org/abs/2107.07112)
- Funcom: [Improved code summarization via a graph neural network"](https://arxiv.org/abs/2004.02843)