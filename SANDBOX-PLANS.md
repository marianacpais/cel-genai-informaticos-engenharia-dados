# Planos de Sandbox — candidatos (nível de planeamento)

> **Estado:** planeamento, NÃO implementação. Aqui só desenhamos *o que poderíamos construir*. A construção vem depois, e o sandbox vive **separado do HTML do deck**.
>
> **Spec detalhada de cada exercício** → `exercicios/` (um doc por exercício):
> [EX-1 SQL que mente](exercicios/EX-1-sql-que-mente.md) · [EX-2 Extrator ETL](exercicios/EX-2-extrator-etl.md) · [EX-3 Espaço dos embeddings](exercicios/EX-3-espaco-embeddings.md) · [EX-4 Laboratório de chunking](exercicios/EX-4-laboratorio-chunking.md) · [EX-5 Pipeline que apodrece](exercicios/EX-5-pipeline-staleness.md)

Cada candidato: objetivo pedagógico · o que o formando manipula · o que prova · stack candidato · esforço de construção · liga a que parte da aula.

---

## SB-1 — "O SQL que mente" (text-to-SQL confiantemente errado)

- **Objetivo:** sentir na pele que NL → SQL produz queries perfeitas que dão números errados.
- **Manipula:** escolhe (ou escreve) uma pergunta em linguagem natural sobre um dataset clínico sintético; vê o SQL gerado, corre-o, e compara com a query *correta* lado-a-lado.
- **Prova:** o erro é semântico e silencioso (grain, linhas em falta, NOT IN+NULL, fan-out). Liga ao catálogo de armadilhas em `BRAINSTORM.md`.
- **Stack candidato:** DuckDB-WASM (SQL no browser, dataset embebido) OU Python + DuckDB/SQLite. O passo "LLM gera SQL" pode ser pré-gravado (queries já capturadas) para não precisar de API ao vivo.
- **Esforço:** médio. O grosso é curar o dataset e o conjunto de perguntas-armadilha com a query certa de cada uma.
- **Decisão em aberto:** LLM ao vivo (precisa key) vs. respostas pré-capturadas (determinístico, sem fricção). Para a sala, pré-capturado é mais seguro.

## SB-2 — "O extrator" (LLM-como-ETL: texto livre → JSON)

- **Objetivo:** ver o LLM como o passo de *transformação* de um pipeline.
- **Manipula:** cola uma nota clínica livre; escolhe o schema-alvo; vê o JSON extraído + validação + flags.
- **Prova:** poder real, mas com falhas concretas — negação ("sem alergias" → `[]`), normalização ("HTA" vs "hipertensão"), alucinação de campos, não-determinismo entre corridas. Toggle com/sem schema-constraint (Pydantic) para mostrar a diferença.
- **Stack candidato:** Python + Pydantic + chamada a um modelo (Claude). Único sandbox que precisa mesmo de API → candidato a **demo guiada da formadora**, não hands-on de todos.
- **Esforço:** médio. Curar 3–4 notas que disparem cada falha de propósito.

## SB-3 — "O espaço dos embeddings"

- **Objetivo:** desmistificar embeddings — texto vira vetor, proximidade = significado.
- **Manipula:** escreve várias frases; vê-as projetadas em 2D (PCA/UMAP); vê a matriz de semelhança (cosine); faz uma busca semântica ("qual a frase mais parecida com X?").
- **Prova:** semântica ≠ palavra-chave (frases sem palavras em comum ficam perto); a base de qualquer RAG. Mostrar pares clínicos próximos ("dor torácica" ~ "enfarte") vs. distantes.
- **Stack candidato:** `transformers.js` (modelo pequeno no browser) OU embeddings pré-calculados em JSON + cosine/projeção em JS. Sem servidor.
- **Esforço:** médio-alto (a projeção 2D é o trabalho).

## SB-4 — "Laboratório de chunking"

- **Objetivo:** provar que "chunking mau = RAG mau" — uma decisão de *engenharia de dados*, não de IA.
- **Manipula:** pega num documento; troca a estratégia de chunking (tamanho fixo, por parágrafo, semântico, com/sem overlap); faz uma pergunta e vê que pedaços são recuperados.
- **Prova:** a mesma pergunta acerta ou falha consoante o chunking. O modelo não mudou — os dados é que mudaram.
- **Stack candidato:** browser (JS) com embeddings pré-calculados por estratégia, OU Python.
- **Esforço:** alto. Possivelmente a versão "lite" (2 estratégias) chega.

## SB-5 — "O pipeline que apodrece" (staleness / CDC)

- **Objetivo:** embeddings são *dados derivados* — ficam rançosos quando a fonte muda.
- **Manipula:** altera um documento-fonte; vê o índice vetorial dar a resposta antiga (stale); aplica re-embedding incremental (CDC) vs. rebuild total.
- **Prova:** o failure mode mais citado de RAG em produção é dados rançosos — engenharia de dados pura.
- **Stack candidato:** mais conceptual; talvez animação/simulação em vez de código vivo.
- **Esforço:** baixo se for ilustrativo; alto se for real.

---

## Priorização sugerida (para ~5h)

| Prioridade | Sandbox | Porquê |
|---|---|---|
| 1 | SB-1 (SQL que mente) | É a demo que mais marca; ataca o mito central. |
| 2 | SB-3 (espaço dos embeddings) | Núcleo da Porta B; desmistifica o conceito-chave. |
| 3 | SB-2 (extrator) | Mostra a Porta B inteira; demo da formadora. |
| 4 | SB-4 (chunking) | Aprofunda "RAG é engenharia de dados" se houver tempo. |
| 5 | SB-5 (staleness) | Conceptual; bom para um slide animado mesmo sem build. |
