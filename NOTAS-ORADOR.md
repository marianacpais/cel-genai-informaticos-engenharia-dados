# Notas de orador — GenAI na Engenharia de Dados (aula completa)

_Guião gerado a partir do deck. O reveal.js mostra estas notas na **vista de orador**: abre o deck e carrega em **`S`** (2.ª janela com nota + próximo slide + cronómetro). Nos separadores de secção a nota é a deixa de transição._

## Como usar

- **Não ler à letra.** Cada bloco é o *essencial a dizer* + as armadilhas a evitar.
- **Ritmo flexível.** Ajustar conforme o aquecimento. Diz-me a duração total e ponho tempos por secção.
- **Sessão remota:** interação por mão-no-ar do Zoom, chat ou microfone.
- **Regras da casa:** erro/aviso em **azeitona, nunca vermelho**; tom adulto; exemplos fora da saúde de propósito.
- **Sandboxes ao vivo (só 1, 2, 3):** há slides «Atividade · sandbox» com botão que abre em separador novo.


> _GenAI na Engenharia de Dados:_ Arranque. Apresentar-me e enquadrar: Aula 5, GenAI para informáticos — do percurso dos dados às pipelines que alimentam a IA. Lembrar que é sessão remota; convidar a usar chat e microfone à vontade.


---

## Abertura — O que está a montante do dataset

> _transição:_ Separador de abertura. Uma frase: nas aulas anteriores partiram do dataset já pronto; hoje olhamos para o que está a montante — a disciplina que o produz.


**1. O dataset que analisaram é o fim de uma cadeia**  ·  <sub>Onde esta aula entra</sub>

Nas aulas 3–4 o dataset já estava preparado — trabalharam a jusante. Esta sessão olha para o que está a montante: a disciplina que produz, integra e mantém esses dados fiáveis e atuais. É engenharia de dados, e é onde a GenAI vai entrar de duas formas.


---

## Fundamentos — O percurso dos dados

> _transição:_ Entramos nos fundamentos. Pelo que ouvi no aquecimento, decido o ritmo: sala sénior, corro; sala mais júnior, demoro. É o percurso ponta-a-ponta.


**2. Duas disciplinas, duas perguntas**  ·  <sub>Enquadramento conceptual</sub>

A distinção não é rígida — há zonas de sobreposição — mas as duas perguntas centrais são genuinamente diferentes. O fio que percorre tudo o que se segue é a fiabilidade: tornar os dados um recurso previsível, não um achado pontual.


**3. Dos dados de origem a quem os usa**  ·  <sub>O percurso</sub>

Tudo o que se segue se pendura nestes cinco estágios: ETL/ELT vive na transformação, batch/streaming na ingestão, warehouse/lake no armazém, a orquestração costura os cinco. Convém fixá-lo como mapa.


**4. A GenAI toca em cada etapa do percurso**  ·  <sub>Onde a GenAI entra</sub>

Visão de conjunto antes do detalhe: a GenAI não é um apêndice no fim do pipeline, atravessa-o todo. Esta dualidade — copiloto de quem constrói, e consumidor novo para quem se constrói — é o que as duas portas desenvolvem. Aqui fica só o mapa.


**5. Fontes de dados**  ·  <sub>Estágio 1</sub>

As fontes são sistemas de outras equipas, com prioridades próprias — o engenheiro de dados é quase sempre consumidor, não dono. Distinção útil: dados operacionais (OLTP, escrever uma linha depressa) vs analíticos (OLAP, ler milhões e agregar). As fontes de eventos empurram para streaming; bases e ficheiros, tipicamente, para batch.


**6. Batch ou streaming**  ·  <sub>Estágio 2 · Ingestão</sub>

Batch: acumula e processa de tempos a tempos — simples, fácil de reprocessar, barato. Streaming: processa cada evento à medida que chega, latência de ms a s, mas obriga a pensar em janelas, dados fora de ordem, estado, exactly-once. Heurística honesta: a maioria dos casos tolera micro-batch de 1–5 min; só vale streaming verdadeiro quando a latência é do próprio produto (fraude, monitorização). Volta mais à frente nesta aula: indexar embeddings é tipicamente batch/CDC; servir a consulta é online.


**7. Onde os dados vivem**  ·  <sub>Estágio 3 · Armazenamento</sub>

Warehouse (BigQuery, Snowflake): schema na escrita, ótimo para SQL analítico, mau para PDFs/imagens. Lake (S3 + Parquet): barato, aceita qualquer ficheiro, schema só na leitura — flexível, mas vira "swamp" sem governança. Lakehouse (Delta, Iceberg): tabelas ACID sobre o lake — transações, time travel, schema enforcement. Importa para o resto desta aula: o texto não-estruturado que alimenta os embeddings vive no lake/lakehouse, e a base vetorial entra como vizinho novo deste stack.


**8. ETL e ELT — a ordem mudou**  ·  <sub>Estágio 4 · Transformação</sub>

ETL clássico transformava num servidor próprio antes de carregar, porque o armazenamento era caro. ELT inverte: a cloud tornou o armazém barato e elástico, por isso carrega-se cru e transforma-se com SQL lá dentro — versionado, testado (dbt). O dado cru fica disponível para reprocessar. Foreshadowing: quando dissermos "o T do ETL pode ser um LLM", é este passo de Transform que se substitui/aumenta — e é por isso que herda os problemas de determinismo.


**9. O grão: o que uma linha representa**  ·  <sub>Transformação · modelação</sub>

Grão = o nível de detalhe de uma linha de facto. "Uma linha por encomenda", "uma por linha-de-encomenda", "uma por cliente por dia" — grãos diferentes, respostas diferentes à mesma pergunta. A modelação dimensional existe para tornar as agregações inequívocas. O LLM lê o schema, não a semântica — o grão é conhecimento de negócio que mora na equipa, não no nome das colunas.


**10. Quem consome os dados**  ·  <sub>Estágio 5 · Serviço</sub>

Quatro grandes consumidores: BI (o clássico), ML (features), aplicações operacionais via reverse ETL, e a GenAI — o consumidor que justifica o resto da aula. A mensagem: a qualidade percebida mede-se à saída, não à entrada. Isto prepara a dualidade: a GenAI entra aqui como consumidor e, ao mesmo tempo, como copiloto de quem constrói os estágios anteriores.


**11. Orquestração: agendar, depender, recuperar**  ·  <sub>Transversal · orquestração</sub>

A orquestração costura os cinco estágios: ordem, tempo, reação a falhas. Airflow é o padrão histórico (DAGs em Python); Dagster é a geração mais nova, orientada a assets e a testes/observabilidade. Conceitos: DAG (dependências sem ciclos → ordem de execução sempre possível), scheduling, retries com backoff, backfill. O ponto crítico: retries e backfill só são seguros se as tarefas forem idempotentes — a ponte para os próximos dois slides.


**12. Um DAG, por dentro**  ·  <sub>Orquestração · um DAG</sub>

Dois extratos independentes correm ao mesmo tempo, convergem num join (fan-in), passam por um quality_check que é gate (se falhar, o load não corre — não publica dados maus), e só então load_dw. O retry ×3 com backoff em clean_orders tolera falhas transitórias. O rótulo "overwrite partição" no load é a escolha que torna a tarefa idempotente: correr de novo não duplica. Acíclico é essencial — um ciclo não teria ordem de execução possível.


**13. Idempotência: correr 2× = correr 1×**  ·  <sub>A propriedade-chave · 1/2</sub>

Idempotência: aplicar uma operação N vezes tem o mesmo efeito que aplicá-la uma vez. Em dados, reprocessar uma partição deve substituí-la, não acumular. À esquerda, overwrite — retry/backfill seguros. À direita, INSERT cego: cada corrida acrescenta de novo as mesmas linhas e o backfill duplica os factos em silêncio. Técnicas: overwrite por partição, upsert/MERGE por chave, chaves determinísticas. É a propriedade que dá fiabilidade — e a que um passo estocástico não respeita.


**14. Reprodutibilidade — e a tensão que se segue**  ·  <sub>A propriedade-chave · 2/2</sub>

Reprodutibilidade é a irmã da idempotência: repetir não só não estraga como produz exatamente o mesmo output — o que exige versionar código, dados de entrada, dependências e configuração. É a base da auditabilidade. Aqui planta-se a tensão central: a engenharia de dados foi construída sobre determinismo; os LLMs são estocásticos por natureza. Quando o LLM entra como passo de transformação, esta garantia parte-se — e a resposta de engenharia (schema-constrain, validação, caching, flags de confiança) é tentar reconstruí-la.


**15. Contratos de dados: o acordo produtor↔consumidor**  ·  <sub>Fiabilidade · contratos</sub>

Um contrato de dados é um acordo explícito e versionado entre quem produz (a equipa da app) e quem consome (a equipa de dados): schema, tipos, nullability, valores permitidos, SLAs de frescura e volume, e a semântica de cada campo. Resolve um problema real: o produtor desconhece quem consome a jusante, e uma renomeação inocente parte dashboards e modelos sem ninguém saber. Com contrato, o schema é uma interface pública — mudanças que o quebram passam por versionamento e validação em CI. A ausência ou violação de contrato manifesta-se como schema drift, o slide seguinte.


**16. Schema drift: quando o contrato se quebra**  ·  <sub>Fiabilidade · a falha</sub>

Schema drift é a deriva, ao longo do tempo, da estrutura dos dados de origem: um campo muda de tipo, é renomeado, deixa de vir, passa a aceitar NULL, ou ganha um valor de enum novo. No exemplo, "amount" passou de número para string formatada — talvez uma alteração inocente no frontend. O consumidor que faz SUM ou rebenta (melhor caso) ou faz cast silencioso para NULL/0 e soma errado sem ninguém ver, durante semanas. Defesas: contratos, schema enforcement no lakehouse, testes de contrato, e observabilidade para detetar cedo.


**17. Dimensões da qualidade de dados**  ·  <sub>Fiabilidade · qualidade</sub>

As dimensões clássicas (completude, validade, consistência, unicidade, atualidade — algumas frameworks acrescentam exatidão e integridade referencial). Estas são exemplos, não um cânone fechado. O ponto operacional: cada uma traduz-se em verificações executáveis que correm no pipeline a cada corrida e o fazem falhar (ou disparam um gate). Ferramentas: dbt tests, Great Expectations, Soda. A diferença entre uma plataforma amadora e uma profissional é esta: a qualidade é asserida por código, não descoberta por um analista três semanas depois.


**18. Observabilidade dos dados**  ·  <sub>Fiabilidade · observabilidade</sub>

Observabilidade de dados é o equivalente, para dados, do monitoring de software. Pilares habituais: freshness, volume, distribuição/schema, e lineage (o grafo de dependências coluna-a-coluna, essencial para impact analysis). Por cima, deteção de anomalias com baselines aprendidos do histórico. Distinção face ao slide anterior: os testes de qualidade verificam hipóteses já formuladas (known unknowns); a observabilidade existe para os unknown unknowns — a falha que ninguém previu. A linhagem volta mais à frente nesta aula (a GenAI pode construí-la; e a versão do modelo de embedding faz parte dela).


**19. Governança e catálogo — nos dois sentidos**  ·  <sub>Fiabilidade · governança</sub>

Governança = políticas e processos que mantêm os dados fiáveis, seguros e usáveis à escala: ownership, definições partilhadas, acesso, privacidade (PII, RGPD, retenção), e o catálogo — o inventário pesquisável de ativos com descrições, donos e linhagem. A relação com a GenAI é bidirecional: por um lado, a qualidade dos metadados é o teto de desempenho dos copilotos (catálogo rico → copiloto útil); por outro, a própria GenAI acelera a catalogação, a deteção de PII e a construção de linhagem. É um dos temas onde os dois mundos mais se reforçam.


**20. O que sustenta uma plataforma de dados**  ·  <sub>Síntese</sub>

Fecho e ponte. Recapitular o percurso e as camadas transversais, mas o que tem de ficar é a card da direita: a engenharia de dados torna os dados determinísticos e fiáveis. Esse é o pano de fundo contra o qual a GenAI faz sentido — como copiloto de quem constrói, e como consumidor novo que parte a idempotência que acabámos de estabelecer. Transição para as duas portas.


**21. GenAI e engenharia de dados — como é que se cruzam?**  ·  <sub>Antes de estruturar</sub>

Puxar pelos formandos ANTES de dar a estrutura — não revelar já as "duas relações". Deixá-los imaginar como a GenAI e a engenharia de dados se cruzam, e onde não se cruzam ou não deviam. Sessão remota: recolher 2-3 ideias do chat ou de quem abrir o microfone. Só depois passar ao slide da tese, que organiza o que eles disserem em duas formas — construir COM e construir PARA. Se ninguém arrancar, provocar: "a GenAI escreve SQL por vocês? confiariam? e o contrário — a GenAI precisa dos vossos dados para quê?".


---

## A tese — Duas formas de abordar o tema

> _transição:_ A tese — só DEPOIS de os ter ouvido no slide anterior. Organizar o que disseram em duas formas: construir COM a GenAI e construir PARA a GenAI.


**22. Duas relações entre uma coisa e a outra**  ·  <sub>GenAI e engenharia de dados</sub>

Duas maneiras de pensar a relação entre GenAI e engenharia de dados. A primeira é a esperada — a GenAI como ferramenta de quem já faz o trabalho; atravessamos com olho crítico. A segunda é onde a engenharia de dados passa a ser parte do funcionamento da própria GenAI: é o núcleo da aula. Agentes ficam para outra sessão — aviso honesto.


---

## Primeira parte — Construir com a GenAI

> _transição:_ A primeira das duas abordagens: o LLM senta-se ao lado de quem já faz o trabalho e acelera-o. Atravessamos com olho crítico, porque é aqui que mora o erro mais perigoso — o que corre sem dar erro. Primeiro o mito do "não é preciso saber SQL", com alguns exemplos reais; depois onde a GenAI acelera de facto.


**23. "Já não é preciso escrever SQL — os LLMs escrevem-no"**  ·  <sub>O mito</sub>

O perigo não é o SQL que rebenta — esse vê-se. É o SQL sintaticamente perfeito que devolve um número plausível e errado. Aqui mostramos uma seleção; o sandbox "O SQL que mente" tem o conjunto completo, para experimentarem com o próprio chatbot.


**24. "Tempo médio até à primeira consulta"**  ·  <sub>O SQL que mente · linhas em falta</sub>

A mesma armadilha veste-se de "tempo de resolução de tickets" no suporte. O viés de sobrevivência: medimos os que sobreviveram ao processo. Os que ainda esperam — os que mais importam — são invisíveis para a média. O número diz 18 dias; a realidade são meses.


**25. "Armazéns que nunca expediram nada"**  ·  <sub>O SQL que mente · NOT IN + NULL</sub>

Footgun clássico de SQL. O NULL na subquery contamina toda a comparação NOT IN. NOT EXISTS é imune porque trabalha por correspondência, não por pertença. O LLM escolhe NOT IN porque é a forma mais frequente no treino — não porque é segura.


**26. "Qual o produto mais vendido?"**  ·  <sub>O SQL que mente · definição ambígua</sub>

Aqui não falta contexto de negócio escondido numa coluna — falta desambiguar a própria pergunta. A caneta ganha em nº de linhas, o portátil em receita. Nenhuma resposta está "errada"; errada é a confiança com que o LLM serve uma só.


**27. "Mostra-me os utilizadores ativos"**  ·  <sub>O SQL que mente · definição de negócio</sub>

A armadilha mais traiçoeira: a coluna existe e o nome bate certo. "Ativo" é um termo de negócio com definição própria — utilizador ativo mensal — que vive na convenção da equipa, não no schema. É onde uma camada semântica faz toda a diferença.


**28. "Doentes com glicemia alta"**  ·  <sub>O SQL que mente · unidades e limiar</sub>

O LLM sabe que 126 mg/dL é o limiar de diabetes em jejum — e despeja-o. Não sabe que esta coluna mistura unidades, nem que metade das colheitas é ocasional. Em qualquer domínio com unidades (€ vs cêntimos, kg vs g) é o mesmo erro. O limiar tem de vir do negócio, não do treino do modelo.


**29. "Quantas encomendas este mês?"**  ·  <sub>O SQL que mente · soft delete</sub>

Soft delete é a regra em sistemas modernos: nada se apaga, marca-se. O LLM não sabe que existe uma coluna `cancelada` (ou `deleted_at`) a não ser que o schema lho diga — e mesmo com o schema, raramente a aplica sozinho. Toda a métrica deste tipo de tabela precisa do filtro. É a convenção mais esquecida.


**30. "Os 3 melhores vendedores do trimestre"**  ·  <sub>O SQL que mente · empates no top-N</sub>

Em RH é um problema de equidade: dois vendedores com o mesmo número, um fica de fora do bónus por acaso da ordenação. RANK/DENSE_RANK tornam a regra explícita e estável. O LLM gera o LIMIT porque é o padrão — não pensa em empates porque os dados de exemplo raramente os têm. Pensar nos casos-limite é trabalho humano.


**31. Porque é que isto é pior do que um erro de execução**  ·  <sub>O SQL que mente · síntese</sub>

Uma falha de execução rebenta e alguém investiga. Isto não rebenta — instala-se. Os exemplos partilham a mesma assinatura: SQL bonito, resultado credível, erro invisível. A defesa não é deixar de usar o copiloto; é saber ler o que ele produz.


**32. Vamos experimentar: o SQL que mente**  ·  <sub>Atividade · sandbox 1</sub>

Mãos-à-obra (EX-1), a seguir aos exemplos de SQL confiantemente errado. Partilhar o ecrã com o sandbox, ou pedir-lhes para abrirem no link. Duas bases de dados, armadilhas de SQL: pôr a pergunta no chatbot deles, colar o SQL, correr, comparar com a resposta esperada. O objetivo é sentirem o "confiantemente errado" nas próprias mãos. O sandbox fica no separador; voltar aos slides quando quiser.


**33. Onde a GenAI acelera de facto**  ·  <sub>A GenAI ao serviço do engenheiro de dados</sub>

Saímos do modo cético. Estas cinco tarefas são onde o copiloto rende de verdade — bem definidas, entrada e saída claras, validação humana no fim. O fio condutor: o LLM faz o primeiro rascunho; o engenheiro orienta e valida. Abrimos cada uma.


**34. O passo crítico: ligar a pergunta ao schema**  ·  <sub>A GenAI ao serviço do engenheiro · porque o NL2SQL falha</sub>

A literatura (NL2SQL, VLDB 2025) é clara: a gramática SQL os modelos já dominam; o gargalo é o schema linking — ligar termos da pergunta às entidades certas. Numa BD com 300 tabelas e nomes ambíguos é onde tudo descarrila. Dar ao modelo um bom dicionário de dados, uma camada semântica e exemplos rende mais do que trocar de modelo. O conhecimento do domínio é a vantagem da equipa.


**35. Traduzir Oracle → BigQuery não é trocar funções**  ·  <sub>A GenAI ao serviço do engenheiro · migração de dialetos</sub>

A migração é uma das melhores aplicações do copiloto — tarefa fechada, alto volume, padrões repetitivos. Mas tem caudas longas: a ordem de avaliação de ROWNUM, NULL em ordenações, datas e fusos, NUMBER vs FLOAT, sintaxe de janelas. O LLM acerta o caso comum e falha o canto. Por isso a migração assistida precisa de testes de equivalência: correr ambas e comparar resultados.


**36. Documentar o data warehouse que ninguém documentou**  ·  <sub>A GenAI ao serviço do engenheiro · catalogação</sub>

O catálogo de dados é o trabalho que toda a gente adia. O LLM lê os nomes das colunas, umas linhas de amostra e os nomes vizinhos, e produz um primeiro rascunho de documentação — incluindo candidatos a PII. As descrições são plausíveis, mas o LLM pode inventar a semântica de negócio (o que `val` significa exatamente): serve para arrancar, não para publicar sem revisão. É também onde se fixa o que "ativo" quer dizer.


**37. O erro que passa em todas as regras**  ·  <sub>A GenAI ao serviço do engenheiro · qualidade semântica</sub>

A qualidade clássica é por regras: tipos, intervalos, não-nulos. Apanha o malformado, não o implausível. Um bebé com menopausa, ou uma alta antes da admissão, passa em todos os testes de coluna — cada valor é válido por si. O LLM lê a linha como uma frase e estranha a combinação. Não substitui as regras: complementa-as, levantando candidatos a revisão (e inventa falsos positivos, por isso humano no fim).


**38. Estes copilotos já vêm nas plataformas de dados**  ·  <sub>A GenAI ao serviço do engenheiro · nas plataformas</sub>

Não é preciso adotar nada novo — o copiloto já vem na plataforma que usam. Têm acesso ao schema, o que ajuda no schema linking, mas continuam sem a camada de negócio. A observação é frequente na comunidade de dados (ver SOURCES). A defesa de fundo é a mesma dos exemplos: uma camada semântica e um humano que sabe ler o resultado.


**39. O valor mudou de lugar**  ·  <sub>A GenAI ao serviço do engenheiro · em síntese</sub>

Fecho da primeira abordagem. O copiloto é útil em tarefas bem definidas, mas todas terminam em revisão. O engenheiro deixou de ser quem escreve mais SQL e passou a ser quem o lê melhor. A seguir, a segunda abordagem — onde o trabalho deixa de ser acelerado e passa a ser novo: a engenharia de dados ao serviço da própria GenAI.


---

## Segunda parte — Construir para a GenAI

> _transição:_ Viramos a página. Até aqui a GenAI foi copiloto — escrevia SQL, sugeria, e nós validávamos. Agora a relação inverte-se: somos nós, engenheiros de dados, a construir os pipelines de que a GenAI precisa para funcionar. Duas peças nucleares: o LLM como passo de transformação (ETL) e os embeddings. Começamos pelo ETL porque é o mais imediato — e o que melhor mostra que isto é engenharia, com método.


**40. O T do ETL pode ser um LLM**  ·  <sub>Construir para a GenAI · LLM-como-ETL</sub>

O ETL tem três letras; a do meio é a que muda. Transformação era código que nós escrevíamos linha a linha. Aqui o input é prosa que nenhum parser apanha — a nota de triagem de um enfermeiro — e o output é um JSON com schema. Reparem em dois detalhes que já anunciam o resto da secção: "sem alergias conhecidas" tem de virar lista vazia, não a presença de alergias (negação); e "HTA" vira "hipertensão" (normalização de vocabulário). O modelo faz isto bem na maioria das vezes — e é o "maioria das vezes" que nos vai ocupar.


**41. Dez fontes sujas, dez extrações**  ·  <sub>Construir para a GenAI · o roteiro</sub>

Este é o mapa. Escolhi dez domínios de propósito longe da saúde, porque vocês são informáticos — quero que vejam o padrão, não o tema. Cada um foi escolhido para disparar uma dificuldade diferente: a negação clínica, a inferência no suporte, os números numa cláusula, o OCR sujo, a síntese num CV. E um dos dez — a linha de log — está aqui exatamente para mostrar onde o LLM é a ferramenta errada. Vamos um a um.


**42. Inferir o que está implícito — sem inventar o que falta**  ·  <sub>Exemplo 2 · suporte</sub>

Aqui há duas coisas a separar. A primeira é boa: o modelo infere severidade que não está escrita como campo — junta "urgente", "crasha", "desde a atualização" e classifica "alta". Isto é exatamente o que um regex nunca faria. A segunda é a armadilha: o cliente não diz nada sobre reembolso. A tentação do modelo é preencher `false`. Mas `false` é uma afirmação — "o cliente não quer reembolso" — e isso é falso; o cliente simplesmente não falou. A diferença entre `false` e `null` é a diferença entre uma resposta e uma alucinação. Voltamos a isto na falha das alucinações.


**43. O número está certo — o qualificador é que não**  ·  <sub>Exemplo 3 · jurídico</sub>

Troquei o exemplo dos "dois inteiros baralhados" — um modelo atual raramente os baralha quando a sintaxe é clara — por um erro mais traiçoeiro e que ainda acontece: o número é extraído corretamente, mas o qualificador que lhe dá magnitude perde-se. "2% ao mês" são quase 24% ao ano; se o pipeline guardar só "2%" sem o período, ou assumir "anual" por defeito, o erro é de uma ordem de grandeza e passa despercebido porque 2% é um valor perfeitamente plausível. O schema-constraint não apanha isto: "mensal" e "anual" são ambos valores legais do campo. A defesa é validação de domínio — exigir sempre o período junto da taxa, e marcar para revisão quando ele não aparece explicitamente no texto. É engenharia de dados clássica, constraints, aplicada à saída do modelo.


**44. Texto sujo, e a vírgula decimal portuguesa**  ·  <sub>Exemplo 4 · recibo (OCR)</sub>

Dois problemas portugueses num só recibo. A vírgula decimal: 23,90 são vinte e três euros e noventa cêntimos, não dois mil trezentos e noventa. Um modelo treinado maioritariamente em inglês trata a vírgula como separador de milhares e multiplica a fatura por cem. E a data: "2026-06-12" já vem em ISO, mas o modelo pode "normalizar" para dd/mm e introduzir ambiguidade onde não havia. A lição: a localização — moeda, decimais, datas — é um requisito de correção, e deve estar explícita no prompt e validada à saída (`total` é um decimal positivo plausível para um recibo de mercearia).


**45. Sintetizar factos que o texto não nomeia**  ·  <sub>Exemplo 5 · CV</sub>

Este é o exemplo que mostra a força genuína do LLM. O campo `gestao: true` não corresponde a nenhuma palavra — não existe "gestão" no texto. O modelo lê "liderou equipa de 4" e sintetiza um booleano. Um regex não tem hipótese; precisaríamos de uma lista interminável de padrões ("liderou", "geriu", "coordenou", "foi responsável por..."). E as siglas: "EI" é Engenharia Informática, "FEUP" é a faculdade do Porto — conhecimento de mundo que o modelo traz. Mas atenção: esta mesma capacidade de sintetizar é a porta da alucinação. A fronteira entre "inferir gestão" e "inventar reembolso" é ténue, e é por isso que precisamos de schema e validação.


**46. Parsing de morada, com campos que podem faltar**  ·  <sub>Exemplo 6 · morada</sub>

Dois pontos. Primeiro, o óbvio: campos opcionais. Esta morada tem andar; a próxima pode não ter — schema `Optional`, regra "null se ausente", nunca "inventa um 1º andar". Segundo, o ponto que interessa mesmo: a morada parece um caso trivial para um LLM e não é. O parsing de moradas internacionais é um problema clássico e difícil — ordem dos campos, abreviaturas por língua, escritas diferentes — a ponto de existir uma biblioteca especializada, o libpostal (de Al Barrentine / Mapzen), que NÃO é um LLM: é um parser estatístico (averaged perceptron) treinado em 50+ milhões de moradas do OpenStreetMap, com ~98,9% de parses corretos. O autor é explícito: isto "nunca se poderia resolver deterministicamente com regex". Mas a resposta também não é o LLM genérico — é a ferramenta construída para o problema. A lição de engenharia: antes de apontar o LLM a um problema, pergunta se já existe uma ferramenta especializada melhor, mais barata e auditável. Liga diretamente ao critério "quando não usar LLM". Fonte: Al Barrentine, "Statistical NLP on OpenStreetMap" (libpostal).


**47. Sentimento não é um número — é por aspeto**  ·  <sub>Exemplo 7 · review</sub>

A análise de sentimento ingénua devolve um número — "negativo", ou três estrelas. Mas esta review não é simplesmente negativa: o cliente adorou o produto e detestou a entrega. Colapsar isso num escalar perde exatamente a informação acionável. A extração por aspeto separa material (positivo), acabamento (negativo) e entrega (negativo) — e de repente a tabela diz à empresa que o problema é a logística, não o produto. Reparem que o modelo até inferiu um aspeto "acabamento" a partir de "risco na lateral", que não está nomeado. Esta estrutura — lista de objetos com aspeto e polaridade — é onde o schema-constraint brilha: força a forma, e o modelo preenche.


**48. O caso em que o regex bate o LLM**  ·  <sub>Exemplo 8 · linha de log</sub>

Pus este exemplo no meio dos dez de propósito, para vos tirar do encantamento. Uma linha de log tem formato fixo: timestamp ISO, nível, componente, mensagem, host. Isto é um caso de escola para um regex — uma expressão captura todos os grupos, corre em microssegundos, é determinística e auditável, e custa zero. Meter um LLM aqui é absurdo em três eixos: custo (chamada de API por linha, a milhões de linhas), latência (300ms vs microssegundos) e, o pior, não-determinismo — a mesma linha pode dar saídas ligeiramente diferentes, o que num pipeline de observabilidade é veneno. Guardem esta intuição: o LLM paga-se quando o texto é genuinamente livre. Quando há estrutura, há ferramenta melhor.


**49. Documento longo: o que cabe, e mantém-se consistente?**  ·  <sub>Exemplo 9 · carta de alta</sub>

Documentos longos trazem problemas que os exemplos curtos não têm. Primeiro, o contexto: estas três páginas cabem, mas um processo clínico de 200 páginas, mesmo cabendo na janela, faz a atenção do modelo diluir — o que está a meio tende a ser pior recuperado (o efeito "lost in the middle"). Segundo, a consistência interna: a medicação mencionada na história clínica tem de coincidir com a lista de medicação à alta, e o modelo pode extrair versões diferentes de secções diferentes. Estratégias de engenharia: dividir o documento por secções e extrair de cada uma, depois reconciliar; ou pedir citações (de que parte do texto veio cada campo) para auditar. Isto liga diretamente ao chunking que vimos nos embeddings.


**50. Muitos tipos numa frase — o trabalho passa para a especificação**  ·  <sub>Exemplo 10 · anúncio imobiliário</sub>

O anúncio imobiliário é o oposto da carta de alta: tudo numa frase, mas com seis tipos de dados encavalitados. "T2" é um enum, "78m²" perde a unidade e fica inteiro, "2º andar" separa-se da tipologia, "com elevador" vira booleano, "320.000€" perde o ponto de milhares e o símbolo, "Cedofeita" é texto livre. Há aqui duas coisas a dizer. Primeira, honestidade: para uma frase tão curta, o LLM é quase um martelo para uma mosca — mas a variabilidade entre anúncios (cada um escreve à sua maneira) é o que o justifica face a um regex que partia ao primeiro formato novo. Segunda, e é o ponto que quero que fique: o que faz o modelo acertar não é "ser esperto", é a especificação. Cada campo precisa de uma descrição precisa — o que é "andar", que unidade tem "área", o que conta como "elevador: true". À medida que empurramos mais extração para LLMs, o valor desloca-se para quem define bem o schema e os metadados. A especificação passa a ser o produto.


**51. "Sem X" significa ausência de X, não a sua presença**  ·  <sub>Falha 1 · negação</sub>

A negação é a falha número um porque é silenciosa e perigosa. Uma extração que apanha a palavra "alergias" e ignora o "sem" inverte o significado — regista que existe alergia quando não existe. Modelos modernos lidam bem com negações simples, mas complicam-se com âmbito longo ("nega febre, tosse ou dispneia nas últimas 48h" — quantos campos isso nega?) e com dupla negação. Há aqui um segundo ponto, mais subtil, que vale a pena fazer em voz alta: quando processamos negações, há o risco oposto — reduzir informação. Uma lista de alergias vazia pode significar duas coisas muito diferentes: "perguntou-se e não há" (ausência informada, que é um facto clínico) ou "ninguém perguntou" (ausência de informação). Se o pipeline as colapsa nas duas no mesmo `[]`, deita fora um sinal real. A boa extração distingue ausência informada de campo não preenchido — tipicamente, lista vazia para a primeira, null para a segunda. A defesa não é só confiar: é testar explicitamente casos de negação no golden set e, em domínios críticos, validar. Uso azeitona para o erro, nunca vermelho — regra da identidade CeL.


**52. Uma só forma canónica — mas quem a define?**  ·  <sub>Falha 2 · normalização</sub>

"EUA", "Estados Unidos", "U.S." e "USA" são a mesma coisa para um humano, e o modelo até as colapsa razoavelmente. Escolhi de propósito um exemplo fora da saúde — isto é universal: clientes ("IBM" / "I.B.M." / "International Business Machines"), moedas, unidades, categorias de produto. A pergunta difícil não é se o modelo normaliza — é normalizar para quê. Qual é a forma canónica? Se pipelines diferentes escolherem formas diferentes ("Estados Unidos" aqui, "US" ali), os dados não se juntam. É um problema de governança tão antigo quanto os data warehouses — master data, terminologias controladas. Em saúde tem nomes próprios (ICD, SNOMED), mas o princípio é o mesmo em todo o lado. O LLM é uma ferramenta poderosa para mapear texto livre para um vocabulário; o vocabulário é que tem de ser definido pela organização. O modelo executa a política; não a decide.


**53. Preencher o que o texto não diz**  ·  <sub>Falha 3 · alucinação</sub>

A alucinação de campos é a sombra da capacidade de inferir. O mesmo modelo que corretamente deduz "gestao: true" do CV vai, se o deixarmos, deduzir "pedido_reembolso: false" de um e-mail que nunca falou em reembolso. A diferença é que no CV há evidência ("liderou equipa") e aqui não há nenhuma. `false` é uma afirmação positiva de ausência de pedido; `null` é honestidade sobre o que o texto não cobre. Para downstream isto é enorme: um relatório que conta "clientes que não querem reembolso" baseado em `false` alucinados é lixo. A mitigação tem duas pernas: a instrução explícita "null se ausente" no prompt, e tipos `Optional` no schema que permitem o null em vez de forçar um default.


**54. A mesma entrada, duas saídas**  ·  <sub>Falha 4 · não-determinismo</sub>

Esta é a falha que mais incomoda um engenheiro de dados, porque ataca um princípio, não um valor. Idempotência: correr o mesmo pipeline sobre os mesmos dados tem de dar o mesmo output, senão não há reprodutibilidade, não há testes fiáveis, não há reconciliação. Um LLM é não-determinístico por natureza — até com temperatura zero há variação, e uma atualização silenciosa do modelo muda tudo. As mitigações: `temperature=0` reduz mas não elimina; fixar a versão exata do modelo nos metadados; e a mais importante para um pipeline — cache por hash do input, ou seja, guarda-se a extração e só se volta a chamar o modelo se o texto de origem mudar. Assim o pipeline volta a ser idempotente na prática, mesmo que o modelo não seja. Isto liga diretamente ao slide da idempotência no início da aula.


**55. Regra determinística ou LLM?**  ·  <sub>Construir para a GenAI · o critério</sub>

Recolhemos a intuição dos dez exemplos numa decisão. A coluna da esquerda — regra determinística — vence sempre que o formato é fixo e estável, sempre que a auditabilidade é exigida, sempre que o volume é massivo ou a latência crítica, e sempre que é preciso custo previsível. O log é o caso de escola. A coluna da direita — LLM — só se justifica quando o texto é genuinamente livre: variabilidade linguística alta, necessidade de inferir ou sintetizar, uma cauda longa de formatos que nenhuma regra cobre. E vale a pena insistir num ponto: "determinístico" não é só regex — inclui parsers especializados, como o libpostal para moradas, muitas vezes melhores e mais baratos do que o LLM. A resposta certa raramente é "ou/ou": é híbrido — a regra apanha o que é estável, o LLM trata do resto. Engenharia é escolher a ferramenta mais barata que resolve, não a mais vistosa.


**56. O schema é um contrato, não uma sugestão**  ·  <sub>Construir para a GenAI · a resposta de engenharia · 1/2</sub>

Vocês são informáticos, por isso mostro o código. O schema-constraint — structured output, function calling, ou Pydantic com uma biblioteca como o Instructor — transforma "por favor devolve JSON" numa garantia. O modelo é forçado, ao nível da geração de tokens, a produzir algo que encaixa neste tipo. `idade` é um inteiro entre 0 e 120, não a string "cinquenta e quatro". `alergias` é uma lista, com default lista vazia, o que reforça a regra da negação. E acrescentei um campo `confianca`: peço ao próprio modelo uma estimativa de quão seguro está, que uso a seguir para flag de revisão. Sem schema-constraint, o JSON parte — chaves a mais, vírgulas a faltar, prosa à volta — e o parser estoira em produção. Com ele, o JSON é válido por construção.


**57. O LLM exige o contrato de dados, não o dispensa**  ·  <sub>Construir para a GenAI · a resposta de engenharia · 2/2</sub>

Fechamos o ETL com as quatro defesas que tornam isto produção, não demo. Schema-constraint para o formato nunca partir. Validação de domínio à saída — tipos, intervalos, e regras de negócio como a do contrato — para apanhar a semântica que o schema não apanha. "Null se ausente", instrução e Optional, contra a alucinação de campos. E flag de baixa confiança, usando aquele campo `confianca`, para mandar o duvidoso para um humano em vez de o deixar entrar silenciosamente nos dados. A mensagem do painel é a tese desta segunda parte inteira: a GenAI no pipeline não dispensa a disciplina de engenharia de dados — schema, validação, idempotência, linhagem — ela exige-a com mais força, porque acrescenta uma fonte de erro nova e não-determinística. Quem trata o LLM como uma caixa fechada constrói dívida; quem o trata como mais um passo de transformação com contrato constrói sistemas.


**58. Vamos experimentar: o extrator (LLM-como-ETL)**  ·  <sub>Atividade · sandbox 2</sub>

Mãos-à-obra (EX-2), a seguir aos exemplos e falhas do LLM-como-ETL. Vários casos, do simples ao complexo, com detetores semânticos: negação, campos ausentes, números na prosa, localização. Deixá-los ver a diferença entre "diverge do esperado" e "erro real". Fica no separador; voltar aos slides para os embeddings.


---

## Construir para a GenAI · núcleo — Embeddings, a fundo

> _transição:_ Abrimos a caixa dos embeddings: cinco passos, do texto ao vetor. Desmistificar o suficiente para usarem o Sandbox 3 — sem exigir que decorem nada.


**59. Do texto ao vetor, em cinco passos**  ·  <sub>A cadeia</sub>

O mapa do que vem a seguir. Cinco passos, do texto livre a um vetor único: tokenização (partir o texto em peças), matriz (cada peça tem um vetor), atenção (o vetor passa a depender do contexto), pooling (juntar tudo num só) e treino (porque perto = parecido). Ninguém tem de decorar isto — o objetivo é desmistificar o suficiente para usarem o Sandbox 3 com confiança.


**60. O texto vira tokens — as peças que o modelo processa**  ·  <sub>Passo 1 · Tokenização</sub>

O modelo não processa palavras nem letras — processa TOKENS, peças de um vocabulário fixo (dezenas de milhares). As palavras comuns são um token; as raras partem-se em sub-palavras reutilizáveis, e é assim que o modelo lida com palavras que nunca viu. O ponto que quero deixar: não há um só tokenizador — modelos diferentes partem o mesmo texto de formas diferentes. Consequência prática: o português e o código costumam gastar mais tokens do que o inglês, o que custa mais e enche a janela de contexto mais depressa. (Se alguém souber, é o BPE/WordPiece — mas não é preciso nomear.)


**61. Um vetor por token — mas estático**  ·  <sub>Passo 2 · Matriz de embeddings</sub>

Há uma tabela (vocabulário x dimensões, ex. 50 000 x 768): cada token tem uma linha, o seu vetor inicial. Ir buscá-lo é um lookup, não um cálculo. O problema: 'banco' tem UM só vetor, seja o do dinheiro ou o do jardim. Este é o nível word2vec, de 2013 — estático, um sentido por palavra. Durante anos foi assim; o salto vem no slide seguinte.


**62. A mesma palavra, dois vetores**  ·  <sub>Passo 3 · Contextualização</sub>

O salto: com a self-attention — o mecanismo do Transformer, do paper 'Attention Is All You Need' (2017), a mesma arquitetura por trás dos GPTs e dos modelos de embeddings de hoje — cada token passa a 'olhar' para os vizinhos, e o seu vetor passa a depender da frase toda. 'Banco' em 'levantar dinheiro' e 'banco' no 'jardim' ficam com vetores distantes (cosseno baixo). Isto confirma-se ao vivo no Sandbox 3: pôr as duas frases do 'banco' e ver a distância. É esta contextualização que torna a busca semântica possível.


**63. A média dilui**  ·  <sub>Passo 4 · Pooling</sub>

A atenção dá um vetor POR TOKEN; mas para pesquisar precisamos de um vetor por frase/chunk. Juntam-se, tipicamente, pela MÉDIA dos vetores dos tokens. E a média trata todas as palavras por igual — por isso um texto com muitos assuntos fica 'morno', no centro de tudo e perto de nada. É a ponte direta para o chunking do RAG: como se parte o texto decide se a busca funciona. No Sandbox 3, o bloco 'a média dilui' mostra um chunk focado a ficar perto e um chunk enorme a afastar-se.


**64. Porque é que perto = parecido?**  ·  <sub>Passo 5 · Treino</sub>

A pergunta que fecha a intuição: porque é que textos parecidos ficam perto? Não é emergente nem mágico — é o OBJETIVO DE TREINO. Treino contrastivo: mostram-se pares que devem ficar PERTO (uma pergunta e a resposta certa, uma frase e a sua paráfrase) e pares que devem ficar LONGE (textos sem relação); o modelo ajusta os vetores para puxar os positivos e afastar os negativos. Logo 'perto = parecido' é verdade por construção — e 'parecido' é exatamente o que os dados de treino disseram que era. Deixa para a consequência: se o vosso domínio tem outra noção de parecido, o modelo geral está errado para vocês.


**65. Para a engenharia, o vetor é mais um dado**  ·  <sub>Embeddings · consequências de engenharia</sub>

Aterrar tudo na engenharia de dados. O vetor é um dado derivado, como uma coluna calculada — e traz obrigações: trocar de modelo obriga a re-embeber TUDO (espaços de modelos diferentes não se misturam); a dimensão custa (storage e índice escalam); a versão do modelo faz parte da linhagem (com que modelo foi feito cada vetor?); os vetores desatualizam quando a fonte muda; e o idioma/domínio importam. Nenhuma destas é uma questão de ML — são todas de dados. É a mentalidade que levamos para o RAG.


**66. O que distingue os dois**  ·  <sub>Embeddings · síntese</sub>

Fecho dos embeddings. A intuição (texto -> vetor, perto = parecido) chega para perceber o conceito; o que distingue o engenheiro é tratar o vetor como dado — versionado, com custo, com linhagem, que desatualiza. Ponte para o que vem a seguir: um RAG inteiro é um pipeline de dados.


**67. Vamos experimentar: o espaço dos embeddings**  ·  <sub>Atividade · sandbox 3</sub>

Mãos-à-obra (EX-3), depois de explicar como se produzem os embeddings. Há 5 espaços semânticos prontos no seletor (sinónimos, suporte, Java/Python, notícias, reviews) — ou escrevem as próprias frases. Mostrar ao vivo o "banco" (dois sentidos, distância grande) e a busca semântica. O modelo descarrega na 1.ª vez (~135 MB) — pedir para abrirem antes se a ligação for lenta. Fica no separador.


---

## Construir para a GenAI · RAG — Recuperar para gerar

> _transição:_ RAG. A tese que atravessa a secção: um RAG é um problema de engenharia de dados disfarçado de IA — a parte de IA é a mais pequena e a mais resolvida.


**68. Disfarçado de IA**  ·  <sub>O sentimento de 2026</sub>

Abrir o RAG com humildade: a parte "IA" é a mais pequena e a mais resolvida. O trabalho difícil — e o que distingue um RAG que funciona de um que mente — é tudo o que está antes do LLM. Tom: isto é território do engenheiro de dados, não do investigador de ML. A frase é sentimento da comunidade (ver SOURCES), usá-la como tal, sem aspas atribuídas.


**69. O LLM não conhece os seus dados**  ·  <sub>RAG · o problema que resolve</sub>

RAG = Retrieval-Augmented Generation. A intuição: em vez de fine-tunear o modelo com o seu conhecimento (caro, congela na hora do treino, difícil de atualizar), busca em tempo de pergunta os pedaços relevantes e dá-los ao modelo como contexto. Vantagem de engenharia: o conhecimento vive numa base de dados que atualiza como qualquer outra — não num modelo que teria de re-treinar. Por isso RAG é, no fundo, um problema de manter uma base de dados fresca e bem indexada.


**70. Seis caixas — só a última é "IA"**  ·  <sub>RAG · a pipeline</sub>

Percorrer a cadeia da esquerda para a direita e prometer que vamos abrir cada caixa. Insistir: ingestão, chunking, embeddings, indexação e retrieval são ETL puro — extrair, transformar, carregar, consultar. A geração é a parte que a comunidade já resolveu. Quem mete um RAG em produção e só mexe no prompt está a otimizar a caixa errada.


**71. Preparar antes, responder na hora**  ·  <sub>RAG · dois momentos</sub>

Distinção que organiza tudo o resto. A pipeline offline é um job de dados clássico: idempotente, reprocessável, monitorizável — e é onde o engenheiro de dados manda. A online é um serviço de baixa latência. Confundir os dois ritmos é erro comum: re-embeber tudo a cada pergunta (caro, lento) ou nunca re-indexar (stale). Mesmos vetores, dois SLAs opostos.


**72. Onde os dados vivem — e em que estado chegam**  ·  <sub>RAG · ingestão</sub>

Para um público informático: isto é integração de dados clássica, só que a saída alvo é "texto pronto para embeber" em vez de uma tabela. O ponto subtil: a unidade não é o ficheiro, é o conteúdo — e o mesmo conteúdo chega em mil embalagens. Mencionar conectores prontos (Unstructured.io, LlamaIndex readers) mas avisar que conectar é o fácil; o difícil vem a seguir, no parsing.


**73. Garbage in, garbage out — começa no PDF**  ·  <sub>RAG · parsing</sub>

Aqui mora metade dos problemas de RAG na vida real. Extrair texto de um PDF complexo é tudo menos trivial: ordem de leitura, tabelas, multi-coluna, figuras com legenda, OCR de scans. Existem parsers layout-aware (e até LLMs multimodais a fazer document understanding), mas nenhum é perfeito. A mensagem: se o parser estraga o texto, nada a jusante recupera. É a regra do garbage-in elevada ao quadrado, porque o erro é silencioso.


**74. O chunk é a unidade de tudo**  ·  <sub>RAG · chunking</sub>

Fixar a ideia antes das estratégias: o chunk é simultaneamente a unidade de embedding, de retrieval e de contexto. Um chunk demasiado grande dilui o vetor (já vimos na pooling — "a média dilui") e enche o prompt de ruído. Demasiado pequeno parte factos e perde contexto. O chunking é onde o engenheiro de dados tem mais controlo e menos código — e é por isso que é tantas vezes feito à toa (split a cada 1000 caracteres e segue).


**75. Tamanho fixo — o corte cego**  ·  <sub>Chunking · estratégia 1/5</sub>

A baseline. Vantagem real: simples, previsível, idempotente — parte sempre igual. Defeito fatal: não sabe nada sobre a estrutura do texto, por isso o corte cai onde calha. É o ponto de partida de qualquer biblioteca (LangChain CharacterTextSplitter) e o culpado nº1 de respostas truncadas. Guardar o exemplo do facto partido para daqui a uns slides — aqui basta plantar a semente do problema.


**76. Com overlap — a janela que desliza**  ·  <sub>Chunking · estratégia 2/5</sub>

O patch mais comum ao corte fixo: sobrepor as janelas para que o que cai na fronteira de uma apareça inteiro na seguinte. Trade-off explícito de engenharia de dados: overlap de 25% significa ~25% mais vetores para armazenar e indexar, mais custo de embedding e índice maior. É a primeira vez na aula que se vê o custo de storage a depender de uma decisão de chunking — ligar de volta às "consequências de engenharia" dos embeddings.


**77. Estrutural — cortar onde o documento já corta**  ·  <sub>Chunking · estratégia 3/5</sub>

Em vez de impor um tamanho, segue a estrutura que o autor já deu: parágrafos, headings, células. Funciona muito bem em FAQs, documentação técnica e Markdown — onde os limites lógicos já estão marcados. Cai mal em texto corrido sem estrutura (e aí o overlap volta a ser melhor). Mensagem: a melhor estratégia depende do corpus, não há default universal — adiantar o slide de trade-offs.


**78. Semântico — cortar onde o tema muda**  ·  <sub>Chunking · estratégia 4/5</sub>

O passo seguinte: deixar o conteúdo decidir o corte. Embebe-se cada frase, mede-se a similaridade entre vizinhas e corta-se nos vales — onde o assunto muda. Produz os chunks mais coerentes mas é o mais caro a construir (embeddings extra só para decidir os cortes) e não-determinístico se o modelo de embedding mudar. Bom exemplo de "mais inteligência no offline custa mais compute" — uma decisão de custo, não de IA.


**79. Layout-aware — a tabela não se parte**  ·  <sub>Chunking · estratégia 5/5</sub>

A estratégia mais sofisticada e a que mais depende de um bom parser. A ideia: uma tabela partida ao meio é inútil para retrieval (metade das colunas num chunk, metade noutro), por isso trata-se a tabela, a lista, o bloco de código como unidades atómicas e cola-se cada heading à secção que introduz. É aqui que document-understanding com modelos multimodais entra. Ligar ao slide de parsing: layout-aware chunking só é tão bom quanto o parser que lê o layout.


**80. Mesma pergunta, mesmo modelo, resultado oposto**  ·  <sub>Chunking · o caso que torna o ponto inegável</sub>

Este é o slide-âncora da secção de chunking e o coração do EX-4. Demonstração em sala: a mesma pergunta, mesmo modelo, mesmo prompt — com corte fixo nenhum chunk contém "por predefinição são 5 tentativas" inteiro, e o retrieval traz contexto truncado ou o chunk errado; com overlap ou corte por parágrafo, o facto fica inteiro e acerta. Reutiliza o componente errado-vs-certo (azeitona, nunca vermelho). Martelar: a variável independente foi a engenharia de dados.


**81. Pequeno ou grande: um compromisso, não uma receita**  ·  <sub>Chunking · o compromisso</sub>

Fechar o chunking sem dar uma receita única — porque não há. O dilema é real: pequeno = preciso mas frágil; grande = robusto mas diluído (ligar à "média dilui" da pooling). A resposta de engenheiro não é "escolha X", é "meça": defina um conjunto de perguntas de avaliação e compare estratégias por recall. Plantar a noção de avaliação que vem já a seguir. Tamanho, overlap e estratégia são hiperparâmetros do pipeline.


**82. O que parte um RAG são problemas de dados**  ·  <sub>RAG · produção</sub>

Fecho da secção RAG e prova final da tese-zeitgeist. Em produção, os RAGs raramente falham por o LLM ser fraco; falham porque a plataforma de dados por baixo não tem disciplina: índices stale (já vimos, CDC resolve), schema drift que partiu a ingestão sem ninguém ver, backfills que misturaram vetores de modelos diferentes no mesmo índice (espaços incompatíveis — ligar a "trocar de modelo = re-embeber tudo"), e a falta de contratos de dados a montante. A boa notícia para um engenheiro de dados: o ferramental que já domina — observabilidade, data contracts, testes, lineage — é exatamente o que falta a estes sistemas. Esta é a oportunidade do «construir para a GenAI».


---

## Transversal — Limites e cuidados

> _transição:_ O contrapeso crítico da aula. Estocástico vs determinístico, corrupção silenciosa, custo e latência, e quando NÃO usar um LLM. Critério, não ideologia.


**83. Estocástico vs determinístico**  ·  <sub>Limites e cuidados · 1</sub>

A colisão de fundo da aula toda, agora explícita. A engenharia de dados foi construída sobre reprodutibilidade: reprocessar um job tem de dar o mesmo. O LLM é probabilístico por construção — mesmo a temperatura 0 não é garantia (hardware, batching, versões do modelo introduzem variação). Implicação prática: onde meter um LLM no pipeline, perde a garantia de que o reprocessamento é idêntico. As mitigações — schema-constrain, cache de saídas, validação, congelar versão do modelo — são justamente o que vimos no LLM-como-ETL. Não se elimina o estocástico; cerca-se.


**84. Corrupção silenciosa**  ·  <sub>Limites e cuidados · 2</sub>

Costurar os fios da aula: o SQL sintaticamente perfeito que dá o número errado (construir com a GenAI), o chunk cortado a meio, o índice stale (RAG). Têm todos a mesma assinatura: não há exceção, não há vermelho, o pipeline reporta sucesso. O custo é diferido e composto — a decisão errada já foi tomada a montante quando o erro aparece. A única defesa é tratar as saídas de LLM como dados não-confiáveis: validar, testar contra expected, monitorizar distribuições, flag de baixa confiança. Repetir a regra da casa: avisar a azeitona, nunca o vermelho — mas o conteúdo é mesmo este alarme.


**85. Custo e latência: um LLM sobre milhões de linhas**  ·  <sub>Limites e cuidados · 3</sub>

Ordem de grandeza concreta: uma chamada de LLM é dezenas a centenas de milissegundos e custa tokens; multiplique por 10 milhões e tem horas de wall-clock e uma fatura de quatro a cinco dígitos. Uma transformação SQL/regex sobre as mesmas linhas é segundos e custa praticamente nada. A maturidade de engenharia não é "LLM em tudo", é arquitetar para minimizar chamadas: pré-filtrar com regras o que é trivial, processar só o que mudou (CDC), cachear saídas determinísticas, agrupar em batch, e reservar o LLM para o subconjunto que mesmo precisa de compreensão. O LLM é um recurso caro de latência alta — trata-se como tal.


**86. Quando NÃO usar um LLM**  ·  <sub>Limites e cuidados · 4</sub>

O contrapeso honesto a uma aula que passou muito tempo a entusiasmar com o «construir para a GenAI». O reflexo do hype é meter LLM em tudo; o reflexo de engenheiro é perguntar se uma regra determinística resolve — porque a regra é mais barata, mais rápida, reprodutível e auditável. Voltar ao caso dos logs do slide 14: ali o regex bate o LLM em todas as dimensões. O LLM justifica-se exatamente onde as regras explodem em complexidade: negação, normalização de sinónimos, ambiguidade, intenção, variação linguística sem fim. Critério de decisão, não ideologia. Isto é o "olho crítico" do arco da aula.


---

## Síntese — O que muda no seu trabalho

> _transição:_ Síntese. Recolher o arco: o valor desloca-se de escrever para desenhar e validar. Voltar às leituras que a sala deu no aquecimento.


**87. De escrever pipelines a desenhar ecossistemas**  ·  <sub>Síntese · o papel</sub>

Recolher o arco. A linha escrever → orientar → validar do «construir com a GenAI» reaparece aqui em escala maior: escrever pipelines individuais → desenhar o ecossistema (fontes, contratos, índices, atualização) → validar que o todo está correto. O ponto tranquilizador para a sala: nada disto pede que se tornem investigadores de ML. Pede que apliquem rigor de dados — observabilidade, contratos, testes, linhagem — a um tipo de dado novo (o vetor) e a um componente novo (o LLM). Construir para a GenAI não substitui o engenheiro de dados; precisa dele mais do que nunca.


**88. Ler e validar passou a valer mais**  ·  <sub>Síntese · o que passa a distinguir</sub>

Duas mensagens de carreira. Primeira: o code review sobe de importância. Quando gerar SQL/ETL passa a ser barato, o gargalo e o valor mudam-se para validar — correção semântica, performance, intenção de negócio (fonte Monte Carlo: "code review vale mais, não menos"). Segunda: clareza > esperteza. Tanto humanos como LLMs trabalham melhor sobre código legível, com restrições explícitas e intenção clara; o código "esperto" e denso é agora um anti-padrão, porque é mais caro de validar e a máquina também o lê mal. A disciplina que isto premeia é a mesma que já era boa prática — só que agora paga mais.


**89. O próximo passo: pipelines que se auto-curam**  ·  <sub>A seguir</sub>

Teaser honesto, sem aprofundar — agentes são tema de outra aula (e está PARKED nas fontes). A ponte: o passo seguinte à GenAI-como-ferramenta é a GenAI-como-ator — agentes que propõem e executam mudanças no pipeline. Mas o gancho crítico fecha o arco da aula: um agente só é seguro sobre uma base de dados disciplinada — contratos, validação, políticas, linhagem. Automatizar sobre dados sem governança é amplificar a corrupção silenciosa. Por isso a aula de hoje (os alicerces) vem antes da dos agentes. Não vender futuro; ancorar no que construímos.


> _Obrigada:_ Fecho. Agradecer e abrir para perguntas. Lembrar: os materiais e os sandboxes ficam no link do repositório; podem explorar depois. Voltar, se der, às leituras que deram no aquecimento.
