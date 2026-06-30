# Sandbox EX-3 — "O espaço dos embeddings"

App de browser (ficheiro único, `index.html`). O formando escreve as suas próprias frases e um **modelo de embeddings multilingue real corre no browser** (transformers.js) para as transformar em vetores. Quatro explorações interativas, na identidade CeL e em linguagem formal.

- **Motor:** [transformers.js](https://github.com/xenova/transformers.js) + `Xenova/paraphrase-multilingual-MiniLM-L12-v2` (384 dim). Multilingue de propósito — em português, um modelo só-inglês troca as semelhanças (a busca "problema no automóvel" devolvia "banco do jardim").
- **Sem backend.** O modelo descarrega na 1ª vez (~135 MB) e fica em cache; depois corre offline.

## Secções
1. **Tokenização** — escreve um texto, vê os tokens (sub-palavras) + IDs. Ex.: `hipertensão` → `hiper · ten · são`.
2. **O espaço semântico** (frases editáveis):
   - **Matriz de semelhança** (heatmap de cosseno) — `carro`≈`viatura` (0.72) sem palavras em comum; `banco`(dinheiro) vs `banco`(jardim) baixo (0.24) apesar da palavra igual; `dor no peito`≈`dor torácica` (0.86).
   - **Busca semântica** — pergunta → frases ordenadas por proximidade.
   - **Mapa 2D (PCA)** — as 384 dimensões projetadas para se verem os agrupamentos.
3. **A média dilui** — pergunta vs chunk focado vs chunk enorme; o enorme fica "morno" (0.50 vs 0.67). Liga ao chunking do RAG.

## Como correr
O WASM/worker **não corre via `file://`** — sirva por http:
```bash
cd sandboxes/ex3-espaco-embeddings
python3 -m http.server 8000   →  http://localhost:8000
```
(1º arranque mais lento: descarrega o modelo. Para deploy, qualquer host estático serve.)

## Notas
- Tudo interativo: as frases, a pergunta e os chunks são editáveis — os formandos exploram com os seus próprios exemplos.
- PCA implementado em JS (power iteration, determinístico). Cosseno = produto interno (vetores normalizados).
- Verificado por render headless: modelo carrega, tokeniza, embeddings/heatmap/busca/mapa/dilui corretos em PT.
- Próximos: ligar ao slide de embeddings do deck; opção de comparar dois modelos (multilingue vs só-inglês) para ilustrar o slide "Multilingual e o português".
