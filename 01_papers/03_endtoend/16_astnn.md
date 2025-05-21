# AST-based Neural Network (ASTNN)
## Method

This method parses the code snippet into its AST representation. Then, the AST is split into a sequence of statement trees by pre-order traversal. These statement trees are encoded by Statement Encoders. Finally, Bi-GRUs are used to model the naturalness of these statements, and the hidden states are sampled by pooling into one single vector. This vector is the representation of the code fragment.

### Constructing Statement Tree Sequences

The tree is explored by a pre-order depth-first walk. During this traversal, the statement nodes are selected, and added to their statement tree, which are recursively added to the statement tree sequence.

### Encoding Statements on Multi-Way Statement Trees
Word2Vec is used to learn a representation of all the symbols in the AST. These embeddings are used as the initial parameters of an RvNN based statement encoder, which is used to learn the vector representations of statements. The encoder traverses the statement tree and recursively takes the symbol of the current node as input, together with the hidden states of its children nodes.

### Representing the Sequence of Statements
To encode the statement tree sequence, the author used a Bi-GRU. So, assuming the sequence contains $T$ statement trees, they get $T$ representations that have the contextual information of all the sequences. These are all merged together via max pooling.

## Evaluation
This method was evaluated on code classification and code clone detection. For that, they used the OJ and the BigCloneBench datasets.

## References
- Original Paper: ["A Novel Neural Source Code Representation Based on Abstract Syntax Tree"](https://ieeexplore.ieee.org/document/8812062)