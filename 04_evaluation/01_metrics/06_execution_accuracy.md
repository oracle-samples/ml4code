# Execution Accuracy

Considering the result set as $V\_n$ executed by the $n^\text{th}$ ground-truth SQL $Y_n$, and the result set $\tilde{V}\_n$ executed by the predicted SQL $\tilde{Y}\_n$, EX can be computed by

$$
\text{EX} = \frac{\sum_{n=1}^N \mathbb{1}(v^n, \hat{v}^n)}{N},
$$

where

$$
\mathbb{1}(v, \hat{v}) = \begin{cases} 
1, & \text{if } v\_i \in \hat{v}, \, \forall v\_i \in v, \\\
0, & \text{if } v\_i \notin \hat{v}, \, \exists v\_i \in v.
\end{cases}
$$