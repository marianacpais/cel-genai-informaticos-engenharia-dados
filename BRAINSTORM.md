# Brainstorm — Aula 5: Engenharia de Dados × GenAI

Documento vivo. Captura a tese, as decisões e as perguntas em aberto. Vamos lendo e mexendo.

---

## Onde a aula encaixa no arco

- Aula 2 → documentação de software (modelo ER, APIs).
- Aulas 3–4 → ciência de dados sobre um *dataset já existente* (explorar + estatística).
- **Aula 5 (esta) → engenharia de dados:** a fábrica que produz e mantém esse dataset. *De onde veio o Excel? Quem o limpou? Como se mantém fresco?*

Gancho de abertura: aulas 3–4 trataram os dados como adquiridos; nós mostramos a canalização que está antes.

---

## Tese central — as duas portas

> A GenAI entra na engenharia de dados por duas portas opostas. A segunda mudou o que um engenheiro de dados *é*.

**Porta A — GenAI como copiloto DO engenheiro de dados.** Atravessar (é o esperado), mas sem demorar. Aqui mora a demo do **text-to-SQL confiantemente errado**.

**Porta B — o engenheiro de dados a construir PARA a GenAI.** ⭐ A estrela da aula. Categoria de trabalho nova. Foco em **embeddings** e na ideia de que "fazer IA" é, na prática, 80% engenharia de dados.

**Agentes:** FORA desta aula (reservado para sessões futuras). Mencionar de passagem no fecho, no máximo.

---

## Decisões tomadas (de conversa com a Mariana)

1. Porta A atravessada, Porta B aprofundada. Embeddings é obrigatório.
2. Slides **concretos e fechados**, anti-slop. Nada de bullets vagos; cada slide com um exemplo ou um número.
3. Conceitos **entrelaçados** com prática (não "primeiro teoria, depois lab").
4. **Fora do Colab** (já gasto nas aulas 3–4). Precisamos de um sandbox novo — ver secção própria.
5. Duas demos âncora: **LLM-como-ETL** (texto livre → JSON) + **text-to-SQL**.
6. Público informático, não-saúde → **aligeirar** RGPD/saúde. Governança/lixo-à-entrada: mencionar, não centrar.
7. A demo de ETL pode manter texto clínico-ish (a Mariana está à vontade), mas o enquadramento é genérico.
8. **Exemplos de SQL em domínio de saúde** (a Mariana domina-o melhor), não vendas. A armadilha favorita — média inflacionada por linhas em falta — recasta-se em "tempo médio até primeira consulta".
9. **Sandbox NÃO vai no HTML do deck.** É artefacto separado. Ordem de trabalho: (a) apresentação teórica → (b) **planificar** grandes sandboxes candidatos (ver `SANDBOX-PLANS.md`) → (c) só depois aprofundar/implementar.
10. **Dimensionar para ~5h** de conteúdo. Preferir conteúdo a mais.
11. **Data: 1 jul 2026** (sessão de 17 jun não se realizou; curso deslizou).
12. **Domínios variados, não só saúde.** Público é informático, não profissional de saúde → exemplos espalhados por vendas, suporte/SaaS, RH, logística + alguns de saúde. Fio narrativo **misto/independente**: as demos podem partilhar um cenário, mas os exemplos teóricos escolhem o domínio mais claro para cada ponto.
13. **Embeddings a FUNDO — "como se produzem"** (tokenização → matriz de embeddings → contextualização → pooling → objetivo de treino). Ver `EMBEDDINGS.md`.
14. **Turma mista em DE → desenhar com rampa:** base rápida mas sólida (secção 1) e depois profundidade.
15. **Arco: capacitar com olho crítico.** Entusiasmo na Porta B; ceticismo cirúrgico na Porta A. Não é uma aula cautelar nem hype.
16. **Sem travão de profundidade.** Assumir capacidade de aprendizagem infinita; explicar conceitos complexos passo a passo, slides bem delineados e espalhados. Conteúdo diferenciado (mais para uns, mais para outros) a Mariana gera depois. Cai a preocupação anterior com a turma mista.
17. **Cada exercício prático tem um doc próprio** em `exercicios/` (spec detalhada para inspeção). O `SANDBOX-PLANS.md` fica como índice/visão-geral.
18. **ETL com muitos exemplos diversos** (vários domínios) — quantas mais ilustrações melhor.
19. **Design: identidade CeL** (didactica `designs/cel`). Referência de tom: `.cellar/Claude Design - Curso GenAI.pdf`. Tokens e convenções em `DESIGN-NOTES.md`.
20. **Slides diagrama-primeiro.** Pouco texto no ecrã; a profundidade vive no esquema visual e nas notas do orador. Cada slide especifica o diagrama concretamente. Fio visual recorrente nos embeddings (cadeia que se preenche).

---

## Demo 1 — "Já não é preciso escrever SQL" é mentira (text-to-SQL confiantemente errado)

**Objetivo pedagógico:** provar por A+B que perguntas em linguagem natural são ambíguas e o LLM resolve a ambiguidade *silenciosamente* — gerando SQL **sintaticamente perfeito, que corre sem erro, e dá o número errado**. O perigo não é o crash; é a corrupção silenciosa.

Punchline para slide: *o LLM não conhece o **grain** da tua tabela, as tuas definições de negócio, nem o comportamento dos teus NULLs.*

### Catálogo de armadilhas — multi-domínio (escolher 4–5 para a demo)

Espalhadas por vendas, suporte/SaaS, RH e saúde de propósito — o público é informático, não da saúde. Cada pitfall é universal; o domínio só muda a roupagem.

| # | Domínio | Pergunta NL | O que o LLM faz | Porque está errado | Pitfall |
|---|---|---|---|---|---|
| 1 | Vendas | "Quantos clientes temos?" | `COUNT(*)` em `encomendas` | Conta *encomendas*, não clientes. Um cliente com 5 compras conta 5×. Faltava `COUNT(DISTINCT cliente_id)`. | **Grain** |
| 2 | Suporte | "Tempo médio de resolução de tickets." | `AVG(dias)` sobre tickets fechados | Tickets ainda **abertos** (sem data de fecho) caem fora → a média parece melhor do que a realidade. | **Linhas em falta** (a favorita) |
| 3 | Saúde | "Tempo médio até à primeira consulta." | `AVG(dias)` sobre quem teve consulta | Doentes que **nunca** tiveram consulta não estão na tabela → caem do denominador. Mesma armadilha, roupa clínica. | **Linhas em falta** |
| 4 | Vendas | "Produtos que nunca foram vendidos." | `id NOT IN (SELECT produto_id FROM vendas)` | Um único `NULL` em `vendas.produto_id` devolve **conjunto vazio** sem erro. | **NOT IN + NULL** |
| 5 | SaaS | "Mostra os utilizadores ativos." | inventa `WHERE estado='ativo'` | "Ativo" = login < 30 dias? subscrição não cancelada? Definição de negócio que o LLM adivinha; pode nem existir a coluna. | **Definição de negócio** |
| 6 | Vendas | "Qual o produto mais vendido?" | `COUNT(*)` por produto | "Mais vendido" = nº de unidades? nº de encomendas? receita? Escolhe um critério em silêncio. | **Definição ambígua** |
| 7 | Vendas | "Receita total por região." | join `encomendas` × `linhas_encomenda` | Várias linhas por encomenda → receita **contada em dobro** (fan-out do join). | **Double counting** |
| 8 | RH | "Salário médio por departamento." | `AVG(salario)` | Inclui contratados a part-time/estagiários sem normalizar? Pessoas sem departamento (`NULL`) caem fora do `GROUP BY`. | **NULL no group / grain** |
| 9 | Saúde | "Doentes com glicemia alta." | `WHERE glicemia > 126` | Mistura mg/dL e mmol/L; "alta" depende de jejum vs ocasional. Crava um limiar sem o contexto. | **Unidades / limiar** |
| 10 | Vendas | "Número de encomendas." | `COUNT(*)` | Encomendas `cancelada = true` contadas como reais (soft delete ignorado). | **Soft delete** |
| 11 | Logística | "Entregas em 2025." | `WHERE YEAR(data) = 2025` | Por data de envio ou de entrega? Uma entrega de dez/2024 a jan/2025 cai onde? | **Semântica de datas** |

**Estrutura da demo:** mostrar pergunta → SQL gerado (lindo) → resultado (errado) → SQL correto lado-a-lado → diferença no número. Repetir 3–4×. Fechar com: *o copiloto acelera quem já sabe ler SQL; é uma armadilha para quem o usa para não aprender.* (Liga à Aula 1: escrever → orientar → **validar**.)

---

## Demo 2 — LLM-como-ETL (não-estruturado → estruturado)

**Objetivo:** mostrar a Porta B inteira num exercício — o LLM *é* o passo de transformação que converte texto livre num registo estruturado com schema.

**Exemplo:** nota livre (ex.: triagem / sumário) → JSON `{idade, sexo, diagnóstico, medicação:[], alergias:[]}`, com schema (Pydantic / structured output).

**Mostrar o poder E as falhas concretas:**
- **Negação:** "sem alergias conhecidas" tem de virar `alergias: []`, não `["alergias"]`. Erro de negação é o clássico.
- **Normalização:** "HTA" vs "hipertensão arterial" — mesma entidade, formas diferentes. Quem decide o vocabulário canónico?
- **Alucinação de valores:** o LLM preenche um campo que o texto não tinha.
- **Determinismo:** correr duas vezes pode dar saídas diferentes → como é que isto entra num pipeline que exige idempotência?

**Lição:** schema-constrain + validação + flag de baixa confiança. O LLM não dispensa o contrato de dados — **exige-o**.

---

## Embeddings (núcleo da Porta B) — como dar isto concreto, sem slop

Pontos fechados a transmitir (cada um vira no máximo 1 slide):
1. **O que é um embedding:** texto → vetor de N dimensões onde proximidade = semelhança semântica. Não é palavra-chave; é significado.
2. **Porque importa para a engenharia de dados:** embeddings são **dados derivados**. Como qualquer coluna derivada, ficam *stale* quando a fonte muda → precisam de re-cálculo. (Ligação a CDC: tratar embeddings como dados incrementais, não lote único.)
3. **O pipeline de RAG é um pipeline de dados:** ingestão → chunking → embeddings → vector store → retrieval. Duas pipelines: *offline* (indexar) e *online* (consultar).
4. **Onde falha (failure modes de DADOS, não de IA):** chunking mau, dados rançosos, schema drift, ausência de contratos produtor↔consumidor. ← é aqui que entra a frase-zeitgeist.

Demo possível (ligada ao sandbox): calcular embeddings de meia dúzia de frases, mostrar a matriz de semelhança (cosine), e ver a busca semântica a funcionar — sem servidor, sem Colab.

---

## Sandbox (decisão em aberto — proposta abaixo)

Requisitos: fora do Colab; interativo; informáticos conseguem mexer; idealmente sem fricção de setup.

**Opção recomendada — sandbox no browser, dentro do próprio deck:**
- **Text-to-SQL:** [DuckDB-WASM](https://duckdb.org/docs/api/wasm/overview) corre SQL no browser, sem servidor. Dataset pequeno embebido → eles escrevem/colam SQL e veem o resultado errado vs. certo ao vivo. Zero setup.
- **Embeddings:** modelo pequeno via `transformers.js` (corre no browser) OU embeddings pré-calculados servidos em JSON + cálculo de cosine em JS. Mostra busca semântica sem chamar API.

**Alternativa — repo com devcontainer:** um GitHub Codespaces / devcontainer Python com notebooks `.py` (não Colab). Mais poderoso, mais setup, e arrisca cheirar a "Colab com outro nome".

**Trade-off:** browser/WASM = zero fricção e cabe no deck, mas o LLM-como-ETL precisa mesmo de uma chamada a um modelo (API key) — essa parte talvez fique como **demo guiada da formadora** em vez de hands-on de todos. A decidir.

---

## Itens estacionados

- ⚠️ **Data da aula por confirmar** (yaml diz 17 jun, já passou). Provável slot de julho.
- **Falta o sumário da Aula 4 (estatística)** — preciso dele para fechar a fronteira aula 4 ↔ 5 e não sobrepor.
- Decidir nome final do repo GitHub + visibilidade (público/privado) antes de fazer push.
- Decidir design: usar o design **cel** da didactica? (provável sim.)

## Perguntas em aberto para a próxima ronda

1. Sandbox: browser/WASM (recomendado) vs devcontainer?
2. Quanto tempo dos 3h damos a cada porta? Proposta: ~40% A (com a demo SQL), ~60% B (embeddings + ETL).
3. O LLM-como-ETL é hands-on-de-todos ou demo-da-formadora?
