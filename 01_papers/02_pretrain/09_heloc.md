# Hierarchical ConstrastivE Learning of Code (HELoC)
## Data
The model was pre-trained on the Java-Large dataset. For code classification, the authors evaluated HELoC on two datasets: Google Code Jam and Online Judge. For code clone detection, they used the BigCloneBench, Google Code Jam and OJClone (subset of Online Judge) datasets. For code clustering, they used the Online Judge and the Sorting Algorithm datasets.

## Input Representation
The model takes as input the entire AST. The idea is that an AST contains two types of information: information represented by the AST nodes and information represented by the edges between nodes. As such, the authors consider node embeddings and path embeddings.

The information in a node is a string (type, content, position) and it can be directly embedded using a Document Embedding technique. The full node embedding then becomes $X_0^{node}=\{x_{n_1},...,x_{n_N}\}\in\mathbb{R}^{N\times H}$, where $x_{n_i}$ represents the embedding of node $n_i$. Additionally, an adjacency matrix $A\in\mathbb{R}^{N\times N}$ is used to represent the adjacency between nodes.

To encode the paths, first every root-to-terminal path is extracted. Each path is encoded in a similar fashion as the nodes, via Document Embedding, and they produce the full path embeddings $X_0^{path}\in\mathbb{R}^{M\times H}$. 

Finally, the embeddings of all the paths each node belongs to are added to the corresponding node embeddings to produce the final augmented node embeddings $X\_0^{AST}=\\{\tilde{x}_{n_1},...,\tilde{x}\_{n_N}\\}\in\mathbb{R}^{N\times H}$.

## Residual Self-Attention GNN (RSGNN)
GNN-based representations tend to be local and struggle to leverage long-range interactions. The authors used self-attention to prevent this, combining the two into the design of RSGNN, additionally using residual connections to alleviate gradient vanishing.

RSGNN consists of $N_D$ layers, where each layer contains two different sub-layers, one residual self-attention mechanism sub-layer ($RSM_{sub}$) and one graph convolutional sub-layer ($GCN_{sub}$).

### Residual Self-Attention Mechanism Sub-Layer
This layer takes as input the output of the previous RSGNN ($X_{i-1}^{AST}$), and performs graph convolution mappings on it to compute $Q$, $K$, $V$, like
```math
    Q=GCN(X_{i-1}^{AST},\tilde{A})=\sigma(D^{-1}\tilde{A}X_{i-1}^{AST}W_Q)
```
where $\tilde{A}=A+I_N$, $D$ is a diagonal matrix with $D_{ii}=\sum_j\tilde{A}_{ij}$ and $W_Q$ is the learnable weight matrix. $K$ and $V$ are computed similarly.

To prevent vanishing gradients, they also add internal and external residual connections. Internal residual connections add the attention score from the previous layer as an additional input to the attention score of the attention layer
```math
Attn = mathrm{softmax}\left( \frac{QK^T}{\sqrt{d}} + prev \right)
```
which is fed alongside $V$ to a GCN to get the output
```math
    X_{attn}=GCN((Attn,V),\tilde{A})=\sigma\left( D^{-1}V\tilde{A}\left( mathrm{softmax}\left( \frac{QK^T}{\sqrt{d}} + prev \right)\right) \cdot W_l \right)
```
where $W_l$ is a learnable weight matrix.

The external residual connections are introduced to obtain the final output of $RSM_{sub}$, connecting it to the output of the previous layer
```math
RSM_{sub}\left( X_{i-1}^{AST} \right) = X_{i-1}^{AST} + X_{attn}
```

### Graph Convolution Sub-Layer
This layer consists of two GCNs with a ReLU activation between them. The output of the $i$-th RSGNN layer is calculated as follows
```math
    \tilde{X}\_i^{AST}=mathrm{LayerNorm}\left( X_{i-1}^{AST} + RSM_{sub}(X_{i-1}^{AST}) \right)
```

```math
    X_i^{AST}=mathrm{LayerNorm}\left( \tilde{X}\_i^{AST} + GCN_{sub}(\tilde{X}\_i^{AST},\tilde{A}) \right)
```

After $N_D$ layers, we obtain the representation of the AST nodes, which is denoted as $X_{N_D}^{AST}$.

## Pre-Training
### Hierarchical Contrastive Learning

The authors proposed a hierarchical contrastive learning method, that takes the learning of AST hierarchy as the pretext task for contrastive learning. In the context of this task, we first define an anchor node, which can be any AST node. Then, nodes at the same level as the anchor node are treated as positive samples while nodes at different levels are negative samples. Triplets of anchor, positive sample and negative sample are produced. The goal is to get maximize the similarity between node representations at the same level, while minimizing it for nodes at different levels. Additionally, the further away a node is to another node, the more different their representations should be.

To achieve this, two pre-train tasks are used: node level prediction (NEP) and node relationship optimization (NRO). In the first task the goal is to predict the tree level of a given node, and in the second task the goal of the predict whether two nodes are 1) on the same level, 2) in adjacent levels or 3) on non-adjacent levels. The authors assign pseudo-labels to each of the nodes, where the label takes the value of the level of the node (0 for the root, then 1 for its descendants, then 2,...).

Given a nodes representation, its level is computed via a single linear layer, connected to a cross-entropy loss function
```math
\mathcal{L}\_h=\sum\_{i=1}^N\left(-\log\frac{\exp(y\_i^{level})}{\sum_j\exp(y\_j^{level}}\right)
```

For NRO, we simply use the node's representation, and a triplet loss function
```math
\mathcal{L}\_t=\sum_{i=1}^n\left[||\tilde{x}_{n_i}^{anc}-\tilde{x}\_{n_i}^{pos}||^2-||\tilde{x}\_{n_i}^{anc}-\tilde{x}\_{n_i}^{neg}||^2+\Delta l+\alpha\right]\_+
```

Then the full loss function becomes
```math
\mathcal{L}=\frac{1}{\sigma^2}\mathcal{L}\_h+\frac{1}{\tau^2}\mathcal{L}\_t+\log\sigma+\log\tau
```
where $\sigma,\tau$ are learnable scalars, $N$ is the number of nodes in the AST, $\Delta l$ denotes the difference in levels between the anchor node and the negative node and $\alpha$ is a margin that is enforced between positive and negative pairs.

### Implementation Detaisl
For pre-training, the authors used the Java-large dataset.They limited the ASTs to a max depth of 30, the max path number per AST to 200, and the max node number to 1000. This values were decided based on statistical information from the dataset.

## Downstream Tasks
The downstream tasks considered were code classification, code clone detection and code clustering.

### Code Classification

The goal of code classification is to, given a code snippet $c$, determine which class it belongs to, from a fix number $N$ of classes. To do this, the authors attached a fully-connected layer to the code representation, and used cross-entropy loss to fine-tune the model.

### Code Clone Detection
For code clone detection, they take the code representations, and compute their cosine similarity. The loss function for fine-tuning is then
```math
\mathcal{L}\_{clone}=\left(y_i^{clo}-\frac{r_1\cdot r_2}{|r_1|\cdot|r_2|}\right)^2
```

For inference, and given that the cosine similarity takes values in the range $[-1,1]$, the snippets are considered to be clones if the value is positive, and not clone otherwise.

### Code Clustering

Code clustering is an unsupervised task, where given a corpus of code snippets and a number of clusters, the goal is to cluster the snippets into the number of clusters, while maximizing the intra-cluster similarity and minimizing the inter-cluster similarity. This can be done via K-means using a Euclidean distance-based similarity measure.

## References
- Original Paper: ["HELoC: hierarchical contrastive learning of source code representation"](https://arxiv.org/abs/2203.14285)
- [Java-large dataset](https://s3.amazonaws.com/code2seq/datasets/java-large.tar.gz)
- [Google Code Jam](https://github.com/Jur1cek/gcj-dataset)
- [Online Judge - Site Down](https://sites.google.com/site/treebasedcnn/)
- [BigCloneBench: "Towards a Big Data Curated Benchmark of Inter-project Code Clones"](https://ieeexplore.ieee.org/document/6976121)
- [Sorting Algorithms: "Bilateral dependency neural networks for cross-language algorithm classificationalgorithm classification"](https://github.com/bdqnghi/bi-tbcnn)