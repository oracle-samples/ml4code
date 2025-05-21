# Code2Seq
Code2Seq is a model developed for generating a natural language sequence from a given snippet of code. It first represents a code snippet as a set of compositional paths over its abstract syntax tree, where each path is compressed to a fixed-length vector using LSTMs. During decoding, CODE2SEQ relies on attention mechanisms to compute each token.

## Pipeline Overview
This method takes as input a code snippet, which represented as an AST. The AST itself is represented as a set of paths, where a path is defined as a sequence of nodes between terminals (leaves of the tree). Given that a code snippet can have an arbitrary number of paths, for each snippet CODE2SEQ samples only $k=200$ paths (the authors found that $k<100$ decreased performance and $k>300$ did not increase it).

Each node in the path is represent using a learned embedding matrix, and then the whole sequence is encoded via the final states of a bidirectional LSTM. However, the first and last nodes are tokens, and so these are encoded via subtoken encoding (similar to byte-pair encoding). These are passed through a linear layer to produce the final path representation.

Finally, the decoder uses all the context paths encoded via attention mechanisms to produce a sequence of NL tokens.

## Data
For code summarization, they use the Java-small (700 thousand code snippets), Java-med (4 million code snippets) and Java-large (16 million code snippets) datasets. For code captioning they use the CodeNN dataset (66 thousands pairs of questions and answers).

## Architecture
CODE2SEQ is based on an encoder-decoder architecture.  However, it does not read the input as a flat sequence of tokens. Instead, it creates a vector representation for each AST path separately. The decoder, on the other hand, attends to all encoded AST paths while generating the target sequence.

### Encoder
Given a set of AST paths $\{x_1,...,x_k\}$, the encoder creates a vector representation $z_i$ for each one separately. The paths are encoded using a bi-directional LSTM, and sub-token embeddings are used to capture the compositional nature of the terminals' values.

Each node is represented using a learned embedding matrix $E^{nodes}$ and then the entire sequence is encoded using the final states of a bi-directional LSTM
```math
h_1,...,h_l=mathrm{LSTM}(E_{v_1}^{nodes},...,E_{v_l}^{nodes})
```
```math
encode\_path(v_1...v_l)=[h_l^{\rightarrow};h_1^{\leftarrow}]
```

Additionally to encoding the paths, it is also necessary to encode the first and last nodes, because these are terminals whose values are tokens in the code. For that, we split the code tokens into subtokens, and these in turn are represented by a learned embedding matrix $E^{subtokens}$. The full token is represented by the sum of the subtoken vectors.
```math
encode\_token(w) = \sum_{s\in split(w)}E_s^{subtokens}
```

The final path representation is obtained by concatenating the path's representation with the token representation's of each terminal node, and applying a fully connected layer
```math
z = tanh(W_{in}[encode\_path(v_1...v_l);encode\_token(value(v_1));encode\_token(value(v_l))])
```

### Decoder

The decoder needs an initial state, which is given by the average of the combined representations of all the paths in the given example. Then the decoder generates the output sequence, while attending over all the representations, with the state being updated after every generated token.

## Training

Unlike the heavier Transformer-based methods, there is no distinction between pre-training and fine-tuning for CODE2SEQ. Instead, for each downstream task it is trained from scratch.

## References

- Original Paper: ["code2seq: Generating Sequences from Structured Representations of Code"](https://arxiv.org/abs/1808.01400)
- [Java-small dataset](https://s3.amazonaws.com/code2seq/datasets/java-small.tar.gz)
- [Java-med dataset](https://s3.amazonaws.com/code2seq/datasets/java-med.tar.gz)
- [Java-large dataset](https://s3.amazonaws.com/code2seq/datasets/java-large.tar.gz)
- [DeepCom dataset](https://github.com/xing-hu/DeepCom/blob/master/data.7z)
- [CodeNN's C\# dataset](https://github.com/sriniiyer/codenn/tree/master/data/stackoverflow/csharp)
