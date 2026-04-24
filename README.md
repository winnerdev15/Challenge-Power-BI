# Challenge Power BI – Chaîne d’approvisionnement Cacao (Côte d’Ivoire)

Analyse de la chaîne d’approvisionnement du cacao en Côte d’Ivoire à partir d’un dataset de ~150 000 lignes (1 table de faits + 9 dimensions), avec nettoyage avancé, modélisation en étoile et création d’un dashboard interactif dans Power BI.


## 1. Contexte du challenge

Ce projet a été réalisé dans le cadre du Challenge Power BI – Chaîne d’approvisionnement Cacao (Côte d’Ivoire), organisé par Trésor Kaleto ADOSSI sur LinkedIn.  
L’objectif du challenge est de construire un modèle Power BI propre et un rapport clair permettant de suivre la chaîne cacao :

> Ferme → Coopérative → Site → Transport → Port → Export

À partir du dataset fourni, le participant doit :
- Nettoyer, transformer et modéliser les données (Power Query + schéma en étoile).
- Construire une table de dates dédiée et établir correctement les relations.
- Identifier et traiter plusieurs problèmes de qualité de données.
- Produire un rapport Power BI lisible, pertinent et visuellement attractif.

## 2. Objectifs métiers

Ce projet vise à répondre aux questions métiers suivantes :

- Comment évoluent les volumes, montants et prix/kg par campagne, région, coopérative et variété ?
- Quels segments (coopératives, régions, grades) sont les plus performants en termes de volumes, prix/kg et export ?
- Où se concentrent les anomalies de qualité de données pouvant fausser les indicateurs (doublons, montants incohérents, clés manquantes, etc.) ?
- Comment améliorer la visibilité sur la chaîne complète, depuis la ferme jusqu’au port et à l’export ?

## 3. Données

Le dataset fourni dans le cadre du challenge comprend :

- Environ 150 000 lignes.
- 1 table de faits et 9 tables de dimensions.
- Format : fichiers CSV regroupés dans un pack fourni par l’organisateur.

La granularité principale des données est le mouvement de cacao le long de la chaîne d’approvisionnement.

Les tables incluent notamment :
- `fact_Mouvements` : table de faits des mouvements de cacao.
- Dimensions : producteurs, coopératives, sites, variétés, grades, transport, ports, devises, etc.


## 4. Préparation & qualité des données

Une attention particulière a été portée à la qualité des données, conformément au cahier des charges du challenge.  
Les principales anomalies identifiées et traitées sont les suivantes :

- Doublons
  - Détection et suppression ou consolidation des doublons dans `fact_Mouvements` et certaines dimensions afin d’éviter de surévaluer volumes et montants.

- Devises incohérentes  
  - Harmonisation des devises (XOF, EUR, USD, etc.).  
  - Normalisation des libellés et conversion des montants en une devise de référence.

- Valeurs négatives (poids / sacs)
  - Identification des enregistrements avec poids ou nombres de sacs négatifs.
  - Création de colonnes “corrigées” en parallèle des valeurs brutes, afin de préserver la traçabilité tout en sécurisant les agrégations.

- Montants incohérents
  - Comparaison entre montant “déclaré” et montant “recalculé” à partir du poids et du prix/kg.
  - Mise en place de flags d’anomalies lorsque l’écart dépasse un certain seuil et usage du montant recalculé dans les agrégations critiques.

- Clés étrangères manquantes ou hors plage
  - Vérification des relations (par ex. `Port_ID` dans `fact_Mouvements` vs `dim_Port`).
  - Identification des clés sans correspondance ou hors plage et reclassement approprié (ex. création d’une catégorie “Non export via port” dans le canal cacao).

- Dates en formats mixtes
  - Uniformisation des formats de dates (conversion texte → date, harmonisation des formats).
  - Construction d’une table de dates propre et reliée à la table de faits.

- Incohérences d’accents, de casse et de libellés
  - Nettoyage des libellés (trim, casse, accents, orthographe) pour améliorer la cohérence des regroupements (régions, coopératives, sites, producteurs).

- Duplicats de producteurs et autres entités 
  - Identification des producteurs dupliqués ou quasi-duplicats.
  - Mise en place de règles de consolidation dans les dimensions.

Les transformations ont été réalisées principalement dans Power Query, avec quelques ajustements via DAX dans le modèle.

## 5. Modélisation (schéma en étoile)

Le modèle de données de ce projet adopte un **schéma en étoile**, afin de respecter les bonnes pratiques de modélisation Power BI.

- Table de faits
  - `fact_Mouvements` : enregistre les mouvements de cacao, avec les clés de liaison vers les dimensions (producteur, coopérative, site, port, etc.).

- Tables de dimensions
  - `dim_Producteur`  
  - `dim_Cooperative`  
  - `dim_Site`  
  - `dim_Variete`  
  - `dim_Grade`  
  - `dim_Transport`  
  - `dim_Port`  
  - `dim_Devise`  
  - `dim_Date` (table de dates construite spécifiquement pour le modèle)

Les relations ont été configurées pour :
- Garantir un filtrage correct depuis les dimensions vers la table de faits.
- Permettre l’utilisation fluide des fonctions de **time intelligence** (analyses par campagne, année, mois, etc.).

## 6. Table de dates

Une table de dates dédiée a été construite afin de :

- Couvrir l’ensemble de la période contenue dans le dataset.
- Proposer des colonnes dérivées (année, mois, trimestre, campagne, etc.).
- Supporter les mesures DAX liées aux périodes (YTD, comparaisons entre campagnes, etc.).

Cette table est reliée à la table `fact_Mouvements` via le champ de date approprié (par exemple date du mouvement ou date d’export, selon le besoin métier).

## 7. Dashboard & visualisations

Le rapport Power BI comprend plusieurs vues permettant une lecture fluide de la chaîne cacao, avec des filtres dynamiques (période, région, coopérative, variété, grade, etc.).

### 7.1 Vue globale

- Evolution des volumes et montants par campagne, région et coopérative.
- Prix moyen par kg, répartition par variétés et grades.
- Indicateurs de haut niveau sur l’export (volumes exportés vs non exportés).

### 7.2 Qualité des données

- Visualisation des différents types d’anomalies (doublons, montants incohérents, clés manquantes, valeurs négatives…).
- Indicateurs de taux de lignes impactées.
- Impact de la qualité de données sur les métriques clés (volumes, montants, prix/kg).

### 7.3 Performance des coopératives et sites

- Classement des coopératives et sites par volumes, CA, prix/kg.
- Analyse des performances par régions et campagnes.
- Identification des acteurs les plus réguliers et des zones à potentiel d’amélioration.

### 7.4 Analyse par variété, grade et canal

- Répartition des volumes par variété et grade.
- Analyse par canal (export via port, non export via port, etc.).
- Contribution de chaque segment aux résultats globaux.



## 8. Mesures DAX principales

Parmi les mesures DAX implémentées dans le modèle, on retrouve notamment :

- Volumes & poids
  - Poids total (kg)
  - Nombre total de sacs

- Indicateurs financiers
  - Chiffre d’affaires total
  - Chiffre d’affaires net (après prise en compte des pertes ou corrections)
  - Prix moyen par kg

- Export & chaîne d’approvisionnement
  - Volume exporté
  - Taux d’export
  - Répartition des volumes par port et par canal

- Qualité des données
  - Nombre / pourcentage de lignes avec anomalies
  - Indicateurs dédiés par type d’anomalie (montants incohérents, valeurs négatives, clés manquantes, etc.)

(Les noms exacts des mesures sont décrits dans le fichier `.pbix`.)


## 9. Reproduire le projet

### 9.1 Prérequis

- **Power BI Desktop** (version récente recommandée).

### 9.2 Étapes

1. Cloner ce dépôt :
   ```bash
   git clone https://github.com/TON-UTILISATEUR/project-cacao-powerbi.git
   cd project-cacao-powerbi
   ```

2. Télécharger le pack de données du challenge (CSV + README) depuis le lien fourni par l’organisateur et placer les fichiers dans le dossier `data/`.

3. Ouvrir le fichier Power BI situé dans `models/`, par exemple :
   ```text
   models/challenge_cacao_ci.pbix
   ```

4. Vérifier, dans Power Query, que les chemins d’accès aux fichiers CSV pointent bien vers le dossier `data/` de votre environnement local. Ajuster si nécessaire.

5. Actualiser le modèle (`Actualiser`) pour rejouer l’ensemble des transformations et mettre à jour les visualisations.


## 10. Auteurs & challenge

- Auteur : ETEDJI Ahouangba Othniel  
  Étudiant en Master Management de la Transformation Digitale, passionné par l’IA, le Big Data et la finance digitale.

- Challenge: Challenge Power BI – Chaîne d’approvisionnement Cacao (Côte d’Ivoire).  

- Organisateur : Trésor Kaleto ADOSSI  


## 11. Remerciements

Merci à l’organisateur du challenge pour la mise à disposition du dataset et du cadre d’apprentissage, ainsi qu’à la communauté Power BI pour les ressources et bonnes pratiques de modélisation et de visualisation.