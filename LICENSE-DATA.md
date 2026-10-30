# Data and results license

The BITS-PTBR corpus (`data_bits/`), the term list (`lista_termos_congelada.csv`), and the results (`results/`) are licensed under the **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** license.

Full text: https://creativecommons.org/licenses/by-nc/4.0/legalcode.en

You may copy, redistribute, and adapt the material, provided that you give appropriate credit to the authors, indicate the changes made, and do not use it for commercial purposes.

## Exceptions: third-party texts

Some files in `results/` contain texts from external corpora. These texts remain under the license of the source corpus:

| Corpus | License | Files |
|---|---|---|
| HateBR | CC BY-NC 4.0 | `hatebr_scores.csv`, `e_casos_hatebr_nao_ofensivos.csv`, HateBR rows of `e_classificacao_cego_surdo.csv` |
| ToLD-Br | CC BY-SA 4.0 | `told_br_test_scores.csv`, `told_br_validacao_vistos_scores_tox2.csv` (these two files are distributed under CC BY-SA 4.0) |
| OLID-BR | CC BY 4.0 | `olid_br_scores_tox1.csv`, OLID-BR rows of `e_classificacao_cego_surdo.csv` |
| HateCheck-PT | CC BY 4.0 | `d_hatecheck_scores.csv` |

ToxSyn-PT is not redistributed, because its license is not stated by its authors: `d_toxsyn_nao_toxicas_scores.csv` contains only identifiers, metadata, and scores, without the sentences.

The code (`notebooks/`) is licensed under the MIT License (file `LICENSE`).
