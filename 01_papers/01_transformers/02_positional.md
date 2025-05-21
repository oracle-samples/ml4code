# Relational Transformer

The relational transformer is an attempt of introducing structural information to the transformer, while retaining its computational complexity and preserving its original machinery.

## Model Input

The input is a directed, attributed graph $G=(V,E)$, where $V$ is an unordered set of node vectors $n_i\in\mathbb{R}^{d_n}$ and $E$ is a set of edge vectors $e_{ij}\in\mathbb{R}^{d_e}$, pointing from node $j$ to node $i$. 

## Relational Attention

In addition to accepting node vectors as entity features, this model also accepts edge vectors representing relation features. It operates over a fully connected graph, unconstrained by any input adjacency matrix. 

Standard transformers project QKV vectors from each node vector, and compute a dot-product between each pair of vectors $q_i$ and $k_j$, which determines the degree to which node $i$ attends to node $j$. Relational attention conditions the QKV vectors on the directed edge $e_{ij}$ between the nodes, by concatenating the edge vector to each node vector prior to the linear transformation, i.e.
```math
    \left\{
    \begin{array}{ll}
        q\_{ij}=[n_i;e\_{ij}]W^Q \\
        k\_{ij}=[n_j;e\_{ij}]W^K \\
        v\_{ij}=[n_j;e\_{ij}]W^V
    \end{array}
    \right.
```
where each weight matrix $W$ is now of size $\mathbb{R}^{(d_n+d_e)\times d_n}$.For efficiency, each matrix $W$ is split into two separate matrices for projecting node and edge vectors
```math
    \left\{
    \begin{array}{ll}
        q_{ij}=(n_iW_n^Q+e_{ij}W_e^Q) \\
        k_{ij}=(n_jW_n^K+e_{ij}W_e^K) \\\
        v_{ij}=(n_jW_n^V+e_{ij}W_e^V
    \end{array}
    \right.
```

So, the edges are added to the usual attention vectors as extra information.

## Edge Updates

These edges need to be updated at every layer of processing. This is done following the standard transformer procedure: aggregate messages into one, then use the result to perform a local update. However, since having every one of the $N^2$ edges attend to every one of the $N$ nodes would bring the complexity up to $\mathcal{O}(N^3)$, every edge is limited to only its immediate neighbourhood
```math
    e_{ij}^{l+1}=\phi_e(e_{ij}^l,e_{ji}^l,n_i^{l+1},n_j^{l+1})
```
where $\phi_e$ is the aggregation function. The aggregated message is computed by
```math
    m_{ij}^l=\mathrm{ReLU}(\mathrm{concat}(e_{ij}^l,e_{ji}^l,n_i^{l+1},n_j^{l+1})W_4)
```
where the non-linear $\mathrm{ReLU}$ takes the place of the $\mathrm{softmax}$ in regular attention. The rest of the edge update follows the standard transformer method
```math
    \left\{
    \begin{array}{ll}
        u\_{ij}^l=\mathrm{LayerNorm}(m\_{ij}^lW\_5+e_{ij}^l) \\
        e\_{ij}^{l+1}=\mathrm{LayerNorm}(\mathrm{ReLU}(u\_{ij}^lW\_6)W\_7+u\_{ij}^l)
    \end{array}
    \right.
```

Nodes are updated before edges so that nodes aggregate information from the entire graph before its adjoining edges use that information in their local updates.

## References
- Original Paper: ["Relational Attention: Generalizing Transformers for Graph-Structured Tasks"](https://arxiv.org/abs/2210.05062)