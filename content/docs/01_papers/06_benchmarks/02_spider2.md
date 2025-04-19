---
title: "Spider 2.0"  
math: true  
weight: 2  
url: spider2overview  
---

# Spider 2.0

Spider 2.0 is a next-generation benchmark designed to evaluate the capabilities of text-to-SQL models in handling **real-world enterprise data workflows**. It goes beyond traditional benchmarks by introducing complex tasks that require multi-step reasoning, SQL dialect adaptation, and integration with external tools.

---

## Dataset Design

Spider 2.0 includes 213 enterprise-grade databases sourced from platforms like BigQuery, Snowflake, SQLite, DuckDB, and PostgreSQL. These databases are significantly larger and more complex than those used in previous benchmarks, with an average of 743 columns per database. Many of the databases feature nested schemas, partitioned tables, and other advanced structures that reflect real-world enterprise scenarios. Tasks in Spider 2.0 also require models to handle diverse SQL dialects, including platform-specific syntax such as BigQuery’s `DATE_TRUNC` for date aggregation and Snowflake’s `ST_DISTANCE` for geospatial operations.

The benchmark is designed around 632 tasks that simulate realistic data workflows. These tasks span various operations, including data wrangling (cleaning and restructuring data), data transformation (applying multi-step operations), and data analysis (producing business insights). In addition, tasks often require external knowledge, such as schema documentation, project codebases, and metadata. This inclusion ensures the benchmark reflects the complexity and challenges faced in actual enterprise environments.

Many tasks involve multi-step workflows, where models must iteratively refine queries and integrate them with external tools like Python scripts or DBT pipelines. The SQL queries in Spider 2.0 frequently involve advanced features such as window functions, subqueries, geospatial operations, and recursive queries.

---

## Variants of Spider 2.0

Spider 2.0 offers three variants tailored to different research and application needs. The full version simulates end-to-end workflows, combining SQL generation with external tools such as Python. In this variant, models must interpret documentation and metadata to understand schema details. The Lite version focuses solely on SQL query generation, with preprocessed schema descriptions to simplify the task. Finally, Spider 2.0-Snow is specialized for Snowflake-hosted databases, emphasizing challenges related to SQL dialect adaptation.

---

## Evaluation Metrics

Spider 2.0 uses two key metrics to evaluate model performance.

The **Success Rate (SR)** measures how often the model completes a multi-step task successfully. It is calculated as:

$$
SR = \frac{\text{Number of Successfully Completed Tasks}}{\text{Total Number of Tasks}}
$$

The **Execution Accuracy (EX)** assesses whether the generated SQL produces the correct result. It is defined as:

$$
EX = \frac{\sum_{n=1}^{N} 1(V_n = \hat{V}_n)}{N}
$$

Where:
- \( N \): Total number of tasks.
- \( V_n \): Correct query result for task \(n\).
- \( \hat{V}_n \) \: Result of the model-generated query.
- \( 1(V_n = \hat{V}_n) \) : Indicator function (1 if results match, 0 otherwise).

---

## Key Challenges

Spider 2.0 introduces several unique challenges. One major challenge is schema linking, which requires identifying relevant tables and columns in large, noisy databases with inconsistent naming conventions. For example, a question about "yearly revenue" might need to link to a table with columns named `total_sales` and `fiscal_year`.

Another challenge is adapting to diverse SQL dialects. Platforms like BigQuery and Snowflake use different syntax for similar operations, such as `DATE_TRUNC('month', timestamp)` in BigQuery versus `DATE_PART('month', timestamp)` in Snowflake. Models must handle these variations seamlessly.

Workflow complexity is another obstacle. Many tasks require reasoning across multiple steps, including generating intermediate queries and refining results iteratively. For instance, calculating retention rates for user cohorts might involve multiple joins and nested aggregations.

Finally, efficiency remains a significant issue. Models must generate SQL queries that are not only correct but also optimized for execution on terabyte-scale datasets.

---

## Experimental Results

The performance of state-of-the-art models on Spider 2.0 reveals significant challenges. The best-performing model, o1-Preview, achieves a Success Rate (SR) of only 17.01%, while human performance exceeds 92%. Execution Accuracy (EX) on Spider 2.0-Lite tasks is similarly low, with even the top models scoring under 6%. 

| **Model**           | **Spider 2.0 (SR)** | **Spider 2.0-Lite (EX)** |  
|----------------------|---------------------|--------------------------|  
| o1-Preview          | 17.01%             | 5.68%                   |  
| GPT-4               | 8.86%              | 3.84%                   |  
| Claude-3.5-Sonnet   | 9.02%              | 3.68%                   |  
| Qwen2.5-72B         | 6.17%              | 2.53%                   |  
| Human Performance   | 92.96%             | 90.27%                  |  

These results highlight several critical observations. There is a substantial gap between human and AI performance, underscoring the complexity of Spider 2.0 tasks. Models struggle particularly with schema linking, handling large, nested schemas and partitioned tables. SQL dialect adaptation also poses a significant challenge, with notable performance drops on Snowflake-specific tasks. Moreover, multi-step tasks that require external documentation or metadata remain especially difficult for current models.

---

## References

- Original paper: ["Spider 2.0: Evaluating Language Models on Real-World Enterprise Text-to-SQL Workflows"](https://arxiv.org/abs/2411.07763)  
- Related benchmarks:
  - Spider 1.0: ["Spider: A Large-Scale Dataset for Complex Text-to-SQL"](https://arxiv.org/abs/1809.08887)  
  - BIRD: ["A Big Bench for Large-Scale Database-Grounded Text-to-SQLs"](https://arxiv.org/abs/2305.03111)  
