# Licença dos dados e resultados

O corpus BITS-PTBR (`data_bits/`), a lista de termos (`lista_termos_congelada.csv`) e os resultados (`results/`) estão sob a licença **Creative Commons Atribuição-NãoComercial 4.0 Internacional (CC BY-NC 4.0)**.

Texto completo: https://creativecommons.org/licenses/by-nc/4.0/legalcode.pt

Você pode copiar, redistribuir e adaptar o material, desde que atribua o crédito aos autores, indique as alterações feitas e não o use para fins comerciais.

## Exceções: textos de terceiros

Alguns arquivos de `results/` contêm textos de corpora externos. Esses textos continuam sob a licença do corpus de origem:

| Corpus | Licença | Arquivos |
|---|---|---|
| HateBR | CC BY-NC 4.0 | `hatebr_scores.csv`, `e_casos_hatebr_nao_ofensivos.csv`, linhas do HateBR em `e_classificacao_cego_surdo.csv` |
| ToLD-Br | CC BY-SA 4.0 | `told_br_test_scores.csv`, `told_br_validacao_vistos_scores_tox2.csv` (esses dois arquivos são distribuídos sob CC BY-SA 4.0) |
| OLID-BR | CC BY 4.0 | `olid_br_scores_tox1.csv`, linhas do OLID-BR em `e_classificacao_cego_surdo.csv` |
| HateCheck-PT | CC BY 4.0 | `d_hatecheck_scores.csv` |

O ToxSyn-PT não é redistribuído: `d_toxsyn_nao_toxicas_scores.csv` traz só identificadores, metadados e scores.

O código (`notebooks/`) está sob a licença MIT (arquivo `LICENSE`).
