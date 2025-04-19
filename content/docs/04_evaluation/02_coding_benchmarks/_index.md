---
title: Coding Benchmarks
url: coding-benchmarks
weight: 1
---

## Coding Benchmarks

|Title|Description|Links|
|---|---|---|
|[Aider Benchmarks](https://aider.chat/docs/leaderboards/)| [Code Editing](https://aider.chat/docs/leaderboards/edit.html), [Code Refactoring](https://aider.chat/docs/leaderboards/refactor.html), [Polyglot Exercism](https://aider.chat/2024/12/21/polyglot.html#the-polyglot-benchmark)|[GitHub](https://github.com/Aider-AI/polyglot-benchmark)|
|[AppBench](https://arxiv.org/abs/2410.19743)|||
|[APPS](https://arxiv.org/pdf/2105.09938.pdf)|10'000 Python programming problems from public coding platforms.. Measures average of passing test cases and | [Data](https://huggingface.co/datasets/codeparrot/apps)|
|[aider Code Editing Benchmark](https://aider.chat/docs/benchmarks.html)|The benchmark uses 133 practice exercises from the Exercism python repository. These exercises were designed to help individuals learn Python and hone their coding skills.|[GitHub](https://github.com/paul-gauthier/aider/tree/main/benchmark)|
|[BaxBench](https://arxiv.org/abs/2502.11844)|392 tasks each requiring the LLM to fully implement a correct and secure backend application|[Website](https://baxbench.com/), [GitHub](https://github.com/logic-star-ai/baxbench)|
|[BigCodeBench](https://arxiv.org/pdf/2406.15877)|challenges LLMs to invoke multiple function calls as tools from 139 libraries and 7 domains for 1,140 f ine-grained tasks. To evaluate LLMs rigorously, each task encompasses 5.6 test cases with an average branch coverage of 99%|[GitHub](https://github.com/bigcode-project/bigcodebench), [HuggingFace](https://huggingface.co/datasets/Rtian/DebugBench), [Website](https://bigcode-bench.github.io/)|
|[ClassEval](https://mingwei-liu.github.io/assets/pdf/ICSE2024ClassEval-V2.pdf)|100 class-level Python code generation tasks evaluate the model's capability of generating a class of multiple interdependent methods for the given natural language description |[GitHub](https://github.com/FudanSELab/ClassEval)|
|[COBOLEval](https://github.com/BloopAI/cobolEval)|ranspilation of the HumanEval benchmark from Python into COBOL|[GitHub](https://github.com/BloopAI/cobolEval)|
|[CodeElo](https://arxiv.org/pdf/2501.01257v2)|Recent six months of contest problems on CodeForces|[GitHub](https://github.com/QwenLM/CodeElo), [Website](https://codeelo-bench.github.io/)|
|[CodeEval Pro](https://arxiv.org/abs/2412.21199)|MBPP Pro and HumanEval Pro|[Data](https://github.com/CodeEval-Pro/CodeEval-Pro/tree/main), [Site](https://answers111.github.io/evalpro.github.io/)|
|[CodeRAG-Bench](https://arxiv.org/abs/2406.14497)|Benchmark for RAG-based code generation|[Website](https://code-rag-bench.github.io/)|
|[CoderEval](https://arxiv.org/pdf/2302.00288.pdf)|230 Python and 230 Java code generation problems carefully curated from popular real world open-source projects and a self-contained execution platform to automatically assess the functional correctness of generated code|-|
|[Code Lingua](https://arxiv.org/pdf/2308.03109)| Transpilation with 1'700 code samples from and to C, C++, Go, Java as well as Python|[Website](https://codetlingua.github.io/leaderboard.html)|
|[CodeXGLUE](https://arxiv.org/pdf/2102.04664.pdf)|Includes 14 datasets for 10 diversified code intelligence tasks covering the following scenarios: code-code (clone detection, defect detection, cloze test, code completion, code repair, and code-to-code translation), text-code (natural language code search, text-to-code generation), code-text (code summarization), text-text (documentation translation)|[arXiv](https://github.com/microsoft/CodeXGLUE)|
|[CrossCodeEval](https://arxiv.org/abs/2310.11248)| Using real-world repositories in Python, Java, TypeScript, and C# checking for accurate code-completion over a cross-file context|[Website](https://crosscodeeval.github.io/) [GitHub](https://github.com/amazon-science/cceval)|
|[CRUXEval](https://arxiv.org/pdf/2401.03065)|Code Reasoning, Understanding, and eXecution Evaluation - 800 Python functions: Each function comes with an input-output pair, leading to two natural tasks: input prediction and output prediction.|[GitHub](https://crux-eval.github.io/)|
|[DS-1000](https://arxiv.org/pdf/2211.11501)|Python 1000 problems originating from 451 unique StackOverflow problems|[GitHub](https://github.com/xlang-ai/DS-1000/tree/main), [Data](https://github.com/HKUNLP/DS-1000/blob/main/ds1000_data.zip)|
|[Decompile-Eval](https://arxiv.org/pdf/2403.05286.pdf)|HumanEval benchmark converted into C to evaluate decompilation pipelines|[GitHub](https://github.com/albertan017/LLM4Decompile/tree/main/decompiler-eval)|
|[DebugBench](https://arxiv.org/pdf/2401.04621.pdf)|4253 instances, 4 bug categories, 18 minor types, C++, Java, Python, easy, medium, hard|[GitHub](https://github.com/thunlp/DebugBench), [HuggingFace](https://huggingface.co/datasets/Rtian/DebugBench)|
|[EquiBench](https://arxiv.org/pdf/2502.12466)|Benchmarking Code Reasoning Capabilities of Large Language Models via Equivalence Checking|
|[EvalPERF](https://openreview.net/forum?id=IBCBMeAhmC)|118 performance-exercising tasks where code efficieincy is measured via Differencial Performance Score (DPS)|[Website](https://evalplus.github.io/evalperf.html), [GitHub](https://github.com/evalplus/evalplus)|
|[EvalPlus](https://openreview.net/forum?id=1qvx610Cu7)|extends the test-cases of HumanEval by 80×|[Website](https://evalplus.github.io/leaderboard.html)|
|[EvoEval](https://evo-eval.github.io/)|transforms and evolves existing coding benchmarks (e.g, HumanEval) into different problem classes such as difficult, creative, subtle, combine and tool use. |[Website](https://evo-eval.github.io/)|
|[HumanEval](https://arxiv.org/pdf/2107.03374.pdf)|164 hand-crafted Python programming challanges and corresponding unit tests|[Data](https://huggingface.co/datasets/openai_humaneval), [Julia Version](https://github.com/01-ai/HumanEval.jl)|
|[InfiBench](https://arxiv.org/abs/2404.07940)|234 carefully selected Stack Overflow questions that span across 15 programming languages - keyword matching, blank filling, unit testing and dialogue similarity|[Website](https://infi-coder.github.io/infibench/)|
|[LiveCodeBench](https://arxiv.org/abs/2403.07974)|Continous problem collection and evaluation from LeetCode, AtCoder, and CodeForces|[Website](https://livecodebench.github.io/), [GitHub](https://github.com/LiveCodeBench/LiveCodeBench)|
|[LiveSWEBench](https://liveswebench.ai/)|Evaluates the software engineering capabilities of AI agent applications with three criteria: autonomy, editing capabilities as well as autocompletion capabilities.|[Website](https://liveswebench.ai/), [GitHub](https://github.com/livebench/liveswebench)|
|[MBPP](https://arxiv.org/abs/2108.07732)|Mostly Basic Python Problems - 1,000 crowd-sourced Python programming problems|[Data](https://huggingface.co/datasets/mbpp)|
|[MultiPL-E](https://arxiv.org/pdf/2208.08227.pdf)|HumanEval translated to 18 other programming languages.|[GitHub](https://github.com/nuprl/MultiPL-E)|
|[NaturalCodeBench](https://arxiv.org/pdf/2405.04520)|402 high-quality problems in Python and Java, meticulously selected from natural user queries from online coding services, covering 6 different domains measuring pass@1 and a custom test case construction score|[GitHub](https://github.com/THUDM/NaturalCodeBench)|
|[OOP](https://aclanthology.org/2024.findings-acl.808.pdf)|431 Python programs that encompass essential OOP concepts and features like classes and encapsulation methods - pass@o metric derived from pass@k but specialized for OOP concepts|[GitHub](https://github.com/alphadl/OOP-eval)|
|[RepoBench](https://arxiv.org/abs/2306.03091)|Python and Java - measures a combination of both, code retrieval and next line prediction in cross-file contexts|[GitHub](https://github.com/Leolty/repobench)|
|[SAFIM](https://arxiv.org/pdf/2403.04814v2)|syntax-aware completions of program structures such as code blocks and conditional expressions, and includes 17,720 examples from multiple programming languages|[GitHub](https://github.com/gonglinyuan/safim), [Website](https://safimbenchmark.com/)|
|[SWE-Bench](https://arxiv.org/pdf/2310.06770.pdf)|2,294 software engineering problems drawn from real GitHub issues and corresponding pull requests across 12 popular Python repositories| [Leaderboard](https://www.swebench.com/), [GitHub](https://github.com/princeton-nlp/SWE-bench)|
|[SWE-Bench Verified](https://openai.com/index/introducing-swe-bench-verified/)|a subset of the original test set from SWE-bench, consisting of 500 samples verified to be non-problematic by our human annotators.|[Dataset](https://huggingface.co/datasets/princeton-nlp/SWE-bench_Verified)|
|[SWE-bench Multimodal](https://arxiv.org/pdf/2410.03859)|SWE-bench M features 617 task instances collected from 17 JavaScript libraries used for web interface design, diagramming, data visualization, syntax highlighting, and interactive mapping. Each SWE-bench M task instance contains at least one image in its problem statement or unit tests.|[Leaderboard](https://www.swebench.com/multimodal.html), [dataset](https://huggingface.co/datasets/princeton-nlp/SWE-bench_Multimodal)|
|[SWE-Lancer](https://arxiv.org/abs/2502.12115)|1400 freelance software engineering tasks from Upwork, valued at $1 million USD total in real-world payouts|[GitHub](https://github.com/openai/SWELancer-Benchmark)|
|[SWT-Bench](https://arxiv.org/pdf/2406.12952)|SWE-Bench for Test Generation|[Website](https://swtbench.com/), [GitHub](https://github.com/logic-star-ai/SWT-Bench)|
|[UNITE](https://arxiv.org/pdf/2305.16265.pdf)|This benchmark is composed of 18 publicly available text-to-SQL datasets, containing natural language questions from more than 12 domains, SQL queries from more than 3.9K patterns, and 29K databases.|[data](https://github.com/awslabs/unified-text2sql-benchmark)|
|[τ-bench](https://arxiv.org/abs/2406.12045)|User-Agent interaction for coding|[GitHub](https://github.com/sierra-research/tau-bench)|

### Leaderboards

- [Aider](https://aider.chat/docs/leaderboards/)
- [HuggingFace - Big Code Models](https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard)
- [BigCodeBench](https://bigcode-bench.github.io/)
- [CodeElo](https://codeelo-bench.github.io/#leaderboard-table)
- [LiveBench](https://livebench.ai/#/)
- [EvalPlus](https://evalplus.github.io/leaderboard.html)
- [Spider 2.0](https://spider2-sql.github.io/)
- [LiveCodeBench](https://livecodebench.github.io/leaderboard.html)
- [LiveSWEBench](https://liveswebench.ai/)
- [SWE-Bench](https://www.swebench.com/#verified)
- [SWT-Bench](https://swtbench.com/?results=lite)
- [BaxBench](https://baxbench.com/)

## SQL

|Title|Description|Link|
|---|---|---|
|[BIRD-SQL](https://bird-bench.github.io/)|A Big Bench for Large-Scale Database Grounded Text-to-SQLs. BIRD contains over 12,751 unique question-SQL pairs, 95 big databases with a total size of 33.4 GB. It also covers more than 37 professional domains, such as blockchain, hockey, healthcare and education, etc.|[Leaderboard](https://bird-bench.github.io/), [arXiv](https://arxiv.org/pdf/2305.03111.pdf), [code](https://github.com/AlibabaResearch/DAMO-ConvAI/tree/main/bird)|
|[Spider 1.0](https://arxiv.org/abs/1809.08887)|10,181 questions and 5,693 unique complex SQL queries on 200 databases with multiple tables, covering 138 different domains|[GitHub](https://github.com/taoyds/spider), [Leaderboard](https://yale-lily.github.io/spider)|
|[Spider 2.0](https://spider2-sql.github.io/)|595 real-world text-to-SQL workflow problems derived from enterprise-level database use cases. The databases in Spider 2.0 are sourced from real data applications, often containing over 1,000 columns and stored in local or cloud database systems such as BigQuery and Snowflake.|[Paper](https://openreview.net/pdf?id=XmProj9cPs), [GitHub](https://github.com/xlang-ai/Spider2) , [Leaderboard](https://spider2-sql.github.io/)|
|[SParC 1.0](https://arxiv.org/pdf/1906.02285)| 4,298 coherent question sequences (12k+ individual questions annotated with SQL queries). It is obtained from controlled user interactions with 200 complex databases over 138 domains.|[Leaderboard](https://yale-lily.github.io/sparc), [GitHub](https://github.com/taoyds/sparc)|
|[CoSQL 1.0](https://arxiv.org/pdf/1909.05378)|It is the dialogue version of the Spider and SParC tasks. CoSQL consists of 30k+ turns plus 10k+ annotated SQL queries, obtained from a Wizard-of-Oz collection of 3k dialogues querying 200 complex databases spanning 138 domains.|[Leaderboard](https://yale-lily.github.io/cosql)|
|[SQLEval](https://defog.ai/blog/open-sourcing-sqleval/)|based off the schema from the Spider, but with a new set of hand-selected questions and queries grouped by query category|[GitHub](https://github.com/defog-ai/sql-eval)|
|[WikiSQL](http://arxiv.org/abs/1709.00103)|A large crowd-sourced dataset for developing natural language interfaces for relational databases. Evaluation with logical form and execution accuracy.|[GitHub](https://github.com/salesforce/WikiSQL)|
|[BEAVER](https://peterbaile.github.io/beaver/)|BEAVER: An Enterprise Benchmark for Text-to-SQL - 203 queries - 6 enterprise-grade databased. Measures execution accuracy.|[arXiv](https://arxiv.org/pdf/2409.02038), [GitHub](https://github.com/peterbaile/beaver)|
|[BIRD-CRITIC](https://bird-critic.github.io/)|The benchmark comprises 600 tasks for development and 200 held-out out-of-distribution (OOD) tests. SQL dialects: MySQL, PostgreSQL, SQL Server, and Oracle.|[Leaderboard](https://bird-critic.github.io/)|

### Metrics 

|Title|Description|Link|
|---------|--------------------|---|
|Exact Execution (EX)|||
|Component Matching|||
|Valid Efficiency Score (VES)|||
|Edit similarity|-|-|