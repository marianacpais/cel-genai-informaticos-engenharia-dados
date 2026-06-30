# Notas de design — Aula 5 (identidade CeL, slides diagrama-primeiro)

Fonte canónica: [`projects/library/didactica/designs/cel/DESIGN.md`](../../library/didactica/designs/cel/DESIGN.md). Referência visual de tom: `.cellar/Claude Design - Curso GenAI.pdf` (deck CeL da Mariana). Build futuro: skill `/revealjs` + `/didactica`.

## Princípio condutor desta aula
**Diagrama primeiro, texto a seguir.** Cada slide deve carregar a profundidade num esquema robusto, não em bullets. Pouco texto no ecrã; a teoria vive nas notas do orador e na clareza do diagrama. Revelação progressiva (fragmentos) onde a ordem ensina.

## Paleta (do logo, canónica)
| Papel | Hex | Uso |
|---|---|---|
| Petróleo | `#2F4E5E` | títulos, separadores (fundo cheio), caixas de destaque |
| Azeitona | `#6D6D3E` | acentos, sublinhados, indicadores laterais, setas de processo |
| Sálvia | `#B8B59D` | fundos suaves de card/callout (nunca texto) |
| Ardósia | `#74808D` | texto secundário, legendas, números de slide |
| Texto | `#3A3838` | corpo (nunca `#000`) |
| Off-white | `#F3F3F3` | fundo principal |
| Branco | `#FFFFFF` | cards, texto sobre petróleo |

> Sinal de aviso: **não** usar laranja/vermelho — usar azeitona escurecido. (Importante porque esta aula tem muitos momentos "errado vs certo".)

## Tipografia
- Títulos: **Libre Franklin** 600/700. Corpo: **Open Sans** 400/600. Código/vetores/SQL: **mono** (JetBrains/IBM Plex).
- Etiqueta de secção: ALL CAPS pequena, tracking +0.08em, cor azeitona, com linha de acento curta.
- Nunca centrar parágrafos; centrar só títulos curtos e capa.

## Vocabulário de slides (padrões CeL que vou usar)
- **Separador de secção:** fundo petróleo cheio, etiqueta + título branco à esquerda. Transição fade.
- **Slide de conteúdo:** fundo off-white, etiqueta + título no topo, linha fina sálvia a separar, diagrama a ocupar o corpo.
- **Card:** fundo branco, borda esquerda 3px (azeitona ou petróleo), sem sombra, margem interna generosa.
- **Callout:** fundo sálvia ~30%, indicador lateral azeitona; legenda em ardósia por baixo.
- **Painel de síntese:** caixa petróleo cheia, texto branco — para a conclusão de um diagrama (estilo "→ painel escuro" do slide 4 do PDF dela).
- **Diagrama de processo:** caixas ligadas por setas/linha azeitona; a linha desenha-se (scaleX) na entrada.

## Convenções de diagrama (para esta aula)
- **Vetor** = pílula mono com números: `[0.12 −0.04 0.91 …]`. Sempre o mesmo motivo visual quando falamos de embeddings.
- **Token** = tile/azulejo pequeno com legenda; palavra parte-se em tiles.
- **Espaço semântico** = scatter 2D com pontos rotulados; proximidade = significado.
- **Errado vs certo** = duas colunas; o errado marcado com indicador azeitona-escuro (não vermelho), o certo com petróleo. (SQL, ETL, staleness.)
- **Pipeline de dados** = caixas em fila com a linha de processo; reaproveitar o mesmo motivo em embeddings, RAG e staleness para a turma reconhecer.

## A "espinha visual" da secção de embeddings
Um diagrama recorrente: `texto → [tokenização] → [matriz] → [atenção] → [pooling] → [treino] → vetor`. Aparece **fechado** (com "?") no início e **preenche-se** ao longo da secção — cada passo acende a sua caixa. Fecha **aberto** no slide de síntese. É o fio que cose a secção.
