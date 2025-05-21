# Code Completion by modeling flattened ASTs as Graphs (CCAG)

## Input Representation

The code is converted into its AST form, which is then flattened by pre-order depth-first traversal. This flattened AST sequence is then represented as a graph. For that, duplicated nodes are merged into one node in the graph, and each edge in the graph indicates that the two linked nodes are adjacent in the sequence. These edges are undirected, and their weight is the frequency of occurrence of the edge in the sequence. To further retain information from the AST, the authors added parent-child edges between nodes. However, these edges are directed and unweighted. Finally, the authors add positional encodings to each graph node, which are defined as the distance between the last occurrence of each node and the right-most node in the sequence.

Each node $n_i$ has a value, a type and a position, which are embedded to $v_i,t_i\in\mathbb{R}^d$ and $p_i\in\mathbb{R}^{2d}$, respectively. Then, the representation of node $n_i$ is given by
```math
    h\_i = mathrm{ReLU}(W^{(p)}([t_i||v_i]+p\_i)+b^{(p)})
```
where $||$ is the concatenation operator, $W^{(p)}$ is a learnable weight matrix and $b^{(p)}$ is a learnable bias vector.

## AST Graph Attention Block (ASTGab)

An ASTGab is based on the idea of GNNs, and it consists of three different layers which extract node features at different levels. Additionally, this model employs residual connections between ASTGabs to make training easier.

### Neighbour Graph Attention Layer (NGAT)

The NGAT extracts features from the first-order neighbourhood along the edges in the AST graph, as strong dependencies typically exist between first-order neighbours. In order to do this, the authors perform self-attention on every pair of first-order neighbours in the graph, computing the first-order neighbour attention coefficient
```math
    e^{(n)}\_{i,j} = mathrm{Att}(W^{(n)}h\_i, W^{(n)}h\_j,w\_{i,j})
```
where $W^{(n)}$ is a learnable weight matrix, and $w_{i,j}$ is the weight of the edge between $i$ and $j$.

Then, the first-order neighbour attention coefficients are normalized by softmax to $\alpha_{i,j}^{(n)}$. Finally, the features of each node are updated as
```math
    h\_i^{(n)}=mathrm{ReLU}(\sum\_{j\in\mathcal{N}\_i}\alpha\_{i,j}^{(n)}W^{(a)}h_j)
```
where $\mathcal{N}_i$ is the set of first-order neighbours of node $i$. The authors further use multi-head self-attention, which implies using multiple independent heads to compute the next iteration of the node feature, and then averaging the results.

### Global Self-Attention Layer (GSAT)

The second component, GSAT, captures the importance of each node to other nodes in the graph. Unlike NGAT, this layer brings a global view of the AST graph. Since the AST graph contains only unique nodes, it is much smaller than the original AST, and as such the entire AST graph can be fed to the GSAT. Then this layer uses standard self-attention mechanisms to compute the global representation $h_i^{(g)}$ of each node, from the local representations computed in the NGAT.

### Parent-Child Attention Layer (PCAT)

The final layer captures the structural information from parent-child edges, and refines the node features from GSAT.  For that, the authors adopt a two-layer attention mechanism
```math
    p\_i = mathrm{ReLU}\left( W¥_1^{(p1)}h\_i^{(g)}+\frac{1}{|\mathcal{P}\_i|}W_2^{(p1)}\sum_{j\in\mathcal{P}\_i}h_j^{(g)} +b^{(p1)} \right)
```
```math
    h\_i^{(p)}=mathrm{ReLU}(W^{(p2)}p_i+b^{(p2)})
```
where $\mathcal{P}_i$ is the set of parent nodes of node $i$, $W$ are the learnable weight matrices and $b$ are the learnable bias vectors.

## Training

This model was trained for next AST node prediction. In order to do that, the right-most node in the sequence is considered, and attention mechanisms are used to incorporate the relevance of each other node to this node. Then the final $s_j$ global representation is obtained by summing the representations of all nodes with the weights computed by the attention mechanisms. The final representation is projected via a linear layer and a softmax into a probability distribution over the vocabulary space to predict the next node. This prediction is composed of two items, the next node value and the next node type. Cross-entropy loss is used to quantify the quality of the prediction.

The final global loss is given by
```math
    \mathcal{L} = \frac{1}{\sigma^2}\mathcal{L}_v + \frac{1}{\tau^2}\mathcal{L}_t + \log\sigma + \log\tau
```

The data used for training consists of the ETH Py150 and ETH JS150 datasets.

## References

- Original Paper: ["Code Completion by Modeling Flattened Abstract Syntax Trees as Graphs"](https://arxiv.org/abs/2103.09499)
