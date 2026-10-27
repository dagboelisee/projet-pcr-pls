# Modélisation prédictive de l'octroi de crédits bancaires : régressions PCR et PLS

Projet de Master 1 (Économétrie et Statistique appliquée) réalisé en binôme avec Ly Néné Gallé, sur R et Quarto.

## Objectif
Construire un modèle prédictif du **montant d'un prêt** (`Montant_Pret`) à partir du profil de l'emprunteur, avec deux méthodes adaptées à la multicolinéarité : la **régression sur composantes principales (PCR)** et la **régression des moindres carrés partiels (PLS)**.

## Données
256 emprunteurs, 10 variables explicatives quantitatives : âge, revenu mensuel, dépenses mensuelles, épargne, score de crédit, durée du prêt, mensualité, taux d'intérêt, ratio dette/revenu, ratio épargne/revenu. Source : Kaggle (jeu de données vraisemblablement synthétique). Les variables qualitatives (sexe, région, type de prêt) ne sont pas utilisées.

## Démarche
1. **Exploration** : séparation apprentissage (70 %) / test (30 %), distributions, asymétrie forte du montant du prêt.
2. **Transformation** de la cible par la racine carrée. Les valeurs atypiques détectées par le test de Rosner (4 montants très élevés) disparaissent après transformation, donc toutes les observations sont conservées. La normalité des résidus s'améliore (Shapiro-Wilk : W = 0,921 avant, W = 0,980 après).
3. **Multicolinéarité** : corrélation de Pearson et VIF montrent de fortes corrélations entre variables explicatives (par exemple 0,80 entre revenu et dépenses). Une ACP résume 45,8 % de la variance en 2 axes et 81,8 % en 5 axes.
4. **Modélisation** : PCR puis PLS, chacune sur la cible brute et sur la cible transformée, avec validation croisée à 10 segments et choix du nombre de composantes par la règle du « one-sigma ».

## Résultats

| Modèle | Cible | Composantes | R² test | RMSE test |
|---|---|---|---|---|
| PCR | Montant brut | 7 | 0,729 | 47 410 |
| PLS | Montant brut | 2 | 0,714 | 48 724 |
| PCR | Racine carrée | 7 | 0,891 | 48 (échelle √) |
| PLS | Racine carrée | 2 | 0,880 | 51 (échelle √) |

- La **transformation racine carrée** améliore nettement l'ajustement, pour la PCR comme pour la PLS.
- La **PLS atteint une performance proche de la PCR avec seulement 2 composantes** au lieu de 7, car elle oriente ses axes en fonction de la variable à expliquer.
- Interprétation des 2 axes PLS : un axe de **capacité financière** (mensualité, ratio dette/revenu, revenu, dépenses) et un axe de **structure du crédit et de sécurité financière** (épargne, taux d'intérêt, durée).

> Les RMSE de la cible brute (en unités monétaires) et de la cible transformée (en racine d'unités) ne sont pas comparables entre eux. Seuls les R² donnent une idée de l'amélioration.

## Limites
- La mensualité du prêt est calculée à partir du montant, du taux et de la durée, et le ratio dette/revenu à partir de la mensualité. Ces variables contiennent donc une partie de l'information sur la cible, ce qui gonfle les performances. Les résultats sont à lire comme une illustration de méthode.
- Un seul découpage apprentissage/test (graine fixée à 123), sur un petit échantillon.

## Contenu du dépôt
| Fichier | Description |
|---|---|
| `projet_pcr_pls.qmd` | Rapport Quarto (code et texte) |
| `projet_pcr_pls.pdf` | Rapport final |
| `data/DATA.csv` | Données |

## Reproduire
Placer `DATA.csv` dans `data/`, adapter la ligne de lecture du fichier (chemin relatif `data/DATA.csv`) et rendre le `.qmd` avec Quarto. Packages : `tidyverse`, `FactoMineR`, `factoextra`, `pls`, `car`, `EnvStats`, `GGally`, `patchwork`, `moments`, `corrplot`.
