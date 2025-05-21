# ContraCode

The base idea behind this method is to use compiler transforms as data augmentations, building sets of equivalent functions. This set can then be used as positive samples for contrastive learning.

## Method

### Compilation as Data Augmentation

Building a dataset of semantically equivalent functions is extremely difficult if done by crawling available code. Instead, the authors suggest using compiler transformations to generate code variants with equivalent functionality.

A transpiler is a specific type of compiler, that emits source code instead of machine code. The authors created a transpiler that, given a code snippet, applies a compiler transformation on it. They considered three kinds of transformations, as well as their compositions: code compression, identifier modifications, and regularizing transforms. The latter reduces the number of trivial positive pairs with high text overlap.

The augmentations introduced by the transpiler are fully deterministic. However, it is beneficial to introduce stochastic augmentations. For that, the authors randomly apply a subset of available compiler passes in a pre-specified order, each step having a certain probability of being applied.

### Contrastive Pre-Learning

An encoder maps each code snippet into a fixed dimensional embedding. Programs are organized into positive pairs and negative pairs. Positive pairs are augmentations from the same code snippet while negative resulted from different code snippets. This way, positive matches are pushed together in the representation space, while negative matches are pushed away. The negative matches are important to prevent the encoder from collapsing all code snippets into one single representation.

The loss is selected so that the contrastive learning task can be approached as a classification task. Namely, the goal is to identify the positive matches among the negatives. So, the loss function is 
```math
    \mathcal{L}_{CL}=-\log\frac{\exp(q\cdot k^+/t)}{\exp(q\cdot k^+/t)+\sum_{k^-}\exp(q\cdot k^-/t)}
```
where $q$ is the query code snippet, $k^+$ is the positive match, $k^-$ are the negative matches and $t$ is a temperature hyperparameter.

## References

- Original Paper: ["Contrastive Code Representation Learning"](https://arxiv.org/abs/2007.04973)

