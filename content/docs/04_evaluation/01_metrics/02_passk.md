---
title: pass@k
url: passk
weight: 2
math: true
bookHidden: true
---
# pass@k
Before introducing the immensely popular HumanEval benchmark, most evaluation methods for generated code involved comparing the produced solution with the ground-truth code. The "correctness" is usually quantified using the BLEU score or any other metric that measures the similarity between different sets of texts.

However, evaluating text similarity differs significantly from judging whether a piece of code can solve a given problem. In complex problem settings, the presented solution may deviate entirely from the sample solution from a "text similarity" perspective but be functionally correct. Human programmers tend to use test-driven development for evaluating written code. The program can be considered "correct" if it can pass certain unit tests. [^1]

## success at B (biased pass@k)
The fraction of test examples where the system generates an accepted program under the budget of B trials. Leads to high variance.

## pass@k
Unbiased estimator of success at B:

The goal is to estimate the probability that at least one of the top k samples is correct, given that there are c-correct samples in total out of n-generated samples.

- $n:=$ total number of samples
- $k:=$ number of top samples considered
- $c:=$ number of correct samples

$$pass@k := \underset{\textit{Problems}}{\mathbb{E}} \left[ 1 - \frac{ n - c \choose k }{n \choose k} \right]$$

```
def pass_at_k(n, c, k): 
  """ 
  :param n: total number of samples 
  :param c: number of correct samples 
  :param k: k in pass@$k$ 
  """ 
  if n - c < k: 
    return 1.0 
  return 1.0 - np.prod(1.0 - k / np.arange(n - c + 1, n + 1)
```

- success at B paper: ["SPoC: Search-based Pseudocode to Code"](https://proceedings.neurips.cc/paper/2019/file/7298332f04ac004a0ca44cc69ecf6f6b-Paper.pdf)
- pass@k paper: [Evaluating Large Language Models Trained o nCode](https://arxiv.org/pdf/2107.03374.pdf)
- [leaderboard](https://paperswithcode.com/sota/code-generation-on-humaneval)

[^1]: Some parts of this page are copy pasted from [deepgram - HumanEval: Decoding the LLM Benchmark for Code Generation](https://deepgram.com/learn/humaneval-llm-benchmark)