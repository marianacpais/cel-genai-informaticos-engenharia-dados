# Embeddings a fundo — "como se produzem"

Material de apoio para a secção 4.2 (núcleo da Porta B). Profundidade pedida: **como se produzem**, não só "o que são". Público informático, turma mista → começar firme e subir.

O objetivo é **desmistificar**: no fim, ninguém deve achar que o embedding é opaco. A geometria do espaço foi *otimizada* para pôr significados próximos perto — e isso tem consequências de engenharia de dados muito concretas.

---

## A cadeia de produção (passo a passo)

### 1. Tokenização — texto vira tokens, não palavras
- O modelo não vê "hipertensão"; vê tokens de sub-palavra (BPE / WordPiece). "hipertensão" pode partir em `hiper` + `tensão`; "antihipertensor" reaproveita os mesmos pedaços.
- **Porquê sub-palavra:** lida com palavras nunca vistas (OOV), morfologia e erros ortográficos sem um vocabulário infinito.
- ▶ Demo: passar 2–3 frases por um tokenizador e mostrar os IDs. Ver "São Paulo" vs "saopaulo" tokenizar diferente já planta a semente de que **a forma do texto importa** (liga à normalização do ETL).

### 2. Matriz de embeddings — cada token tem um vetor aprendido
- Existe uma tabela `vocabulário × d` (ex.: 50k tokens × 768). Cada token → uma linha = um vetor. É um *lookup*, não um cálculo.
- Este é o nível "estático" (à word2vec): "banco" tem **um só** vetor, independentemente do contexto. Insuficiente — daí o passo seguinte.

### 3. Contextualização — a atenção torna o vetor dependente do contexto
- Os vetores estáticos passam por camadas de *self-attention* (Transformer). Cada token "olha" para os vizinhos e o seu vetor é reescrito em função deles.
- **O salto:** "banco" em "sentei-me no banco" ≠ "banco" em "fui ao banco". Vetores diferentes para o mesmo token. É a diferença entre embeddings *estáticos* (word2vec/GloVe) e *contextuais* (BERT e descendentes).
- ▶ Demo (a mais marcante): embeber a mesma palavra em dois contextos e mostrar que a semelhança cosseno entre os dois é baixa.

### 4. Pooling — de muitos vetores de token a UM vetor por chunk
- O vector store guarda **um** vetor por chunk, não um por token. Como se colapsa?
  - **Mean pooling** (média dos tokens) — o mais comum em sentence-transformers.
  - **[CLS]** — usar o vetor de um token especial (modelos encoder).
  - **Last-token** — modelos decoder (estilo GPT) usam o último.
- **Consequência crítica de DE:** a média **dilui**. Um chunk gigante e misturado vira um vetor "morno" que não está perto de nada. Isto é *porque* o chunking importa — é decisão de engenharia, não de IA.
- ▶ Demo: embeber um chunk focado vs um chunk enorme e mostrar que o focado recupera melhor.

### 5. O objetivo de treino — *por que* proximidade = significado
- Esta é a parte que costuma faltar e que fecha tudo. Modelos de embedding (ex.: sentence-transformers) são treinados com **aprendizagem contrastiva**: dados pares positivos (parecidos) e negativos (diferentes), o treino **puxa os positivos para perto e empurra os negativos para longe** (losses tipo InfoNCE / triplet).
- Logo: a semelhança cosseno encode significado **porque foi literalmente o objetivo de otimização**. Não é emergente nem acidental — foi desenhado assim.
- Corolário honesto: "parecido" é definido pelos pares de treino. Um modelo geral pode achar "Java" (ilha) e "Java" (linguagem) parecidos; um modelo de domínio não. → motiva modelos de domínio / fine-tuning.

### 6. Dimensão e normalização (fechar o concreto)
- Dimensões típicas: 384 / 768 / 1024 / 1536. Mais dimensões = mais nuance, mas mais storage e índice mais caro.
- Vetores normalizados (L2) → **cosseno = produto interno**, mais barato de calcular à escala. Detalhe que importa quando são milhões de vetores.

---

## As consequências de engenharia de dados (o gancho de volta à aula)

Isto é o que distingue "saber o que é um embedding" de "ser engenheiro de dados de IA":

1. **O modelo de embedding é uma decisão de schema.** Trocar de modelo = **migração que recalcula TUDO**. Vetores de modelos diferentes vivem em espaços diferentes e **não se misturam** — comparar um vetor do modelo A com um do modelo B não tem significado. É uma *breaking change*.
2. **A dimensão comanda custo.** Storage do vector store e custo do índice (HNSW) escalam com a dimensão e o nº de vetores. Escolha de arquitetura, não detalhe.
3. **Versionar embeddings é linhagem.** A versão do modelo faz parte dos metadados do dado derivado. Sem isso, não sabes com que modelo cada vetor foi feito → caos na próxima migração.
4. **Embeddings são dados DERIVADOS → ficam stale.** Quando a fonte muda, o vetor fica desatualizado. **CDC**: re-embeber só o que mudou (incremental), não rebuild total. (Liga a SB-5.)
5. **Idioma e domínio.** O modelo trata bem PT? Jargão técnico/clínico? Um modelo geral pode ser fraco em domínio → fine-tuning ou modelo especializado. Decisão de qualidade de dados.

---

## Como dar isto sem virar aula de ML (rampa para turma mista)
- Entrar pela intuição (texto → vetor, perto = parecido) e **só depois** abrir a caixa (tokenização → atenção → pooling → treino).
- Cada passo da cadeia tem uma demo de 30s associada → conceito entrelaçado com prática, como pedido.
- Travão: **não** treinar modelos, **não** derivar atenção matematicamente. Mostrar o suficiente para desmistificar e para justificar as 5 consequências de DE. O critério é "isto muda como desenho o pipeline?", não "isto é bonito de saber".

## Pendente
- Escolher o modelo de embedding para as demos (algo pequeno que corra no browser via `transformers.js`, ou pré-calcular).
- Decidir se a projeção 2D usa PCA (simples, determinístico) ou UMAP (mais bonito, mais dependências).
