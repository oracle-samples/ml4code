# CodeBERT

CodeBERT is a bimodal pre-trained model for programming languages (PL) and natural language (NL). It is based on a Transformer architecture, and trained with a hybrid objective function with two tasks: Masked Language Modelling (MLM) and Replace Token Detection (RTD). MLM allows them to use the bimodal data, while RTD exploits both.

## Pipeline Overview

The model takes as input one natural text segment $w$ and one code segment $c$. The NL segment is split into tokens using WordPiece and code is regarded as a sequence of tokens. These are concatenated into $\{[CLS],w_1,w_2,...,w_{|w|},[SEP],c_1,c_2,...,c_{|n|},[EOS]\}$ and sent through the model. The output is the contextual vector representation of each token as well as the representation of the $[CLS]$ token.

## Data

For pre-training, the model used the CodeSearchNet Challenge dataset, which contains 2.1 million bimodal datapoints and 6.4 unimodal codes across six programming languages. For fine-tuning, they used the CodeSearchNet Corpus for natural language code search.

## Pre-Training

The pre-training is done using datapoints from Github repositories, where each bimodal datapoint is an individual function with paired documentation, and each unimodal code is a function without paired documentation. The dataset used is the CodeSearchNet challenge.

CodeBERT was pretrained on two objectives [^1].

[^1]: The notations and explanations introduced in the code are sometimes messy and sometimes unclear. So, since the pre-training codes are not provided in the [GitHub Repo](https://github.com/microsoft/CodeBERT), the analysis performed here may not exactly match the actual pre-training executed.

## Masked Language Modelling (MLM)

Consider an input datapoint $x=\{w,c\}$, containing an NL-PL pair. $w$ is a sequence of NL words and $c$ is a sequence of PL tokens. We randomly select 15\% of the tokens from the concatenated input and replace them with $[MASK]$ token. 

The objective of MLM is to predict the original tokens that were masked out. So, the objective function can be written as
```math
    \mathcal{L}_{MLM}=-\sum_i^M\log p^{MLM}(x_i|w^m,c^m)
```
where $M=w^m\cup c^m$ is the set of masked tokens.

## Replace Token Detection (RTD)

The previous task only harnessed bimodal data. RTD allows to use both bimodal and unimodal datapoints. This task relies on two generators, an NL generator $G_w$ and a PL generator $G_c$, for generating plausible alternatives for the set of randomly masked positions. 

The discriminator is trained to determine whether a word is the original or not, i.e. a binary classification problem. The RTD objective is applied to every token of the input, which gets a label $y_i^{RTD}$ that is 1 if the token has been replaced or 0 if it has not. So, the objective function becomes

```math
    \mathcal{L}_{RTD}=-\sum_i^M\left[ y_i^{RTD}\log p^{RTD}(x_i|w^r,c^r) + (1-y_i^{RTD})\log(1-p^{RTD}(x_i|w^r,c^r)) \right]
```

where $M=w^r\cup c^r$ is the set of replaced tokens.

The generators were implemented using n-gram language models learned from the unimodal datapoints. The unimodal PL data come from the unimodal codes from the dataset, while the unimodal NL data come from the documentation of the bimodal data. 

The final loss function is thus
```math
\mathcal{L}= \mathcal{L}_{MLM} + \mathcal{L}_{RTD}
```

## Fine-Tuning

### Natural Language Code Search

In this task, the original authors used the CodeSearchNet corpus, and for metric they used Mean Reciprocal Rank. The goal of this task is to find the most semantically related code to a given NL input.

The model was fine-tuned for each language, as they saw a multi-lingual model performed worse that several language-specific models. Each model was trained with a binary classification loss function, where a softmax layer is connected to the representation of $[CLS]$.

### Code Documentation Generation

This task is performed by using Seq2Seq, and using the CodeBERT as the encoder. The decoder is a standard Transformer decoder. Then the global model is trained using a standard cross-entropy loss, which fine-tunes CodeBERT.

## References

- Original paper: ["CodeBERT: A Pre-Trained Model for Programming and Natural Languages"](https://arxiv.org/abs/2002.08155)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search"](https://arxiv.org/abs/1909.09436)