# EX-3 — "O espaço dos embeddings"

**Porta:** B · **Momento da aula:** secção 4.2 (embeddings a fundo) · **Formato:** demos curtas (30–60s) entrelaçadas com a teoria de `EMBEDDINGS.md`.

## Objetivo pedagógico
Desmistificar o embedding tornando **cada passo da cadeia de produção visível**: tokenização → vetor → contextualização → pooling → o porquê de "perto = parecido". No fim, ninguém acha que é opaco.

## Estrutura: uma micro-demo por passo da cadeia
Cada uma liga-se a uma secção de `EMBEDDINGS.md`.

### 3a. Tokenizador ao vivo (passo 1)
- Input: 2–3 frases, incl. uma palavra composta ("hipertensão", "antihipertensor") e uma estrangeira.
- Mostra: os tokens e os IDs. "hipertensão" → `hiper`+`tensão`.
- Prova: o modelo não vê palavras, vê pedaços. A **forma** do texto importa (ponte para a normalização do ETL).

### 3b. A mesma palavra, dois contextos (passo 3 — o mais marcante)
- Input: "fui ao **banco** levantar dinheiro" vs "sentei-me no **banco** do jardim".
- Mostra: o vetor do token "banco" é diferente nos dois; cosseno entre eles é baixo.
- Prova: contextual ≠ estático. É o salto do word2vec para os embeddings modernos.

### 3c. Matriz de semelhança (passo 5 — o porquê)
- Input: ~8 frases de domínios variados, algumas sinónimas sem palavras em comum ("o carro não pega" / "a viatura não arranca"), outras só com palavras em comum mas sentido diferente.
- Mostra: heatmap de cosseno.
- Prova: a proximidade segue o **significado**, não as palavras partilhadas — porque foi o objetivo de treino contrastivo.

### 3d. Busca semântica (aplicação)
- Input: uma "query" + um pequeno corpus.
- Mostra: top-k por cosseno.
- Prova: é isto que um vector store faz no retrieval do RAG (ponte para EX-4).

### 3e. "A média dilui" (passo 4 — pooling)
- Input: um chunk focado vs um chunk enorme que mistura 4 assuntos.
- Mostra: o chunk enorme fica "morno", longe de qualquer query específica.
- Prova: o chunking é decisão de engenharia, não de IA (ponte direta para EX-4).

### 3f. (Opcional) Mapa 2D do espaço
- Projeção PCA/UMAP de ~20 frases agrupadas por tema.
- Mostra: clusters por significado.
- Prova: o "espaço" é real e navegável.

## Stack candidato
- **Browser:** `transformers.js` com um modelo pequeno (ex.: `all-MiniLM-L6-v2`, 384 dim). Corre client-side, fora do Colab.
- **Ou pré-calculado:** embeddings gravados em JSON + cosseno/projeção em JS puro (determinístico, leve, mas inputs fixos).
- Projeção 2D: **PCA** (simples, determinístico, sem deps) vs **UMAP** (mais bonito, mais pesado).

## Esforço
Médio-alto. A projeção 2D (3f) é o pedaço caro; 3a–3e são baratos. Se o tempo apertar, 3f é a primeira a cair.

## Decisões em aberto
- `transformers.js` ao vivo (input livre) vs pré-calculado (inputs fixos, à prova de falhas).
- PCA vs UMAP para o mapa.
- Modelo de embedding a usar nas demos (idealmente o mesmo do EX-4, por coerência).
