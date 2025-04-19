---
title: code2vec
math: true
url: code2vec
weight: 1
---

# Code2Vec

The goal of code2vec is to learn code embeddings, continuous vectors for representing snippets of code. In the original paper, the motivating task is semantic labelling of code snippets.

code2vec is based on attention mechanisms, which specify how much focus should be given to each element in a bag of path-contexts. It allows to aggregate the information in each individual path-context into a single vector that represent the entire code-snippet. The attention mechanism is learned simultaneously with the embeddings, optimizing both the atomic representations of paths and the ability to compose multiple contexts into a single code vector.

## Pipeline Overview

The model takes as input a code snippet in a given programming language and a parser for that language. The AST is represented as a set of paths from terminal node to terminal node. These paths are represented as a triplet \(\langle x_s, p, x_t\rangle\), where \(x_s\) and \(x_t\) are the terminal nodes and \(p\) is the path connecting them. Each of the components of the triplet is embedded via a value vocabulary and path vocabulary learned during training. These components are then combined into a single vector by a fully-connected layer, and multiple paths are combined by an attention mechanism, with a learned global attention vector.

Since the number of paths grows quadratically with the number of terminals, the authors applied some heuristics to limit the number of paths. Namely, they limited the paths by maximum length and by maximum width (the maximal difference in child index between two child nodes of the same intermediate node). The exact values were set as hyperparameters but not disclosed in the paper.

## Data

The authors say they used a dataset containing 14M code snippets, but they don't say what the dataset was.

## Model Description

From a high-level perspective, each code snippet is composed of a bag of contexts, and each context is represented by a learned vector. The vector values capture two distinct goals: the semantic meaning of the context and the amount of attention this context should get. The base idea behind code2vec is that all context vectors need to be considered, but the model should be allowed to learn how much focus to give to each vector. The weighted average of the context vectors is obtained by weighting each vector by a factor of the dot product of it with another global attention vector. The vector of each context and the global attention vector are trained and learned simultaneously.

The input of the model is a code snippet in some programming language and a parser for that language. Let \(\textit{Rep}\) be the representation function that transforms a code snippet into a mathematical object that can be used in a learning model. Then, \(C\) is represented as the set of triplets \(\langle x_s, p, x_t \rangle\) such that \(x_s\) and \(x_t\) are values of AST terminals, and \(p\) is the AST path that connects them.

### Path-Attention Model

The model learns embeddings for paths and names \(\textit{value\_vocab}\) and \(\textit{path\_vocab}\), a fully connected layer \(W\), an attention vector \(a\) and embeddings to the tags, i.e. method names, \(\textit{tags\_vocab}\). The embedding vocabularies, \(\textit{value\_vocab}\) and \(\textit{path\_vocab}\) are matrices in which every row corresponds to an embedding associated with a certain object. The values of these matrices are learned during training. The width of the matrix \(W\) is the embeddings size \(d\) (\(d\) can be different for path embeddings and value embeddings).

Consider a bag of path-contexts \(\mathcal{B}=\{b_1,...,b_n\}\) that were extracted from a given code snippet. Let \(b_i=\langle x_s,p_j,x_t\rangle\) be one of these path-contexts. Each component of the path-context is looked-up and mapped to its corresponding embedding. The three embeddings of each path-context are concatenated to a single context vector
$$
c\_i = \left[value\_vocab_s ; path\_vocab_, ; value\_vocab_t \right]\in\mathbb{R}^{3d}
$$

The fully connected layer has the purpose of combining the components of the context vector. Let \(\tilde{c}_i\) be the output of the fully connected layer, also called combined vector context, for path \(b_i\). Mathematically, we have
$$
\tilde{c}_i = \tanh(W\cdot c_i)\in\mathbb{R}^d
$$
So, the fully connected linear layer compresses the context vector from \(3d\) dimensions back to \(d\) dimensions by combining its components.

The attention mechanism aggregates the combined context vectors of the code snippet into one single vector, by determining the appropriate weight to give to each vector. The attention vector \(a\in\mathbb{R}^d\) is learned with the network, and given the combined context vectors, the attention weight \(\alpha_i\) of each one is computed as the normalized inner product between the combined context vector and the global attention vector (exponentiation is used to keep the weights positive)
$$
\alpha_i = \frac{\exp(\tilde{c}_i^T\cdot a}{\sum\_{j=1}^n\exp(\tilde{c}_j^T\cdot a)}
$$

The aggregate code vector \(v\) is then finally
$$
v = \sum_{i=1}^n\alpha_i\cdot \tilde{c}_i
$$

The predictions of the tag is performed using the code vector and the tags vocabulary. The tag probability distribution is computed by the dot-product between the code vector \(v\) and each of the tag embeddings
$$
\text{for }y_i\in Y: q(y_i)=\frac{\exp(v^T\cdot tags\_vocab_i)}{\sum_{y_j\in Y}\exp(v^T\cdot tags\_vocab_j}
$$

## Training

The network was trained using cross-entropy loss between the predicted distribution \(q\) and the true distribution \(p\). Since \(p\) simply assigns 1 to the actual tag and 0 to all the others, the loss simply becomes
$$
\mathcal{L}=-\log q(y_{true})
$$

## References

- Original Paper: ["code2vec: Learning Distributed Representations of Code"](https://arxiv.org/abs/1803.09473)