---
title: Requête Hive avec les outils Hadoop pour Visual Studio | Microsoft Docs
description: Découvrez comment utiliser Hive avec Hadoop dans HDInsight à l’aide des outils Hadoop dans Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr

---
# Exécution de requêtes Hive à l’aide des outils HDInsight pour Visual Studio
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dans cet article, vous allez apprendre à utiliser les outils HDInsight pour Visual Studio afin d’envoyer des requêtes Hive vers un cluster HDInsight.

> [!NOTE]
> Ce document ne fournit pas une description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur le langage HiveQL utilisé dans cet exemple, consultez la rubrique [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md).
> 
> 

## <a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight, Linux ou Windows)
* Visual Studio (l'une des versions suivantes) :
  
    Visual Studio 2013 Community/Professional/Premium/Ultimate avec [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)
  
    Visual Studio 2015 (Community/Enterprise)
* Outils HDInsight pour Visual Studio. Consultez la page [Prise en main des outils Hadoop de Visual Studio pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) pour connaître les étapes d’installation et de configuration des outils.

## <a id="run"></a>Exécution de requêtes Hive à l’aide des outils HDInsight pour Visual Studio
1. Ouvrez **Visual Studio** et sélectionnez **Nouveau** > **Projet** > **HDInsight** > **Application Hive**. Fournissez un nom pour ce projet.
2. Ouvrez le fichier **Script.hql** créé avec ce projet et collez les instructions HiveQL suivantes :
   
        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    Ces instructions effectuent les opérations suivantes :
   
   * **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
   * **CREATE EXTERNAL TABLE** : crée une table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive (les données restent à leur emplacement d’origine).
     
     > [!NOTE]
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe (comme un processus de téléchargement de données automatisé) ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
     > 
     > La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.
     > 
     > 
   * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
   * **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (le répertoire exemple/données) et précise qu'elles sont stockées sous la forme de texte.
   * **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande renvoie la valeur **3**, car trois lignes contiennent cette valeur.
   * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** : indique à Hive de retourner uniquement des données provenant de fichiers se terminant par .log. Cela limite la recherche au fichier sample.log qui contient les données et l'empêche de renvoyer des données provenant d'autres fichiers d'exemple qui ne correspondent pas au schéma que nous avons défini.
3. Dans la barre d’outils, sélectionnez le **cluster HDInsight** que vous souhaitez utiliser pour cette requête, puis sélectionnez **Submit to WebHCat** (Envoyer à WebHCat) pour exécuter les instructions dans le cadre d’un travail Hive à l’aide de WebHCat. Vous pouvez également envoyer le travail en cliquant sur le bouton **Exécuter via HiveServer2** si HiveServer2 est disponible dans votre version de cluster. Le **résumé de tâche Hive** apparaît et affiche des informations sur la tâche en cours d’exécution. Utilisez le lien **Actualiser** pour actualiser les informations sur la tâche, jusqu’à ce que l’**état de la tâche** passe à **Terminé**.
4. Utilisez le lien **Sortie de la tâche** pour afficher la sortie de cette tâche. Il doit afficher `[ERROR] 3`, qui est la valeur retournée par l’instruction SELECT.
5. Vous pouvez également exécuter des requêtes Hive sans créer de projet. À l’aide de l’**Explorateur de serveurs**, développez **Azure** > **HDInsight**, cliquez avec le bouton droit sur votre serveur HDInsight, puis sélectionnez **Écrire une requête Hive**.
6. Dans le document **temp.hql** qui s’affiche, ajoutez les instructions HiveQL suivantes :
   
        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    Ces instructions effectuent les opérations suivantes :
   
   * **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive.
     
     > [!NOTE]
     > Contrairement aux tables **EXTERNES**, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
     > 
     > 
   * **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
   * **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.
7. Dans la barre d’outils, sélectionnez la liste déroulante pour **Envoyer**, afin d’exécuter la tâche. Utilisez l’**état de la tâche** afin de déterminer si la tâche est terminée.
8. Pour vérifier que le travail est terminé et a créé une nouvelle table, utilisez l’**Explorateur de serveurs** et développez **Azure** > **HDInsight** > votre cluster HDInsight > **Bases de données Hive** > et **Par défaut**. Vous devez voir les deux tables **errorLogs** et **log4jLogs**.

## <a id="summary"></a>Résumé
Comme vous pouvez le voir, les outils HDInsight pour Visual Studio fournissent un moyen facile d’exécuter des requêtes Hive sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur les outils de HDInsight pour Visual Studio :

* [Prise en main des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=AcomDC_0914_2016-->