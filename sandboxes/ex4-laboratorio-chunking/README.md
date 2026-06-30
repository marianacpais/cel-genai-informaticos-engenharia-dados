# Sandbox EX-4 — "Laboratório de chunking"

App de browser (ficheiro único, `index.html`). Mini-RAG real (transformers.js) onde o formando muda a **estratégia de chunking** e vê o retrieval acertar ou falhar — com o mesmo documento e o mesmo modelo.

- **Motor:** transformers.js + `Xenova/paraphrase-multilingual-MiniLM-L12-v2` (mesmo do EX-3/EX-5). Sem backend; modelo em cache após a 1ª vez.

## Fluxo
1. **Documento** editável; o facto-chave ("5 tentativas") está a meio de um parágrafo, de propósito.
2. **Estratégia**: tamanho fixo (sem/com overlap, tamanho e overlap configuráveis), por parágrafo, por frase → **Partir e indexar**. Os chunks aparecem, com o facto destacado.
3. **Pergunta** + facto a encontrar → **Recuperar**: mostra o chunk recuperado (realçado) e se contém a resposta.

## O contraste (verificado)
| Estratégia | Resultado |
|---|---|
| Tamanho fixo · 90 car (default) | **✗** o corte parte a resposta; recupera o chunk com "tentativas de login" mas o "5 tentativas" ficou noutro chunk |
| Tamanho fixo · 70 car | ✓ (o facto fica inteiro) |
| Por parágrafo | **✓** o parágrafo inteiro é um chunk |

Mesma pergunta, mesmo modelo, resultado oposto — só mudou a engenharia de dados.

## Como correr
```bash
cd sandboxes/ex4-laboratorio-chunking
python3 -m http.server 8000   →  http://localhost:8000
```

## Notas
- Liga diretamente ao slide "O chunking importa" do deck (o exemplo do facto partido a meio).
- Verificado por render headless: fixo-90 falha, fixo-70 e parágrafo acertam.
