---
title: Recursive Auto Encoder Method
math: true
url: raem
weight: 8
---

# Recursive Auto Encoder Method

This method is developed for code clone detection, and is composed of two phases. The first is feature extraction, which is done by weighted recursive auto encoders (RAE), and the second phase is a fast approximate nearest neighbour search (ANNS). 

## Feature Extraction

The method starts by converting the code snippet into its AST representation. However, the weighted RAE cannot take arbitrary tree structures as input, it needs full binary trees. So, the AST is transformed into a full binary tree by applying a set of rules for handling nodes with more than two children, and another set of rules for processing nodes with single children. Once the AST has been transformed into a full binary tree, it is flattened by post-order traversal, turning it into a sentence.

There are two kinds of nodes, terminal and non-terminal. Terminal nodes correspond to words in the code, and they can be modelled to vector representations. Non-terminal nodes, on the other hand, cannot, as they do not correspond to specific words but only have a type. To determine the importance of non-terminal nodes, the authors weighted them by TF-IDF (term frequency, document inverse frequency).

The RAE takes the full binary tree, and continuously merges and reconstructs vectors of child nodes at each non-terminal node recursively and bottom-up, starting from leaves and ending at the root node. The goal is to minimize reconstruction errors that occur at all non-terminal nodes, and calculate their semantic vector representations. The learned representations combine the structural information of the AST and can effectively represent the program. The weights of each non-terminal node are used to give different weights to each node when merging all the node encodings into one single tree encoding. 

## References

- Original Paper: ["Fast Code Clone Detection Based on Weighted Recursive Autoencoders"](https://ieeexplore.ieee.org/document/8822436)