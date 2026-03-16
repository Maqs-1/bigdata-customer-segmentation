# bigdata_customer_segmentation_Max_Guiriec
Université Paris 1 Panthéon-Sorbonne · DU Data Analytics
Projet de fin de formation — Big Data & Cloud Computing

Description
Ce projet implémente un pipeline complet de segmentation clientèle appliqué à un dataset e-commerce réel de 541 909 transactions.
L'objectif est de transformer des données transactionnelles brutes en segments clients actionnables via la méthode RFM (Recency, Frequency, Monetary), puis de construire un modèle prédictif capable d'identifier les clients à forte valeur.
Le projet adopte une approche duale :

Apache Spark / PySpark pour le traitement distribué Big Data (pipeline production-ready)
Pandas + Scikit-learn pour l'analyse locale, le clustering et l'évaluation complète du modèle supervisé


Résultats clés
IndicateurValeurTransactions analysées541 909Clients profilés4 338Segments identifiés4AUC-ROC (modèle optimisé)0.911F1-Score (cross-validation 5-fold)0.773Accuracy (jeu de test 30%)84 %

Segments identifiés
SegmentClientsRécence moy.Fréquence moy.Montant moy.Champions / VIP869 (20.4%)15 jours9.2 commandes3 525 EURFidèles Réguliers1 162 (27.3%)84 jours3.5 commandes1 418 EURClients à Potentiel842 (19.8%)22 jours1.9 commandes471 EURA Risque / Inactifs1 380 (32.5%)196 jours1.2 commandes302 EUR

Stack technique
Big Data

Apache Spark 4.1 / PySpark
Spark ML (BisectingKMeans, CrossValidator, VectorAssembler, StandardScaler)
Spark SQL / DataFrame API

Machine Learning

Scikit-learn (KMeans, RandomForestClassifier, GridSearchCV)
Evaluation : Silhouette Score, AUC-ROC, F1, Stratified K-Fold

Data Engineering

Pandas, NumPy
Feature Engineering RFM
Transformation log1p, gestion des outliers (percentile 99)

Visualisation

Matplotlib, Seaborn
Boxplots RFM, Scatter plots, Matrice de confusion, Courbe ROC


Structure du projet
bigdata-customer-segmentation/
│
├── README.md
│
├── notebook/
│   └── Segmentation_Ecommerce_BigData.ipynb   # Pipeline complet commenté
│
├── presentation/
│   └── BigData_Segmentation_Recruteur.pptx    # Slides de soutenance
│
└── data/
    └── .gitkeep                                # Dataset non versionné (voir section Dataset)

Pipeline méthodologique
Etape 1 — Chargement des données
Lecture du CSV (séparateur point-virgule, encodage UTF-8-sig) via Spark et Pandas.
541 909 lignes, 8 colonnes : InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country.
Etape 2 — Nettoyage

Correction du type UnitPrice (virgule vers point)
Suppression des lignes sans CustomerID (135 080 lignes)
Filtrage des quantités et prix négatifs ou nuls
Résultat : 397 884 lignes valides

Etape 3 — Feature Engineering RFM

Recency : nombre de jours depuis le dernier achat (date de référence : 2011-12-10)
Frequency : nombre de commandes distinctes (InvoiceNo unique)
Monetary : somme des dépenses totales (Quantity x UnitPrice)
Suppression des outliers au-delà du 99e percentile (Frequency, Monetary)

Etape 4 — Préparation pour le clustering

Transformation logarithmique log1p pour réduire l'asymétrie des distributions
Standardisation Z-score (StandardScaler, mean=0, std=1)

Etape 5 — Clustering non supervisé

Méthode du coude (Elbow) et score Silhouette testés pour k = 2 à 8
K-Means retenu avec k = 4 pour richesse business des segments
PySpark : BisectingKMeans avec sélection du k via ClusteringEvaluator

Etape 6 — Modèle supervisé

Label binaire : Monetary > 1 000 EUR = 1 (gros dépensier), sinon 0
Features : Recency, Frequency
RandomForestClassifier, split 70/30 stratifié
Optimisation GridSearchCV (n_estimators, max_depth, min_samples_leaf)
Cross-validation StratifiedKFold 5 folds
Meilleurs paramètres : max_depth=5, n_estimators=300


Installation et exécution
Prérequis
Python >= 3.9
Installer les dépendances
bashpip install pandas numpy scikit-learn matplotlib seaborn
Pour l'approche PySpark (optionnel)
bashpip install pyspark
Lancer le notebook
bashjupyter notebook notebook/Segmentation_Ecommerce_BigData.ipynb
Configurer le chemin du dataset
Dans la cellule 1.3 du notebook, modifier la variable :
pythonDATASET_PATH = "chemin/vers/Online_Retail_CSV.csv"

Dataset
Le dataset utilisé est le Online Retail Dataset disponible publiquement sur l'UCI Machine Learning Repository.
Lien : https://archive.ics.uci.edu/ml/datasets/online+retail
Le fichier CSV n'est pas inclus dans ce repository en raison de sa taille (46 Mo).
Télécharger le fichier, le placer dans le dossier data/ et mettre à jour DATASET_PATH dans le notebook.
Description des colonnes
ColonneTypeDescriptionInvoiceNostringIdentifiant de factureStockCodestringCode produitDescriptionstringLibellé produitQuantityintegerQuantité commandéeInvoiceDatestringDate et heure de la commandeUnitPricestringPrix unitaire (format FR : virgule décimale)CustomerIDfloatIdentifiant client (peut être nul)CountrystringPays du client

Recommandations par segment
Champions / VIP
Programme de fidélité premium, accès anticipé aux nouvelles collections, programme de parrainage.
Fidèles Réguliers
Stratégie upsell et cross-sell, programme de points cumulables, email automation post-achat.
Clients à Potentiel
Séquence de nurturing sur 30 jours, offre bundle premier achat, test A/B sur seuil de livraison offerte.
A Risque / Inactifs
Campagne Win-back avec code promotionnel, email de réactivation personnalisé, enquête satisfaction.

Auteur
Projet réalisé dans le cadre du DU Data Analytics
Université Paris 1 Panthéon-Sorbonne
Cours : Big Data & Cloud Computing
