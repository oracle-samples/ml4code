---
title: Deckard
math: true
url: deckard
weight: 5
---

# Deckard
## Method
### Encodings Generation
This method is based on tree-encodings, and its goal is to detect code clones. To compute the tree-encodings, the authors the authors start by eliminating irrelevant nodes from the AST. Afterwards, the encodings are generated via a post-order traversal of the AST by summing up the vectors for children with the vector for the parent's node. Additionally, they introduce a procedure they call vector merging, in which vectors of certain node sequences are summed up. This is done by using a sliding window, which moves along a serialized form of the parse tree. The merging is applied only on a specific set nodes, chosen to make good boundaries among cloned code snippets. Specifically, they consider roots of expression trees.

### Code Clone Detection
In order to efficiently perform near-neighbour search, the authors hash each vector with respect to the distances among them, and then look for similar vector by only comparing vectors with equal hash values. For this purpose, they use locality sensitive hashing (LSH). Locality sensitive hashing constructs a special family of hash functions that can hash two similar vectors to the same hash value with arbitrarily high probability, and hash two distant vectors to the same hash with arbitrarily low probability. For finding queries, a target vector is only compared with vectors with the same hash value. This reduces the computational cost of pairwise searching, while maximizing the probability of finding near-neighbours. 

Additionally, the authors consider a size-sensitive metric for claiming that two codes are clones, as larger code fragments should be allowed more points in common before being considered clones. To implement that they resort to vector grouping, where vectors for a program are separated into different groups based on the sizes of their corresponding code fragments. Then LSH is applied on a group level, and each group is assigned a different threshold for code clone classification. Vector grouping not only allows to introduce code size-sensitivity to the method, but allows improves scalability.

## Evaluation
This method was evaluated on the code for JDK 1.4.2 and Linux kernel 2.6.16. It was tasked with finding code clones within each of these two code bases. 

## References
- Original Paper: ["DECKARD: Scalable and Accurate Tree-Based Detection of Code Clones"](https://ieeexplore.ieee.org/document/4222572)