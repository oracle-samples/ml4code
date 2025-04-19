---
title: BLEU
url: bleu
weight: 1
math: true
bookHidden: true
---
# BLEU

BLEU is a metric originally developed for measuring the quality of text produced in machine-translation. Quality is considered to be the correspondence between a machine's output and that of a human.

Formally, BLEU is based on a modified n-gram precision function. Consider a candidate corpus $\hat{S}=(\hat{y}^{(1)},...,\hat{y}^{(M)}$ and a reference candidate corpus $S=(S_1,...,S_M)$, where $S_i=(y^{(i,1)},...,y^{(i,N_i)})$. Then, the modified n-gram precision function is defined as
$$
    p_n(\hat{S};S)=\frac{\sum_{i=1}^M\sum_{s\in G_n(\hat{y}^{(i)})}\min(C(s,\hat{y}^{(i)}),\max_{y\in S_i}C(s,y))}{\sum_{i=1}^M\sum_{s\in G_n(\hat{y}^{(i)})}C(s,\hat{y}^{(i)})}
$$
where $C(x_1,x_1)$ gives the number of appearances of $x_1$ as a substring of $x_2$, and $G_n(y)$ gives the set of (unique) n-grams of string $y$.  

To allow different weights for each n-gram precision function, the final BLEU function is given by
$$
    \operatorname{BLEU}(\hat{S},S)=\exp{(-(r/c-1)^+)}\cdot\exp\left(\sum_{n=1}^{\infty}w_n\log p_n(\hat{S};S)\right)
$$
where $w$ is a weighting vector, $(\cdot)^+=\max(0,\cdot)$, $c$ is the length of the candidate corpus and $r$ is the effective reference corpus length.

## References
- Original Paper: ["BLEU: a method for automatic evaluation of machine translation"](https://dl.acm.org/doi/10.3115/1073083.1073135)
- Further insights: [Bleu Score and WER Metrics](https://towardsdatascience.com/foundations-of-nlp-explained-bleu-score-and-wer-metrics-1a5ba06d812b)