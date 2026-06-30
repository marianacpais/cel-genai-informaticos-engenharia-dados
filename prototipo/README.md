# Protótipo de diagramas — Embeddings (identidade CeL)

Protótipo visual para a Mariana inspecionar a fasquia dos diagramas antes de produzir o resto da aula em série.

## Como ver
Abrir `index.html` num browser. Navegar com as setas. **Dois slides têm revelação progressiva (clicar/seta-baixo):**
- *Vamos abrir a caixa* — a cadeia preenche-se caixa a caixa.
- *Porque é que perto = parecido?* — pares positivos/negativos e depois o painel de síntese.

## O que está aqui
8 slides: capa · separador · cadeia que se preenche · tokenização · "banco" contextual · a média dilui (scatter) · treino contrastivo + painel · card de fecho.

## Ficheiros
- `index.html` — o deck (reveal.js via CDN).
- `cel-base.css` — cópia do CSS do design CeL (tokens da marca, padrões de slide, animações).
- `diagrams.css` — componentes de diagrama desta aula (pílula-vetor, tiles de token, cadeia, scatter, forças, painel escuro, cards de contraste).
- `assets/logo-cel-horizontal.svg` — logo.

## Notas técnicas
- A cadeia usa `:has()` para acender a caixa quando o seu rótulo (fragmento) fica visível.
- Os diagramas SVG (scatter, forças) são desenhados à mão; rótulos com halo (`paint-order`) para assentarem sobre as linhas.
- Verificado por render headless (chromium) a 1920×1080.
