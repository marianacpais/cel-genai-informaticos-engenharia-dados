# GenAI para Informáticos — Aula 5: Engenharia de Dados

Deck e materiais para a **sessão 5** do curso *GenAI para Informáticos* (edição `GenAIInf0526ST`).

- **Formadora:** Mariana Canelas Pais
- **Tema:** GenAI aplicada à **engenharia de dados** (na sequência das aulas 3–4, de ciência de dados)
- **Data:** **2026-07-01** (a sessão de 17 jun não se realizou; o curso deslizou para a frente — yaml da edição precisa de reconciliação via skill `calendarizacao-genai`)
- **Estado:** 🟢 **deck a fundo (2ª passagem)** em `index.html` — **121 slides**, design CeL, construído com 4 agentes em paralelo (Fundações · Porta A · ETL+embeddings · RAG+crítico) e montado/renumerado/renderizado. Renders em `previews/`. Backup da 1ª passagem em `index.pre-assembly.html`. Pré-repo.
- **Dimensionamento:** trabalhar como se fossem **5h** de conteúdo (preferir conteúdo a mais; a Mariana corta no dia se transbordar)

## Ficheiros

| Ficheiro | O que é |
|---|---|
| **[`index.html`](index.html)** | **O deck (reveal.js, design CeL). Abrir num browser. Renders em `previews/`.** |
| [`BRAINSTORM.md`](BRAINSTORM.md) | Espinha da aula: tese, decisões tomadas, demos, perguntas em aberto. Documento vivo. |
| [`OUTLINE.md`](OUTLINE.md) | Espinha teórica (~5h), profundidade assimétrica. |
| [`EMBEDDINGS.md`](EMBEDDINGS.md) | Cadeia de produção dos embeddings a fundo + consequências de DE. |
| [`DESIGN-NOTES.md`](DESIGN-NOTES.md) | Identidade CeL: paleta, tipografia, convenções de diagrama. |
| [`SANDBOX-PLANS.md`](SANDBOX-PLANS.md) | Índice dos exercícios + priorização. |
| [`slides/`](slides/) | Conteúdo ao nível de slide, diagrama-primeiro (4.2 embeddings feito). |
| [`exercicios/`](exercicios/) | Spec detalhada de cada exercício prático (EX-1…EX-5). |
| [`sandboxes/`](sandboxes/) | **Os 5 sandboxes interativos construídos e verificados:** EX-1 SQL que mente (DuckDB-WASM) · EX-2 Extrator ETL (JS) · EX-3 Espaço dos embeddings · EX-4 Laboratório de chunking · EX-5 Pipeline que apodrece (transformers.js). Cada um: `python3 -m http.server` na sua pasta. |
| [`SOURCES.md`](SOURCES.md) | Todas as fontes de pesquisa, anotadas e agrupadas. |

## Decisões âncora (ver BRAINSTORM para detalhe)

- Estrutura: **Porta A** (GenAI como copiloto do engenheiro de dados — atravessar, é o esperado) → **Porta B** (engenheiro de dados a construir para a GenAI — a estrela, com foco em **embeddings**).
- **Sem agentes** (reservado para aulas futuras).
- Conceitos **entrelaçados** com mãos-na-massa. **Fora do Colab.**
- Duas demos âncora: **LLM-como-ETL** (texto livre → JSON) e **text-to-SQL** (com foco no "SQL confiantemente errado").
- Público informático **não** necessariamente da saúde → aligeirar RGPD/saúde.
