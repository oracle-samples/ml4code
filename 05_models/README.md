# Models

## Code

|Name|Parameter Size|Comments|License|Links|
|-|-|-|-|-|
|[StarCoder2](https://arxiv.org/pdf/2402.19173.pdf)|3B/7B/15B|[TechCrunch](https://techcrunch.com/2024/02/28/starcoder-2-is-a-code-generating-ai-that-runs-on-most-gpus/)|[BigCode Open RAIL-M v1 License Agreement](https://huggingface.co/spaces/bigcode/bigcode-model-license-agreement)|HuggingFace-{[15B](https://huggingface.co/bigcode/starcoder2-15b),[7B](https://huggingface.co/bigcode/starcoder2-7b),[3B](https://huggingface.co/bigcode/starcoder2-3b)}, [Code](https://github.com/bigcode-project/starcoder2)|
|[StarCoder](https://arxiv.org/pdf/2305.06161.pdf)|1B/3B/7B|-|[BigCode Open RAIL-M v1 License Agreement](https://huggingface.co/spaces/bigcode/bigcode-model-license-agreement)|HuggingFace-{[1B](https://huggingface.co/bigcode/starcoderbase-1b),[3B](https://huggingface.co/bigcode/starcoderbase-3b),[7B](https://huggingface.co/bigcode/starcoderbase-7b)}, [Code](https://github.com/bigcode-project/starcoder/tree/main)|
|[DeepSeek Coder v2](https://arxiv.org/pdf/2401.14196.pdf)|16B/236B|Advanced model for deep code generation tasks|[Deepseek License Agreement](https://github.com/deepseek-ai/DeepSeek-LLM/blob/main/LICENSE-MODEL)|[HuggingFace](https://huggingface.co/deepseek-ai), [Code](https://github.com/deepseek-ai/deepseek-coder/)|
|[CodeLlama](https://arxiv.org/pdf/2308.12950.pdf)|7B-70B|Optimized for high-performance code generation|[LLAMA 2 Community License Agreement](https://github.com/facebookresearch/llama/blob/main/LICENSE)|[HuggingFace](https://huggingface.co/codellama), [Code (Inference)](https://github.com/facebookresearch/codellama)|
|[CodeGeeX](https://arxiv.org/abs/2303.17568)|-|-|Apache-2.0|[Code](https://github.com/THUDM/CodeGeeX)|
|[CodeShell](https://github.com/WisdomShell/codeshell)|-|-|[CodeShell Model License Agreement](https://github.com/WisdomShell/codeshell/blob/main/License.pdf)|[HuggingFace](https://huggingface.co/WisdomShell), [Code](https://github.com/WisdomShell/codeshell/blob/main/README_EN.md)|
|[CodeGemma](https://storage.googleapis.com/deepmind-media/gemma/codegemma_report.pdf)|2B/7B|Focused on generating syntactically accurate code|[Gemma Terms of Use](https://ai.google.dev/gemma/terms)|[HuggingFace](https://huggingface.co/blog/codegemma), [Models](https://huggingface.co/collections/google/codegemma-release-66152ac7b683e2667abdee11)|
|[StableCode](https://stability.ai/news/stable-code-2024-llm-code-completion-release)|3B|Designed for stability and reliability|[Stability AI Non-Commercial Research Community License Agreement](https://huggingface.co/stabilityai/stable-cascade/blob/main/LICENSE)|[GitHub](https://github.com/Stability-AI/StableCode), [HuggingFace](https://huggingface.co/stabilityai/stable-code-3b)|
|[OpenCoder](https://arxiv.org/abs/2411.04905)|-|OpenCoder matches performance of top-tier code LLMs|MIT License|[GitHub](https://github.com/OpenCoder-llm), [HuggingFace](https://huggingface.co/collections/infly/opencoder-672cec44bbb86c39910fb55e)|
|[Qwen2.5-Coder](https://arxiv.org/pdf/2409.12186)|7B|92 programming languages, 128K token context length|Apache 2.0|[GitHub](https://github.com/QwenLM/Qwen2.5-Coder)|
|[WizardCoder](https://huggingface.co/WizardLM/WizardCoder-Python-34B-V1.0)|7B-34B|Focuses on high-quality code generation|-|[HuggingFace](https://huggingface.co/WizardLM/WizardCoder-Python-34B-V1.0)|
|[Codestral](https://mistral.ai/news/codestral-2501/)|22B|Code Generation|-|[Mistral AI](https://docs.mistral.ai/capabilities/code_generation/)|
|[Jina Code Embeddings V2](https://arxiv.org/abs/2310.19923)|English and 30 programming languages, 8192 tokens|Apache 2.0|[HuggingFace](https://huggingface.co/jinaai/jina-embeddings-v2-base-code)|
|[voyage-code-3](https://blog.voyageai.com/2024/12/04/voyage-code-3/)|?|Commercial|[HuggingFace](https://huggingface.co/voyageai)|
|[CodeSage](https://arxiv.org/abs/2402.01935)|130M-356M-1.2B params, 1024 tokens, 9 programming languages|Apache 2.0|[HuggingFace](https://huggingface.co/codesage)|
|[UniXcoder](https://arxiv.org/pdf/2203.03850)|java, ruby, python, php, javascript, go, c, c++ and c# - 512 token window|||


## SQL

|Name|Parameter Size|Training Focus|Comments|License|Links|
|-|-|-|-|-|-|
|[Defog SQLCoder](https://github.com/defog-ai/sqlcoder)|-|SQL Generation|Fine-tuned Starcoder|CC BY-SA 4.0, OpenRAIL-M clauses|[HuggingFace](https://huggingface.co/defog/sqlcoder)|
|[SQL-PaLM](https://arxiv.org/pdf/2306.00739.pdf)|-|SQL Generation|Training the model to generate SQL queries based on demonstrations and context, with domain-specific fine-tuning|Google|-|
|[DolphinCoder](https://huggingface.co/cognitivecomputations/dolphin-2.6-mixtral-coder)|7B|SQL, Code Generation|Specialized in generating SQL code with high accuracy, tailored for database query optimization|BigCode Open RAIL-M v1 License|[Ollama](https://ollama.com/library/dolphincoder)|
|[SQLCoder](https://huggingface.co/defog/sqlcoder)|7B/15B/70B|SQL Queries|Precision-engineered for SQL code generation, ensuring high accuracy in generating executable queries|CC BY-SA 4.0 license|[HuggingFace](https://huggingface.co/defog/sqlcoder)|


## Text
|Name|Parameter Size|Comments|License|Links|
|-|-|-|-|-|
|LLaMA 1|7B/13B/33B/65B|-|-|[HuggingFace](https://huggingface.co/docs/transformers/en/model_doc/llama)|
|LLaMA 2|7B/13B/70B|-|-|[HuggingFace](https://huggingface.co/docs/transformers/en/model_doc/llama) [Ollama](https://ollama.com/library/llama2) |
|LLaMA 3|7B/13B/70B+|Superior performance with added functionalities|[Meta LLaMa 3 Community License Agreement](https://llama.meta.com/llama3/license/)|[GitHub](https://github.com/meta-llama/llama3) [Ollama](https://ollama.com/library/llama3)|
|[Gemma](https://blog.google/technology/developers/gemma-open-models/)|2B/7B|Deep contextual comprehension|-|[Ollama](https://ollama.com/library/gemma) [GCP](https://ai.google.dev/gemma/docs/model_card) [Technical Report](https://storage.googleapis.com/deepmind-media/gemma/gemma-report.pdf)|
|[Gemma 2](https://blog.google/technology/developers/google-gemma-2/)|2B/7B/27B|Context Window 8192|[Gemma Terms of Use](https://ai.google.dev/gemma/terms)|[HuggingFace](https://huggingface.co/docs/transformers/en/model_doc/gemma2) [Technical Report](https://storage.googleapis.com/deepmind-media/gemma/gemma-2-report.pdf)|
|[Gemma 3](https://blog.google/technology/developers/gemma-3/)|1B/4B/12B/27B/4B|Context Windows 128K|[Gemma Terms of Use](https://ai.google.dev/gemma/terms)|[GitHub](https://github.com/google-deepmind/gemma/tree/main) [Technical Report](https://storage.googleapis.com/deepmind-media/gemma/Gemma3Report.pdf)|
|Mixtral|7B|Performance-optimized model|-|[Ollama](https://ollama.com/library/mixtral) [HuggingFace](https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1) |
|Grok-1|314B|8 experts, choose 2|-|[X.ai](https://x.ai/blog/grok) |
|[DBRX](https://www.databricks.com/blog/introducing-dbrx-new-state-art-open-llm)|132B|16 experts and chooses 4, while Mixtral and Grok-1 have 8 experts and choose 2|[Databricks Open Model License](https://www.databricks.com/legal/open-model-license)|[GitHub](https://github.com/databricks/dbrx), [HuggingFace](https://huggingface.co/collections/databricks/dbrx-6601c0852a0cdd3c59f71962)|
|[DeepSeek-V2](https://github.com/deepseek-ai/DeepSeek-V2/blob/main/deepseek-v2-tech-report.pdf)|-|-|[DeepSeek License Agreement](https://github.com/deepseek-ai/DeepSeek-V2/blob/main/LICENSE-MODEL)|[GitHub](https://github.com/deepseek-ai/DeepSeek-V2/tree/main)|
|[DeepSeek-V3](https://github.com/deepseek-ai/DeepSeek-V3/tree/main)|671B|128K Context Window|[DeepSeek License Agreement](https://github.com/deepseek-ai/DeepSeek-V3/blob/main/LICENSE-MODEL)|[HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-V3), [arXiv](https://arxiv.org/abs/2412.19437)|
|[DeepSeek-R1](https://github.com/deepseek-ai/DeepSeek-R1/blob/main/DeepSeek_R1.pdf)|1.5/7B/8B/14B/32B/70B |130K Context Window|[MIT License](https://github.com/deepseek-ai/DeepSeek-R1/blob/main/LICENSE)|[HuggingFace](https://huggingface.co/deepseek-ai/DeepSeek-R1)|
|Mistral-Nemo|12B|Combines Mistral's efficiency with NeMo's capabilities|-|[Ollama](https://ollama.com/library/mistral-nemo) [HuggingFace](https://huggingface.co/mistralai/Mistral-Nemo-Instruct-2407)|
|Phi3|14B|Focuses on deep semantic understanding|-|[Ollama](https://ollama.com/library/phi3), [HuggingFace](https://huggingface.co/microsoft/Phi-3-mini-4k-instruct-gguf)|
|[Phi-4](https://arxiv.org/abs/2412.08905)|14B|Focus on training data quality and logic tasks|-|MIT|[HuggingFace](https://huggingface.co/microsoft/phi-4/blob/main/README.md#phi-4-model-card)|
|Zephyr|7B/141B|Streamlined architecture for real-time applications|-|[Ollama](https://ollama.com/library/zephyr) [HuggingFace](https://huggingface.co/HuggingFaceH4)|
|Qwen2|7B|Tailored for specific applications|-|[Ollama](https://ollama.com/library/qwen2)|
|Qwen3|235B/32B/30B/4B/14B/8B/4B/1.7B/0.6B|-|Apache 2.0|[GitHub](https://github.com/QwenLM/Qwen3)|
|LLaVA|7B/13B/34B|Adaptability to diverse tasks|-|[Ollama](https://ollama.com/library/llava) [HuggingFace](https://huggingface.co/liuhaotian/llava-v1.5-7b)|
|Nova|Mirco/Lite/Pro|-|-|?|[Amazon](https://aws.amazon.com/ai/generative-ai/nova/understanding/)|
|ModernBERT|150M/300M/500M|8192 context length, 2T tokens|Apache 2.0|[HuggingFace](https://huggingface.co/answerdotai/ModernBERT-base)|

## Multimodal

|Name|Parameter Size|Comments|License|Links|
|-|-|-|-|-|
|Qwen2.5-VL|32B|-|[Apache 2.0](https://github.com/QwenLM/Qwen2.5-VL?tab=Apache-2.0-1-ov-file#readme)|[GitHub](https://github.com/QwenLM/Qwen2.5-VL)|

## Others
|Name|Parameter Size|Comments|License|Links|
|-|-|-|-|-|
|[DeepSeek-Prover-V2](https://github.com/deepseek-ai/DeepSeek-Prover-V2/blob/master/DeepSeek_Prover_V2.pdf)|671B|-|[DeepSeek License](https://github.com/deepseek-ai/DeepSeek-Prover-V2?tab=readme-ov-file#License-1-ov-file)|[GitHub](https://github.com/deepseek-ai/DeepSeek-Prover-V2)|