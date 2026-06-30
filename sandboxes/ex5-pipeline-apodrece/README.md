# Sandbox EX-5 — "O pipeline que apodrece" (staleness / CDC)

App de browser (ficheiro único, `index.html`). Um **mini-RAG real** sobre uma base de conhecimento editável, a correr no browser (transformers.js). Demonstra que os embeddings são **dados derivados** que apodrecem quando a fonte muda — e como o **CDC** os põe em dia re-embebendo só o delta.

- **Motor:** transformers.js + `Xenova/paraphrase-multilingual-MiniLM-L12-v2` (mesmo modelo do EX-3). Sem backend. O modelo descarrega na 1ª vez e fica em cache.

## O fluxo (interativo)
1. **Indexar** a base de conhecimento → o índice é uma *fotografia* (texto + embedding + hash + versão de cada chunk).
2. **Perguntar** → o retrieval procura no índice (não na fonte) e devolve o chunk mais próximo. Inicialmente correto.
3. **Editar a fonte sem reindexar** (ex.: "5 tentativas" → "3 tentativas") → aparece o aviso de deriva e o chunk fica marcado "⚠ desatualizado".
4. **Perguntar de novo** → a resposta continua a vir do índice antigo ("5 tentativas"), com confiança e sem erro — **corrupção silenciosa**. Um aviso mostra que a fonte atual já diz outra coisa.
5. **Atualizar o índice**: *Reconstruir tudo* (re-embebe os N) vs **CDC** (re-embebe apenas os chunks alterados — ex.: 1/4). A resposta corrige-se; a versão do índice incrementa.

## Pontos de engenharia que o sandbox materializa
- Embedding = coluna derivada → precisa de estratégia de atualização.
- **CDC**: reprocessar só o delta (barato) vs rebuild total (caro à escala).
- **Linhagem**: cada chunk no índice mostra hash + versão do embedding.
- O failure mode mais citado de RAG em produção não é o modelo — são **dados rançosos**.

## Como correr
O WASM/worker **não corre via `file://`** — sirva por http:
```bash
cd sandboxes/ex5-pipeline-apodrece
python3 -m http.server 8000   →  http://localhost:8000
```

## Notas
- A deriva é detetada por **hash** de cada chunk vs. a fotografia no índice (recalculada ao vivo enquanto edita a fonte).
- Verificado por render headless: indexar → responder (correto) → editar (stale, com resposta antiga) → CDC (re-embebe 1/4) → responder (corrigido).
- Próximos: ligar ao slide de staleness do deck; opcionalmente, um modo "agenda" que simula a fonte a mudar sozinha ao longo do tempo.
