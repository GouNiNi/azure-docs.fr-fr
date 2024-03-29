---
title: Génération de recommandations à l’aide de Mahout de HDInsight basé sur Linux | Microsoft Docs
description: Apprenez à utiliser la bibliothèque à apprentissage automatique Apache Mahout pour générer des recommandations de films avec HDInsight sous Linux (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: larryfr

---
# Génération de recommandations de films à l’aide d’Apache Mahout avec Hadoop sous Linux dans HDInsight
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Apprenez à utiliser la bibliothèque à apprentissage automatique [Apache Mahout](http://mahout.apache.org) avec Azure HDInsight pour générer des recommandations de films

Mahout est une bibliothèque à [apprentissage automatique][ml] pour Apache Hadoop. Mahout contient des algorithmes pour le traitement des données, tel que le filtrage, la classification et le clustering. Dans cet article, vous utiliserez un moteur de recommandation pour générer des recommandations en fonction des films vus par vos amis.

> [!NOTE]
> Les étapes décrites dans ce document nécessitent un cluster Hadoop sous Linux dans HDInsight. Pour plus d’informations sur l’utilisation de Mahout avec un cluster Windows, consultez la page [Génération de recommandations de films à l’aide d’Apache Mahout avec HDInsight](hdinsight-mahout.md)
> 
> 

## Composants requis
* Un cluster Hadoop Linux sur HDInsight. Pour des informations sur la création de ce dernier, consultez la page [Prise en main de Hadoop sous Linux dans HDInsight][getstarted].

## Contrôle de version Mahout
Pour plus d'informations sur la version de Mahout incluse dans votre cluster HDInsight, consultez [Versions de HDInsight et composants Hadoop](hdinsight-component-versioning.md).

> [!WARNING]
> S’il est possible de télécharger une version différente de Mahout dans le cluster HDInsight, seuls les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
> 
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).
> 
> 

## <a name="recommendations"></a>Présentation des recommandations
L’une des fonctions fournies par Mahout est un moteur de recommandation. Ce moteur accepte les données au format `userID`, `itemId` et `prefValue` (les préférences utilisateur pour l’élément). Mahout peut alors effectuer une analyse des co-occurrences afin de déterminer que *les utilisateurs ayant une préférence pour un élément ont également une préférence pour ces autres éléments*. Mahout détermine ensuite des utilisateurs avec des préférences similaires, qui peuvent alors être utilisées pour faire des recommandations.

Voici un exemple très simple de cette méthode pour les films :

* **Co-occurrence** : Jean, Alice et Robert ont tous aimé *La Guerre des étoiles*, *L’Empire contre-attaque* et *Le Retour du Jedi*. Mahout détermine que les utilisateurs qui aiment l’un de ces films aiment également les deux autres.
* **Co-occurrence** : Robert et Alice ont aussi aimé *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*. Mahout détermine que les utilisateurs qui ont aimé les trois films précédents aiment également ces trois films.
* **Recommandation par similarité** : Puisque Jean a aimé les trois premiers films, Mahout regarde les films que d’autres personnes ayant les mêmes goûts que lui ont aimés aussi, mais que Jean n’a pas encore vus (aimés/notés). Dans ce cas, Mahout recommande *La Menace fantôme*, *L’attaque des clones* et *La revanche des Sith*.

### Vue d’ensemble des données
De façon pratique, [GroupLens Research][movielens] fournit des données d’évaluation de films dans un format compatible avec Mahout. Ces données sont disponibles sur le stockage par défaut de votre cluster à `/HdiSamples/HdiSamples/MahoutMovieData`.

Il existe deux fichiers, `moviedb.txt` (informations sur les films,) et `user-ratings.txt`. Le fichier user-ratings.txt de l'utilisateur est utilisé pendant l'analyse, tandis que le fichier moviedb.txt est utilisé pour fournir des informations texte conviviales lors de l'affichage des résultats de l'analyse.

Les données contenues dans le fichier user-ratings.txt respectent la structure suivante : `userID`, `movieID`, `userRating` et `timestamp`, ce qui nous indique la note attribuée par chaque utilisateur à un film. Voici un exemple de ces données :

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## Exécution de l'analyse
Utilisez la commande suivante pour exécuter la tâche de recommandation :

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [!NOTE]
> L’exécution de la tâche peut durer quelques minutes et entraîner l’exécution de plusieurs tâches MapReduce.
> 
> 

## Affichage du résultat
1. Une fois la tâche terminée, utilisez la commande suivante pour afficher le résultat généré :
   
        hdfs dfs -text /example/data/mahoutout/part-r-00000
   
    La sortie s’affiche comme suit :
   
        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
   
    La première colonne est `userID`. Les valeurs contenues entre « [ » et « ] » sont `movieId`:`recommendationScore`.
2. Vous pouvez utiliser la sortie ainsi que le fichier moviedb.txt pour afficher des informations plus conviviales. Nous devons tout d'abord copier les fichiers localement en utilisant les commandes suivantes :
   
        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
   
    Cette commande copie les données de sortie dans un fichier nommé **recommendations.txt** dans le répertoire actif, avec les fichiers de données de film.
3. Utilisez la commande suivante pour créer un script Python afin de rechercher les noms de film présents dans les données de sortie des recommandations :
   
        nano show_recommendations.py
   
    Lorsque l’éditeur s’ouvre, utilisez les données suivantes comme contenu du fichier :
   
        #!/usr/bin/env python
   
        import sys
   
        if len(sys.argv) != 5:
                print "Arguments: userId userDataFilename movieFilename recommendationFilename"
                sys.exit(1)
   
        userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]
   
        print "Reading Movies Descriptions"
        movieFile = open(movieFilename)
        movieById = {}
        for line in movieFile:
                tokens = line.split("|")
                movieById[tokens[0]] = tokens[1:]
        movieFile.close()
   
        print "Reading Rated Movies"
        userDataFile = open(userDataFilename)
        ratedMovieIds = []
        for line in userDataFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        ratedMovieIds.append((tokens[1],tokens[2]))
        userDataFile.close()
   
        print "Reading Recommendations"
        recommendationFile = open(recommendationFilename)
        recommendations = []
        for line in recommendationFile:
                tokens = line.split("\t")
                if tokens[0] == userId:
                        movieIdAndScores = tokens[1].strip("[]\n").split(",")
                        recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                        break
        recommendationFile.close()
   
        print "Rated Movies"
        print "------------------------"
        for movieId, rating in ratedMovieIds:
                print "%s, rating=%s" % (movieById[movieId][0], rating)
        print "------------------------"
   
        print "Recommended Movies"
        print "------------------------"
        for movieId, score in recommendations:
                print "%s, score=%s" % (movieById[movieId][0], score)
        print "------------------------"
   
    Appuyez sur **Ctrl-X**, **O**, et enfin **Entrée** pour enregistrer les données.
4. Pour rendre le fichier exécutable, utilisez la commande suivante :
   
        chmod +x show_recommendations.py
5. Exécutez le script Python. La procédure suivante suppose que vous avez ouvert le répertoire dans lequel tous les fichiers ont été téléchargés :
   
        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
   
    Il examinera les recommandations générées pour l’utilisateur ID 4.
   
   * Le fichier **user-ratings.txt** est utilisé pour récupérer les films évalués par l’utilisateur
   * Le fichier **moviedb.txt** est utilisé pour récupérer les noms des films
   * Le fichier **recommendations.txt** est utilisé pour récupérer les recommandations de films pour cet utilisateur
     
     Le résultat de cette commande doit ressembler à ceci :
     
       Reading Movies Descriptions
       Reading Rated Movies
       Reading Recommendations
     
     ##   Rated Movies
       Mimic (1997), rating=3
       Ulee's Gold (1997), rating=5
       Incognito (1997), rating=5
       One Flew Over the Cuckoo's Nest (1975), rating=4
       Event Horizon (1997), rating=4
       Client, The (1994), rating=3
       Liar Liar (1997), rating=5
       Scream (1996), rating=4
       Star Wars (1977), rating=5
       Wedding Singer, The (1998), rating=5
       Starship Troopers (1997), rating=4
       Air Force One (1997), rating=5
       Conspiracy Theory (1997), rating=3
       Contact (1997), rating=5
       Indiana Jones and the Last Crusade (1989), rating=3
       Desperate Measures (1998), rating=5
       Seven (Se7en) (1995), rating=4
       Cop Land (1997), rating=5
       Lost Highway (1997), rating=5
       Assignment, The (1997), rating=5
       Blues Brothers 2000 (1998), rating=5
       Spawn (1997), rating=2
       Wonderland (1997), rating=5
     
     ##   In & Out (1997), rating=5
     ##   Recommended Movies
       Seven Years in Tibet (1997), score=5.0
       Indiana Jones and the Last Crusade (1989), score=5.0
       Jaws (1975), score=5.0
       Sense and Sensibility (1995), score=5.0
       Independence Day (ID4) (1996), score=5.0
       My Best Friend's Wedding (1997), score=5.0
       Jerry Maguire (1996), score=5.0
       Scream 2 (1997), score=5.0
       Time to Kill, A (1996), score=5.0
     
     ##   Rock, The (1996), score=5.0

## Suppression des données temporaires
Les tâches Mahout ne suppriment pas les données temporaires créées lors du traitement de la tâche. C’est la raison pour laquelle le paramètre `--tempDir` est spécifié dans la tâche donnée en exemple : il isole les fichiers temporaires dans un chemin spécifique afin de pouvoir les effacer facilement. Pour supprimer les fichiers temporaires, utilisez la commande suivante :

    hdfs dfs -rm -f -r /temp/mahouttemp

> [!WARNING]
> Si vous souhaitez réexécuter la commande, vous devez également supprimer le répertoire de sortie. Utilisez la commande suivante pour supprimer ce répertoire :
> 
> ```hdfs dfs -rm -f -r /example/data/mahoutout```
> 
> 

## Étapes suivantes
Maintenant que vous avez appris à utiliser Mahout, découvrez d’autres façons d’utiliser les données dans HDInsight :

* [Hive avec HDInsight](hdinsight-use-hive.md)
* [Pig avec HDInsight](hdinsight-use-pig.md)
* [MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools


<!---HONumber=AcomDC_0914_2016-->