# AST-Trans

## Method

This method starts by linearizing the AST, and then computing and storing ancestor-descendant and sibling relationships for all nodes.  These metrics are used to compute tree-structured attention, and irrelevant nodes are dynamically eliminated.\footnote{This paper contains a very large complexity and efficiency analysis of the method. While I will not place it here, it may be useful for further studies.}

### AST Linearization

The authors consider three different methods to linearize the AST: pre-order traversal (POT), structure-based-traversal (SBT) and path decomposition (PD). POT visits all the tree nodes with pre-order traversal. However, this method is lossy as the original AST cannot be unambiguously retrieved from the flattened AST. SBT adds additional brackets to indicate the parental-descendent relationship such that each sequence can be unambiguously mapped back to its original AST. However, this method also doubles the size of the linearized AST. PD represents the AST by concatenating the path between two random lead nodes. However, the total number of paths can be too large, and so random sampling is needed.

For POT and SBT, the linearized trees can be fed directly to the encoder. However, for PD the number of paths is too large for that. In practice, mean pooling is run over the states of each path so that each path has one unique representation.

Given the considerations expressed above, and the empirical finding that SBT does not outperform POT, the authors decided to use POT.

### Relationship Matrices

The authors consider two kinds of relationships: ancestor-descendant ($A$) and sibling ($S$). The former represents the hierarchical information across blocks, while the latter represents the temporal information within one block. Specifically, two nodes have ancestor-descendant relationship if there exists a directed path from the root that can traverse through them. Two nodes have a sibling relationship if they share the same parent node.

Considering that the AST has $N$ nodes, and that the $i$-th node in the linearized AST is represented by $n_i$. $A_{ij}$ is the distance of shortest path and $S_{ij}$ is the horizontal sibling distance between nodes $n_i$ and $n_j$. If these nodes are not ancestor-descendant or siblings, then these values are defined as $\infty$. To further preserve structure information from the AST, these are signed distance, such that $A_{ij}=-A_{ji}$ and $S_{ij}=-S_{ji}$. Then, using the shortest path distance (SPD) and sibling distance (SID), the relationship matrices are defined as
```math
A_{ij}=\left\{
\begin{array}{ll}
    mathrm{SPD}(i,j)\quad\text{if }|mathrm{SPD}(i,j)| \leqslant P \\
    \infty\quad\quad\quad\quad \text{otherwise}
\end{array}
\right.
```

```math
S_{ij}=\left\{
\begin{array}{ll}
    mathrm{SID}(i,j)\quad\text{if }|mathrm{SID}(i,j)| \leqslant P \\
    \infty\quad\quad\quad\quad \text{otherwise}
\end{array}
\right.
```
where $P$ is a pre-defined threshold and nodes with relative distance beyond $P$ will be ignored.

### Tree-Structured Attention

Tree-structured attention is built on standard self-attention with relative position embeddings and disentangled attention. Instead of using the relative positional embeddings, it uses the two relationship matrices derived from the AST. This way, the relative distance between nodes is encoded using
```math
\delta_R(i,j)=\left\{
\begin{array}{ll}
R_{ij}+P+1\quad\text{ if }R_{ij}\in[-P,P] \\
0\quad\quad\quad\quad\quad\quad\text{if }R_{ij}=\infty
\end{array}
\right.
```
where $R_{ij}$ refers either to $A_{ij}$ or $S_{ij}$. Since there are two kinds of relationships, the authors assigned only one kind of relationship per head, splitting the heads into two categories, $h_A$ and $h_S$. The information will be merged together via multi-head attention.

## Evaluation

This method was evaluated on a code summarization task. The authors used the TL-CodeSum (Java) and the Code Docstring Corpus (Python) datasets.

## References

- Original Paper: ["AST-Trans: Code Summarization with Efficient Tree-Structured Attention"](https://ieeexplore.ieee.org/document/9794079)
- TL-CodeSum: ["On the evaluation of neural code summarization"](https://arxiv.org/abs/2107.07112)
- Code Docstring Corpus: ["A parallel corpus of Python functions and documentation strings for automated code documentation and code generation"](https://arxiv.org/abs/1707.02275)