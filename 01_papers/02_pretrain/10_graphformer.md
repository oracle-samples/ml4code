# GraphFormer

GraphFormer is a Transformer-based model that attempts to incorporate structural graph information into its architecture. For that, they add several key structural metrics to the encodings, hoping that these allow the model to utilize not just the semantic information but also the structural information.

## Structural Encodings

### Centrality Encoding

This model uses degree centrality as an additional signal to the network.  The centrality encoding developed assigns each node two real-valued embedding vectors according to its indegree and outdegree. As the centrality encoding is applied to each node, we simply add it to the node features as input.
```math
h_i^{(0)}=x_i + z_{deg^-(v_i)}^- + z_{deg^+(v_i)}^+
```
where $h_i^{(0)}$ is the hidden representation at position $i$ before the first layer, $x_i$ is the node features, and $z^-,z^+$ are learnable embedding vectors specified by the indegree and the outdegree, respectively (for undirected graphs these can be merged). By using centrality encoding in the input, the softmax attention can catch the node importance signal.

### Spatial Encoding

For graph data, it is not possible to assign (relative or absolute) positional encodings like in the standard Transformer architecture, because the data is not sequential. To counter-act this, GraphFormer uses spatial encodings.

For any graph $G$, consider $\phi(v_i,v_j):V\times V\rightarrow\mathbb{R}$ which measures the spatial relation between $v_i$ and $v_j$ in graph $G$. The authors decided to use the distance of the shortest path between $v_i$ and $v_j$ (if two nodes are not connected, then it takes the value $-1$). This metric is transformed into a learnable scalar which will serve as a bias term for the self-attention module
```math
A_{ij}=\frac{(h_iW_Q)(h_jW_K)^T}{\sqrt{d}} + b_{\phi(v_i,v_j)}
```
where $b_{\phi(v_i,v_j)}$ is a learnable scalar shared across all layers.

### Edge Encoding

Edges can also contain structural features (e.g. different kinds of chemical bonds), and such features can't be neglected in the graph representation. The authors add these edge features into the attention layers, as correlation metrics between nodes.

For each node pair $(v_i,v_j)$, they find one of the shortest paths $mathrm{SP}${ij}=(e_1,e_2,...,e_N)$ from $v_i$ to $v_j$, and compute an average of the dot-products of the edge feature and a learnable embedding along the path. This is then added to the self-attention module as a bias term
```math
c_{ij}=\frac{1}{N}\sum_n^Nx_{e_n}(w_n^E)^t
```
where $x_{e_n}$ is the feature of the $n$ -th edge in the shortest path, $w_n^E$ is the $n$-th weight embedding.

```math
A_{ij}=\frac{(h_iW$Q)(h_jW_K)^T}{\sqrt{d}} + b_{\phi(v_i,v_j)}+c_{ij}
```

### Special Node

In analogy to the $[CLS]$ token, the authors add a special node to the graph, called $[VNode]$, which is connected to every other node. This node has the same role as the $[CLS]$ token, aggregating the graph's contextual information for downstream tasks.

## References

- Original Paper: ["Do Transformers Really Perform Badly for Graph Representation?"](https://arxiv.org/abs/2106.05234)