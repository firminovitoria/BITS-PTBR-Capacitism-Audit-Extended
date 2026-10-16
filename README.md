# Capacitismo automatizado: versão estendida (BITS-PTBR v2)

Código, dados e resultados da versão estendida do artigo "Capacitismo Automatizado: Uma Auditoria de Modelos de Linguagem em Português nas Tarefas de Sentimento e Toxicidade" (WICS 2026), submetida ao *Journal on Interactive Systems* (JIS).

O artigo original adapta para o português (BITS-PTBR) a análise de sensibilidade a perturbações (PSA) de Venkit et al. (2023) e mede o viés contra pessoas com deficiência em um classificador de sentimento e dois de toxicidade. A versão estendida pergunta também **de onde vem o efeito** e **se ele aparece fora dos templates**.

## Perguntas de pesquisa

A redação final está no artigo.

- **RQ1.** O efeito é específico de deficiência ou aparece com qualquer menção a uma característica da pessoa?
- **RQ2.** O uso figurado e pejorativo de termos como "cego" e "surdo" ajuda a explicar o efeito? (tratada como hipótese)
- **RQ3.** Como e quanto o efeito varia entre termos, grupos, templates e categorias emocionais?
- **RQ4.** O padrão de falsos positivos dos templates aparece em frases não tóxicas de outros corpora (ToxSyn-PT e HateCheck-PT) que mencionam pessoas com deficiência?
- **RQ5.** As diferenças entre os classificadores de toxicidade podem estar associadas aos dados de treinamento? (pergunta descritiva, não causal)

## Estrutura

```
.
├── README.md
├── LICENSE                        licença do código (MIT)
├── LICENSE-DATA.md                licença dos dados e resultados (CC BY-NC 4.0) e exceções
├── lista_termos_congelada.csv     termos de deficiência usados em todas as buscas (C, D e E)
├── notebooks/                     12 notebooks, numerados na ordem de execução
├── data_bits/                     versões do corpus BITS-PTBR, com e sem scores
├── data_externa/                  corpora externos (não versionados; baixados pelo notebook 01)
└── results/                       tabelas, figuras e scores gerados pelos notebooks
```

## Notebooks

Todos foram executados no Google Colab. Os que rodam modelos precisam de GPU (T4 é suficiente).

| Notebook | O que faz | Contribuição | RQ | GPU |
|---|---|---|---|---|
| `01_baixar_corpora` | Baixa os corpora externos para `data_externa/` | C, D, E | | não |
| `02_inspecao_vazamento_termos` | Inspeciona os corpora, mede a sobreposição entre eles (texto normalizado), monta a tabela de vazamento e congela a lista de termos | C | RQ5 | não |
| `03_calibracao_limiares` | Calibra os limiares dos dois classificadores de toxicidade no HateBR, valida no teste do ToLD-Br, constrói a v1.1 e mede a sensibilidade das métricas binárias ao limiar | C | base para todas | sim |
| `04_psa_v1.1` | Métricas da PSA na v1.1, tabela por termo e bootstrap por contexto e por contexto + termo | B | RQ1, RQ3 | sim |
| `05_fechamento_contribuicao_C` | Controles tóxicos por categoria emocional, termos no treino × efeito, dados vistos × não vistos | C | RQ5 | sim |
| `06_gerar_corpus_v2` | Gera o corpus BITS-PTBR v2 | A | RQ1, RQ3 | não |
| `07_psa_v2` | Inferência na v2, métricas, limiares calibrados, RQ3 e contrastes da RQ1 | B | RQ1, RQ3 | sim |
| `08_subconjunto_literal_figurado` | Constrói o subconjunto literal × figurado e calcula as métricas | A | RQ2 | sim |
| `09_complementos_v2` | Bootstrap da v2 em dois níveis, RQ1 com todos os grupos de comparação e RQ2 com os limiares calibrados | B | RQ1, RQ2 | sim (parte 3) |
| `10_contribuicao_D_falsos_positivos` | Taxa de falso positivo por grupo-alvo no ToxSyn-PT e no HateCheck-PT | D | RQ4 | sim |
| `11_contribuicao_E_uso_real` | Trechos justificativos do HateBRXplain, trechos tóxicos do OLID-BR, classificação manual de "cego" e "surdo" e estudo de casos | E | RQ2, RQ4, RQ5 | não |
| `12_atualizacao_v2_C_D` | Refaz com a v2 três análises feitas antes com a v1.1: controles tóxicos por categoria emocional, termo no treino × efeito (RQ5) e relação da contribuição D com a PSA | C, D | RQ3, RQ4, RQ5 | não |

Cada notebook começa com uma célula de texto que descreve entradas e saídas, e termina, quando é o caso, com a leitura dos resultados.

**Caminhos.** Os notebooks 01, 02, 03, 05, 09, 10, 11 e 12 usam `ROOT = "/content/drive/MyDrive/JIS - capacitismo"`, com as subpastas deste repositório. Os notebooks 04, 06, 07 e 08 foram executados em outra pasta do Drive (variável `PASTA`); para reexecutá-los, aponte `PASTA` para `data_bits/` e `results/`.

## Corpus BITS-PTBR

| Arquivo (`data_bits/`) | Conteúdo |
|---|---|
| `BITS_PTBR_v1_pareado_com_scores.csv` | v1: corpus do artigo do WICS (3.105 pares, com 375 sentenças repetidas) |
| `BITS_PTBR_v1.1_pareado_com_scores.csv` | v1.1: v1 sem as sentenças repetidas (2.730 pares, 130 controles, 21 termos) |
| `V1_LIMPA_PAREADA_COM_PREDICOES.csv` | v1.1 com os scores dos três modelos e os rótulos de sentimento previstos (entrada do notebook 04) |
| `BITS_PTBR_v1.1_conferencia.csv` | versão da v1.1 preparada separadamente, usada só para conferência no notebook 03 |
| `BITS_PTBR_v2_corrigido.csv` | v2: 5.863 pares, 145 controles, 41 termos em 8 grupos, 20 templates |
| `BITS_PTBR_v2_corrigido_com_predicoes.csv` | v2 com os scores dos três modelos |
| `BITS_PTBR_v2_RQ2_literal_figurado_pareado.csv` | subconjunto literal × figurado (50 pares) |

Cada par tem a frase com o termo (`Sentence`) e o controle sem ele (`Sentence_control`). O delta é score(frase com o termo) − score(controle).

**Grupos da v2:** Clínico (6 termos), Discurso Social (10), Sem Deficiência (4), NRMA (5), Contraste de Idade (3), de Nacionalidade (3), de Religião (3) e Controle de comprimento (7). Os termos de idade não entram em T1 e T9 ("criança"), e "idoso" não entra em T12 ("rapaz"); por isso o grupo de idade tem 117 ou 118 pares por termo, e as comparações com ele usam só os controles compartilhados.

## Modelos

| Nome no artigo | Modelo no Hugging Face | Score |
|---|---|---|
| BERTweet-PT (sentimento) | `pysentimiento/bertweet-pt-sentiment` | P(positivo) − P(negativo) |
| BERTimbau toxic | `dougtrajano/toxic-comment-classification` | P(ofensivo) |
| ToxiGuardrail-PT | `nicholasKluge/ToxiGuardrailPT` | 1 − sigmoide do logit |

## Limiares

Escolhidos na metade A do HateBR (semente 42), avaliados na metade B e validados nos 2.010 tuítes do teste do ToLD-Br não vistos pelos modelos (`results/thresholds.json`). Decisão: tóxico quando score ≥ limiar.

| | 0,5 (WICS) | Youden | 5% de falsos positivos |
|---|---|---|---|
| BERTimbau toxic | 0,500 | 0,797 | 0,841 |
| ToxiGuardrail-PT | 0,500 | 0,432 | 0,877 |

O limiar de 5% de falsos positivos é o usado no corpo do artigo; os outros entram na análise de sensibilidade. Os limiares só afetam as métricas binárias (FlipRate e LabelDist); scores e deltas não dependem deles.

## Métricas

- **ScoreSense:** média dos deltas.
- **ScoreDev:** para cada contexto, desvio padrão dos scores das frases com cada termo do grupo; depois, a média entre os contextos.
- **FlipRate:** proporção de pares em que a decisão muda entre controle e frase com o termo, reportada por direção (para tóxico e para não tóxico).
- **LabelDist:** distância de Jaccard entre os conjuntos de frases positivas (ou tóxicas) com e sem o termo. Satura em 0 ou 1 quando os controles não têm casos positivos, como no ToxiGuardrail-PT.
- **Taxa de falso positivo:** entre frases rotuladas como não tóxicas, fração classificada como tóxica.
- **Intervalos:** bootstrap percentil de 95% com 10 mil reamostragens e semente 42, por contexto (controle) e por contexto + termo. Contrastes entre grupos: pareados por contexto, com correção de Holm.

## Resultados (`results/`)

Os prefixos indicam a origem:

| Prefixo ou arquivo | Notebook |
|---|---|
| `c1_*`, `c2_*` | 02 |
| `calibration_*`, `thresholds.json`, `hatebr_scores.csv`, `told_br_test_scores.csv`, `c4_*_v1.1_*` | 03 |
| `b_tabela_por_termo_v1.1.csv`, `metricas_BITS_PTBR_V1_1_JACCARD.csv`, `diagnostico_contextos_*_V1_1_*`, `distribuicoes_*_V1_1_*`, `BOOTSTRAP_V1_1_*` (v1.1) e `BOOTSTRAP_V1_*` (mesmo notebook aplicado à v1) | 04 |
| `c5_*`, `c6_*`, `c7_*`, `olid_br_scores_tox1.csv`, `told_br_validacao_vistos_scores_tox2.csv` | 05 |
| `BITS_PTBR_v2_exclusoes_idade.csv`, `BITS_PTBR_v2_duplicatas_removidas.csv`, `BITS_PTBR_v2_alertas_revisao.csv` (auditoria da geração) | 06 |
| `metricas_BITS_PTBR_v2_JACCARD.csv`, `metricas_gerais_BITS_PTBR_v2.csv`, `scoredev_contextos_BITS_PTBR_v2.csv`, `V2_*` (auditoria de duplicatas, vazios), `c4_*_v2_*`, `PSA_v2_principal_fpr5.csv`, `PSA_BITS_PTBR_v2_limiares.csv`, `bootstrap_BITS_PTBR_v2_IC95.csv`, `RQ1_contextos_compartilhados.csv`, `RQ1_contrastes_Holm_IC95.csv`, `RQ3_metricas_por_fatores.csv`, `RQ3_heatmap_*.png` | 07 |
| `BITS_PTBR_v2_literal_figurado_com_scores.csv`, `BITS_PTBR_v2_literal_figurado_metricas.csv`, `BITS_PTBR_v2_literal_figurado_comparacao.csv` | 08 |
| `v2_*` | 09 |
| `d_*` | 10 |
| `e_*` | 11 |
| `c5_v2_*`, `c6_v2_*`, `d_v2_*` | 12 |

As tabelas do artigo para a v2 usam os arquivos `v2_*` (intervalos em dois níveis e RQ1 com todos os grupos de comparação).

## Corpora externos

Não estão no repositório. O notebook 01 baixa todos para `data_externa/`.

| Corpus | Fonte | Uso | Licença |
|---|---|---|---|
| HateBR e HateBRXplain | GitHub `franciellevargas/HateBR` | calibração (C), estudo de casos (E) | CC BY-NC 4.0 |
| ToLD-Br | GitHub `JAugusto97/ToLD-Br` | validação do limiar (C), vazamento | CC BY-SA 4.0 |
| OLID-BR | Hugging Face `dougtrajano/olid-br` | vazamento, vistos × não vistos (C), trechos tóxicos (E) | CC BY 4.0 |
| ToxSyn-PT (Brito et al., 2026, LREC) | Hugging Face `AKCIT/ToxSyn-PT` | falsos positivos por grupo (D) | não informada pelos autores |
| HateCheck-PT | Hugging Face `Paul/hatecheck-portuguese` | falsos positivos por grupo (D) | CC BY 4.0 |
| harmful-text (parte em português) | Hugging Face `nicholasKluge/harmful-text` | dado de treino do ToxiGuardrail-PT (vazamento) | Apache 2.0 |
| TuPy-E | Hugging Face `Silly-Machine/TuPyE-Dataset` | inspeção e sobreposição (C) | CC BY 4.0 |

**Textos de terceiros em `results/`.** Alguns arquivos trazem o texto dos corpora junto com os scores e seguem a licença do corpus de origem:

- HateBR (CC BY-NC 4.0, uso não comercial): `hatebr_scores.csv`, `e_casos_hatebr_nao_ofensivos.csv` e as linhas do HateBR em `e_classificacao_cego_surdo.csv`;
- ToLD-Br (CC BY-SA 4.0, obras derivadas sob a mesma licença): `told_br_test_scores.csv` e `told_br_validacao_vistos_scores_tox2.csv`;
- OLID-BR (CC BY 4.0): `olid_br_scores_tox1.csv` e as linhas do OLID-BR em `e_classificacao_cego_surdo.csv`;
- HateCheck-PT (CC BY 4.0): `d_hatecheck_scores.csv`.

**O ToxSyn-PT não é redistribuído.** Como o conjunto de dados não declara licença, `d_toxsyn_nao_toxicas_scores.csv` traz apenas o identificador (`id`), os metadados e os scores, sem o texto. Para recuperar as frases, baixe o corpus pelo notebook 01 e junte pela coluna `id`. A licença do artigo (CC BY 4.0, ACL Anthology) não se aplica ao conjunto de dados.

## Observações

- A coluna `entra_no_v2` de `lista_termos_congelada.csv` registra o estado no dia do congelamento (12/10/2026). Os quatro termos marcados "a confirmar" (autista, deficiente, deficiente mental e em cadeira de rodas) foram depois incluídos na v2.
- O notebook 07 também traz um bootstrap por par, que trata os pares como independentes. Ele foi mantido como registro; as análises do artigo usam o bootstrap em dois níveis do notebook 09.
- No subconjunto literal × figurado, o controle literal remove o termo e o figurado o substitui por outra palavra. Por isso a comparação entre os usos é feita pelo score absoluto das frases com o termo (notebook 09).
- A classificação de "cego" e "surdo" (notebook 11) foi feita de forma independente pelas duas autoras e depois consolidada (`results/e_classificacao_cego_surdo.csv`).

## Licença

- **Código** (`notebooks/`): MIT (`LICENSE`).
- **Corpus BITS-PTBR, lista de termos e resultados** (`data_bits/`, `lista_termos_congelada.csv`, `results/`): CC BY-NC 4.0 (`LICENSE-DATA.md`), compatível com o HateBR.
- **Exceções:** textos de corpora externos em `results/` seguem a licença do corpus de origem. Os dois arquivos com textos do ToLD-Br ficam sob CC BY-SA 4.0, como exige essa licença. Detalhes em `LICENSE-DATA.md`.

## Referência

Venkit, P. N., Srinath, M., & Wilson, S. (2023). Automated Ableism: An Exploration of Explicit Disability Biases in Sentiment and Toxicity Analysis Models. *Proceedings of the 3rd Workshop on Trustworthy Natural Language Processing (TrustNLP 2023)*.
