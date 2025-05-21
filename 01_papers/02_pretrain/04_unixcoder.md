# UniXcoder

UniXcoder is a unified cross-modal pre-trained encoder-decoder model that leverages multimodal data (code comments and AST) to pretrain code representation. It was developed to support both code-related understanding and generation tasks, and it uses mask attention matrices with prefix adapters to control the behavior of the model. 

## Pipeline Overview

This model takes as input a code snippet (in the form of an AST) and a comment. The AST is flattened and tokenized using a one-to-one mapping, and the two sequences of tokens (tokenized comment and AST nodes) are concatenated, along with a prefix indicating mode of operation. The mode of operation dictates the masked attention matrix used in the model. The output of the model is a set of contextualized embeddings that can be used for downstream tasks.

## Data

For pre-training, the model used the CodeSearchNet dataset with 2.3 million functions paired with comments for bimodal data, and the C4 dataset and CodeSearchNet for unimodal data. For fine-tuning, there were several tasks considered. For clone detection, they used POJ-104 and BigCloneBench. For code search, they used CSN, AdvTest and CosQA. For code summarization, they use the dataset published by CodeXGLUE. For code generation, they use the CONCODE dataset. For code completion, they use PY150 and Github Java Corpus. Finally, for zero-shot code-to-code search they developed their own dataset from the CodeNet corpus. 

## Input Representation

The authors propose a one-to-one mapping of the AST of a given code snippet, which would convert it into a flattened sequence of tokens. This mapping is done via a recursive function, and it takes a root node, and adds all its children to the output list, enclosing them with special suffixes (\textit{left} and \textit{right}). For example, if we consider the branch "$parameters\rightarrow (data)$", applying the mapping function to it would return "$\langle parameters, left\rangle (data) \langle parameters, right\rangle$". The benefit of this mapping over other candidates (e.g. depth-first traversal, pre-order traversal,...) is the fact that there is a one-to-one relationship between the tree and the flattened token sequence.

The other part of the input is the comment $W$ associated to the code $C$. The full input is obtained by concatenating the comment, $W$, with the flattened AST, $\mathcal{F}(\mathcal{T}(C))$, as well as with a prefix representing the work mode of the model (encoder-only $[Enc]$, decoder-only $[Dec]$, encoder-decoder $[E2D]$).

## Architecture

This model follows the standard Transformer architecture with the addition of attention mask matrices. These control the context a token can attend to when computing its contextual representation. Formally, for the $l$-th transformer layer, the output of the multi-headed self-attention is computed by
```math
head=mathrm{softmax}\left(\frac{QK^T}{\sqrt{d_k}}+M\right)V
```
where the $M$ matrix is controlling the context a token can attend to. If the $i$-th token is allowed to attend to the $j$-th token, then $M_{ij}$ is 0, otherwise it's $-\infty$. In $[Enc]$ mode, all elements of the mask are set to 0, as all tokens can attend to all other tokens. In $[Dec]$ mode, the upper triangular part of the mask is set to $-\infty$, as each token can only attend to itself and previous tokens. Finally, in $[E2D]$ mode, tokens in the source input are allowed to attend to each other, but tokens in the target input can only attend to themselves and previous ones.

## Pre-Training

During the pre-training phase, model parameters are shared in the different modes and optimized with several tasks.

## Masked Language Modelling (MLM)

Masked Language Modelling allows the model to use information from the comments and the AST to infer masked code tokens. This task, executed in encoder-only mode, is done in the same way as BERT. 15\% of the tokens are selected, and then from those 80\% are replaced with $[MASK]$, 10\% are replace with a random token, and 10\% are left unchanged. The task is to predict original tokens from masked tokens. That leads to the objective function
```math
\mathcal{L}_{MLM} = -\sum_i^M\log p^{MLM}(x_i|a^m,w^m)
```
where $M=a^m\cup w^m$ is the set of masked tokens.

### Unidirectional Language Modelling (ULM)

Unidirectional Language Modelling pre-trains the decoder mode for auto-regressive tasks like code completion. The model is tasked to predict the next token $x_i$ one by one conditioned on previous tokens and itself. This leads to the objective function
```math
\mathcal{L}_{ULM}=-\sum_i^n\log p^{ULM}(x_i|x_{t < i})
```
where $n$ is the number of tokens.

### Denoising (DNS)

This is an objective for the encoder-decoder mode, and it randomly masks spans with arbitrary lengths and then generates these masked spans in encoder-decoder mode. For that, the sequence is split into $\max(\lfloor n\cdot r/l\rfloor, 1)$ chunks and then randomly mask a span ranging from 1 to $2l-1$ tokens for each chunk, where $n$ is the input length, $r$ is the corruption rate and $l$ is the average length of masked spans. The concatenation of $\{y_1,y_2,...,y_n\}$ of all masked spans will be used as output. So, the objective function is

```math
\mathcal{L}_{DNS}=-\sum_i^n\log p^{DNS}(y_i|a^m,w^m,y_{ t < i})
```

### Multi-Modal Contrastive Learning

This task intends to teach the model how to construct semantic embeddings $\tilde{h}_i$ (obtained by mean-pooling over the hidden states of the source input).  For that, the same input using different hidden dropout mask is sent to the model as a positive sample $\tilde{h}_i^+$, and other representations in the same batch are sent as negative samples. The loss is calculated to maximize the similarity between positive samples and minimize it between negative samples. The objective function is
```math
\mathcal{L}_{MCL}=-\sum_i^b\log\frac{mathrm{e}^{\cos(\tilde{h}_i,\tilde{h}_i^+)/\tau}}{\sum_j^bmathrm{e}^{\cos(\tilde{h}_i,\tilde{h}_j^+)/\tau}}
```
where $b$ is the batch size, $\tau$ is a temperature hyperparameters and $mathrm{cos}(\cdot,\cdot)$ is the cossine similarity.

### Cross-Modal Generation

The goal of cross-modal generation is also to help the model generate better semantic embeddings. Now the task is for the model to generate its comment $w=\{w_1,w_2,...,w_m\}$, describing the function of the code. Since the generation of the comment is conditioned on the code, it will force the model to fuse semantic information from the comment into the hidden states of the code. The objective function is then
```math
\mathcal{L}_{CMG}=-\sum_i^m\log p^{CML}(w_i|a,w_{t < i})
```

## References

- Original Paper: ["UniXcoder: Unified Cross-Modal Pre-training for Code Representation"](https://arxiv.org/abs/2203.03850)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search"](https://arxiv.org/abs/1909.09436)
- C4 dataset: ["Exploring the Limits of Transfer Learning with a Unified Text-to-Text Transformer"](https://arxiv.org/abs/1910.10683)
POJ-104 dataset: ["Convolutional Neural Networks over Tree Structures for Programming Language Processing"](https://arxiv.org/abs/1409.5718)
- BigCloneBench: ["Towards a Big Data Curated Benchmark of Inter-project Code Clones"](https://ieeexplore.ieee.org/document/6976121)
- CSN dataset: ["GraphCodeBERT: Pre-training Code Representations with Data Flow"](https://arxiv.org/abs/2009.08366)
- AdvTest dataset: ["CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation"](https://arxiv.org/abs/2102.04664)
- CosQA dataset: ["CoSQA: 20,000+ Web Queries for Code Search and Question Answering"](https://arxiv.org/abs/2105.13239)
- CONCODE dataset: ["Mapping Language to Code in Programmatic Context"](https://arxiv.org/abs/1808.09588)
- PY150 dataset: [PY150](https://www.sri.inf.ethz.ch/py150)
- Github Java Corpus: ["Mining source code repositories at massive scale using language modelling"](https://ieeexplore.ieee.org/document/6624029)