# Sandbox EX-2 — "O extrator" (LLM-como-ETL)

App de browser (ficheiro único, `index.html`, **JS puro — sem modelo nem API**). O formando escolhe um caso (texto sujo + schema-alvo), **leva ao seu chatbot**, traz o JSON e cola-o. O sandbox compara com uma **extração de referência** e sinaliza onde a extração escorrega — estrutural e, sobretudo, **semanticamente**.

## Casos (7, crescentemente complexos)
- **Nível 1** — Saúde (triagem) · Suporte (e-mail) · Finanças (recibo OCR).
- **Nível 2** — Jurídico (cláusula de renovação) · RH (CV denso).
- **Nível 3** — Saúde (nota clínica longa) · Imobiliário (anúncio denso).

Texto cada vez mais denso → o LLM tropeça mais.

## Pitfalls semânticos (o que o sandbox apanha)
Cada caso tem campos curados com a sua armadilha; a validação compara com a referência e explica a divergência:
- **Âmbito da negação** — "Nega febre, tosse ou dor torácica" → nenhum é sintoma.
- **Negação** — "Sem alergias" → `[]`, não `["medicamentosas"]`.
- **Raciocínio temporal** — varfarina *suspensa* / apixabano *iniciado* → medicação atual é só o apixabano.
- **Âmbito da condição** — "salvo se… denunciar" → a renovação é automática até alguém denunciar (não `false`).
- **Confusão de grandezas** — 12 meses (duração) vs 60 dias (pré-aviso).
- **Coreferência numérica** — 7 anos de experiência vs 3 a liderar.
- **Qualificador ignorado** — "experiência pontual em Rust" não é linguagem sólida.
- **Em conclusão ≠ concluído** — mestrado em curso → `null`.
- **Campo ambíguo** — área útil (92) vs bruta (110).
- **Unidades** — `23,90` € vs `2390`.

Mais a validação **estrutural** (tipos, obrigatórios, campos inventados). Botões "carregar referência / extração típica de LLM" para demonstrar sem chatbot.

## Como correr
JS puro — funciona até por `file://`, mas para consistência:
```bash
cd sandboxes/ex2-extrator-etl
python3 -m http.server 8000   →  http://localhost:8000
```

## Notas
- A lição: o JSON pode passar toda a validação estrutural e estar semanticamente errado — é aí que entra a comparação com referência e a revisão humana.
- Verificado por render headless: os 7 casos disparam as bandeiras certas; a referência valida limpo.
