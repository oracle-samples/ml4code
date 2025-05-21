# Transformer

Transformers surface as an attempt to allow models to more easily capture long-range dependencies, which they can achieve by harnessing multi-head self-attention.

## Model Architecture

The transformer's architecture is based on stacked self-attention and fully-connected layers. 

The encoder is composed of $N$ identical layers. Additionally, it contains residual connections around each of the two sublayers[^1], followed by layer normalization (output=LayerNorm(x+SubLayer(x))).

The decoder is also composed of $N$ identical layers. However, the decoder has an additional sublayer, which performs multi-head attention over the output of the encoder stack. The self-attention sublayers are also modified to prevent positions from attending to subsequent positions, forcing the predictions to position $i$ to only depend on the know outputs at positions less than $i$.

## Attention

### Single-Head Attention

Attention maps a query and a set of key-values to an output. The output is computed as a weighted sum of the values, where the weights are determined by the compatibility between the query and the corresponding key. The input consists of queries and keys of dimension $d_k$, and values of dimension $d_v$. In practice, we compute the attention on a set of queries, packed together into a matrix $Q$. The keys and values are also packed into matrices $K$ and $V$. So, the attention is given by
```math
    \mathrm{Attention}(Q,K,V)=\mathrm{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right)V
```
i.e. we project the queries into the keys, we rescale them and then obtain a probability distribution by softmaxing the output. The factor of $1/\sqrt{d_k}$ is there to counter-act the vanishing gradient for large $d_k$.

### Multi-Head Attention

Multi-head attention allows the model to jointly attend to information from different representation subspaces at different positions, which is inhibited with single-head attention. This is implemented by concatenating the results from multiple heads, and projecting them to a common subspace. Formally, we have $(3h+1)$ projection matrices, where $h$ is the number of projection heads, given by $W_i^Q\in\mathbb{R}^{d_{model}\times d_k}$, $W_i^k\in\mathbb{R}^{d_{model}\times d_k}$, $W_i^V\in\mathbb{R}^{d_{model}\times d_k}$ for head $i$, and $W^O\in\mathbb{R}^{hd_v\times d_{model}}$ for the final projection. All these matrices are learnable, which allows each head to attend to a different subspace. For each of these, $d_k=d_v=d_{model}/h$. The attention for each head is now given by $\mathrm{head}_i=\mathrm{Attention}(QW_i^Q, KW_i^K, VW_i^V)$, and the final result is achieved by $\mathrm{MultiHead}=\mathrm{Concat}(\mathrm{head}_1,...,\mathrm{head}_h)W^O$.

## Positional Encodings

A transformer operates on sets. That is, it has no information about the order of the sequence it is attending to. This information needs to be injected into the model, via positional encodings to the input embeddings at the bottom of the encoder and decoder stacks. The standard approach is to use sine and cosine functions of different frequencies
```math
    \left\{
    \begin{array}{ll}
        \mathrm{PE}_{(pos,2i)}=\sin\left( pos/10000^{2i / d_{model}} \right) \\
        \mathrm{PE}_{(pos,2i+1)}=\cos\left( pos/10000^{2i / d_{model}} \right)
    \end{array}
    \right.
```
where $pos$ is the position and $i$ is the dimension.

These positional encodings are added to the input, and they should allow the model to learn to attend by relative positions.

## References

- Original paper: ["Attention is all you need"](https://arxiv.org/abs/1706.03762)
- Good explanation: ["The Annotated Transfomer"](https://nlp.seas.harvard.edu/2018/04/03/attention.html)
- [How does the (decoder-only) transformer architecture work?](https://ai.stackexchange.com/questions/40179/how-does-the-decoder-only-transformer-architecture-work)
- [Navigating Transformers: A Comprehensive Exploration of Encoder-Only and Decoder-Only Models, Right Shift, and Beyond](https://medium.com/@amirhossein.abaskohi/navigating-transformers-a-comprehensive-exploration-of-encoder-only-and-decoder-only-models-right-a0b46bdf6abe)

[^1]: To be clear, by sublayer we mean either the self-attention or the fully-connected blocks. The sum of these two sublayers forms one layer.
