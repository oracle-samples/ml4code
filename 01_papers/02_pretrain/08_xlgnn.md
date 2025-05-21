# Transformer-XL+GNN

## Pipeline Overview
This model takes as input a code snippet. It extracts the AST from that code snippet, and builds an encoding of that AST. That is done via a ConvGNN, that takes the node encodings and the AST edges to build a vector representation of the AST. This representation is sent through a Transformer-XL, and the output is a contextual representation of the nodes, and specifically of the code tokens, as well as the aggregated contextual representation of the AST via the $[CLS]$ token. 

## Data
For fine-tuning for code summarization, the authors used a public Java dataset called Funcom.

## Architecture
This model is based on the Transformer-XL architecture, and also uses a ConvGNN. It is composed of three stages: AST encoding, code representation, and summary generation. 

### AST Encoding
The authors use ConvGNN to encode ASTs into high-dimensional vectors. This allows the model to learn each node's representation based on its neighbouring nodes. The ConvGNN takes the AST nodes' embeddings and the AST edges as input. The representation of node $nv$ in the $k$-th layer is given by
```math
H=g_{nv}^{k-1}+\sum_{\forall u\in N(nv)}g_u^{k-1}
```
```math
g_{nv}^k=f(HW^k+b^k)
```
where $f(\cdot)$ is an activation function.

### Code Representation
The code representation vectors are obtained by sending the AST encodings through a Transformer-XL model. Compared to a standard transformer, a Transformer-XL includes a segment-level recurrence mechanism and a positional encoding scheme.

The segment-level recurrence mechanism allows information flowing between the consecutive segments, solving the limitation of using a fixed-length context and allowing the model to capture long-term dependencies. The positional encoding scheme used by the Transformer-XL is one of relative positional encodings, assuming that only the relative positions are relevant.

The result from the Transformer is the contextual vector representations of the tokens.

## Fine-Tuning

The model is fine-tuned for code summarization. For that, the authors used a Java dataset that unfortunately is no longer publicly acessible.

## References
- Original Paper: ["Transformer-XL With Graph Neural Network for Source Code Summarization"](https://ieeexplore.ieee.org/document/9658619)
- Java dataset: ["Recommendations for Datasets for Source Code Summarization"](https://arxiv.org/abs/1904.02660)