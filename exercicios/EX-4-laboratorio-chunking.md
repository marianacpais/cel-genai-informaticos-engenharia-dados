# EX-4 — "Laboratório de chunking"

**Porta:** B · **Momento da aula:** secção 4.3 (RAG é um pipeline de dados) · **Formato:** demo guiada + experimentação.

## Objetivo pedagógico
Provar que **"chunking mau = RAG mau"** — e que isso é uma decisão de *engenharia de dados*, não do modelo. A mesma pergunta acerta ou falha consoante como partiste o documento. O modelo nunca muda; os dados mudam.

## O que o formando faz (passo a passo)
1. Escolhe um documento (ver corpus abaixo).
2. Escolhe uma **estratégia de chunking**:
   - tamanho fixo por caracteres (ex.: 200) **sem** overlap;
   - tamanho fixo **com** overlap (ex.: 200/50);
   - por parágrafo / por título (estrutural);
   - "semântico" (corte onde o tema muda).
3. Faz uma pergunta cuja resposta está **a cavalo entre dois chunks** numa estratégia e inteira noutra.
4. Vê os chunks recuperados (top-k) e se a resposta está lá.
5. Troca a estratégia e repete → vê a recuperação mudar.

## O caso que torna o ponto inegável
Preparar um documento onde um facto está dividido a meio:

> "…o limite de tentativas é configurável. **Por predefinição são 5** tentativas antes do bloqueio da conta…"

- **Fixo 200 sem overlap:** o corte cai entre "predefinição são" e "5 tentativas" → nenhum chunk contém o facto completo → o retrieval falha ou traz contexto truncado.
- **Com overlap / por parágrafo:** o facto fica inteiro num chunk → acerta.

Mesma pergunta ("qual o nº de tentativas por predefinição?"), mesmo modelo, resultado oposto. Só mudou a engenharia de dados.

## Corpus candidato (pequeno, multi-domínio)
- 1 manual técnico curto (com factos a cavalo de parágrafos).
- 1 FAQ (estrutura clara → favorece chunking estrutural).
- 1 documento corrido sem estrutura (favorece overlap).

## Métricas a mostrar
- Que chunks vieram no top-k.
- A resposta está contida? (sim/não).
- Opcional: nº de chunks, tamanho médio, sobreposição.

## Stack candidato
- Browser com embeddings pré-calculados por estratégia (determinístico) OU `transformers.js` ao vivo.
- Reutiliza o modelo de embedding do EX-3.

## Esforço
Alto se for totalmente interativo; **baixo na versão "lite"**: 2 estratégias (fixo-sem-overlap vs por-parágrafo) e 1 documento com o facto a cavalo. A versão lite já prova o ponto.

## Decisões em aberto
- Lite (2 estratégias) vs completo (4 estratégias + corpus variado).
- Mostrar também o efeito no *custo* (mais chunks = mais vetores = índice maior)? Liga às consequências de DE em `EMBEDDINGS.md`.
