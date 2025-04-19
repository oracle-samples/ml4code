---
title: ASTToken2Vec
math: true
url: asttoken2vec
weight: 13
---

# ASTToken2Vec

## Model Architecture

The ASTToken2Vec is a four layer neural network, containing one input layer, one hidden layer and two output layer. The authors define two models: one that receives as input a non-terminal node (NT2V) and one that receives as input a terminal node (TT2V). The input layer is a one-hot encoding representation of the node. There are two output layers because there are two kinds of nodes: terminal and non-terminal. The length of these two output layers is the size of the terminal or non-terminal vocabulary, respectively.

## Node Embeddings

### Terminal Nodes

TT2V uses both the terminal and non-terminal context of a node. A training tuple contains three elements: \(\textit{(target terminal, non-terminal context, terminal context)}\), where the \(\textit{target non-terminal}\) is the non-terminal token which the TT2V model generates the embedding vector for. The second and third elements are contexts to the input node.

The non-terminal context is defined as its \(n\) parent non-terminal nodes.

The terminal context is defined as its \(m\) neighbour terminal nodes in the AST. Neighbour terminal nodes of a target terminal mean its sibling terminals which have the same parent. If a terminal node does not have any neighbour terminal nodes, then a special token is used.

### Non-Terminal Nodes

NT2V employs both the terminal and non-terminal context of a node. A training tuple contains three elements: \(\textit{(target non-terminal, non-terminal context, terminal context)}\), where the \(\textit{target non-terminal}\) is the non-terminal token which the NT2V model generates the embedding vector for. The second and third elements are contexts to the input node.

The non-terminal context is defined as the surrounding non-terminal nodes in the AST. Concretely, the \(n\) parent non-terminal nodes of a node, and all its non-terminal children are used as context.

The terminal context is defined as all its children terminal nodes. If a node doesn't have terminal children nodes, then a special token is assigned.

## Joint Loss Function

Since there are two output layers, the authors designed a joint loss function combining the non-terminal context output and terminal context output. The loss of non-terminal context output is given by
$$
\mathcal{L}_{nt}=-\sum_{i=1}^N\left(y_{nt-context}^i\log(\hat{y}_{nt-context}^i)\right)
$$
where \(y_{nt-context}\) represents the ground-truth non-terminal context, and \(\hat{y}_{nt-context}\) is the model output.

The loss of terminal context output is given by
$$
\mathcal{L}_{tt}=-\sum_{j=1}^M\left(y_{tt-context}^j\log(\hat{y}_{tt-context}^j)\right)
$$
where \(y_{tt-context}\) represents the ground-truth terminal context, and \(\hat{y}_{tt-context}\) is the model output.

Finally, the full loss function is given by 
$$
\mathcal{L}=\alpha\mathcal{L}_{nt}+(1-\alpha)\mathcal{L}_{tt}
$$

## Evaluation

The end goal of this model is code completion. For that, an LSTM is used to take all the node encodings and produce a next token prediction. The two embeddings obtained from the previous steps are joined after each being applied a learned linear transformation.

The authors used a Javascript dataset with 150k ASTs.

## References

- Original Paper: ["ASTToken2Vec: An Embedding Method for Neural Code Completion"](https://www.semanticscholar.org/paper/ASTToken2Vec%3A-An-Embedding-Method-for-Neural-Code-Hidehiko/bfdc3b20deafa1bffc622961bd4a886433b078f5)
- Javascript dataset: No longer available.