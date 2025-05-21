# Contrastive Method

## Input Representation

The training data consists of paired samples, $\{(x_i,y_i)\}^N_{i=1}$, where $(x_i,y_i)$ corresponds to a positive example pair, i.e. $x_i$ and $y_i$ are semantically similar or contextually relevant. For text, these positive pairs are constructed by setting $y_i$ as one of the neighbouring pieces of the around $x_i$. For code, these positive pairs are constructed as code and its associated documentation.

Each input sequence receives $[SOS]$ and $[EOS]$ tokens at the start and end, respectively. The embedding of the input sequence is considered to be the hidden state from the last layer corresponding to the special token $[EOS]$.

## Method

This method is based on the Transformer architecture. Each element of the input pair is encoded independently into $v_x$ and $v_y$, and the similarity between them is quantified by cosine similarity.

The training objective is based on contrastive learning, where the model is forced to maximize the similarity between positive samples and minimize it for negative samples. Negative samples are selected in-batch, i.e. from other pairs in the same batch. Considering that one batch contains $M$ samples, each sample has $(M-1)$ negative samples. The logits for one batch are a $M\times M$ matrix, where each entry is given by
```math
mathrm{logit}(x_i,y_j)=mathrm{sim}(x_i,y_j)\cdot mathrm{exp}(\tau)\quad,\quad\forall_{(i,j)}:i,j\in\{1,...,M\}
```
where $\tau$ is a learnable temperature parameter.

Entries on the diagonal are considered positive samples, while any other entry is a negative sample. The final training loss is the sum of the cross-entropy loss computed row-wise and column-wise.

## References

- Original Paper: ["Text and Code Embeddings by Contrastive Pre-Training"](https://arxiv.org/pdf/2201.10005.pdf)