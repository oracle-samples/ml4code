---
title: CodeBLEU
url: codebleu
weight: 3
math: true
bookHidden: true
---
# CodeBLEU

CodeBLEU surfaced as an attempt to modify BLEU to better fit programming language, since BLEU suffered from a lack of syntax and implementation knowledge. For that, the authors added extra terms to BLEU, defining CodeBLEU as a weighted combination of four parts
$$
    \operatorname{CodeBLEU}=\alpha\cdot\operatorname{BLEU}+\beta\cdot\operatorname{BLEU}_\{weight}+\gamma\cdot\operatorname{Match}\_{ast}+\delta\cdot\operatorname{Match}\_{df}
$$
where $\operatorname{BLEU}$ is the standard metric, and the remaining components will be now explained

## $\operatorname{BLEU}_{weight}$

The original BLEU compares n-grams between the candidate and the reference, and calculates the ratio of matched n-grams. However, in programming languages there is a very small number of keywords, and applying traditional BLEU ignores their importance. So the authors introduce a weighted n-gram match precision, which modifies Equation~\ref{eq:n-gram_precision} by adding a weight $\mu_n^i$, which is 5 times larger for keywords than for other tokens. Additionally, this term is only applied to uni-grams, which is equivalent to setting $w_1=1$ in Equation~\ref{eq:BLEU}.

## $\operatorname{Match}_{ast}$

The code syntactic information is added into CodeBLEU by matching tree structures, namely considering AST structures. Since CodeBLEU only wants to look at syntax, they ignore the AST leaves. To calculate the syntactic AST match score, they start by extracting all sub-trees of the candidate and the reference ASTs. Then the match score is computed via
$$
\operatorname{Match}\_{ast}=\operatorname{Count}\_{clip}(T_{cand})/\operatorname{Count}(T\_{ref})
$$
where $\operatorname{Count}(T_{ref})$ is the total number of reference subtrees, and $\operatorname{Count}\_{clip}(T\_{cand})$ is the number of subtrees that are matched in the reference.

## $\operatorname{Match}_{df}$

Finally, semantic information is further added via data-flow graph comparison. For that, the authors start by extracting the data-flow graphs for the candidate and the reference. Based on AST, they use the leaves to identify a variable sequence $V=\{v\_0,v_1,...,v\_m\}$. The graph is built by adding edges to connect the variables $v\_i,v\_j$ if the value of $v¥_j$ comes from $v\_i$. All names are normalized to $\texttt{var\\_i}$ where $\texttt{i}$ is the order of the variables appearing in the data-flow items.

Finally, the match score is computed as
$$
    \operatorname{Match}\_{df}=\operatorname{Count}\_{clip}(DF_{cand})/\operatorname{Count}(DF_{ref})
$$
where $\operatorname{Count}(DF_{ref})$ is the total number of reference data-flows and $\operatorname{Count}\_{clip}(DF_{cand})$ is the number of matched candidate flows.

- Original Paper: [CodeBLEU: a Method for Automatic Evaluation of Code Synthesis](https://arxiv.org/pdf/2009.10297)
- [A Gentle Introduction to Code Generation Evaluation](https://towardsdatascience.com/a-gentle-introduction-to-code-generation-evaluation-c8dff8c3d19a)