# Espinha teórica — Aula 5 (dimensionada para ~5h)

Outline da apresentação. Profundidade **assimétrica** de propósito: a Porta B leva o grosso. Conceitos entrelaçados com os momentos de demo/sandbox (marcados ▶). Sem agentes (só teaser final).

---

## 0. Abertura — o gancho (curto)
- Recuperar as aulas 3–4: vocês exploraram a *BD Partos* e fizeram estatística. **Mas de onde veio aquele ficheiro?** Quem o produziu, limpou, e o mantém fresco quando chegam dados novos?
- Resposta: **engenharia de dados** — a canalização antes da água. Hoje é sobre a fábrica, não sobre o produto.

## 1. Engenharia de dados em 5 minutos (enquadramento, não aula de DE)
- Público é informático → assumir base. Fixar só o vocabulário que vamos usar: pipeline, ETL vs ELT, batch vs streaming, data warehouse/lake, orquestração, qualidade, lineage, governança.
- A propriedade que vai assombrar a aula toda: **engenharia de dados vive de idempotência e reprodutibilidade**. Guardar isto — vai colidir com os LLMs.

## 2. A tese — as duas portas (1 slide forte)
- Porta A: GenAI como **copiloto do** engenheiro de dados.
- Porta B: o engenheiro de dados a construir **para a** GenAI. ← onde passamos a maior parte do tempo.
- Aviso honesto: agentes ficam para outra aula.

---

## 3. PORTA A — copiloto do engenheiro de dados (atravessar com firmeza, sem demorar)

### 3.1 O mito: "já não é preciso escrever SQL"
- ▶ **Demo SB-1 — o SQL que mente.** 4–5 armadilhas em domínio de saúde (ver `BRAINSTORM.md`): grain, linhas em falta, NOT IN+NULL, definição ambígua, fan-out.
- Punchline: SQL **sintaticamente perfeito, corre sem erro, dá o número errado**. O LLM não conhece o grain, as tuas definições de negócio, nem os teus NULLs.
- Citação-âncora (comunidade, não autor): copilotos "confidently write SQL that joins on the wrong column".

### 3.2 O resto do copiloto (panorâmico, 1 slide cada)
- Geração de ETL / dbt models + testes + docs; migração e tradução de dialetos; catalogação e metadados; qualidade de dados *semântica* (erros contextuais de uma linha).
- Copilotos de plataforma: Databricks Genie, Snowflake Cortex, BigQuery Gemini, MS Fabric Copilot. Landscape, não tutorial.

### 3.3 A lição da Porta A
- Fecha o ciclo da Aula 1: escrever → orientar → **validar**. Em 2026 o *code review* vale mais, não menos. O copiloto acelera quem sabe ler; é armadilha para quem o usa para não aprender.

---

## 4. PORTA B — engenharia de dados PARA a GenAI (o coração da aula)

### 4.1 O LLM como passo de transformação (LLM-como-ETL)
- A ideia que vira a cabeça: o **T** do ETL pode ser um LLM. Texto livre não-estruturado → registo estruturado com schema.
- ▶ **Demo SB-2 — o extrator.** Nota clínica livre → JSON `{idade, sexo, diagnóstico, medicação[], alergias[]}`.
- As falhas concretas (é aqui que está a engenharia): negação, normalização de vocabulário, alucinação de campos, **não-determinismo** (corre 2× → saídas diferentes — colide com a idempotência do ponto 1).
- A resposta de engenharia: schema-constrain (Pydantic) + validação + flag de confiança. O LLM **exige** o contrato de dados, não o dispensa.

### 4.2 Embeddings — A FUNDO, "como se produzem" (núcleo da aula — ver `EMBEDDINGS.md`)
- A cadeia real, passo a passo: **tokenização** (subword/BPE) → **matriz de embeddings** (lookup) → **contextualização** (atenção: a mesma palavra muda de vetor consoante o contexto) → **pooling** (vetor único por chunk) → **objetivo de treino** (contrastivo: é *por isto* que cosseno = significado — a geometria foi otimizada para isso).
- Desmistificação: os embeddings não são opacos; o espaço foi *treinado* para pôr significados próximos perto. Mostrar o "porquê" desmistifica tudo o resto.
- ▶ **Demo SB-3 — o espaço dos embeddings.** Tokenizador ao vivo → mesma palavra em dois contextos = dois vetores → matriz cosseno → busca semântica → "a média dilui" (chunk gigante vs focado).
- A virada de engenharia de dados (consequências diretas): o **modelo de embedding é uma decisão de schema** (mudar de modelo = migração que recalcula TUDO; espaços diferentes não se misturam); a **dimensão** comanda storage/índice; **versionar embeddings** é parte da linhagem; embeddings são **dados DERIVADOS** → ficam *stale* e precisam de **CDC** (incremental, não lote único).

### 4.3 RAG é um pipeline de dados disfarçado de IA
- A frase-zeitgeist (enquadrar como sentimento da comunidade 2025–26, não citação).
- O pipeline: ingestão → chunking → embeddings → vector store → retrieval. **Duas pipelines**: *offline* (indexar) e *online* (consultar).
- ▶ **Demo SB-4 (se houver tempo) — laboratório de chunking.** Mesma pergunta acerta ou falha consoante a estratégia de chunking. O modelo não mudou; os dados mudaram.
- Failure modes reais de RAG em produção = failure modes de **DADOS**: dados rançosos (▶ SB-5), schema drift, backfills inconsistentes, ausência de contratos produtor↔consumidor.

### 4.4 O novo membro do stack: a base de dados vetorial
- Onde encaixa ao lado do warehouse/lake; índices (HNSW), busca híbrida (vetor + BM25) + rerank. Panorâmico.

---

## 5. O fio crítico (transversal — costurar ao longo, não secção isolada)
- **Estocástico vs determinístico:** meter um LLM no pipeline parte a garantia de reprodutibilidade. Quando NÃO usar LLM (regras determinísticas são mais baratas, auditáveis, rápidas).
- **Corrupção silenciosa:** o custo de uma alucinação em dados não é um crash — é um número errado que propaga a jusante durante semanas.
- **Lixo à entrada:** agentes/copilotos falham sobre metadados pobres e colunas ambíguas → reforça contratos e documentação (ligação à Aula 2). *Mencionar, não centrar — há aulas futuras sobre governança.*
- **Custo/latência:** correr um LLM sobre milhões de linhas é caro e lento.

## 6. Síntese e fecho
- Como muda o papel: de escrever pipelines a **desenhar ecossistemas** e validar. Clareza > esperteza.
- Teaser (1 slide): pipelines agênticos que se auto-curam — **próximas aulas**.

---

### Notas de dimensionamento
- Para 5h: Porta A ~1/3, Porta B ~2/3. Se transbordar, a Mariana corta no dia (prefere conteúdo a mais).
- Pendente para fechar fronteira: **sumário da Aula 4 (estatística)** — para garantir que 4.x não repete o que a ciência de dados já deu.
