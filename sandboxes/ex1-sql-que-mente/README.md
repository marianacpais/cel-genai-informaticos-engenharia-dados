# Sandbox EX-1 — "O SQL que mente"

App de browser (ficheiro único, `index.html`). O formando seleciona um desafio, **leva o contexto + a pergunta ao seu próprio chatbot**, traz a consulta SQL de volta, executa-a sobre um dataset sintético e **compara com a referência** — e descobre que o número está errado apesar de a consulta correr sem erro. A referência e o pitfall são revelados a seguir.

- **Motor:** DuckDB-WASM (corre no browser, sem backend). Dataset embebido e semeado para disparar cada armadilha; as consultas correm ao vivo (Δ reais).
- **Diagrama de entidades** (ER) das 6 tabelas, com as sementes anotadas (FK que pode ser NULL, soft delete, vendedores sem vendas).
- **Traga-o-seu-chatbot:** botão que copia o prompt (schema + pergunta) para colar no ChatGPT/Claude. Quem não tiver à mão, carrega "usar uma resposta típica de LLM".
- **Comparação automática:** o resultado do formando vs. a referência → coincide / não coincide + explicação.
- **Consola SQL** livre para exploração. Linguagem formal (sem tratamento por "tu").

## Como correr
O WASM/worker **não corre bem via `file://`** — serve por http:
```bash
cd sandboxes/ex1-sql-que-mente
python3 -m http.server 8000
# abrir http://localhost:8000
```
(Para deploy, qualquer host estático serve — ex.: GitHub Pages.)

## Os 6 desafios (Δ reais do dataset)
| Pergunta | Típico (LLM) | Referência | Pitfall |
|---|---|---|---|
| Quantos clientes temos? | 12 | 5 | grain (`COUNT(*)` em encomendas) |
| Comissão média por vendedor | 3250 | 1625 | linhas em falta (vendedores sem vendas caem do denominador) |
| Produtos nunca vendidos | 0 linhas | 2 linhas | NOT IN + NULL |
| Receita total (cêntimos) | 109 500 | 63 500 | fan-out do join |
| Quantas encomendas? | 12 | 9 | soft delete (`estado='cancelada'`) |
| Qual o cliente que mais gastou? | Bruno (17 000) | Carla (16 500) | soft delete escondido num `GROUP BY` |

## Notas
- Tem um visualizador do dataset (tabelas + nº de linhas) e um playground de SQL livre para explorarem.
- Verificado por render headless (chromium): motor carrega, dataset semeia, as 5 queries correm e os Δ batem certo.
- Próximos: ligar ao deck (link a partir do slide "O SQL que mente") e, se quiseres, um modo "ao vivo" com chamada a um LLM para gerar o SQL na hora.
