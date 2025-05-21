# BIRD

BIRD (Big Bench for Large-Scale Database-Grounded Text-to-SQL) is a benchmark designed to evaluate text-to-SQL models in realistic scenarios involving **large-scale, noisy, and complex enterprise databases**. It focuses on testing both accuracy and efficiency in query generation, making it uniquely suited for real-world applications.

---

## Dataset Design

The BIRD dataset consists of **95 databases** sourced from real-world domains such as healthcare, finance, and sports. These databases are significantly more complex than those in previous benchmarks, with an average size of 549,000 rows per database. Additionally, BIRD includes messy, noisy, and unstructured data, reflecting real-world challenges. Tasks often require models to integrate **external knowledge** and reason beyond the database schema.

The benchmark contains **12,751 natural language questions**, each paired with a corresponding SQL query. These queries involve:
- Multi-table joins.
- Aggregations.
- Complex filtering.
- Nested subqueries.

BIRD emphasizes **execution efficiency** by including performance metrics that measure both the correctness and runtime performance of generated SQL queries.

---

## Evaluation Metrics

BIRD uses two key metrics to evaluate the performance of models:

The **Execution Accuracy (EX)** measures how often the SQL query retrieves the correct result. It is defined as:

```math
EX = \frac{\sum_{n=1}^{N} 1(V_n = \hat{V}_n)}{N}
```

Where:  
- $ N $: Total number of tasks.  
- $ V_n $: Correct query result for task $ n $.  
- $ \hat{V}_n $ : Result of the model-generated query.  
- $ 1(V_n = \hat{V}_n) $: Indicator function (1 if results match, 0 otherwise).

The **Valid Efficiency Score (VES)** evaluates the efficiency of the generated SQL, considering both correctness and resource usage (e.g., runtime performance). It is defined as:

```math
VES = \frac{\sum_{n=1}^{N} 1(V_n = \hat{V}_n) \cdot R(Y_n, \hat{Y}_n)}{N}
```

Where:  
- $ R(Y_n, \hat{Y}_n) $ : A runtime-based efficiency ratio comparing the AI-generated SQL $\hat{Y}_n $  to the ground-truth SQL $ Y_n $ .

---

## Key Challenges

BIRD introduces several unique challenges for text-to-SQL models:

1. **Schema Linking:** Models must accurately identify the correct tables and columns in databases with thousands of fields, many of which have inconsistent or ambiguous names.

2. **Messy Data:** Unlike clean, curated benchmarks, BIRD databases include noisy, missing, and unstructured data, making schema understanding and query generation more difficult.

3. **External Knowledge Integration:** Many tasks require reasoning beyond the database, such as interpreting domain-specific terminology or incorporating additional rules and formulas.

4. **Efficiency Requirements:** Models must generate SQL queries that are not only accurate but also optimized for execution on large-scale datasets.

5. **Complex SQL Logic:** Tasks often involve nested queries, advanced aggregations, and multi-step reasoning.

---

## Experimental Results

State-of-the-art models perform significantly worse on BIRD compared to simpler benchmarks. The gap highlights the difficulty of handling real-world complexity and efficiency requirements.

| **Model**          | **Execution Accuracy (EX)** | **Valid Efficiency Score (VES)** |  
|---------------------|-----------------------------|-----------------------------------|  
| GPT-4              | 54.89%                     | 60.77%                           |  
| Claude-2           | 50.31%                     | 57.28%                           |  
| Llama-2            | 33.25%                     | 40.12%                           |  
| Human Performance  | 92.96%                     | 90.27%                           |  

---

## Key Observations

1. **Performance Gap:**  
   - Human performance far exceeds that of AI models, with GPT-4 achieving only 54.89% EX compared to 92.96% for humans.

2. **Schema Linking Errors:**  
   - Errors in linking the correct tables and columns account for 41.6% of failures, underscoring the difficulty of navigating large, complex schemas.

3. **Efficiency Challenges:**  
   - Many models generate SQL queries that are correct but inefficient, resulting in suboptimal runtime performance and lower VES scores.

4. **Complex Query Logic:**  
   - Tasks involving nested queries and advanced reasoning see the highest failure rates.

5. **Need for External Knowledge:**  
   - Models struggle to incorporate domain-specific knowledge or infer rules not explicitly defined in the schema.

---

## References

- Original paper: ["BIRD: A Big Bench for Large-Scale Database-Grounded Text-to-SQL"](https://arxiv.org/abs/2305.03111)  
- Related benchmarks:
  - Spider 1.0: ["Spider: A Large-Scale Dataset for Complex Text-to-SQL"](https://arxiv.org/abs/1809.08887)  
  - Spider 2.0: ["Spider 2.0: Evaluating Language Models on Real-World Enterprise Text-to-SQL Workflows"](https://arxiv.org/abs/2411.07763)  
