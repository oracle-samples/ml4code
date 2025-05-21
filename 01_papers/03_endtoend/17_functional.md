# Functional Method

## Method

This method is composed of three basic components. First, the functionality of each file is identified using the call graph. Then, syntactic and semantic representations are extracted from the AST and the CFG. Finally, a neural network is trained for binary classification.

## Identifying Functionality with Call Graph

The call graph represents calling relationships between methods in a program. Each node represents a method, and each edge $(f,g)$ indicates that method $f$ calls method $g$. After tagging all statements with an id, the caller-callee relationship can be expressed in the form of a triplet $\langle$callerId, statementId, calleeId$\rangle$. The methods with caller-callee relationships are combined together as a functionality, while the methods without any caller-callee relationship are regarded as self-contained functionalities.

## Extracting Syntactic and Semantic Representations

Taking a code snippet, the authors start by converting it into its AST form. Then, they traverse it in pre-order traversal building an identifier sequence. This sequence is considered to represent the syntactic information of the code snippet. To reduce the variability introduced by variable names, these are replaced by its type and a global self-increasing number ($\langle$int$_1\rangle$, $\langle$int$_2\rangle$,...). Then, using the caller-callee relationships computed in the previous step, the AST sequences of each connected method are joined together as a set of ASTs. The sets (with possibly one single element for self-contained functionalities) are considered to be the syntactic representations of each functionality.

The CFG is used to capture semantic information. Let $G=(V,E)$ be the CFG, where $V$ is a set of vertexes and $E$ is a set of edges. Each vertex contains a statement, and the edges indicate the control flow of the statements. According to the call graph extracted previously, the CFGs are connected to form one large CFG per functionality. These graphs are considered to be the semantic representations of each functionality.

Finally, each of these complementary representations are encoded into its vector feature representation. To encode the identifier sequence, the authors used Word2Vec, which condenses the syntactic information of each method into a fixed-length vector. The representation of each functionality is computed by average pooling on all the methods it contains. To encode the CFGs, the authors used Graph2Vec, which again condenses the information in an arbitrary shape graph into a fixed-length vector.

### Binary Classifier

The input of the classifier is the stack syntactic and semantic vectors for two functionalities, and the output is a binary classification, stating whether or not the two functionalities are clones.

## Evaluation

The model was evaluated on OJClone for code clone detection.

## References

- Original Paper: ["Functional code clone detection with syntax and semantics fusion learning"](https://dl.acm.org/doi/10.1145/3395363.3397362)