# Conformité RGPD et Protection des Données

## 1. Introduction

Ce document détaille les mesures de conformité avec le Règlement Général sur la Protection des Données (RGPD) UE 2016/679 pour le projet de prédiction de churn client TeleCom+.

Le traitement des données personnelles dans ce projet doit respecter les principes fondamentaux du RGPD : licéité, loyauté, transparence, limitation des finalités, minimisation des données, exactitude, intégrité et confidentialité.

## 2. Données Personnelles Traitées

### Catégories de données

Le dataset contient les données personnelles suivantes relatives aux clients de TeleCom+ :

- Identifiants : customerID (supprimé avant utilisation)
- Données démographiques : gender, SeniorCitizen, Partner, Dependents
- Données contractuelles : Contract type, duration, payment method
- Historique d'utilisation : tenure, monthly charges, total charges
- Services utilisés : InternetService, phone service, backup, security, tech support
- Comportement churn : historique de départ (variable cible)

### Volume et portée

- Nombre de personnes concernées : 7043 clients
- Période historique : données anonymisées, pas de date de collecte spécifiée dans le dataset
- Sensibilité : données économiques et comportementales, non sensibles au sens de l'article 9 RGPD

## 3. Base Juridique du Traitement

### Finalités du traitement

Le traitement des données poursuit les fins suivantes :

1. Prédiction du churn pour identifer les clients à risque de départ
2. Développement d'actions de rétention ciblées
3. Optimisation des stratégies commerciales
4. Amélioration de la relation client et de la satisfaction

### Bases juridiques (article 6 RGPD)

Le traitement s'appuie sur :

- **Intérêt légitime** (article 6.1.f) : TeleCom+ a un intérêt légitime à réduire le churn et à conserver sa base de clients, à condition que cet intérêt ne soit pas contraires aux droits des personnes.
- **Consentement** (article 6.1.a) : idéalement obtenu lors de la signature du contrat ou lors de communications commerciales antérieures.

Le responsable de traitement (TeleCom+) doit vérifier que les clients ont consenti ou ont reçu une information claire sur l'utilisation prédictive de leurs données.

## 4. Principes RGPD Appliqués

### Minimisation des données

Le dataset utilisé contient uniquement les variables nécessaires à la prédiction du churn. Les identifiants de clients (customerID) ont été supprimés avant le traitement analytique pour éviter l'identification directe dans les modèles.

Recommandation : ne pas conserver les données clients au-delà de la période nécessaire pour les actions de rétention (recommandé : 6 mois après identification).

### Intégrité et confidentialité

- Les données sont stockées sur serveurs sécurisés avec chiffrement
- L'accès au dataset est limité aux personnes autorisées (équipes data science, customer success)
- Les données ne sont pas transmises à des tiers sans consentement exprès
- Les prédictions ne sont utilisées que à des fins de rétention, pas de discrimination

### Transparence

Les clients doivent être informés que :
- Leurs données sont utilisées pour prédire leur probabilité de départ
- Un modèle automatisé évalue leur profil de risque
- Ils peuvent solliciter des informations sur les critères pris en compte
- Ils ont accès aux droits RGPD (accès, rectification, suppression, portabilité)

### Limitation des finalités

Les données ne doivent pas être utilisées pour :
- La discrimination basée sur le genre, l'âge, la situation familiale
- La revente à des tiers sans accord explicite
- La profilage à des fins mauvaises ou contraires à l'intérêt du client
- La surveillance ou le contrôle comportemental intrusive

## 5. Droits des Personnes

### Droit d'accès (article 15)

Tout client peut demander à TeleCom+ l'accès à ses données personnelles traitées pour la prédiction de churn. TeleCom+ doit fournir une copie des informations dans les 30 jours.

### Droit de rectification (article 16)

Si les données sont inexactes ou incomplètes, le client peut demander leur correction. Par exemple : actualiser son contrat actuel, sa tenure réelle, ses services actuels.

### Droit à l'oubli / suppression (article 17)

Le client peut demander la suppression de ses données personnelles, sauf si TeleCom+ justifie un besoin plus long (ex : obligations légales, contentieux en cours).

### Droit à la portabilité (article 20)

Le client peut demander à récupérer ses données dans un format structuré et machine-lisible pour les transférer à un autre prestataire.

### Droit d'opposition (article 21)

Le client peut s'opposer au traitement de ses données à des fins de marketing ou profilage. TeleCom+ doit cesser toute action de rétention le concernant.

### Droits liés aux décisions automatisées (articles 13-14)

TeleCom+ doit informer les clients sur :
- L'existence d'une décision entièrement automatisée (le modèle de prédiction)
- La logique du modèle et les variables utilisées
- Les conséquences pour le client (ciblage pour rétention)
- Le droit de contester ou de demander une intervention humaine

## 6. Mesures de Sécurité Techniques et Organisationnelles

### Sécurité des données

- Chiffrement en transit (TLS 1.2+) et au repos (AES 256)
- Authentification multi-facteurs pour accès au dataset
- Audit des accès (logs d'accès conservés 12 mois min)
- Sauvegarde régulière avec test de restauration

### Gestion des accès

- Accès limité aux employés TeleCom+ autorisés (principe de moindre privilège)
- Contrats de confidentialité signés par tout personnel ayant accès aux données
- Formation RGPD annuelle obligatoire
- Révocation d'accès à la séparation

### Évaluation d'impact (AIPD)

Recommandation : réaliser une Analyse d'Impact relative à la Protection des Données (AIPD) article 35 RGPD pour ce projet car :
- Traitement automatisé à grande échelle
- Utilisation de données pour prédire le comportement
- Potentiel impact significatif sur les droits des clients

### Pseudonymisation

Les données utilisées dans le modèle prédictif pourraient être pseudonymisées en supprimant le customerID et en utilisant un identifiant hachéisé à la place. Cela réduit les risques d'identification directe.

## 7. Conformité Légale et Réglementaire

### CNIL (Commission Nationale de l'Informatique et des Libertés)

Pour les entreprises françaises traitant des données personnelles, la conformité CNIL est obligatoire :

- Enregistrement du traitement dans le registre CNIL interne
- Notification CNIL obligatoire si violation (dans les 72h)
- Respect du Code de la Consommation français

### Directives éventuelles

Si TeleCom+ opère dans l'UE, vérifier les régulations locales supplémentaires :
- GDPR Recital 47 sur marketing direct
- Directive 2002/58/CE sur l'utilisation de données pour ciblage commercial
- National Data Protection Authorities (DPA) requirements

## 8. Transparence et Communication

### Politique de Confidentialité

TeleCom+ doit publier une Politique de Confidentialité mentionnant :

- Identité et coordonnées du responsable de traitement
- Finalités et bases juridiques du traitement
- Catégories de données collectées
- Durée de conservation
- Droits des personnes
- Procédure de plainte

### Information aux clients

Lors de l'inscription ou renouvellement de contrat, les clients doivent être informés que :

"Vos données personnelles sont utilisées pour analyser votre profil de churn et développer des offres de rétention. Vous avez le droit d'accéder, rectifier ou supprimer vos données. Contactez privacy@telecom.fr pour exercer vos droits RGPD."

### Gestion des demandes RGPD

Processus recommandé :

1. Client envoie demande à privacy@telecom.fr
2. Vérification identité du demandeur
3. Localisation et compilation des données concernées
4. Réponse dans les 30 jours calendaires (extensible à 60-90 jours si complexe)
5. Documentation et archivage de la demande

## 9. Durée de Rétention des Données

### Données sources

Les données clients originales (03_DONNEES.csv) :
- Conservées 12 mois pour audit et conformité légale
- Suppression sécurisée après cette période
- Exception : données requises par obligations légales ou contentieux

### Scores de prédiction

Les prédictions de churn et probabilités associées :
- Utilisées pendant 6 mois pour actions de rétention
- Suppression après 12 mois
- Exception : données utilisées dans litige client

### Données de modèle

Paramètres et coefficients du modèle prédictif :
- Conservés tant que le modèle est en production
- Suppression lors du remplacement par nouveau modèle
- Archivage des versions anciennes pour traçabilité

## 10. Incidents et Violations

### Plan de réponse aux violations

En cas d'accès non autorisé, perte ou divulgation de données clients :

1. Notification immédiate à la direction IT et juridique
2. Évaluation du risque pour les personnes concernées
3. Notification CNIL dans les 72 heures si risque élevé
4. Communication aux clients concernés si nécessaire
5. Documentation complète de l'incident

### Contact responsable données

Responsable de la Protection des Données (Data Protection Officer) :

- Email : dpo@telecom.fr
- Téléphone : [coordonnées à remplir]
- Adresse postale : [adresse TeleCom+]

## 11. Conformité du Algorithme

### Transparence algorithmique

Le modèle de régression logistique utilisé pour la prédiction de churn doit satisfaire à :

- Explicabilité : les coefficients sont interprétables et documentés
- Non-discrimination : pas de variables prohibées (race, religion, convictions)
- Audit : le modèle et ses performances sont documentées
- Contestabilité : clients peuvent contester une décision de rétention

### Biais et équité

Application de class_weight="balanced" pour limiter les biais liés au déséquilibre données (86% non-churn, 14% churn). Cependant :

- Recommandation : tester régulièrement les performances sur sous-groupes démographiques
- Vérifier que le modèle ne discrimine pas les seniors, les femmes, ou autres groupes
- Ajuster les seuils de décision si biais détecté

## 12. Responsabilités des Parties

### Responsable de traitement (TeleCom+)

- Décide des finalités et moyens du traitement
- Met en place les mesures de sécurité
- Répond aux demandes droits RGPD
- Notifie les violations à la CNIL
- Tient un registre de conformité

### Sous-traitants (Data Scientists, Equipes Analytics)

- Traitent les données uniquement selon instructions TeleCom+
- Signent contrats de confidentialité
- Respectent les mesures de sécurité imposées
- N'utilisent pas les données à d'autres fins

### Clients / Personnes concernées

- Exercent leurs droits RGPD par les canaux prévus
- Mettent à jour leurs données inexactes
- Acceptent ou refusent le traitement de leurs données

## 13. Révision et Mise à Jour

Ce document doit être revu annuellement ou en cas de changement :

- Evolution de la réglementation RGPD
- Modification des finalités du traitement
- Intégration de nouvelles données
- Incident ou plainte utilisateur significative

Date dernière révision : Mars 2026
Prochaine révision : Mars 2027

---

Pour toute question concernant la conformité RGPD de ce projet, contacter le Responsable de la Protection des Données ou l'équipe juridique TeleCom+.
