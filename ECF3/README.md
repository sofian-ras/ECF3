# Machine Learning Supervisé - Prédiction de Churn Client TeleCom+

## Description

Ce projet applique le machine learning supervisé pour prédire le churn client chez TeleCom+. Il compare deux approches technologiques : Scikit-learn pour le traitement classique et Spark MLlib pour la scalabilité Big Data.

L'objectif est d'identifier les clients à risque de départ afin de mettre en place des actions de rétention ciblées et mesurables en termes de ROI.

## Dataset

Fichier : `03_DONNEES.csv`

- 7043 clients
- 20 variables explicatives (13 catégorielles, 5 numériques)
- Variable cible : Churn (Yes/No)
- Pas de valeurs manquantes

## Structure du Projet

```
ECF3/
├── RASMI_sofian_ecf3.ipynb      # Notebook Jupyter principal
├── rapport.md                     # Rapport d'analyse complet
├── 03_DONNEES.csv               # Dataset source
├── predictions_test.csv         # Prédictions sur l'échantillon test
├── model_metrics.json           # Métriques des modèles
├── feature_importance.csv       # Importance des variables
├── requirements.txt             # Dépendances Python
├── .gitignore                   # Fichiers à ignorer en versioning
└── README.md                    # Ce fichier
```

## Installation

### Prérequis

- Python 3.8 ou supérieur
- pip ou conda pour gérer les dépendances

### Étapes

1. Cloner le repository ou télécharger les fichiers
2. Créer un environnement virtuel (optionnel mais recommandé)
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   venv\Scripts\activate     # Windows
   ```

3. Installer les dépendances
   ```bash
   pip install -r requirements.txt
   ```

4. Lancer le notebook Jupyter
   ```bash
   jupyter notebook RASMI_sofian_ecf3.ipynb
   ```

## Contenu du Notebook

Le notebook est organisé en 5 sections principales et 1 section bonus :

### Section 1 : Exploration et Préparation
- Chargement et visualisation des données
- Analyse de la distribution du churn
- Détection et traitement des valeurs manquantes
- One-Hot Encoding des variables catégorielles
- Normalisation StandardScaler des variables numériques

### Section 2 : Modélisation
- Split train/test (70/30, stratifié, seed=42)
- Entraînement de 3 modèles avec class_weight="balanced"
  - Logistic Regression
  - Random Forest
  - Histogram Gradient Boosting
- Calcul des métriques (accuracy, precision, recall, f1-score)

### Section 3 : Évaluation et Interprétation
- Matrice de confusion du meilleur modèle
- Extraction et visualisation des 10 features les plus importantes
- Validation croisée à 5 folds
- GridSearchCV pour optimisation du paramètre C

### Section 4 : Recommandations Métier
- Identification des 20 clients les plus à risque (probabilité >80%)
- Analyse des profils caractéristiques
- Définition de 3 actions de rétention
- Calcul du ROI (865% annuel)

### Section 5 BONUS : Spark MLlib
- Initialisation SparkSession
- Preprocessing Spark (StringIndexer, VectorAssembler, StandardScaler)
- Modélisation Spark (3 modèles)
- Comparaison performance et scalabilité Scikit-learn vs Spark

## Résultats Principaux

### Performance du Modèle Sélectionné

Modèle : Logistic Regression avec class_weight="balanced" et C=10

Métriques sur l'échantillon test (2113 clients) :
- Accuracy : 62,0%
- Precision : 21,5%
- Recall : 62,2% (priorité : détecter les churners)
- F1-score : 32,0%
- AUC-ROC : 0,683

### Top 3 Features les Plus Importantes

1. Contract_Two year : coefficient -0,88 (effet protecteur)
2. Contract_Month-to-month : coefficient +0,70 (risque élevé)
3. tenure : coefficient -0,38 (ancienneté protège)

### Analyse des Clients à Risque

Les 20 clients identifiés comme les plus à risque :
- 55% sont effectivement des churners confirmés
- Profil type : contrats mensuels (100%), fiber optic (75%), tenure faible (~10 mois), charges élevées (~84 euros/mois)

### Rentabilité Estimée

Actions de rétention sur les 20 clients jugés les plus à risque :
- Coût des 3 actions : 2000 euros
- Épargne annuelle (clients sauvés) : 19 300 euros
- ROI annuel : 865%
- Point mort : 2e mois

## Recommandations

1. Déployer le modèle LogisticRegression en production immédiatement
2. Utiliser Scikit-learn pour volumes jusqu'à 100k clients
3. Basculer à Spark MLlib au-delà de 1M clients
4. Mettre en place un scoring mensuel automatisé
5. Réentraîner le modèle trimestriellement
6. Tester des améliorations : XGBoost, feature engineering, optimisation du seuil

## Fichiers de Résultats

Les fichiers suivants sont générés lors de l'exécution du notebook :

- `predictions_test.csv` : Prédictions avec probabilités pour chaque client test
- `model_metrics.json` : Métriques consolidées des modèles sklearn et Spark
- `feature_importance.csv` : Coefficients et importance des 20 variables

## Limites et Considerations

- Le taux de churn observé (14%) est inférieur aux 26,5% attendus selon les spécifications
- Le modèle commet 802 fausses alertes (clients prédits churn mais restants)
- Les variables manquantes (réclamations, qualité réseau) pourraient améliorer la performance
- Les causalités ne sont pas prouvées, seulement les associations statistiques

## Technologies Utilisées

- **Données** : pandas, numpy
- **ML classique** : scikit-learn
- **ML distribuée** : PySpark
- **Visualisations** : matplotlib, seaborn
- **Statistiques** : scipy

## Dépendances

Voir `requirements.txt` pour les versions exactes. Installation rapide :
```bash
pip install -r requirements.txt
```

## Auteur

RASMI Sofian - Évaluation Certificative Finale (ECF) Machine Learning Supervisé

## Date

Mars 2026

## Documentation Détaillée

Pour une analyse approfondie des méthodologies, résultats détaillés et perspectives d'amélioration, consulter le fichier `rapport.md`.

## Notes d'Utilisation

- Le notebook utilise la seed aléatoire 42 pour la reproducibilité
- Toutes les cellules Python sont commentées et expliquées
- Les cellules doivent être exécutées de haut en bas
- Spark nécessite une installation Java 8+ sur la machine
- Le preprocessing est intégré dans les pipelines pour éviter les fuites de données
