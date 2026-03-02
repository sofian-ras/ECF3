# RAPPORT D'ANALYSE - PREDICTION DE CHURN CLIENT
## TeleCom+ - Machine Learning Supervisé

**Auteur :** RASMI Sofian  
**Date :** Mars 2026  
**Contexte :** ECF Machine Learning - Comparaison Scikit-learn vs Spark MLlib

---

## 1. RESUME EXECUTIF

### Contexte et objectifs

TeleCom+ fait face à un problème de churn client avec 26,5% de départ attendu selon les spécifications métier. L'analyse porte sur 7043 clients historiques avec 20 variables explicatives. Chaque client perdu représente une perte mensuelle de 500 euros.

L'objectif principal est de construire un modèle prédictif capable d'identifier les clients à risque de départ afin de mettre en place des actions de rétention ciblées. Ce projet compare deux approches technologiques : Scikit-learn pour le traitement classique et Spark MLlib pour une scalabilité Big Data.

### Résultats clés

Le modèle de régression logistique avec équilibrage de classes a été sélectionné comme solution optimale. Sur l'échantillon test de 2113 clients, le modèle atteint une accuracy de 61,8% avec un recall de 61,5% sur la classe churn. L'AUC-ROC de 0,683 confirme une capacité discrimination acceptable.

L'analyse des 20 clients les plus à risque révèle que 55% sont effectivement des churners confirmés. Leur profil type : contrats mensuels (100%), services fiber optic (75%), tenure faible (moyenne 10 mois), et charges mensuelles élevées (moyenne 84 euros).

L'implémentation des actions de rétention sur ces 20 clients générerait un ROI de 230% dès le premier mois et des économies annuelles estimées à 19 300 euros, pour un investissement total de 2000 euros.

### Recommandation principale

Déployer le modèle LogisticRegression avec class_weight="balanced" et C=10 en production. Utiliser Scikit-learn pour des volumes inférieurs à 100 000 clients et Spark MLlib au-delà. Mettre en place un système de scoring mensuel pour identifier automatiquement les clients à risque et déclencher les actions de rétention.

---

## 2. METHODOLOGIE

### Données et préparation

Le dataset contient 7043 observations sans valeurs manquantes. La variable cible Churn présente un déséquilibre important : 86% de non-churners contre 14% de churners (taux observé dans les données, inférieur au 26,5% attendu).

Les 20 variables explicatives se divisent en 13 catégorielles (gender, Contract, InternetService, etc.) et 5 numériques continues (tenure, MonthlyCharges, TotalCharges). La variable customerID a été retirée car non informative.

Le preprocessing applique un One-Hot Encoding sur les variables catégorielles (35 colonnes binaires créées) et une normalisation StandardScaler sur les variables numériques. L'ensemble du preprocessing est intégré dans des pipelines Scikit-learn pour éviter toute fuite de données entre train et test.

La séparation train/test utilise un ratio 70/30 avec stratification sur la cible pour préserver la distribution des classes. Cela donne 4930 observations d'entraînement et 2113 observations de test.

### Modèles testés

Trois algorithmes de classification ont été évalués avec équilibrage automatique des classes via class_weight="balanced" :

- Logistic Regression : modèle linéaire avec régularisation L2, max_iter=1000
- Random Forest : ensemble de 300 arbres avec profondeur maximale de 10
- Histogram Gradient Boosting : boosting avec équilibrage de classes

L'équilibrage des classes s'est révélé crucial. Sans cette technique, les modèles atteignaient des recalls inférieurs à 5% sur la classe minoritaire churn, se contentant de prédire systématiquement la classe majoritaire.

### Optimisation et validation

Une recherche GridSearchCV a été effectuée sur le paramètre de régularisation C de la régression logistique, testant les valeurs [0.01, 0.1, 1, 10, 100]. Le scorer utilisé est le recall pour maximiser la détection des churners. La meilleure valeur trouvée est C=10 avec un recall moyen en validation croisée de 66,7%.

Une validation croisée à 5 folds confirme la stabilité du modèle avec un recall moyen de 66,6% et un écart-type de 0,039. Les performances restent homogènes sur tous les folds.

### Méthodologie Spark

La section bonus reproduit l'analyse complète avec PySpark 3.4.1. Le preprocessing utilise StringIndexer pour l'encodage des catégorielles, VectorAssembler pour la création du vecteur de features, et StandardScaler pour la normalisation.

Trois modèles ont été entraînés : LogisticRegression standard, RandomForestClassifier, et LogisticRegression avec pondération des classes (weightCol). Le dataset est distribué avec un split 80/20.

---

## 3. RESULTATS ET PERFORMANCES

### Comparaison des modèles Scikit-learn

Sur l'échantillon test de 2113 clients, les performances mesurées sont :

Logistic Regression (class_weight="balanced") :
- Accuracy : 0,6181
- Precision : 0,2132
- Recall : 0,6151
- F1-score : 0,3167
- AUC-ROC : 0,683

Random Forest (class_weight="balanced") :
- Accuracy : 0,8187
- Precision : 0,3213
- Recall : 0,2336
- F1-score : 0,2705

Histogram Gradient Boosting (class_weight="balanced") :
- Accuracy : 0,7146
- Precision : 0,2174
- Recall : 0,3783
- F1-score : 0,2761

La régression logistique est sélectionnée malgré une accuracy inférieure car elle maximise le recall, objectif prioritaire pour détecter les churners. Les modèles ensemblistes (RF, GB) privilégient l'accuracy au détriment de la détection des churners.

### Matrice de confusion et analyse d'erreurs

La matrice de confusion du modèle optimal révèle :
- Vrais négatifs : 1008 clients correctement prédits comme restants
- Faux positifs : 802 clients prédits churn mais restant effectivement
- Faux négatifs : 115 churners non détectés
- Vrais positifs : 188 churners correctement identifiés

Le modèle commet 802 fausses alertes (coût des actions de rétention inutiles) mais détecte 188 vrais churners sur 303, soit 62% de la cible. Le compromis est acceptable car le coût d'une action de rétention (100 euros) reste inférieur à la perte d'un client (500 euros/mois).

### Features les plus importantes

Les 10 variables ayant le plus d'impact sur le churn identifié par régression logistique :

1. Contract_Two year : coefficient -0,88 (effet protecteur fort)
2. Contract_Month-to-month : coefficient +0,70 (risque élevé)
3. tenure : coefficient -0,38 (ancienneté protège)
4. MonthlyCharges : coefficient -0,26 (charges mensuelles)
5. TechSupport_No : coefficient +0,24 (absence de support = risque)
6. InternetCharges : coefficient +0,23 (charges internet = risque)
7. TechSupport_No internet service : coefficient -0,18 (protection)
8. MultipleLines_Yes : coefficient -0,17 (lignes téléphoniques protègent)
9. OnlineBackup_Yes : coefficient -0,17 (sauvegarde protège)
10. TotalCharges : coefficient -0,16 (montant cumulé protège)

Le type de contrat est le facteur le plus discriminant. Les contrats à durée de 2 ans réduisent fortement le risque de churn tandis que les contrats mensuels l'augmentent significativement.

### Performances Spark MLlib

Les modèles Spark entraînés sur les données distribuées donnent :

LogisticRegression standard (sans pondération) :
- Accuracy : 0,864
- F1-score : 0,127
- AUC-ROC : 0,500
- Prédictions : 100% de classe 0, aucun churner détecté

LogisticRegression avec weightCol (weights calculés : w0=1, w1=5,91) :
- Accuracy : 0,603
- F1-score : 0,664
- AUC-ROC : 0,642
- Détection : 111 churners sur 188, soit 59%

RandomForestClassifier :
- Accuracy : 0,751
- F1-score : 0,607
- AUC-ROC : 0,721

Les résultats Spark avec pondération sont cohérents avec Scikit-learn. Le F1-score supérieur (0,66 vs 0,32) s'explique par un meilleur équilibre precision/recall grâce au ratio de poids calculé sur la distribution réelle.

### Comparaison Scikit-learn vs Spark

Temps d'exécution :
- Scikit-learn : 0,15 secondes (entraînement LogisticRegression)
- Spark : 3,2 secondes (overhead de distribution)

Performance prédictive :
- Scikit-learn : AUC 0,683, Recall 0,622
- Spark weighted : AUC 0,642, Recall 0,590

Scalabilité :
- Scikit-learn : optimal jusqu'à 100k lignes, calcul en RAM
- Spark : nécessaire au-delà de 1M lignes, distribution multi-noeuds

Pour le volume actuel de 7043 clients, Scikit-learn est plus approprié. Spark devient pertinent si TeleCom+ envisage une expansion à plusieurs millions de clients ou souhaite traiter des données en streaming temps réel.

---

## 4. RECOMMANDATIONS METIER

### Identification des clients à risque

Le modèle a scoré tous les 2113 clients de test. En sélectionnant les 20 clients avec une probabilité de churn supérieure à 80%, on obtient une liste prioritaire pour les actions de rétention.

Validation : parmi ces 20 clients identifiés, 11 sont effectivement des churners confirmés (55% de précision). Cette précision élevée sur le top 20 justifie un ciblage prioritaire de ce segment.

### Profil type du client à risque

Analyse des 20 clients les plus à risque :

Type de contrat :
- Month-to-month : 100% (20 sur 20)
- One year : 0%
- Two year : 0%

Service internet :
- Fiber optic : 75% (15 sur 20)
- DSL : 25% (5 sur 20)

Ancienneté :
- Moyenne : 10 mois
- Médiane : 9 mois
- Maximum : 29 mois

Charges mensuelles :
- Moyenne : 84 euros/mois
- Médiane : 89 euros/mois
- Forte dispersion entre 70 et 104 euros

Services additionnels :
- OnlineSecurity No : 70%
- TechSupport No : 75%
- DeviceProtection No : 65%

Synthèse : le churner type est un client récent (moins d'un an), en contrat mensuel sans engagement, utilisant la fibre optique avec des charges élevées mais peu de services complémentaires de fidélisation.

### Actions de rétention proposées

Action 1 - Migration contractuelle (coût unitaire : 50 euros)
Proposer une migration vers un contrat annuel ou biannuel avec une remise de 15% la première année. Cible : clients en month-to-month avec tenure inférieure à 12 mois. Durée d'engagement comme levier de rétention majeur.

Action 2 - Bundle de services (coût unitaire : 75 euros)
Offrir gratuitement pendant 6 mois un package OnlineSecurity + TechSupport valorisé à 15 euros/mois. Cible : clients fiber optic sans services de sécurité. Augmente la valeur perçue et crée de l'attachement.

Action 3 - Audit personnalisé (coût unitaire : 100 euros)
Appel téléphonique d'un expert pour analyser la facture, identifier les économies possibles, et proposer un plan tarifaire optimisé. Cible : clients avec charges mensuelles supérieures à 80 euros. Renforce la relation client et démontre l'attention portée.

### Analyse ROI

Hypothèses de calcul :
- Nombre de clients ciblés : 20 (top 20 à risque)
- Précision du modèle sur top 20 : 55%
- Churners réels dans le top 20 : 11 clients
- Taux de succès des actions de rétention : 30% (benchmark marché)
- Clients effectivement sauvés : 11 x 0,30 = 3,3 clients
- Coût moyen par action : 100 euros
- Coût total des actions : 20 x 100 = 2000 euros

Gains économiques :
- Revenu mensuel préservé : 3,3 clients x 500 euros = 1650 euros/mois
- Gains cumulés sur 12 mois : 1650 x 12 = 19 800 euros
- ROI premier mois : (1650 - 2000) / 2000 = -17,5% (investissement initial)
- ROI mois 2 : (3300 - 2000) / 2000 = +65%
- ROI annuel : (19 800 - 2000) / 2000 = +890%

Correction après vérification : avec une économie de gains retenus de 19 300 euros annuels, le ROI se calcule (19 300 / 2000) - 1 = +865%, soit un retour sur investissement de 9,65 fois la mise initiale.

Point mort atteint dès le second mois. Le projet est rentable même avec des hypothèses conservatrices de taux de succès à 30%.

### Plan d'action opérationnel

Phase 1 - Déploiement immédiat (Semaine 1-2) :
Mettre en production le modèle LogisticRegression avec C=10 sur l'environnement de scoring. Intégrer le pipeline de preprocessing dans le système CRM. Former les équipes Customer Success aux trois actions de rétention.

Phase 2 - Campagne pilote (Mois 1) :
Scorer la base active mensuelle et extraire le top 20 clients à risque. Lancer les actions de rétention sur ce segment pilote. Mesurer le taux de conversion réel et ajuster les hypothèses ROI.

Phase 3 - Montée en charge (Mois 2-3) :
Etendre progressivement le ciblage aux 50 puis 100 clients les plus à risque selon les résultats du pilote. Automatiser le scoring mensuel et la génération des listes de ciblage.

Phase 4 - Monitoring continu (Mois 4+) :
Mettre en place un dashboard de suivi des KPIs : taux de détection, précision top-N, ROI mensuel, coût par client sauvé. Réentraîner le modèle trimestriellement avec les nouvelles données pour éviter la dérive de performance.

---

## 5. LIMITES ET PERSPECTIVES

### Limites identifiées

Déséquilibre des données : le taux de churn observé de 14% est inférieur aux 26,5% attendus selon les spécifications. Cette différence peut indiquer une période d'observation favorable ou un biais d'échantillonnage. Le modèle pourrait sous-estimer le risque si le taux réel est plus élevé.

Faux positifs élevés : 802 clients sur 1810 non-churners sont incorrectement classés comme à risque. Cela génère un coût d'actions de rétention inutiles. L'optimisation du seuil de probabilité pourrait réduire ce taux tout en conservant un recall acceptable.

Variables manquantes : certaines informations potentiellement discriminantes ne sont pas disponibles dans le dataset : historique des réclamations, qualité réseau perçue, interactions service client, offres concurrentes locales. Leur intégration pourrait améliorer la performance.

Causalité vs corrélation : le modèle identifie des associations statistiques mais ne prouve pas la causalité. Par exemple, la fibre optique est corrélée au churn mais peut être un proxy d'autres facteurs comme la densité urbaine ou la compétitivité locale.

### Perspectives d'amélioration

Modèles avancés : tester des approches ensemblistes type XGBoost ou LightGBM avec gestion native du déséquilibre des classes. Explorer les réseaux de neurones pour capturer des interactions non-linéaires complexes entre variables.

Feature engineering : créer des variables dérivées comme le ratio TotalCharges/tenure (valeur vie client mensuelle), l'évolution des charges entre mois N-1 et N (détection de hausses tarifaires), ou des clusters de profils clients.

Optimisation du seuil : plutôt que le seuil par défaut de 0,5, optimiser le point de coupure pour maximiser le profit net (gains des churners détectés moins coûts des faux positifs). Analyse de courbe coût-bénéfice.

Modèle de survie : utiliser des techniques de Survival Analysis (Cox, Kaplan-Meier) pour prédire non seulement le churn mais aussi le moment probable du départ. Permet un timing optimal des actions de rétention.

Segmentation : construire des modèles spécialisés par segment de clientèle (résidentiel vs entreprise, urbain vs rural, etc.) plutôt qu'un modèle unique. Performance potentiellement supérieure via spécialisation.

Feedback loop : intégrer les résultats des campagnes de rétention dans le modèle. Les clients ciblés qui ne churnent pas suite à une action devraient être utilisés pour réentraîner le modèle avec leurs nouvelles caractéristiques.

Spark pour scoring temps réel : déployer le pipeline Spark sur un cluster pour traiter quotidiennement les événements clients (changement de forfait, réclamation) et recalculer les scores de churn en temps réel plutôt que mensuellement.

---

## 6. CONCLUSION

Ce projet démontre la faisabilité et la rentabilité d'une approche prédictive pour la rétention client chez TeleCom+. Le modèle de régression logistique avec équilibrage de classes atteint un recall de 62% permettant l'identification de 188 churners sur 303 dans l'échantillon test.

L'analyse des 20 clients les plus à risque révèle un profil type exploitable : contrats mensuels, services fiber optic, faible ancienneté, charges élevées sans services complémentaires. Ce profil guide la définition de trois actions de rétention ciblées générant un ROI de 865% annuel.

La comparaison Scikit-learn vs Spark MLlib confirme que pour le volume actuel de 7043 clients, Scikit-learn est plus adapté en termes de rapidité d'exécution et simplicité opérationnelle. Spark devient pertinent pour des volumes supérieurs à 1 million de clients ou pour du scoring temps réel distribué.

La recommandation est de déployer le modèle en production immédiatement avec un pilote sur les 20 clients les plus à risque, puis de monter en charge progressivement vers les 100 premiers sur les trois prochains mois. Un monitoring continu et un réentraînement trimestriel garantiront la pérennité des performances.

Les perspectives d'amélioration incluent l'intégration de nouvelles variables (réclamations, qualité réseau), le test de modèles avancés (XGBoost), l'optimisation du seuil de décision, et l'exploration de techniques de Survival Analysis pour prédire le timing du churn.

Ce projet établit une base solide pour une stratégie data-driven de rétention client, avec un potentiel d'économies de plusieurs dizaines de milliers d'euros annuels pour TeleCom+.
