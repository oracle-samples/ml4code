# A Deobfuscation Pre-Training Objective for Programming Languages

This paper introduces a new pre-training task, which intends to be a better version of Masked Language Modelling for source code. Instead of simply masking the input, it obfuscates it, forcing the model to learn the code structure.

The obfuscation is done by replacing class, function and variables names with special tokens, and the model is trained to recover the original names. When an identifier is selected, \textit{all} of its instances in the code are replaced by the same special token. This differs from MLM where the name of a variable can appear multiple times while being masked only once. This pre-training task only obfuscates identifiers. Standard functions (like \texttt{append} or \texttt{def}) are left unchanged.

The advantage obfuscation has over MLM is that, by leaving some instances of an identifier unmasked, the model can simply copy identifier names into the masked positions. However, by replacing every instance of an identifier, that becomes impossible, which forces the model to develop a deeper understanding of the problem.

## Data
For pre-training, they used the dataset Google BigQuery. For fine-tuning, they used the CodeXGLUE dataset.

## Pre-Training
The authors trained a seq2seq model with attention using their pre-training objective. 

## Fine-Tuning
For fine-tuning, several tasks were considered. Namely, clone detection,  code summarization, code search and code translation.

## References
- Original Paper: ["DOBF: A Deobfuscation Pre-Training Objective for Programming Languages"](https://arxiv.org/abs/2102.07492)
- AdvTest dataset: ["CodeXGLUE: A Machine Learning Benchmark Dataset for Code Understanding and Generation"](https://arxiv.org/abs/2102.04664)
- [Google BigQuery](https://console.cloud.google.com/marketplace/details/github/github-repos)