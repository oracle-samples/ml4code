---
title: "GraphCodeBERT"
math: true
url: graphcodebert
weight: 6
---

# GraphCodeBERT

GraphCodeBERT is a pre-trained model for programming languages, that uses data flows instead of ASTs to capture the code structure. Data flows are a less complex data structure that ASTs, which makes the model more efficient.

## Pipeline Overview

The model takes as input a source code \(C\) and its associated natural language comment \(W\). The source is converted into an AST, which is then converted into a data flow graph. For that, the AST terminals are used to identify the variable sequence \(V\) (nodes of the graph), and the edges are built to indicate where each value comes from. The input is then concatenated into \(X=\{[CLS],W,[SEP],C,[SEP],V\}\). 
The attention layers are modified so that the nodes can only attend to other nodes they are connected to in the data flow. Other than that, the mechanism is the same as the standard transformer, and the output is the contextual representations as well as the \([CLS]\) representations.

## Data

GraphCodeBERT is pre-trained on the CodeSearchNet Challenge dataset, which includes 2.3 million functions of six programming languages, with natural language documentation. For fine-tuning, the authors considered several tasks. For natural language code search, the authors used the CodeSearchNet code corpus. For code clone detection, they used the BigCloneBench dataset. For code translation, they used a custom dataset of function crawled from open-source projects.

## Input Representation

A data flow is a graph that represents dependencies relations between variables, where nodes represent variables and directed edges represent where the value of each variable comes from. 

Given a source code \(C=\{c_1,c_2,...,c_n\}\), to extract the data flow we first need to convert the source code into its AST. The AST terminals (leaves) are used to identify the variable sequence \(V=\{v_1,v_2,...,v_k\}\). Each variable is taken as a node of the graph, and a directed edge \(\epsilon=\langle v_i, v_j\rangle\) from \(v_i\) to \(v_j\) indicates that the value of the \(j\)-th variable comes from the \(i\)-th variable. Letting the set of directed edges be \(E=\{\epsilon_1,\epsilon_2,...,\epsilon_n\}\), the graph \(\mathcal{G}(C)=(V,E)\) is the data flow representation of the source code \(C\). 

The model additionally takes as input the code tokens and the natural language comment \(W\) associated to the code. The input then becomes \(X=\{[CLS],W,[SEP],C,[SEP],V\}\). The edges are not directly sent through the model, but used later to define the graph-guided masked attention filter.

## Model Architecture

Each token from the input is converted into the corresponding input vector by summing its token and position embeddings. The variable take a special position embedding to indicate they are nodes of data flow. The model then follow a standard Transformer encoder architecture, with a modified attention layer. For the \(n\)-th layer, the output of the multi-head self attention for head \(i\) is computed via
$$
head_i = \operatorname{softmax}\left(\frac{Q_iK_I^T}{\sqrt{d_k}}+M\right)V_i
$$
where \(d_k\) is the dimension of the head and \(M\in\mathbb{R}^{|X|\times|X|}\) is a mask matrix. If the \(i\)-th token is allowed to attend to the \(j\)-th token, then \(M_{ij}\) takes the value 0. Otherwise, it takes the value \(-\infty\).

To incorporate the graph structure into the Transformer, the authors define a graph-guided masked attention function to filter out irrelevant signals. To represent dependency relation between variables, a node-query \(q_{v_i}\)is allowed to attend to a node-key \(k_{v_j}\) if there is a direct edge from the node \(v_j\) to the node \(v_i\) or if they are the same node. Let \(E'\) be the set containing \(\langle v_i, c_j\rangle\) and \(\langle c_j, v_i\rangle\) if the variable \(v_i\) is identified from the source code token \(c_j\). So, node \(q_{v_i}\) and code \(k_{c_j}\) can only attend to each other if \(\langle v_i, c_j\rangle ,\langle c_j, v_i\rangle\in E'\). Formally, the mask matrix \(M\) is defined as

$$
M\_{ij}=
\left\{
\begin{array}{ll}
    0 \quad\quad \text{ if } q_i \in \{[CLS],[SEP]\} \text{ or } q_i, k_j \in W \cup C \text{ or } \langle q_i,k_j \rangle \in E \cup E' \\
    -\infty \quad \text{otherwise}
\end{array}
\right.
$$

## Pre-Training

GraphCodeBERT uses three pre-training tasks: masked language modelling, edge prediction and node alignment.

### Masked Language Modelling

For this task, the authors followed the same procedure as BERT. For that they sample 15\% of the tokens from the source code and paired comment. From these, 80\% are replaced with a \([MASK]\) token, 10\% with a random token and 10\% are left unchanged. The MLM objective is to predict original tokens from the masked tokens. In particular, the model can leverage comment context is the source code is not sufficient to infer the masked code context, which encourages the model to align the NL and PL representations.

### Edge Prediction

The motivation of this pre-training task is to encourage the model to learn structure-aware representations that encode the relation of "where-the-value-comes-from" for better code understanding. For that, they randomly sample 20\% of the nodes \(V_s\) in the data flow and mask direct edges connecting these sampled nodes by adding \(-\infty\) to the mask matrix. Then the task becomes predicting these masked edges \(E_{mask}\). Letting \(E_c=V_s\times V\cup V\times V_s\) be a set of candidates for edge prediction, and \(y_{ij}\) being 1 if \(\langle v_i,v_j\rangle\in E\) and 0 otherwise, then the loss function becomes

$$
\mathcal{L}_{EP}=-\sum_{e_{ij}\in E_c}[y_{ij}\log p_{ij}+(1-y_{ij})\log(1-p_{ij})]
$$

where \(p_{ij}\) is the probability of existing an edge from \(i\)-th to \(j\)-th node, calculated by the dot product followed by a sigmoid function using representations of the two nodes from GraphCodeBERT.

## Node Alignment

The goal of this task is to align representations between source code and data flow, implemented by predicting edges between code tokens and nodes. For that, they randomly sample 20\% of the nodes \(V_s'\) and mask edges between code tokens and sampled nodes. The task is then to predict the masked edges \(E_{mask}'\). The pre-training objective of this task is similar to \(\mathcal{L}_{EP}\), where \(E_c'=V_s'\times C\) is now the set of candidates for node alignment. The loss function is
$$
\mathcal{L}_{NA}=-\sum_{e_{ij}\in E_c'}[y_{ij}\log p_{ij}+(1-y_{ij})\log(1-p_{ij})]
$$

## References

- Original Paper: ["GraphCodeBERT: Pre-training Code Representations with Data Flow"](https://arxiv.org/abs/2009.08366)
- CodeSearchNet Challenge: ["CodeSearchNet Challenge: Evaluating the State of Semantic Code Search"](https://arxiv.org/abs/1909.09436)
- BigCloneBench: ["Towards a Big Data Curated Benchmark of Inter-project Code Clones"](https://ieeexplore.ieee.org/document/6976121)
