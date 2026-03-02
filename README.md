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
./
├── README.md                     # Ce fichier (racine du dépôt)
└── ECF3/
   ├── data/                     # Données sources
   │   └── 03_DONNEES.csv        # Dataset client (7043 lignes)
   ├── notebooks/                # Notebooks Jupyter
   │   └── RASMI_sofian_ecf3.ipynb
   ├── outputs/                  # Résultats générés
   │   ├── sklearn/              # Résultats Scikit-learn
   │   │   ├── predictions_test.csv
   │   │   ├── model_metrics.json
   │   │   └── feature_importance.csv
   │   └── spark/                # Résultats Spark MLlib
   │       ├── predictions_spark.csv
   │       ├── model_metrics_spark.json
   │       └── feature_importance_spark.csv
   ├── docs/                     # Documentation
   │   ├── rapport.md
   │   ├── RGPD.md
   │   └── DOCKER_GUIDE.md
   ├── requirements.txt
   ├── Dockerfile
   ├── docker-compose.yml
   ├── .dockerignore
   └── .gitignore
```

## Installation

### Méthode 1 : Docker (RECOMMANDÉE)

Cette méthode garantit un environnement identique sur toutes les machines avec Java et toutes les dépendances pré-installées.

#### Prérequis
- Docker Desktop installé ([télécharger ici](https://www.docker.com/products/docker-desktop))
- Docker Compose inclus avec Docker Desktop

#### Étapes

1. Cloner le repository ou télécharger les fichiers

2. Se placer dans le dossier projet :
   ```bash
   cd ECF3
   ```

3. Lancer le conteneur avec Docker Compose :
   ```bash
   docker compose up -d --build
   ```

4. Accéder à Jupyter Notebook :
   - Ouvrir votre navigateur à l'adresse : `http://localhost:8888`
   - Aucun token requis (accès direct)

5. Naviguer dans le dossier `notebooks/` et ouvrir `RASMI_sofian_ecf3.ipynb`

6. Arrêter le conteneur après utilisation :
   ```bash
   docker compose down
   ```

#### Commandes Docker utiles

```bash
# Construire l'image manuellement
docker compose build

# Voir les logs du conteneur
docker compose logs -f

# Redémarrer le conteneur
docker compose restart

# Supprimer le conteneur et les volumes
docker compose down -v
```

### Méthode 2 : Installation locale (Alternative)

#### Prérequis

- Python 3.8+ (testé avec Python 3.11.9)
- pip pour gérer les dépendances
- Java 8+ (requis uniquement pour la section 5 Spark - BONUS)

#### Étapes

1. Cloner le repository ou télécharger les fichiers

2. Se placer dans le dossier projet :
   ```bash
   cd ECF3
   ```

3. Créer un environnement virtuel (optionnel mais recommandé)
   ```bash
   python -m venv venv
   source venv/bin/activate  # Linux/Mac
   venv\Scripts\activate     # Windows
   ```

4. Installer les dépendances
   ```bash
   pip install -r requirements.txt
   ```

5. Lancer Jupyter Notebook
   ```bash
   jupyter notebook
   ```
   Puis naviguer dans le dossier `notebooks/` et ouvrir `RASMI_sofian_ecf3.ipynb`

## Avantages de la méthode Docker

**Environnement identique** : Même configuration Python, Java et dépendances sur toutes les machines  
**Installation simplifiée** : Une seule commande `docker compose up -d --build`  
**Pas de conflit** : L'environnement est isolé de votre système  
**Spark fonctionnel** : Java pré-installé pour la section 5 bonus  
**Reproductibilité garantie** : Le jury exécutera exactement le même environnement  
**Nettoyage facile** : Suppression complète avec `docker compose down -v`

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
- Calcul du ROI (230% au mois 1, gain annuel net : 19 300 euros)

### Section 5 BONUS : Spark MLlib
- Initialisation SparkSession
- Preprocessing Spark (StringIndexer, VectorAssembler, StandardScaler)
- Modélisation Spark (3 modèles)
- Comparaison performance et scalabilité Scikit-learn vs Spark

## Résultats Principaux

### Performance du Modèle Sélectionné

Modèle : Logistic Regression avec class_weight="balanced" et C=10

Métriques sur l'échantillon test (2113 clients) :
- Accuracy : 61,8%
- Precision : 21,3%
- Recall : 61,5% (priorité : détecter les churners)
- F1-score : 31,7%
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
- Coût total des actions : 500 euros
- Gain annuel net estimé : 19 300 euros
- ROI mois 1 : 230%
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
cd ECF3
pip install -r requirements.txt
```

## Auteur

RASMI Sofian - Évaluation Certificative Finale (ECF) Machine Learning Supervisé

## Date

Mars 2026

## Documentation Détaillée

Pour une analyse approfondie des méthodologies, résultats détaillés et perspectives d'amélioration, consulter le fichier `ECF3/docs/rapport.md`.

## Notes d'Utilisation

- Le notebook utilise la seed aléatoire 42 pour la reproducibilité
- Toutes les cellules Python sont commentées et expliquées
- Les cellules doivent être exécutées de haut en bas (ou "Run All")
- **Section 5 (Spark)** : Nécessite Java 8+. Cette section est un BONUS optionnel - les sections 1-4 couvrent tous les livrables obligatoires
- Le preprocessing est intégré dans les pipelines pour éviter les fuites de données
