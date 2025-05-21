# CodeBERTScore

CodeBERTScore takes in two code snippets as well as their context (shared NL comment). Each snippet+context is encoded independently by BERT. Then, all context tokens as well as non-alphanumeric code tokens (except arithmetic operators) are masked out. Taking only the remaining token vectors, cosine similarity is computed between all candidate-reference pairs of tokens. Then precision, recall and $mathrm{F_1}$ are computed using these similarity scores, according to

$$
\left\\{
\begin{array}{ll}
mathrm{CodeBERTScore}\_P = \frac{1}{|\hat{y}|} \sum\_{\hat{y}\_j\in\hat{y}}\max\_{y\_i\in y}mathrm{sim}(y\_i,\hat{y}\_j)\\\
mathrm{CodeBERTScore}\_R = \frac{1}{|y|}\sum\_{y\_i\in y}\max\_{\hat{y}\_j\in\hat{y}}mathrm{sim}(y\_i,\hat{y}\_j) \\\
mathrm{CodeBERTScore}\_{F\_1} = \frac{2\cdotmathrm{CodeBERTScore}\_P\cdotmathrm{CodeBERTScore}\_R}{mathrm{CodeBERTScore}\_P+mathrm{CodeBERTScore}\_R}
\end{array}
\right.
$$

where $y$ are the reference tokens and $\hat{y}$  are the candidate tokens.

- Original Paper: ["CodeBERTScore: Evaluating Code Generation with Pretrained Models of Code"](https://arxiv.org/abs/2302.05527)