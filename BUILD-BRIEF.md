# Build Brief — slides do deck "GenAI na Engenharia de Dados" (Aula 5)

Instruções partilhadas para quem produz slides desta apresentação. **Lê isto + os ficheiros referidos antes de escrever.** O objetivo é uma aula **enorme, a fundo, muito visual**, na identidade CeL.

## Contexto
- Aula 5 do curso *GenAI para Informáticos* (CeL). Público: **informáticos**, não profissionais de saúde. Turma mista em engenharia de dados → começar firme, subir.
- Formadora: Mariana Canelas Pais. Língua: **português de Portugal**.
- Tese central: a GenAI entra na engenharia de dados por **duas portas** — A (copiloto *do* engenheiro) e B (engenheiro a construir *para* a GenAI). Arco: **capacitar com olho crítico**.

## Ficheiros a ler antes de escrever (obrigatório)
- `index.html` — o deck atual. **A secção de embeddings (slides "A cadeia" → "O que distingue os dois") é o PADRÃO-OURO de profundidade e estilo. Imita esse nível.**
- `diagrams.css` e `cel-base.css` — **os componentes que PODES usar (lista abaixo). Não inventes CSS novo sem necessidade; reutiliza estas classes.**
- `EMBEDDINGS.md`, `BRAINSTORM.md`, `OUTLINE.md`, `SOURCES.md`, e os `exercicios/EX-*.md` relevantes — conteúdo e catálogos (ex.: as 11 armadilhas de SQL estão em `BRAINSTORM.md`; os 10 exemplos de ETL em `exercicios/EX-2-extrator-etl.md`).

## Regras de voz (CLAUDE.md)
- Direto, sem linguagem corporativa nem de marketing ("a promessa é clara", "sem precedentes" → **proibido**).
- **PROIBIDO o registo "magia"**: nada de "não é magia", "não mágico", "caixa preta mágica", "receita mágica", "menos magia". A Mariana acha-o nojento. Para desmistificar, descrever o mecanismo (foi treinado/otimizado/desenhado assim) — sem nunca invocar a palavra magia.
- Anti-slop: **pouco texto no slide**; a profundidade vive no **diagrama** e nas `<aside class="notes">`.
- **Um conceito por slide**, construído passo a passo. Estrutura assimétrica (nem todas as secções com o mesmo nº de slides).
- Ritmo de frase variado. Cético onde deve, entusiasta na Porta B.
- "Errado-vs-certo": usar **azeitona** (`#6D6D3E`), **nunca vermelho** (a spec CeL proíbe cores quentes para aviso).

## Padrão de slide (copiar exatamente)
```html
<section class="content-slide">
  <span class="label-tag">Etiqueta da secção</span>
  <h2>Título curto e concreto</h2>
  <div class="separator"></div>
  <div class="stage">
    <!-- DIAGRAMA / componente aqui (o .stage centra na vertical e preenche) -->
  </div>
  <div class="footer"><img class="footer-logo" src="assets/logo-cel-horizontal.svg" alt=""><span class="slide-num">NN</span></div>
  <aside class="notes">O que a formadora diz (profundidade teórica vive aqui).</aside>
</section>
```
Separador de secção:
```html
<section class="section-divider" data-transition="fade" data-background-transition="fade">
  <p class="label">Etiqueta</p><h2>Título da secção</h2>
</section>
```
> Não escrevas `<html>/<head>/<script>` — só os `<section>`. Não te preocupes com o número do slide (`NN`), eu renumero.

## Paleta (tokens já definidos)
petróleo `#2F4E5E` (títulos, caixas, painéis) · azeitona `#6D6D3E` (acentos, "errado", setas) · sálvia `#B8B59D` (fundos suaves, nunca texto) · ardósia `#74808D` (texto 2º, legendas) · texto `#3A3838` · fundo `#F3F3F3` · branco. Mono para vetores/SQL/JSON.

## Biblioteca de componentes (REUTILIZA — não reinventes)
Cada um já tem CSS. Vê exemplos vivos no `index.html`.

- **Cadeia/pipeline:** `.chain` > `.chain-col` ( > `.chain-box` ou `.chain-box.fill` + `.cap` legenda ) com `.arrow` entre colunas; `.node` para extremos (texto). Para revelação progressiva: `.chain-box` com `<span class="q">?</span><span class="lbl fragment" data-fragment-index="N">label</span>`. `.chain-hint` p/ legenda final.
- **Pontos com marcador:** `.points`(`.two` p/ 2 colunas) > `.point`(`.m` azeitona ou `.m.sq2` petróleo) > `<p><b>título</b><span class="sub">subtítulo</span></p>`.
- **Duas colunas:** `.cols2`(`.l-wide`).
- **Cards de info:** `.card`(`.secondary`/`.accent`) borda esquerda.
- **Grelha de cartões com ícone:** `.tiles-grid`(`.three`) > `.tcard` > `<i class="fa-solid fa-...">` + `<h4>` + `<p>` (Font Awesome 6 disponível).
- **Duas portas / comparação:** `.doors` > `.door.a` (claro) / `.door.b` (petróleo) > `.kick`+`h3`+`p`+`.foot`.
- **Errado-vs-certo (SQL/código):** `.qbar`(`.ico`) pergunta em NL; `.sql-grid` > `.sql-card.wrong`/`.sql-card.right` > `.tag`+`<pre>`código+`.res`(com `<b>`número); fechar com `<p class="why"><b>Pitfall:</b> …</p>`. **Reutiliza este para qualquer "o LLM gera X errado / o certo é Y".**
- **Painel escuro de síntese:** `.panel-dark` > `.big` + `.tags`.
- **Callout (aviso/nota):** `.callout` > `.k` (título) + `<p>`.
- **Contraste 2 cards:** `.contrast` > `.c` / `.c.weight`(petróleo, com `.chips`).
- **Camadas do stack:** `.layers` > `.layer.l1/.l2/.l3` (com `.tag` mono à direita).
- **Vetor/tokens:** `.vec`(`.warm`/`.cool`), `.tiles`>`.tile`(`.hl`/`.big`)+`.id`, `.word-arrow`.
- **Fluxo ETL:** `.etl` > `.doc` + `.ar`(seta) + `.llm`(+`.sch`) + `.ar` + `.out` (usa `.neg`/`.ok` para destacar negação/[]).
- **Citação grande:** `.zeitgeist` > `.quote`(com `<em>`) + `.src`.
- **Diagrama SVG à mão:** `.figure` (define `max-width` inline) > `<svg viewBox="0 0 1000 H">`. Usa `.svg-label`(`.muted`) p/ rótulos (têm halo). Cores = paleta. Vê o scatter "a média dilui" e o diagrama de forças "treino contrastivo" como referência. **Inventa SVG novo quando o conceito for geométrico** (ex.: ETL/ELT, schema drift, DAG, HNSW, fan-out de join, retrieval híbrido).

## Regras de layout (lições já aprendidas — não repitas os erros)
- O corpo vai **sempre** dentro de `<div class="stage">` (centra na vertical, preenche a altura).
- **Caixas `.chain-box` não cabem palavras > ~11 caracteres** a 22pt → usa rótulos curtos (ex.: "armazém", não "armazenamento") ou deixa a legenda `.cap` levar o resto.
- Diagramas **grandes** (preencher o ecrã), pouco texto. Evita o slide "colado ao topo".
- SVG: define `max-width` no `.figure` para a altura não rebentar (viewBox largo e baixo).

## Saída esperada
Devolve **apenas** a sequência de `<section>…</section>` da tua secção (com um `<section class="section-divider">` no início se fizer sentido), pronta a colar. Comenta cada bloco com `<!-- TÍTULO -->`. Profundidade: **muitos slides pequenos, um conceito cada**. Não poupes — a aula é para ser enorme.
