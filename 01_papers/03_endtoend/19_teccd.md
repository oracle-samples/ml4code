# Tree Encodings for Code Clone Detection (TECCD)

## Method

Overall, pre-processing is composed of the following steps. The code snippet is converted into its AST using ANTLR. The AST is filtered and redundant nodes are removed. Then a random walk is performed on the AST to obtain a set of node sequences. Finally, the nodes sequence is sent through a skip-gram model and a node-vector dictionary is generated, where the node name is used as index for querying the corresponding vector.

In order to perform the random walk, the authors have to modify the AST, and add directed edges between sibling nodes. Then, given a source node $u$, each node in a fixed length random walk is generated with the following distribution
```math
P(c_i=x|c_{i-1}=v)=\left\{
\begin{array}{ll}
\frac{\pi_{vx}}{Z}\quad\text{if }(v,x)\in E \\
0\quad\quad\text{otherwise}
\end{array}
\right.
```
where $1\leqslant i\leqslant l$, $l$ is the fixed length of the walk, $v$ is the current node and $x$ is the next node to visit. The transition probability $\pi_{vx}=\alpha_{pq}(t,x)$ is defined as
```math
\alpha_{pq}(t,x)=\left\{
\begin{array}{ll}
\frac{1}{p}\quad\text{if }d_{tx}=1 \\
\frac{1}{q}\quad\text{if }d_{tx}=2
\end{array}
\right.
```
where $d_{tx}$ is the shortest path distance between nodes $t$ and $x$, and the parameters $p$ and $q$ control the likelihood of doing BFS-like or DFS-like searches, respectively.

Once all the node embeddings are computed, the final AST representation is computed via a sentence embedding method.

## Evaluation

This method was designed for code clone detection. For that, it computes the Euclidean distance between AST vector representations.

The data used for this task was the BigCloneBench dataset.

## References

- Original Paper: ["TECCD: A Tree Embedding Approach for Code Clone Detection"](https://ieeexplore.ieee.org/abstract/document/8918964)
- BigCloneBench: ["Towards a Big Data Curated Benchmark of Inter-project Code Clones"](https://ieeexplore.ieee.org/document/6976121)