---
title: Valid Efficiency Score
url: validefficiencyscore
weight: 7
math: true
bookHidden: true
---

# Valid Efficiency Score


The **Valid Efficiency Score (VES)** is a novel evaluation metric introduced by the BIRD benchmark to assess the performance of text-to-SQL models. Unlike traditional metrics that only evaluate correctness (e.g., Execution Accuracy), VES incorporates the **efficiency** of the generated SQL queries, making it highly relevant for real-world database applications where runtime performance is critical.


The Valid Efficiency Score evaluates how well a model-generated SQL query ($\hat{Y}_n$) performs relative to the ground-truth query ($Y_n$) in terms of **correctness** and **efficiency**. It is defined as:

$$
VES = \frac{\sum_{n=1}^{N} 1(V_n = \hat{V}_n) \cdot R(Y_n, \hat{Y}_n)}{N}
$$

Where:  
- $N$: Total number of tasks in the benchmark.  
- $V_n$: The correct result of the SQL query for task $n$.  
- $\hat{V}_n$: The result of the model-generated SQL query for task $n$.  
- $1(V_n = \hat{V}_n)$: An indicator function that returns 1 if $V_n$ matches $\hat{V}_n$, and 0 otherwise.  
- $R(Y_n, \hat{Y}_n)$: A runtime-based efficiency ratio that compares the performance of the ground-truth query ($Y_n$) and the model-generated query ($\hat{Y}_n$).  


## Key Components

### **Correctness Component**
The first component, $1(V_n = \hat{V}_n)$, ensures that only **valid SQL queries** are considered for the efficiency evaluation. If a model-generated query retrieves incorrect results, it is automatically excluded from the VES calculation.

### **Efficiency Ratio**
The second component, $R(Y_n, \hat{Y}_n)$, measures the relative runtime efficiency of the model-generated query compared to the ground-truth query. This ratio is defined as:

$$
R(Y_n, \hat{Y}_n) = \sqrt{\frac{\text{Runtime}(Y_n)}{\text{Runtime}(\hat{Y}_n)}}
$$

Where:  
- $\text{Runtime}(Y_n)$: Execution time of the ground-truth query.  
- $\text{Runtime}(\hat{Y}_n)$: Execution time of the model-generated query.  

The square root ensures that outliers in runtime differences do not overly skew the VES calculation.

## Importance of VES

Traditional metrics like Execution Accuracy (EX) focus solely on whether the generated SQL produces the correct result. However, in real-world scenarios, efficiency is equally critical. Consider the following scenarios:

1. A correct query that takes **10x longer** to execute than the ground-truth query is impractical in large-scale databases.
2. In enterprise applications, **execution time** directly impacts system performance, costs, and user experience.

By incorporating efficiency, VES provides a more comprehensive evaluation of model performance.


## Applications of VES

### **Benchmarking Model Performance**
VES is particularly useful for evaluating:
- **Large-scale queries** that operate on billions of rows.
- **Enterprise-grade databases** where runtime performance is a critical factor.

### **Identifying Inefficient Query Generation**
By analyzing VES scores, researchers can identify cases where models generate syntactically correct but computationally inefficient queries.


- Original Paper: ["BIRD: A Big Bench for Large-Scale Database-Grounded Text-to-SQL"](https://arxiv.org/abs/2305.03111)  
  