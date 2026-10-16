# Automated Ableism: Extended Version (BITS-PTBR v2)

Code, data, and results for the extended version of the paper "Capacitismo Automatizado: Uma Auditoria de Modelos de Linguagem em Português nas Tarefas de Sentimento e Toxicidade" (*Automated Ableism: An Audit of Portuguese Language Models on Sentiment and Toxicity Tasks*, WICS 2026), submitted to the *Journal on Interactive Systems* (JIS).

The original paper adapts the Perturbation Sensitivity Analysis (PSA) of Venkit et al. (2023) to Brazilian Portuguese (BITS-PTBR) and measures bias against people with disabilities in one sentiment classifier and two toxicity classifiers. The extended version also asks **where the effect comes from** and **whether it appears outside the templates**.

Notebook text cells and column names follow the language used during the project (mostly Portuguese).

## Research questions

The final wording is in the paper.

- **RQ1.** Is the effect specific to disability, or does it appear with any mention of a personal characteristic?
- **RQ2.** Does the figurative and pejorative use of terms such as "cego" (blind) and "surdo" (deaf) help explain the effect? (treated as a hypothesis)
- **RQ3.** How, and by how much, does the effect vary across terms, groups, templates, and emotion categories?
- **RQ4.** Does the false positive pattern observed in the templates also appear in non-toxic sentences from other corpora (ToxSyn-PT and HateCheck-PT) that mention people with disabilities?
- **RQ5.** Can the differences between the toxicity classifiers be associated with their training data? (descriptive, not causal)

## Structure

```
.
├── README.md
├── LICENSE                        code license (MIT)
├── LICENSE-DATA.md                data and results license (CC BY-NC 4.0) and exceptions
├── lista_termos_congelada.csv     frozen list of disability terms used in all searches (C, D and E)
├── notebooks/                     11 notebooks, numbered in execution order
├── data_bits/                     BITS-PTBR corpus versions, with and without scores
├── data_externa/                  external corpora (not versioned; downloaded by notebook 01)
└── results/                       tables, figures and scores produced by the notebooks
```

## Notebooks

All notebooks were run on Google Colab. Those that run models need a GPU (a T4 is enough).

| Notebook | What it does | Contribution | RQ | GPU |
|---|---|---|---|---|
| `01_baixar_corpora` | Downloads the external corpora into `data_externa/` | C, D, E | | no |
| `02_inspecao_vazamento_termos` | Inspects the corpora, measures overlap between them (normalized text), builds the training-data leakage table and freezes the term list | C | RQ5 | no |
| `03_calibracao_limiares` | Calibrates the thresholds of both toxicity classifiers on HateBR, validates them on the ToLD-Br test set, builds v1.1 and measures the threshold sensitivity of the binary metrics | C | basis for all | yes |
| `04_psa_v1.1` | PSA metrics on v1.1, per-term table, and bootstrap by context and by context + term | B | RQ1, RQ3 | yes |
| `05_fechamento_contribuicao_C` | Toxic controls by emotion category, terms in training data vs. effect, seen vs. unseen data | C | RQ5 | yes |
| `06_gerar_corpus_v2` | Generates the BITS-PTBR v2 corpus | A | RQ1, RQ3 | no |
| `07_psa_v2` | Inference on v2, metrics, calibrated thresholds, RQ3 and RQ1 contrasts | B | RQ1, RQ3 | yes |
| `08_subconjunto_literal_figurado` | Builds the literal vs. figurative subset and computes its metrics | A | RQ2 | yes |
| `09_complementos_v2` | Two-level bootstrap for v2, RQ1 with all comparison groups, and RQ2 with calibrated thresholds | B | RQ1, RQ2 | yes (part 3) |
| `10_contribuicao_D_falsos_positivos` | False positive rate by target group on ToxSyn-PT and HateCheck-PT | D | RQ4 | yes |
| `11_contribuicao_E_uso_real` | HateBRXplain rationales, OLID-BR toxic spans, manual classification of "cego" and "surdo", and case study | E | RQ2, RQ4, RQ5 | no |

Each notebook starts with a text cell describing its inputs and outputs and, where applicable, ends with a reading of the results.

**Paths.** Notebooks 01, 02, 03, 05, 09, 10 and 11 use `ROOT = "/content/drive/MyDrive/JIS - capacitismo"` with the subfolders of this repository. Notebooks 04, 06, 07 and 08 were run from another Google Drive folder (variable `PASTA`); to rerun them, point `PASTA` to `data_bits/` and `results/`.

## BITS-PTBR corpus

| File (`data_bits/`) | Content |
|---|---|
| `BITS_PTBR_v1_pareado_com_scores.csv` | v1: corpus of the WICS paper (3,105 pairs, including 375 repeated sentences) |
| `BITS_PTBR_v1.1_pareado_com_scores.csv` | v1.1: v1 without repeated sentences (2,730 pairs, 130 controls, 21 terms) |
| `BITS_PTBR_v1.1_conferencia.csv` | separately prepared version of v1.1, used only as a cross-check in notebook 03 |
| `BITS_PTBR_v2_corrigido.csv` | v2: 5,863 pairs, 145 controls, 41 terms in 8 groups, 20 templates |
| `BITS_PTBR_v2_corrigido_com_predicoes.csv` | v2 with the scores of the three models |
| `BITS_PTBR_v2_RQ2_literal_figurado_pareado.csv` | literal vs. figurative subset (50 pairs) |

Each pair contains the sentence with the term (`Sentence`) and the control without it (`Sentence_control`). The delta is score(sentence with the term) − score(control).

**v2 groups:** Clinical (`Clinico`, 6 terms), Social Discourse (`Discurso_Social`, 10), Non-Disabled (`Sem_Deficiencia`, 4), NRMA (5), Age contrast (`Contraste_Idade`, 3), Nationality contrast (`Contraste_Nacionalidade`, 3), Religion contrast (`Contraste_Religiao`, 3) and Length control (`Controle_Comprimento`, 7). Age terms are not combined with T1 and T9 ("criança", child), and "idoso" (elderly) is not combined with T12 ("rapaz", young man). For this reason the age group has 117 or 118 pairs per term, and comparisons involving it use only shared controls.

## Models

| Name in the paper | Hugging Face model | Score |
|---|---|---|
| BERTweet-PT (sentiment) | `pysentimiento/bertweet-pt-sentiment` | P(positive) − P(negative) |
| BERTimbau toxic | `dougtrajano/toxic-comment-classification` | P(offensive) |
| ToxiGuardrail-PT | `nicholasKluge/ToxiGuardrailPT` | 1 − sigmoid(logit) |

## Thresholds

Selected on half A of HateBR (seed 42), evaluated on half B, and validated on the 2,010 ToLD-Br test tweets not seen by the models (`results/thresholds.json`). Decision rule: toxic when score ≥ threshold.

| | 0.5 (WICS) | Youden | 5% false positive rate |
|---|---|---|---|
| BERTimbau toxic | 0.500 | 0.797 | 0.841 |
| ToxiGuardrail-PT | 0.500 | 0.432 | 0.877 |

The 5% false positive threshold is used in the main text of the paper; the others are reported as a sensitivity analysis. Thresholds affect only the binary metrics (FlipRate and LabelDist); scores and deltas do not depend on them.

## Metrics

- **ScoreSense:** mean of the deltas.
- **ScoreDev:** for each context, standard deviation of the scores of the sentences with each term of the group; then, the mean across contexts.
- **FlipRate:** proportion of pairs whose decision changes between the control and the sentence with the term, reported by direction (to toxic and to non-toxic).
- **LabelDist:** Jaccard distance between the sets of positive (or toxic) sentences with and without the term. It saturates at 0 or 1 when the controls have no positive cases, as with ToxiGuardrail-PT.
- **False positive rate:** among sentences labeled non-toxic, the fraction classified as toxic.
- **Intervals:** 95% percentile bootstrap with 10,000 resamples and seed 42, by context (control) and by context + term. Group contrasts are paired by context, with Holm correction.

## Results (`results/`)

File prefixes indicate their origin:

| Prefix or file | Notebook |
|---|---|
| `c1_*`, `c2_*` | 02 |
| `calibration_*`, `thresholds.json`, `hatebr_scores.csv`, `told_br_test_scores.csv`, `c4_*_v1.1_*` | 03 |
| `b_tabela_por_termo_v1.1.csv`, `BOOTSTRAP_V1_1_COMPLETO.csv` | 04 |
| `c5_*`, `c6_*`, `c7_*`, `olid_br_scores_tox1.csv`, `told_br_validacao_vistos_scores_tox2.csv` | 05 |
| `c4_*_v2_*`, `PSA_v2_principal_fpr5.csv`, `bootstrap_BITS_PTBR_v2_IC95.csv`, `RQ1_contrastes_Holm_IC95.csv`, `RQ3_metricas_por_fatores.csv` | 07 |
| `BITS_PTBR_v2_literal_figurado_metricas.csv` | 08 |
| `v2_*` | 09 |
| `d_*` | 10 |
| `e_*` | 11 |

The v2 tables in the paper use the `v2_*` files (two-level intervals and RQ1 with all comparison groups).

## External corpora

They are not included in the repository. Notebook 01 downloads all of them into `data_externa/`.

| Corpus | Source | Use | License |
|---|---|---|---|
| HateBR and HateBRXplain | GitHub `franciellevargas/HateBR` | calibration (C), case study (E) | CC BY-NC 4.0 |
| ToLD-Br | GitHub `JAugusto97/ToLD-Br` | threshold validation (C), leakage | CC BY-SA 4.0 |
| OLID-BR | Hugging Face `dougtrajano/olid-br` | leakage, seen vs. unseen (C), toxic spans (E) | CC BY 4.0 |
| ToxSyn-PT (Brito et al., 2026, LREC) | Hugging Face `AKCIT/ToxSyn-PT` | false positives by group (D) | not stated by the authors |
| HateCheck-PT | Hugging Face `Paul/hatecheck-portuguese` | false positives by group (D) | CC BY 4.0 |
| harmful-text (Portuguese subset) | Hugging Face `nicholasKluge/harmful-text` | ToxiGuardrail-PT training data (leakage) | Apache 2.0 |
| TuPy-E | Hugging Face `Silly-Machine/TuPyE-Dataset` | inspection and overlap (C) | CC BY 4.0 |

**Third-party texts in `results/`.** Some files include texts from the external corpora alongside the scores and keep the license of the source corpus:

- HateBR (CC BY-NC 4.0, non-commercial use): `hatebr_scores.csv`, `e_casos_hatebr_nao_ofensivos.csv`, and the HateBR rows in `e_classificacao_cego_surdo.csv`;
- ToLD-Br (CC BY-SA 4.0, derivatives under the same license): `told_br_test_scores.csv` and `told_br_validacao_vistos_scores_tox2.csv`;
- OLID-BR (CC BY 4.0): `olid_br_scores_tox1.csv` and the OLID-BR rows in `e_classificacao_cego_surdo.csv`;
- HateCheck-PT (CC BY 4.0): `d_hatecheck_scores.csv`.

**ToxSyn-PT is not redistributed.** Because the dataset does not state a license, `d_toxsyn_nao_toxicas_scores.csv` contains only the identifier (`id`), metadata, and scores, without the text. To recover the sentences, download the corpus with notebook 01 and join on the `id` column. The license of the paper (CC BY 4.0, ACL Anthology) does not apply to the dataset.

## Notes

- The `entra_no_v2` column of `lista_termos_congelada.csv` records the status on the freeze date (2026-10-12). The four terms marked "a confirmar" (to be confirmed: autista, deficiente, deficiente mental and em cadeira de rodas) were later included in v2.
- Notebook 07 also contains a pair-level bootstrap, which treats pairs as independent. It is kept for the record; the analyses in the paper use the two-level bootstrap from notebook 09.
- In the literal vs. figurative subset, the literal control removes the term, while the figurative control replaces it with another word. For this reason, the two uses are compared through the absolute score of the sentences containing the term (notebook 09).
- The classification of "cego" and "surdo" (notebook 11) was done independently by two authors and then reconciled (`results/e_classificacao_cego_surdo.csv`).

## License

- **Code** (`notebooks/`): MIT (`LICENSE`).
- **BITS-PTBR corpus, term list and results** (`data_bits/`, `lista_termos_congelada.csv`, `results/`): CC BY-NC 4.0 (`LICENSE-DATA.md`), compatible with HateBR.
- **Exceptions:** texts from external corpora in `results/` keep the license of their source corpus. The two files with ToLD-Br texts are distributed under CC BY-SA 4.0, as that license requires. See `LICENSE-DATA.md`.

## Reference

Venkit, P. N., Srinath, M., & Wilson, S. (2023). Automated Ableism: An Exploration of Explicit Disability Biases in Sentiment and Toxicity Analysis Models. *Proceedings of the 3rd Workshop on Trustworthy Natural Language Processing (TrustNLP 2023)*.
