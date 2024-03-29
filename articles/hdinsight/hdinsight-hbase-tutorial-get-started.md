---
title: 'Didacticiel HBase : prise en main de HBase dans Hadoop | Microsoft Docs'
description: Suivez ce didacticiel HBase pour apprendre à utiliser Apache HBase avec Hadoop dans HDInsight. Créez des tables à partir de l’interpréteur de commandes HBase et interrogez-les à l’aide de Hive.
keywords: apache hbase,hbase,shell hbase,didacticiel hbase
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jgao

---
# Didacticiel HBase : prise en main de HBase avec Hadoop dans HDInsight Windows
[!INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Découvrez comment créer un cluster HBase dans HDInsight, créer des tables HBase et les interroger à l’aide d’Apache Hive. Pour obtenir des informations générales sur HBase, consultez la page [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

Les informations de ce document sont spécifiques aux clusters HDInsight sous Windows. Pour plus d’informations sur les clusters Windows, utilisez le sélecteur d’onglets en haut de la page pour basculer.

> [!NOTE]
> HBase (version 0.98.0) sous HDInsight Windows peut uniquement être utilisé avec des clusters HDInsight 3.1 (basé sur Apache Hadoop et YARN 2.4.0). Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions]
> 
> 

### Avant de commencer
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer ce didacticiel sur HBase, vous devez disposer des éléments suivants :

* **Un abonnement Microsoft Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Une station de travail** avec Visual Studio 2013 ou version ultérieure : pour obtenir des instructions, consultez [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Créer un cluster HBase
[!INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Pour créer un cluster HBase à l’aide du portail Azure**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **Nouveau** ou sur le symbole **+** en haut à gauche, puis cliquez sur **Données + analyse**, **HDInsight**.
3. Saisissez les valeurs suivantes :
   
   * **Nom du cluster** : entrez un nom permettant d’identifier ce cluster.
   * **Type de cluster**: sélectionnez **HBase**.
   * **Système d’exploitation de cluster** : sélectionnez **Windows**. Pour créer un cluster HBase sur Linux, consultez [Didacticiel HBase : prise en main d’Apache HBase avec Hadoop dans HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
   * **Version** : sélectionnez une version de HBase.
   * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
   * **Groupe de ressources** : créez un groupe de ressources Azure ou sélectionnez un groupe existant. Pour plus d’informations, consultez [Présentation d’Azure Resource Manager](../resource-group-overview.md).
   * **Informations d’identification** : pour les clusters basés sur Windows, vous pouvez créer un utilisateur de cluster (utilisateur HTTP, utilisateur de service web HTTP) et un utilisateur du Bureau à distance. Cliquez sur **Activer le Bureau à distance** pour ajouter les informations d’identification de l’utilisateur du Bureau à distance. La section suivante requiert RDP.
   * **Source de données** : créez un nouveau compte de stockage Azure ou sélectionnez un compte de stockage Azure existant à utiliser comme système de fichiers par défaut pour le cluster. L’emplacement du compte de stockage par défaut détermine l’emplacement du cluster. Le compte de stockage par défaut et le cluster doivent se situer dans le même datacenter.
   * **Niveaux tarifaires des nœuds** : sélectionnez le nombre de serveurs de région pour le cluster HBase.
     
     > [!WARNING]
     > Pour la haute disponibilité des services HBase, vous devez créer un cluster contenant au moins **trois** nœuds. Cela garantit que si un nœud tombe en panne, les régions de données HBase sont disponibles sur d'autres nœuds.
     > 
     > Si vous commencez à découvrir HBase, sélectionnez toujours 1 pour la taille de cluster, puis supprimez le cluster après chaque utilisation pour réduire les coûts.
     > 
     > 
   * **Configuration facultative** : configurez un réseau virtuel Azure, configurez des actions de script et ajoutez des comptes de stockage supplémentaires.
4. Cliquez sur **Create**.

> [!NOTE]
> Après la suppression d’un cluster HBase, vous pouvez créer un autre cluster HBase à l’aide du même compte de stockage par défaut et du conteneur d’objets blob par défaut. Le nouveau cluster utilisera les tables HBase créées dans le cluster d'origine. Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster.
> 
> 

## Créer des tables et insérer des données
Actuellement, il existe deux méthodes pour accéder à HBase. Cette section couvre l’utilisation de l’interpréteur de commandes HBase. La section suivante décrit l’utilisation du Kit de développement logiciel (SDK) .NET.

Pour la plupart des utilisateurs, les données s’affichent sous la forme tabulaire :

![données tabulaires hbase hdinsight][img-hbase-sample-data-tabular]

Dans HBase, qui est une implémentation de BigTable, certaines données ont l’aspect suivant :

![données bigtable hbase hdinsight][img-hbase-sample-data-bigtable]

Cela deviendra plus clair une fois la procédure suivante terminée.

**Pour utiliser l’interpréteur de commandes HBase**

1. Utilisez RDP pour vous connecter à votre cluster HBase dans HDInsight. Pour obtenir des instructions sur RDP, consultez la page [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure][hdinsight-manage-portal].
2. Dans votre session RDP, cliquez sur le raccourci **Ligne de commande Hadoop** sur le Bureau.
3. Ouvrez l’interpréteur de commandes HBase :
   
        cd %HBASE_HOME%\bin
        hbase shell
4. Créez une HBase contenant deux familles de colonne :
   
        create 'Contacts', 'Personal', 'Office'
        list
5. Insérez des données :
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![interpréteur de commandes hbase hadoop hdinsight][img-hbase-shell]
6. Récupérez une seule ligne :
   
        get 'Contacts', '1000'
   
    Comme il n'y a qu'une seule ligne, les résultats sont les mêmes que lors de l'utilisation de la commande d'analyse.
   
    Pour plus d'informations sur le schéma de la table Hbase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir [Guide de référence Apache HBase][hbase-quick-start].
7. Quittez l’interpréteur de commandes.
   
        exit

**Pour charger des données en bloc dans la table de contacts HBase**

HBase propose plusieurs méthodes pour charger des données dans des tables. Pour en savoir plus, consultez la rubrique [Chargement en bloc](http://hbase.apache.org/book.html#arch.bulk.load).

Un exemple de fichier de données a été téléchargé dans un conteneur d’objets blob, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt Le contenu du fichier de données est le suivant :

    8396    Calvin Raji        230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600    Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891    Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272    Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes        599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander    670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443    Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Vous pouvez créer un fichier texte et le télécharger sur votre propre compte de stockage si vous le souhaitez. Pour obtenir des instructions, consultez la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data].

> [!NOTE]
> Cette procédure utilise la table de contacts HBase créée dans la dernière procédure.
> 
> 

1. Dans votre session RDP, cliquez sur le raccourci **Ligne de commande Hadoop** sur le Bureau.
2. Modifiez le répertoire :
   
        cd %HBASE_HOME%\bin
3. Exécutez la commande suivante pour transformer le fichier de données en StoreFiles et le stocker sur un chemin d’accès relatif spécifié par Dimporttsv.bulk.output :
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
4. Exécutez la commande suivante pour charger les données à partir de /example/data/storeDataFileOutput vers la table HBase :
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
5. Vous pouvez ouvrir l’interpréteur de commandes HBase et utiliser la commande d’analyse pour répertorier le contenu de la table.

## Utilisation de Hive pour interroger des tables HBase
Vous pouvez interroger les données stockées dans HBase à l’aide de Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

**Ouverture du tableau de bord de cluster**

1. Accédez à **https://<Nom du cluster HDInsight>.azurehdinsight.net/**.
2. Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop. Le nom d’utilisateur par défaut est **admin** et le mot de passe est celui que vous avez entré pendant le processus de création. Un nouvel onglet de navigateur s'ouvre.
3. Cliquez sur **Éditeur Hive** en haut de la page. L'éditeur Hive se présente comme suit :
   
    ![Tableau de bord de cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Exécution de requêtes Hive**

1. Entrez le script HiveQL suivant dans l’éditeur Hive et cliquez sur **Soumettre** pour créer une table Hive mappant vers la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé dans ce didacticiel en utilisant l'interpréteur de commandes HBase.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
   
    Attendez que l'**état** soit mis à jour sur **Terminé**.
2. Entrez le script HiveQL suivant dans l’éditeur Hive, puis cliquez sur **Soumettre**. La requête Hive interroge les données dans la table HBase :
   
         SELECT count(*) FROM hbasecontacts;
3. Pour extraire les résultats de la requête Hive, lorsque la tâche est terminée, cliquez sur le lien **Afficher les détails** dans la fenêtre **Session de la tâche**. Il n’y a qu’un fichier de sortie de projet, parce que vous placez un enregistrement dans la table HBase.

**Accès au fichier de sortie**

1. Cliquez sur **Explorateur de fichiers** dans la console de requête.
2. Cliquez sur le compte de stockage Azure utilisé comme système de fichiers par défaut pour le cluster HBase.
3. Cliquez sur le nom du cluster HBase. Le conteneur de comptes de stockage Azure par défaut utilise le nom du cluster.
4. Cliquez sur **Utilisateur**, puis sur **Admin**. (Il s'agit du nom d'utilisateur Hadoop.)
5. Cliquez sur le nom de la tâche dont l'heure de **dernière modification** correspond à l'heure d'exécution de la requête Hive SELECT.
6. Cliquez sur **stdout**. Enregistrez le fichier et ouvrez-le avec le Bloc-notes. Il y a un fichier de sortie.
   
    ![Explorateur de fichiers de l’éditeur Hive HDInsight HBase][img-hdinsight-hbase-file-browser]

## Utilisation de la bibliothèque cliente de l’API REST HBase .NET
Vous devez télécharger la bibliothèque cliente API REST de HBase pour .NET à partir de GitHub et générer le projet afin d’utiliser le Kit de développement (SDK) .NET HBase. Vous trouverez des instructions relatives à cette tâche dans la procédure suivante.

1. Créez une application console Windows Desktop Visual Studio en C#.
2. Ouvrez la console du gestionnaire de package NuGet en cliquant sur le menu **Outils** > **Gestionnaire de Package NuGet** > **Console de gestionnaire de package**.
3. Exécutez la commande NuGet suivante dans la console :
   
        Install-Package Microsoft.HBase.Client
4. Ajoutez les instructions **using** suivantes au début du fichier :
   
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
5. Remplacez la fonction **Main** par le code suivant :
   
        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";
   
            string hbaseTableName = "sampleHbaseTable";
   
            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);
   
            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);
   
            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);
   
            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);
   
            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);
   
            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"
   
            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };
   
            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");
   
            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }
   
            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }
6. Définissez les trois premières variables de la fonction **Main**.
7. Appuyez sur **F5** pour exécuter l'application.

## Vérification du statut du cluster
HBase dans HDInsight est livré avec une interface utilisateur web pour la surveillance des clusters. Elle vous permet de demander des statistiques ou des informations sur les régions.

Pour ouvrir cette interface utilisateur web, vous devez ouvrir une session RDP dans le cluster, puis cliquer sur le raccourci de l’interface utilisateur web de HMaster Info sur votre bureau, ou utiliser l’URL suivante dans un navigateur web :

    http://zookeeper[0-2]:60010/master-status

Un lien vers le nœud principal HBase actif qui héberge l'interface utilisateur web est disponible dans un cluster haute disponibilité.

## Suppression du cluster
Pour éviter toute incohérence, nous vous recommandons de désactiver les tables HBase avant de supprimer le cluster. [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Et ensuite ?
Dans ce didacticiel HBase pour HDInsight, vous avez appris à créer un cluster HBase, à créer des tables et à afficher les données de ces tables à partir de l’interpréteur de commandes HBase. Vous avez également appris à utiliser une requête Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

Pour plus d'informations, consultez les pages suivantes :

* [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview]. HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées.
* [Création de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet]. Avec l’intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
* [Configuration de la géo-réplication HBase dans HDInsigtht](hdinsight-hbase-geo-replication.md) Découvrez comment configurer la réplication HBase entre deux centres de données Azure.
* [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment]. Apprenez à effectuer une [analyse des sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) des données volumineuses en temps réel à l’aide de HBase dans un cluster Hadoop dans HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0914_2016-->