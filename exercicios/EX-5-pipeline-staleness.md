# EX-5 — "O pipeline que apodrece" (staleness / CDC)

**Porta:** B · **Momento da aula:** secção 4.3 (failure modes de dados) + fecho dos embeddings · **Formato:** demo/animação ilustrativa (pode não precisar de build pesado).

## Objetivo pedagógico
Mostrar que **embeddings são dados derivados** e, como qualquer dado derivado, **ficam rançosos quando a fonte muda**. O failure mode mais citado de RAG em produção não é o modelo — é dados desatualizados. Engenharia de dados pura.

## O que o formando vê (passo a passo)
1. Estado inicial: documento-fonte → chunk → embedding → índice → uma pergunta devolve a resposta **certa**.
2. **A fonte muda** (ex.: a política passa de "5 tentativas" para "3 tentativas"), mas o índice **não** é re-embebido.
3. A mesma pergunta devolve a resposta **antiga** (stale) — com confiança, sem erro. Corrupção silenciosa, versão RAG.
4. Aplicar **CDC**: detetar o que mudou e re-embeber **só** esse chunk (incremental) vs **rebuild** total.
5. A pergunta volta a acertar.

## O ponto de engenharia
- Embeddings = coluna derivada. Precisam de uma estratégia de atualização, como qualquer materialização.
- **CDC (Change Data Capture):** re-processar só o delta; tratar embeddings como dados incrementais, não como lote único e imutável.
- Versão do modelo + versão da fonte fazem parte da **linhagem** de cada vetor.
- Trade-off incremental vs rebuild: custo, latência, garantia de consistência.

## Stack candidato
- Provavelmente **ilustrativo/animado** (slides ou pequena animação) em vez de pipeline real — o valor é conceptual e a montagem de um CDC real é desproporcionada para a aula.
- Versão "real" possível reaproveitando o stack do EX-3/EX-4, se sobrar ambição.

## Esforço
- **Baixo** como ilustração (a recomendação).
- Alto se for um pipeline funcional com deteção de deltas.

## Decisões em aberto
- Ilustração vs build real.
- Encadear visualmente com o EX-4 (mesmo documento, mesmo modelo) para a turma ver a história completa: chunking → embedding → retrieval → staleness.
