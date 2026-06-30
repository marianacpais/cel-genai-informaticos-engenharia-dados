# Versões do deck

Snapshots auto-contidos em `versions/<versão>/`. O deck **vivo** (último) está na raiz (`index.html` + `cel-base.css` + `diagrams.css` + `assets/`).

## 0.1.0 — deck a fundo (congelado em `versions/0.1.0/`)
- **121 slides.** Identidade CeL, diagrama-primeiro.
- Estrutura: Abertura → Fundações de engenharia de dados → As duas portas → Porta A (copiloto: 11 armadilhas de SQL, schema linking, dbt, migração, qualidade, lineage, copilotos) → Porta B (LLM-como-ETL, embeddings núcleo + suplementar, RAG completo) → Fio crítico → Síntese.
- Construído com 4 agentes em paralelo (Fundações · Porta A · ETL+embeddings · RAG+crítico), montado e renderizado.
- Backup da 1ª passagem (43 slides) em `index.pre-assembly.html`; blocos por agente em `build/`.

## 0.2.0 — (em planeamento)
- Rework a fundo a partir do feedback. Nasce como cópia do 0.1.0 na raiz; congela-se em `versions/0.2.0/` quando estabilizar.
- Notas do orador a escrever sobre esta versão.
