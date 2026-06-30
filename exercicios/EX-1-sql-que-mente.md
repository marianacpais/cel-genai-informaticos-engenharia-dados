# EX-1 — "O SQL que mente" (text-to-SQL confiantemente errado)

**Porta:** A · **Momento da aula:** secção 3.1 (o mito "já não é preciso escrever SQL") · **Formato:** hands-on para todos + condução da formadora.

## Objetivo pedagógico
Provar, por A+B, que NL → SQL produz queries **sintaticamente perfeitas, que correm sem erro, e dão o número errado**. O erro é semântico e silencioso. Mensagem que fica: o copiloto acelera quem sabe ler SQL; é uma armadilha para quem o usa para não aprender.

## O que o formando faz (passo a passo)
1. Lê uma pergunta em linguagem natural (escolhida de uma lista, ou escreve a sua).
2. Vê o SQL gerado para essa pergunta (pré-capturado de um LLM, ou ao vivo — ver "modo").
3. Corre o SQL contra o dataset → vê o resultado (o número *errado*).
4. Carrega em "revelar" → aparece a query **correta** lado a lado + o número certo + a diferença (Δ).
5. Discute: onde estava a ambiguidade? que conhecimento o LLM não tinha?
6. Repete para 4–5 armadilhas de domínios diferentes.

## Dataset (sintético, pequeno, multi-domínio)
Um único ficheiro com poucas tabelas, semeado de propósito para disparar cada armadilha. Schema proposto:

```
clientes(cliente_id, nome, criado_em)
encomendas(encomenda_id, cliente_id, data, estado)        -- estado ∈ {paga, cancelada}
linhas_encomenda(linha_id, encomenda_id, produto_id, qtd, preco_cent)  -- preco em cêntimos
produtos(produto_id, nome, categoria)
vendedores(vendedor_id, nome)
vendas(venda_id, vendedor_id, encomenda_id, comissao_cent) -- vendedores sem vendas NÃO aparecem aqui
tickets(ticket_id, cliente_id, aberto_em, fechado_em)      -- fechado_em NULL = ainda aberto
```

**Sementes que criam as armadilhas (importante: os dados têm de provocar o erro):**
- Clientes com várias encomendas → arma o **grain**.
- Encomendas com várias `linhas_encomenda` → arma o **fan-out**.
- Pelo menos uma `linhas_encomenda.produto_id = NULL` → arma o **NOT IN + NULL**.
- Vendedores na tabela `vendedores` sem qualquer linha em `vendas` → arma as **linhas em falta**.
- Encomendas com `estado='cancelada'` → arma o **soft delete**.
- `preco_cent` em cêntimos → arma a armadilha de **unidades**.
- Tickets com `fechado_em NULL` → arma a média de resolução enviesada.

## As armadilhas concretas (com os números do dataset)
Preencher os Δ reais depois de fixar as sementes. Estrutura de cada cartão:

| Pergunta NL | SQL gerado (errado) | Resultado errado | SQL correto | Resultado certo | Porquê |
|---|---|---|---|---|---|
| "Quantos clientes temos?" | `SELECT COUNT(*) FROM encomendas` | (conta encomendas) | `SELECT COUNT(DISTINCT cliente_id) FROM clientes` | (clientes) | grain |
| "Comissão média por vendedor." | `SELECT AVG(comissao_cent) FROM vendas` | (infla) | total ÷ nº de vendedores em `vendedores` | (real) | linhas em falta |
| "Produtos nunca vendidos." | `... WHERE produto_id NOT IN (SELECT produto_id FROM linhas_encomenda)` | (vazio) | `NOT EXISTS` / `LEFT JOIN ... IS NULL` | (lista real) | NOT IN + NULL |
| "Receita total." | `SELECT SUM(preco_cent) ...` com join a encomendas | (em dobro + cêntimos) | agregar ao grain certo, /100 | (€ certos) | fan-out + unidades |
| "Nº de encomendas." | `SELECT COUNT(*) FROM encomendas` | (inclui canceladas) | `... WHERE estado <> 'cancelada'` | (reais) | soft delete |

→ Demonstração: o SQL errado **corre sem erro**. É essa a lição.

## Modo de execução (decisão em aberto)
- **Pré-capturado (recomendado para sala):** as queries do LLM já estão guardadas; determinístico, sem fricção, sem API key. O foco fica no errado-vs-certo, não em esperar pelo modelo.
- **Ao vivo (opcional):** caixa onde escrevem a pergunta e um LLM gera o SQL na hora. Mais "wow", mas não-determinístico e precisa de key.

## Stack candidato
- **DuckDB-WASM** no browser: SQL corre client-side, dataset embebido como Parquet/CSV. Zero setup, fora do Colab.
- Alternativa: Python + DuckDB/SQLite num devcontainer (mais poder, mais setup).

## Esforço
Médio. O grosso é **curar o dataset e as sementes** para que cada Δ seja gritante, e capturar/escrever as ~10 queries (errada + certa).

## Decisões em aberto
- Pré-capturado vs ao vivo (ver acima).
- Quantas armadilhas no fluxo principal (4–5) e quais ficam como "extra".
- DuckDB-WASM vs devcontainer.
