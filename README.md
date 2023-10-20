# Brief : Analyse de l'Engagement des Utilisateurs sur le Réseau Social Mastodone

## Introduction

Le présent rapport détaille le projet visant à analyser l'engagement des utilisateurs sur le Réseau Social Mastodon en utilisant des technologies telles que MapReduce, HBase et Apache Airflow. Ce projet a été réalisé dans le but de collecter, traiter, stocker et analyser les données provenant de la plateforme Mastodon. Les informations extraites ont permis de mieux comprendre les comportements des utilisateurs, la popularité du contenu, et bien d'autres aspects essentiels.

## Contexte du Projet

Le développement de ce projet est né du besoin de répondre à des défis tels que la collecte de données à partir de la plateforme Mastodon, le traitement des données via MapReduce, le stockage dans HBase, et l'orchestration du workflow avec Apache Airflow. Il s'agissait également de s'assurer que les données collectées étaient conformes au Règlement Général sur la Protection des Données (RGPD).

### Réalisation du Projet

Alors j'ai començé mon projet par:
#### Hadoop Installation
#### Hbase Installation
#### Airflow Installation 

#### Collecte de Données

- Collecte de données brutes à partir de Mastodon en utilisant l'API de la plateforme.
- Stockage des données brutes dans le système de fichiers distribué HDFS.
- Utilisation des bibliothèques datetime, json, Mastodon, InsecureClient, requests pour la collecte de données.

### Traitement MapReduce

- Le Mapper a été utilisé pour traiter les données d'entrée et générer des paires clé-valeur en fonction des métriques à analyser.
- Le Reducer a agrégé les paires clé-valeur pour obtenir des métriques telles que le nombre d'abonnés des utilisateurs, les taux d'engagement, le nombre d'URL, d'emojis, etc.

### Exécution du Travail MapReduce

- L'API de streaming Hadoop a été utilisée pour exécuter la tâche MapReduce.
- Les scripts du Mapper et du Reducer ont été fournis comme entrées dans le travail.
- La progression de la tâche a été surveillée via l'interface utilisateur Web Hadoop.

### Stockage des Résultats dans HBase

- Un schéma HBase a été conçu pour stocker les informations, en suivant les meilleures pratiques de conception.
- Les tables HBase nécessaires ont été créées, et les données ont été insérées à partir du Reducer à l'aide d'un client HBase en Python.

### Orchestration avec Apache Airflow

- Un DAG a été créé pour orchestrer le workflow, avec des tâches dédiées pour exécuter le travail MapReduce et stocker les résultats dans HBase.
- La progression des exécutions du DAG a été surveillée, et des mécanismes de gestion des erreurs ont été mis en place.

### Analyse des Résultats

Des requêtes ont été rédigées pour l'analyse des utilisateurs, du contenu, de la langue et de la région, de l'engagement des médias, des balises et des mentions.

### Exécution du Flux de Travail

Le DAG a été activé dans l'interface utilisateur Web d'Airflow, permettant de surveiller la progression des exécutions et de vérifier les journaux.

### Optimisation et Surveillance

Les scripts MapReduce ont été optimisés pour de meilleures performances, et HBase a été surveillé pour détecter d'éventuels problèmes de stockage.

### Mise à Jour du Paramétrage des Droits

Les tokens API ont été mis à jour en fonction des changements organisationnels, et la documentation des accès et des règles de partage a été tenue à jour.

### Programmation des Évolutions et des Nouvelles Procédures

Les DAGs ont été programmés pour s'exécuter à des intervalles appropriés pour le rafraîchissement des données.

### Mise à Jour du Registre des Traitements de Données Personnelles

Toutes les données personnelles ingérées de Mastodon ont été documentées, et des mesures ont été prises pour garantir la conformité avec le RGPD.

## Conclusion

Ce projet a permis de mettre en place un pipeline automatisé pour analyser les données des réseaux sociaux de manière efficace. Les informations obtenues ont été essentielles pour comprendre l'engagement des utilisateurs, la popularité du contenu, et d'autres aspects importants. En continuant à optimiser et à surveiller ce système, nous nous assurerons qu'il reste performant et conforme aux réglementations.
