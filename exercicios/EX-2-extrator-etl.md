# EX-2 — "O extrator" (LLM-como-ETL: não-estruturado → estruturado)

**Porta:** B · **Momento da aula:** secção 4.1 (o LLM como passo de transformação) · **Formato:** demo guiada da formadora (precisa de API) + os formandos experimentam *inputs* próprios se houver tempo.

## Objetivo pedagógico
Ver o **T** do ETL ser um LLM: texto livre não-estruturado → registo estruturado com schema. E — porque o arco é "capacitar com olho crítico" — ver as **falhas concretas** que tornam isto engenharia, com método.

## O que o formando vê (passo a passo)
1. Um texto livre à esquerda.
2. O schema-alvo (campos + tipos) ao centro.
3. O JSON extraído à direita, com **validação** (Pydantic) e **flags** de problema.
4. Um *toggle*: **com** schema-constraint vs **sem** → mostra a diferença em estabilidade e formato.
5. Correr 2× a mesma entrada → ver que a saída pode mudar (não-determinismo).

## Muitos exemplos diversos (a riqueza pedida)
Cada exemplo expõe uma falha ou força diferente. Misturar domínios de propósito — o público é informático.

| # | Domínio | Input (texto livre) | Schema-alvo | O que ilustra |
|---|---|---|---|---|
| 1 | Saúde | Nota de triagem: "Homem 54a, HTA, sem alergias conhecidas, medicado com ramipril." | `{idade, sexo, diagnosticos[], alergias[], medicacao[]}` | **Negação** ("sem alergias" → `[]`, não `["alergias"]`); **normalização** ("HTA"→"hipertensão") |
| 2 | Suporte | E-mail: "Boa tarde, desde a atualização de ontem o vosso app crasha ao exportar PDF no Windows 11. Já reinstalei. Urgente!" | `{categoria, severidade, plataforma, passos_tentados[], pedido_reembolso:bool}` | **Inferência** (severidade/urgência); **campo ausente** (reembolso não mencionado → não inventar) |
| 3 | Jurídico | Cláusula: "O contrato renova-se automaticamente por períodos de 12 meses salvo denúncia com 60 dias de antecedência." | `{renovacao_automatica:bool, periodo_meses, pre_aviso_dias}` | **Extração numérica** de prosa; risco de trocar 12 e 60 |
| 4 | Finanças | Recibo OCR (ruidoso): "MERCADO XPTO  TOTAL 23,90 EUR  IVA 6%  2026-06-12" | `{comerciante, total, moeda, iva_pct, data}` | **OCR sujo**; **vírgula decimal** PT (23,90 não 2390); data ISO |
| 5 | RH | CV (excerto): "5 anos como backend dev em Python e Go; liderou equipa de 4; mestrado em EI pela FEUP." | `{anos_experiencia, linguagens[], gestao:bool, grau, instituicao}` | **Síntese** ("liderou equipa"→`gestao:true`); siglas ("EI", "FEUP") |
| 6 | Logística | Morada livre: "R. das Flores 12 3ºEsq, 4000-001 Porto" | `{rua, numero, andar, codigo_postal, cidade}` | **Parsing de morada** PT; campos opcionais (andar) |
| 7 | E-commerce | Review: "Adorei o material mas veio com um risco na lateral, e a entrega atrasou 3 dias." | `{sentimento, aspetos[{aspeto, polaridade}], problema_entrega:bool}` | **Sentimento por aspeto** (misto: produto+, entrega−) |
| 8 | DevOps | Linha de log: "2026-06-12T14:03:11Z ERROR db-pool timeout after 30000ms host=pg-primary" | `{timestamp, nivel, componente, mensagem, host, timeout_ms}` | **Texto semi-estruturado**; o LLM é exagero aqui → ver "quando NÃO usar LLM" |
| 9 | Saúde | Carta de alta (parágrafo longo) | `{diagnostico_principal, diagnosticos_secundarios[], medicacao_alta[], seguimento}` | **Documento longo**; o que cabe no contexto; consistência |
| 10 | Imobiliário | Anúncio: "T2 remodelado, 78m², 2º andar com elevador, 320.000€, Cedofeita" | `{tipologia, area_m2, andar, elevador:bool, preco, zona}` | **Mix de tipos** numa frase curta |

> A entrada #8 é deliberadamente um caso onde **um regex é melhor que um LLM** — serve o fio crítico ("quando NÃO usar LLM": determinístico, mais barato, auditável).

## As falhas a destacar (o lado "olho crítico")
- **Negação:** "sem X" tem de virar ausência, não presença.
- **Normalização de vocabulário:** "HTA"/"hipertensão"/"tensão alta" → uma forma canónica. Quem define o vocabulário?
- **Alucinação de campos:** preencher o que o texto não diz. Mitigar com "devolve `null` se não estiver presente".
- **Não-determinismo:** correr 2× → saídas diferentes. Colide com a **idempotência** que a engenharia de dados exige (ligação à secção 1).
- **Formato:** sem schema-constraint, o JSON parte (chaves a mais, vírgulas, prosa à volta).

## A resposta de engenharia
Schema-constrain (Pydantic / structured output) + validação + flag de baixa confiança + regra "null se ausente". **O LLM exige o contrato de dados, não o dispensa.**

## Stack candidato
Python + Pydantic + chamada a um modelo (Claude). Único exercício que precisa mesmo de API → fica como **demo da formadora**. Pré-capturar 2–3 saídas para o caso de a rede falhar na sala.

## Esforço
Médio. Curar os ~10 inputs (cada um a disparar a sua falha) e os schemas. Opcional: app simples (Streamlit/Gradio) com os toggles.

## Decisões em aberto
- Quais dos 10 entram na demo principal (sugiro #1, #2, #4, #8) e quais ficam de reserva.
- Mostrar código (Pydantic) ou só o resultado? (Para informáticos, mostrar o código vale a pena.)
