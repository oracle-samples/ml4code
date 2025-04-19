---
title: 'Datasets'
url: datasets
weight: 4
---

# Datasets

## Public Datasets
### Code Datasets
| Dataset | Download| Data Source | Description | License |
| - | - | - | - | - |
| [The Stack](https://arxiv.org/pdf/2211.15533.pdf) | [HuggingFace](https://huggingface.co/datasets/bigcode/the-stack) | GitHub | 6TB of permissively-licensed source code files covering 358 programming languages | per-repo basis |
| [The Stack V2](https://arxiv.org/pdf/2402.19173)| [HuggingFace](https://huggingface.co/datasets/bigcode/the-stack-v2)| Software Heritage, GitHub Issues, GitHub PRs, Kaggle, Documentation from Package Managers | >600 programming languages with >900B tokens | Agreement with Software Heritage necessary - derivate models must be made available under a suitable license |
|[CodeSearchNet](https://github.com/github/CodeSearchNet) ([Paper](https://arxiv.org/pdf/1909.09436.pdf))|[HuggingFace](https://huggingface.co/datasets/code_search_net)|GitHub| 2M methods and comments Python, JavaScript, Ruby, Go, Java, PHP | per-snippet basis|
|[CuBERT Dataset](https://github.com/google-research/google-research/tree/master/cubert)|[Instructions](https://github.com/google-research/google-research/tree/master/cubert#collection-query)|[BigQuery GitHub Dataset](https://cloud.google.com/blog/topics/public-datasets/github-on-bigquery-analyze-all-the-open-source-code)|Python and Java repos|per-repo basis|
|[ETH Py150](https://www.sri.inf.ethz.ch/py150)|ASTs ([full](http://files.srl.inf.ethz.ch/data/py150.tar.gz), [redistributable](https://github.com/google-research-datasets/eth_py150_open)), [Source Code](http://files.srl.inf.ethz.ch/data/py150_files.tar.gz)|public repos| 150K Python files | per-snippet basis, but all permissive |
| [code2seq](https://github.com/tech-srl/code2seq#datasets) | AWS{[small](https://s3.amazonaws.com/code2seq/datasets/java-small.tar.gz), [med](https://s3.amazonaws.com/code2seq/datasets/java-med.tar.gz), [large](https://s3.amazonaws.com/code2seq/datasets/java-large.tar.gz)} | GitHub | 600k, 3M and 15M Java snippets of different lengths {small, med, large}| per-snipped basis |
| [CodeNN](https://aclanthology.org/P16-1195.pdf) | [GitHub](https://github.com/sriniiyer/codenn/tree/master/data) | StackOverflow | 145,841 C# and 41,340 SQL <title,code> pairs| CC BY-SA 4.0 |
| [BigCloneBench](https://github.com/clonebench/BigCloneBench) | [OneDrive](https://1drv.ms/u/s!AhXbM6MKt_yLj_tk29GJnc9BKoIvCg?e=oVTVJm) | [IJaDataset](https://ieeexplore.ieee.org/document/7332459) | 356M LOC from 25'000 Java projects |  CC BY NC ND 4.0 |
| [CodeTrans](https://arxiv.org/pdf/2102.04664.pdf) | [GitHub](https://github.com/microsoft/CodeXGLUE/tree/main/Code-Code/code-to-code-trans/data) | Crawled from public repos (Lucene, POI, JGit, Antlr) | 13k pairs of Java/C# code | Apache License + BSD |
| [Sorting Algorithms](https://github.com/bdqnghi/bi-tbcnn) | [GitHub](https://github.com/bdqnghi/bi-tbcnn/raw/master/corpus/pairs.csv) | GitHub | - 5k Java files and 5k C++ files | per-snipped basis |
| [Code Docstring Corpus](https://arxiv.org/pdf/1707.02275.pdf) | [GitHub](https://github.com/EdinburghNLP/code-docstring-corpus) | GitHub |  150k Python code/comment pairs | per-snippet |
| [POJ-104](https://github.com/microsoft/CodeXGLUE/tree/main/Code-Code/Clone-detection-POJ-104) | [Google Drive](https://drive.google.com/file/d/0B2i-vWnOu7MxVlJwQXN6eVNONUU/view?usp=sharing) | [OpenJudge](https://github.com/microsoft/CodeXGLUE/tree/main/Code-Code/Clone-detection-POJ-104) - offline | 50000 methods in C++ | unknown |
| [AdvTest](https://github.com/microsoft/CodeXGLUE/tree/main/Text-Code/NL-code-search-Adv) | [GitHub](https://github.com/microsoft/CodeXGLUE/tree/main/Text-Code/NL-code-search-Adv) | Filtered version of CodeSearchNet dataset for NL-to-code search| 250k functions Python, JavaScript, Ruby, Go, Java, PHP | per-snippet |
| [CoSQA](https://arxiv.org/pdf/2105.13239.pdf) | [zenodo](https://zenodo.org/record/7857872/files/python.zip), [HuggingFace](https://huggingface.co/datasets/gonglinyuan/CoSQA) | Bing query logs and CodeSearchNet | 20k query/answer pairs in Python | [CUDA](https://github.com/microsoft/Computational-Use-of-Data-Agreement)|
| [CONCODE](https://www.aclweb.org/anthology/D18-1192.pdf) | [GitHub](https://github.com/microsoft/CodeXGLUE/tree/main/Text-Code/text-to-code)| GitHub | 100'000 Java Classes | per-snippet |
| [GitHub Java](https://groups.inf.ed.ac.uk/cup/javaGithub/) | [University of Edinburgh](https://groups.inf.ed.ac.uk/cup/javaGithub/java_projects.tar.gz) | GitHub | 15K Java Projects, 1.5M tokens | per-snippet |
| [UniXcoder Dataset](https://github.com/microsoft/CodeBERT/tree/master/UniXcoder/downstream-tasks/zero-shot-search) | [Appdomain](https://dax-cdn.cdn.appdomain.cloud/dax-project-codenet/1.0.0/Project_CodeNet.tar.gz), [Instructions](https://github.com/microsoft/CodeBERT/tree/master/UniXcoder/downstream-tasks/zero-shot-search#data-download) | 11,744/15,594/23,530 functions in Ruby/Python/Java from CodeNet  | [CDLA Permissivev 2.0](https://cdla.dev/permissive-2-0/) |
| [CodeNet Dataset](https://jiechenjiechen.github.io/pub/codenet.pdf), [Project CodeNet](https://developer.ibm.com/exchanges/data/all/project-codenet/) | [Appdomain](https://dax-cdn.cdn.appdomain.cloud/dax-project-codenet/1.0.0/Project_CodeNet.tar.gz) | AIZU Online Judge, AtCoder | 13,916,868 submissions, divided into 4053 problems | [CDLA Permissivev 2.0](https://cdla.dev/permissive-2-0/) |
| [TL-CodeSum](https://github.com/xing-hu/TL-CodeSum) | [GitHub](https://github.com/xing-hu/TL-CodeSum/tree/master/data) | GitHub | 100k Java methods and method comments | per-snippet |
|[Sourcerer](https://ics.uci.edu/~lopes/datasets/SDS_source-repo-18k.html) | [UCI](https://ics.uci.edu/~lopes/datasets/SDS_source-repo-18k.html) | various Open Source projects | 18,000 Java projects | non-commercial and non-profit work |
| [DeepCom](https://xin-xia.github.io/publication/icpc182.pdf) | [GitHub](https://github.com/xing-hu/DeepCom) | GitHub | 9,714 Java projects | per-snippet |
| [Google Code Jam, Kick Start, Hash Code](https://zibada.guru/gcj/) | [zibada.guru](https://zibada.guru/gcj/#gcj) | Google  | Competition problem statements and solutions | MIT license for a subset |
| [CodeContests](https://arxiv.org/pdf/2203.07814) | [HuggingFace](https://huggingface.co/datasets/deepmind/code_contests) | Aizu, AtCoder, CodeChef, Codeforces, HackerEarth | Python, C++, Java - training set contains 13328 samples, the validation set 117 samples and the test set 165 samples. | CC BY 4.0 |
|[method2test](https://arxiv.org/pdf/2009.05617.pdf)|[GitHub](https://github.com/microsoft/methods2test)|The dataset contains 780,944 test cases mapped to their corresponding focal methods, extracted from 9,410 unique repositories (91,385 original repositories analyzed) - Java|Contributor License Agreement (CLA)|
|[CommitPackFT](https://arxiv.org/pdf/2308.07124)||GitHub|Filtered version of CommitPack: 4 terabytes of Git commits across 350 programming languages|per-repo (permissive)|

### SQL
| Dataset | Download| Data Source | Description | License
| - | - | - | - | - |
| WikiSQL | [GitHub](https://arxiv.org/pdf/1709.00103) | Wikipedia | A dataset of 80654 hand-annotated examples of questions and SQL queries distributed across 24241 tables from Wikipedia. | BSD-3 |
| Spider | - | - | - | - |
| Spider-Syn | - | - | - | - |
| Spider-DK | - | - | - | - |

### Other Datasets
| Dataset | Download| Data Source | Description | License 
| - | - | - | - | - |
| [C4](https://www.tensorflow.org/datasets/catalog/c4) | [HuggingFace](https://huggingface.co/datasets/allenai/c4/tree/main) | [WWW](https://commoncrawl.org/) | Unprocessed text data | [ODC-BY](https://opendatacommons.org/licenses/by/1-0/), [Common Crawl terms of use](https://commoncrawl.org/terms-of-use/) |
|[The Pile](https://arxiv.org/abs/2101.00027)|[Eye](https://the-eye.eu/public/AI/pile/)|per sub-dataset|constructed from 22 diverse high-quality subsets -- both existing and newly constructed -- many of which derive from academic or professional sources.|-|
|[PRM800K](https://arxiv.org/abs/2305.20050)|[GitHub](https://github.com/openai/prm800k)|MATH|800,000 step-level correctness labels for model-generated solutions to problems from the MATH dataset|[MIT License](https://github.com/openai/prm800k/blob/main/LICENSE)|-|
|[AIME](https://www.kaggle.com/datasets/hemishveeraboina/aime-problem-set-1983-2024)|[Kaggle](https://www.kaggle.com/datasets/hemishveeraboina/aime-problem-set-1983-2024?select=AIME_Dataset_1983_2024.csv)|AIME|The American Invitational Mathematics Examination Dataset from 1983 to 2024|?|-|