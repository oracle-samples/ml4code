# Two-Stage AST Encoding (TSE)

## Method

This method takes as input a code snippet, and parses it into an abstract syntax tree. A pre-order traversal is done to navigate the tree, during which all nodes are tagged as either block nodes or ordinary nodes, following a set of pre-determined rules. Most of the block nodes chosen create a local scope with a specific context.

An AST \(T\) is defined as a collection of its block subtrees
```math
    T=[bT_1, bT_2,...,bT_n]
```
where each block subtree is defined as
```math
	bT_i=[b_i,[oT_{i1},oT_{i2},...]]
```
where \(b_i\) is the root node of the block structure, and \(oT_{ij}\) is an ordinary subtree under \(bT_i\). Each subtree is denoted as
```math
	oT_{ij} = [o_{ij1},o_{ij2},...]
```

After tagging the AST, word2vec is used to convert block nodes and ordinary nodes into \(E\)-dimensional vectors, denoted as \(x_i^b\) and \(x_{ijk}^o\).

## First Stage

In the first stage, the self-attention mechanism is employed to aggregate the information from the ordinary nodes to the block nodes. Each ordinary node receives a positional encoding, which is defined by its depth and the nodes position within that depth. These positional encodings are represented by \(x^{depth}\) and \(x^{num}\), respectively.

Given a subtree with \(N_i\) nodes, its embedding sequence is given by
```math
	X_i^{bT} = \left[ x_i^b,x_{i11}^o+x_{i11}^{depth}+x_{i11}^{num},...,x_{ijk}^{depth}+x_{ijk}^{num} \right]
```

Its encoding \(en_i^{bT}\) is given 
```math
    en_i^{bT}=(W^{en})^Tx_i^b+mathrm{Maxpool}(mathrm{Atten}(X_i^{bT}|x_i^b))+b^{en}
```
where \(W^{en}\) is the learnable weight matrix, \(b^{en}\) is the bias term, and \(X_i^{bT}|x_i^b\) is the sequence \(X_i^{bT}\) excluding \(x_i^b\).

## Second Stage

After the first stage, we are left with the encodings of each block tree. Tree-based LSTM is adopted as the encoder to acquire the AST's final encoding. Specifically, the encoding is computed by max-pooling the hidden states of block nodes
```math
en^T = mathrm{Maxpool}(mathrm{TreeLSTM}(X^T))
```

Then its just a matter of using a linear layer with cross entropy loss for the defect classifier.

## Evaluation

For the task of code defect detection, the authors used the PROMISE repository. For evaluation, they used the \(F_1\) score and the \(MCC\). These metrics are computed as
```math
    F_1=\frac{2\cdot Precision\cdot Recall}{Precision + Recall}
```
```math
    MCC=\frac{TP\cdot TN - FP\cdot FN}{(TP+FP)(TP+FN)(TN+FP)(TN+FN)}
```

# References
- Original Paper: ["Two-Stage AST Encoding for Software Defect Prediction"](https://ksiresearch.org/seke/seke22paper/paper039.pdf)
- [PROMISE dataset](http://promise.site.uottawa.ca/SERepository/datasets-page.html)