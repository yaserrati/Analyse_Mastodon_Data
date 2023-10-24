# Brief : Analyse de l'Engagement des Utilisateurs sur le Réseau Social Mastodone

## Introduction

Le présent rapport détaille le projet visant à analyser l'engagement des utilisateurs sur le Réseau Social Mastodon en utilisant des technologies telles que MapReduce, HBase et Apache Airflow. Ce projet a été réalisé dans le but de collecter, traiter, stocker et analyser les données provenant de la plateforme Mastodon. Les informations extraites ont permis de mieux comprendre les comportements des utilisateurs, la popularité du contenu, et bien d'autres aspects essentiels.

## Contexte du Projet

Le développement de ce projet est né du besoin de répondre à des défis tels que la collecte de données à partir de la plateforme Mastodon, le traitement des données via MapReduce, le stockage dans HBase, et l'orchestration du workflow avec Apache Airflow. Il s'agissait également de s'assurer que les données collectées étaient conformes au Règlement Général sur la Protection des Données (RGPD).

### Réalisation du Projet:

Alors j'ai començé mon projet par:
#### Hadoop Installation
#### Hbase Installation
#### Airflow Installation 

#### Collecte de Données:
J'ai cree un script pour collecter les donnees de social media mastodon par le script scrapper.py 

- Collecte de données brutes à partir de Mastodon en utilisant l'API de la plateforme.
- Stockage des données brutes dans le système de fichiers distribué HDFS.
- Utilisation des bibliothèques datetime, json, Mastodon, InsecureClient, requests pour la collecte de données.


### Traitement MapReduce
j'ai cree mapper.py et reducer.py 

- Le Mapper a été utilisé pour traiter les données d'entrée et générer des paires clé-valeur en fonction des métriques à analyser.
- Le Reducer a agrégé les paires clé-valeur pour obtenir des métriques telles que le nombre d'abonnés des utilisateurs, les taux d'engagement, le nombre d'URL, d'emojis, etc.

### Exécution du Travail MapReduce

- L'API de streaming Hadoop a été utilisée pour exécuter la tâche MapReduce.
- Les scripts du Mapper et du Reducer ont été fournis comme entrées dans le travail.
- La progression de la tâche a été surveillée via l'interface utilisateur Web Hadoop.

Alors pour executé mapreducer on a besoin de faire un streaming, avec cette commande:
command : hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar -files mapper.py,reducer.py -mapper "python3 mapper.py" -reducer "python3 reducer.py" -input /Mostodon/Raw/mastodon_data_2023-10-23.json -output /output/ReducerResult

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




# ----------------------------------------------------------------------------------------------------------------------------------------------------------


voila quelque modification qui j'ai fait pour les configiration:

command : sudo gedit /usr/local/hadoop/etc/hadoop/mapred-site.xml
````
<configuration>
 	<property>
 		<name>mapreduce.framework.name</name>
 		<value>yarn</value>
 	</property>
 	<property>
 		<name>yarn.app.mapreduce.am.env</name>
 		<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
 	</property>
 	<property>
 		<name>mapreduce.map.env</name>
 		<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
 	</property>
 	<property>
 		<name>mapreduce.reduce.env</name>
 		<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
 	</property>
</configuration>
````

command : hadoop jar $HADOOP_HOME/share/hadoop/tools/lib/hadoop-streaming-*.jar -files mapper.py,reducer.py -mapper "python3 mapper.py" -reducer "python3 reducer.py" -input /Mostodon/Raw/mastodon_data_2023-10-23.json -output /output/ReducerResult

create hbase_script.py 

````
#!/usr/bin/env python3

import happybase
import json
from hdfs import InsecureClient

# HBase and HDFS connection parameters
hbase_host = 'localhost'  # HBase host
hbase_port = 9090         # HBase port

hdfs_url = 'http://localhost:9870'
hdfs_client = InsecureClient(hdfs_url, user='hadoop')

#  The input data 
hdfs_file = '/output/ReducerResult43/part-00000'

with hdfs_client.read(hdfs_file) as hdfs_file:
    hdfs_file_contents = hdfs_file.read() # 

try:
    # Initialize a connection to HBase
    connection = happybase.Connection(host=hbase_host, port=hbase_port)
    print("Connected to HBase")

    # Open the 'User' table
    user_table_name = 'User'
    user_table = connection.table(user_table_name) # set a connection to the hbase table 
    print(f"Connected to table: {user_table_name}")
    
    # Read and process User data 
    try:
        data = json.loads(hdfs_file_contents)
        userFollowerCount = data.get('UserFollowerCount')
        userEngagementRate = data.get('UserEngagementRate')

        if userFollowerCount and userEngagementRate:
            for user_id, follower_count in userFollowerCount.items():
                if user_id in userEngagementRate:
                    engagement_rate = userEngagementRate[user_id]

                    user_table.put(
                        user_id.encode('utf-8'),
                        {
                            'UserInfo:FollowerCount': str(follower_count).encode('utf-8'),
                            'UserInfo:EngagementRate': str(engagement_rate).encode('utf-8'),
                        }
                    )
        else:
            print("User Data is not enough in the JSON")      
    except json.JSONDecodeError as e:
    
        print(f"JSON parsing error in user table:  {e}")
    
    # Open the table for Toots language data
    toots_language_table_name = 'Language'
    toots_language_table = connection.table(toots_language_table_name)
    print(f"Connected to table: {toots_language_table_name}")
    
    # Read and process Toots language data from HDFS
    try:
        data = json.loads(hdfs_file_contents)
        toots_language = data.get('Tootslanguage')

        if toots_language:
            for language, count in toots_language.items():
                toots_language_table.put(
                    language.encode('utf-8'),
                    {'Language:Count': str(count).encode('utf-8')}
                )
        else:
            print("No 'Tootslanguage' data in the JSON")
    except json.JSONDecodeError as e:
        print(f"JSON parsing error in language table:  {e}")


    # Open the table for URLShare data
    urlshare_table_name = 'URLShare'
    urlshare_table = connection.table(urlshare_table_name)
    print(f"Connected to table: {urlshare_table_name}")

    # Read and process URLShare data from HDFS
    try:
        data = json.loads(hdfs_file_contents)
        url_content = data.get('Url')

        if url_content:
            for url, share_count in url_content.items():
                urlshare_table.put(
                    url.encode('utf-8'),
                    {
                        'URLInfo:Url': str(url).encode('utf-8'),
                        'ShareCount:Count': str(share_count).encode('utf-8')
                    }
                )
        else:
            print("No 'Url' data in the JSON")
    except json.JSONDecodeError as e:
        print(f"JSON parsing error in url table :  {e}")
        
    # Open the table for tag data
    Mention_table_name = 'Mentions'
    Mention_table = connection.table(Mention_table_name)
    print(f"Connected to table: {Mention_table_name}")
        
    # Read and process Mention data from HDFS
    try:
        data = json.loads(hdfs_file_contents)
        mention_content = data.get('Mention')

        if mention_content:
            for mention, mention_count in mention_content.items():
                Mention_table.put(
                    mention.encode('utf-8'),
                    {
                        'MentionsInfo:Count': str(mention_count).encode('utf-8'),
                    }
                )
        else:
            print("No 'Mention' data in the JSON")     
    except json.JSONDecodeError as e:
        print(f"JSON parsing error in mentions table :  {e}")

    # Open the table for tag data
    Tag_table_name = 'Tags'
    Tag_table = connection.table(Tag_table_name)
    print(f"Connected to table: {Tag_table_name}")
    
    #Read and process URLShare data from HDFS
    try:
        data = json.loads(hdfs_file_contents)
        Tag_content = data.get('Tag')

        if Tag_content:
            for tag, tag_count in Tag_content.items():
                Tag_table.put(
                    tag.encode('utf-8'),
                    {
                        'TagsInfo:Count': str(tag_count).encode('utf-8'),
                    }
                )
        else:
            print("No 'tag' data in the JSON")  
    except json.JSONDecodeError as e:
        print(f"JSON parsing error in tag tables :  {e}")

    # Close the connection
    connection.close()
    print("Connection closed")
    
except Exception as ex:
    print(f"An error occurred: {ex}")
````
command : /usr/local/Hbase/bin/hbase-daemon.sh start thrift

command : /usr/local/Hbase/bin/hbase shell

command : 

command : run hbase_script.py

command : sudo gedit hbase-site.xml
````
<configuration>
    <property>
          <name>hbase.rootdir</name>
          <!-- <value>file:/home/hadoop/Hbase/HFiles</value> -->
          <value>hdfs://0.0.0.0:9000/hbase</value> 
    </property>

    <property>
          <name>hbase.wal.provider</name>
          <value>filesystem</value>
    </property>
    <property>
          <name>hbase.regionserver.thrift.http</name>
          <value>false</value>
    </property>
</configuration>
````

command : sudo gedit hbase-site.xml


````
<configuration>
    <property>
          <name>hbase.rootdir</name>
          <!-- <value>file:/home/hadoop/Hbase/HFiles</value> -->
          <value>hdfs://0.0.0.0:9000/hbase</value> 
    </property>

    <property>
          <name>hbase.wal.provider</name>
          <value>filesystem</value>
    </property>
    <property>
          <name>hbase.regionserver.thrift.http</name>
          <value>false</value>
    </property>
</configuration>
````

command : /usr/local/Hbase/bin/hbase-daemon.sh start thrift
command : /usr/local/Hbase/bin/hbase shell

create 'Language', 'Language'
create 'User', 'UserInfo'
create 'URLShare', 'URLInfo', 'ShareCount'
create 'Tags', 'TagsInfo'
create 'Mentions', 'MentionsInfo'


command : python3 hbase_script.py

output: 
```` 
hadoop@yaserrati:~/mastodon$ python3 hbase_script.py 
Connected to HBase
Connected to table: User
Connected to table: Language
Connected to table: URLShare
Connected to table: Mentions
Connected to table: Tags
Connection closed
````

