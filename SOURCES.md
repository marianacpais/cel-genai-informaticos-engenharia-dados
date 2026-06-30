# Fontes — Aula 5 (Engenharia de Dados × GenAI)

Pesquisa de junho 2026. Agrupadas por tema. As mais úteis estão marcadas com ⭐.

## A frase "RAG é um problema de engenharia de dados disfarçado de IA"

> **Nota de proveniência:** NÃO é citação de um autor único. É uma frase-zeitgeist que apareceu em várias formulações no final de 2025 / início de 2026. Usar como *sentimento da comunidade*, não como aspas atribuídas.

- ⭐ [RAG Is a Data Engineering Problem Disguised as AI](https://dev.to/aws-builders/rag-is-a-data-engineering-problem-disguised-as-ai-39b2) — DEV / AWS Builders, 27 jan 2026 (a formulação exata)
- [RAG: A Data Problem Disguised as AI](https://hackernoon.com/rag-a-data-problem-disguised-as-ai) — HackerNoon
- [RAG Isn't a Modeling Problem. It's a Data Engineering Problem.](https://dev.to/alexmercedcoder/rag-isnt-a-modeling-problem-its-a-data-engineering-problem-11h) — Alex Merced (Dremio)

## Porta A — GenAI como copiloto do engenheiro de dados

- ⭐ [AI Copilots in Data Engineering: What Works, What Doesn't](https://satyamsahu671.medium.com/ai-copilots-in-data-engineering-what-actually-works-what-doesnt-and-where-each-one-fits-47d86a420466) — Satyam Sahu. **Fonte da citação "half will confidently write SQL that joins on the wrong column".**
- [Databricks launches Genie Code](https://www.infoworld.com/article/4144820/databricks-launches-genie-code-to-automate-data-science-and-engineering-tasks.html) — InfoWorld
- [Comparing AI Capabilities in Databricks, Snowflake & Fabric](https://www.neurealm.com/blogs/comparing-ai-capabilities-in-databricks-snowflake-and-microsoft-fabric/) — Neurealm
- [How AI is changing the analytics stack](https://www.getdbt.com/blog/how-ai-is-changing-the-analytics-stack) — dbt Labs

### Text-to-SQL (estado da arte)

- ⭐ [Natural Language to SQL: State of the Art and Open Problems](https://dbgroup.cs.tsinghua.edu.cn/ligl/papers/VLDB2025-NL2SQL-Paper.pdf) — VLDB 2025, Tsinghua. **Schema linking** como passo crítico.
- [A Survey of Text-to-SQL in the Era of LLMs](https://arxiv.org/pdf/2408.05109) — arXiv
- [SLM-SQL: Small Language Models for Text-to-SQL](https://arxiv.org/html/2507.22478v1) — arXiv (modelos 3B–32B chegam para a tarefa)

## Porta B — engenharia de dados PARA a GenAI

### LLM-como-ETL (não-estruturado → estruturado)

- ⭐ [Unstract — LLM ETL for unstructured data](https://unstract.com/) · [GitHub](https://github.com/Zipstack/unstract) — saída JSON com schema, sem fine-tuning
- [Unstructured.io](https://idp-software.com/vendors/unstructured/) — 60+ conectores → vector DB para RAG
- ⭐ [Using LLMs in ETL pipelines: production-scale best practices](https://www.cloverdx.com/blog/using-llms-in-etl-pipelines-production-scale-best-practices) — CloverDX. Quando (não) usar LLM no pipeline.
- [LLM-AIx: information extraction from unstructured medical text](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC11398444/) — PMC (notas clínicas → CSV, privacy-preserving)

### RAG como pipeline de dados / embeddings

- ⭐ [How to Build a RAG Pipeline from Scratch in 2026](https://www.kapa.ai/blog/how-to-build-a-rag-pipeline-from-scratch-in-2026) — kapa.ai
- [From RAG to Context — 2025 year-end review](https://ragflow.io/blog/rag-review-2025-from-rag-to-context) — RAGFlow
- [Build an unstructured data pipeline for RAG](https://docs.databricks.com/aws/en/generative-ai/tutorials/ai-cookbook/quality-data-pipeline-rag) — Databricks docs (pipeline offline+online, CDC para embeddings)
- [Vector Databases for RAG](https://www.ibm.com/think/topics/rag-vector-database) — IBM (chunking, embeddings, hybrid + rerank)

## Qualidade de dados / observabilidade com LLM (Porta A, secundário)

- [Beyond Rule-Based Data Quality — LLM-powered anomaly detection](https://medium.com/@fhuthmacher/beyond-rule-based-data-quality-exploring-llm-powered-anomaly-detection-9a0c7c98c690) — Felix Huthmacher (erros contextuais de uma linha)
- [Boosting Your Anomaly Detection With LLMs](https://towardsdatascience.com/boosting-your-anomaly-detection-with-llms/) — Towards Data Science

## O papel do engenheiro de dados a mudar

- ⭐ [Data Engineer Roadmap & the Rise of AI](https://montecarlo.ai/blog-data-engineer-roadmap/) — Monte Carlo. "Code review vale mais, não menos."
- [How AI Will Reshape Data Engineering](https://dagster.io/blog/ai-and-data-engineering-roles) — Dagster
- [The Data Engineer Role in 2026: what's real, what's noise](https://alper-korukcu.medium.com/the-data-engineer-role-in-2026-whats-actually-changing-and-what-s-just-noise-6eeeddd809b9) — Medium

## Agentes (PARKED — reservado para aulas futuras, não usar nesta)

- [Agentic Data Pipelines](https://www.ishir.com/blog/320917/agentic-data-pipelines-the-shift-to-autonomous-data-engineering.htm) — Ishir
- [AI Agent Use Cases for Data Engineering](https://atlan.com/know/ai-agent-use-cases-data-engineering/) — Atlan
- [DataFlow: LLM-Driven Framework for Data Preparation](https://arxiv.org/pdf/2512.16676) — arXiv
