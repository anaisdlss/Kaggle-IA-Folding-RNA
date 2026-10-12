# Projet Kaggle — exploration et prédiction de la réactivité de l’ARN

Ce projet universitaire étudie les données de **Stanford Ribonanza RNA Folding** : comment préparer des séquences d’ARN et leurs mesures expérimentales pour prédire la réactivité de chaque nucléotide aux expériences DMS et 2A3.

**[Ouvrir le notebook et ses résultats enregistrés](Projet_Kaggle_ARN.ipynb)**

Le notebook rassemble le code, les visualisations et les sorties déjà sauvegardées. Les cibles sont des **réactivités par nucléotide**, pas des coordonnées de structure 3D.

## Ce que contient le notebook

1. **Exploration** : composition des séquences, longueurs, types d’expériences, valeurs manquantes, distributions et visualisations des réactivités.
2. **Nettoyage** : filtre `SN_filter == 1`, seuils de lectures et de signal/bruit, suppression des doublons par séquence et type d’expérience, conservation des séquences disposant des deux mesures.
3. **Préparation des jeux** : séparation par séquence unique, encodage one-hot, normalisation robuste par médiane/MAD et padding à 457 positions.
4. **Modélisation** : essai d’un LSTM avec TensorFlow/Keras et définitions de variantes BiLSTM avec TensorFlow/Keras et PyTorch ; pertes et métriques avec masquage.

## Résultats disponibles

**Les résultats enregistrés sont directement dans les sorties du notebook**, sous les cellules correspondantes : tableaux de contrôle, graphiques d’exploration, dimensions des jeux et journal d’apprentissage.

La préparation sauvegardée aboutit à **167 988 séquences uniques**, réparties comme suit :

| Jeu | Séquences uniques | Part |
| --- | ---: | ---: |
| Entraînement | 117 591 | 70 % |
| Validation | 30 238 | 18 % |
| Test interne | 20 159 | 12 % |

Chaque séquence est associée aux deux expériences. Les nombres de lignes avant encodage sont donc différents des nombres de séquences.

Le journal du LSTM est partiel : l’exécution a été interrompue pendant la dixième époque (`KeyboardInterrupt`). Les cellules d’évaluation finale et d’entraînement des variantes BiLSTM n’ont pas de sorties sauvegardées dans cette version. Aucun score final sur le jeu de test ni classement Kaggle n’est annoncé. Les sorties conservées n’ont pas été recalculées lors de la mise en ligne.

## Consulter ou exécuter le projet

### Lire les résultats sans entraînement

Ouvrir [Projet_Kaggle_ARN.ipynb](Projet_Kaggle_ARN.ipynb) dans GitHub ou Jupyter. Les sorties sauvegardées restent consultables sans télécharger les données ni exécuter les cellules. Les graphiques interactifs Plotly peuvent nécessiter Jupyter ; si GitHub ne charge pas l’aperçu, télécharger le notebook pour le consulter localement.

### Installer les dépendances

Créer un environnement Python compatible avec les versions de TensorFlow et PyTorch choisies, puis installer les bibliothèques utilisées :

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install jupyterlab ipykernel numpy pandas matplotlib seaborn plotly scikit-learn tensorflow torch
```

Sous Windows, l’activation se fait avec `.venv\Scripts\activate`. L’environnement original du notebook indique Python 3.13.5 et une sortie sauvegardée affiche PyTorch 2.9.0 ; ces informations ne constituent pas un environnement de dépendances verrouillé.

### Ajouter les données

Télécharger les fichiers depuis la [page de données de la compétition Kaggle](https://www.kaggle.com/competitions/stanford-ribonanza-rna-folding/data), en suivant ses conditions d’accès, puis les extraire dans un dossier `data/` à côté du notebook :

```text
Kaggle-IA-Folding-RNA/
├── README.md
├── Projet_Kaggle_ARN.ipynb
└── data/
    ├── train_data.csv
    └── test_sequences.csv
```

Les données et les poids des modèles ne sont pas inclus dans le dépôt. Le dossier peut être changé avec la variable d’environnement `RNA_DATA_DIR`, utilisée dans la première cellule de code.

### Lancer Jupyter

```bash
jupyter lab Projet_Kaggle_ARN.ipynb
```

Exécuter les cellules d’exploration et de préparation dans l’ordre. Les exports filtrés `df_train_filtered.csv`, `df_val_filtered.csv` et `df_test_filtered.csv` sont écrits dans le dossier de travail.

La partie modèles contient plusieurs essais alternatifs : **choisir une branche TensorFlow/Keras ou PyTorch plutôt qu’exécuter tout le notebook d’un seul coup**. Des variables comme `model` sont réutilisées d’un essai à l’autre ; relancer la préparation après un redémarrage du noyau avant de changer de branche. Le fichier Kaggle `test_sequences.csv` est exploré au début ; le test interne avec cibles est ensuite construit à partir des données d’entraînement filtrées.

L’encodage crée des tableaux volumineux et l’entraînement peut être long. PyTorch sélectionne MPS sur Mac compatible ou le CPU ; les cellules TensorFlow utilisent aussi une sélection de GPU à adapter à l’environnement.

## Points méthodologiques à améliorer

- La normalisation est calculée séparément sur les jeux d’entraînement, de validation et de test. Pour une évaluation rigoureuse, ajuster les paramètres sur le train puis les réutiliser sur les autres jeux.
- Certains masques utilisent des cibles nulles pour identifier le padding ou les valeurs manquantes. Un masque explicite permettrait de distinguer ces cas de véritables valeurs nulles.

Ce dépôt présente un travail exploratoire universitaire, avec ses sorties enregistrées et les étapes encore à finaliser.
