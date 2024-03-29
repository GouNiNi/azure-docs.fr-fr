---
title: "Interrogation des données depuis un stockage d’objets blob compatible avec HDFS | Microsoft Docs"
description: "HDInsight utilise le stockage d’objets blob Azure comme magasin Big Data pour HDFS. Apprenez à interroger des données depuis un stockage d’objets blob et à stocker les résultats de votre analyse."
keywords: "stockage d’objets blob, hdfs, données structurées, données non structurées"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1d2e65f2-16de-449e-915f-3ffbc230f815
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/06/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7baf1aa756221df62a36cd975ffb92fc8cd27232


---
# <a name="use-hdfscompatible-azure-blob-storage-with-hadoop-in-hdinsight"></a>Utilisation du stockage d’objets blob Azure compatible avec HDFS avec Hadoop dans HDInsight
Découvrez comment utiliser le stockage d’objets blob Azure à faible coût avec HDInsight, créer un compte de stockage Azure et un conteneur de stockage d’objets blob, puis y envoyer des données.

Le stockage d’objets blob Azure est une solution de stockage à la fois robuste et polyvalente qui s’intègre en toute transparence à HDInsight. Grâce à une interface HDFS (Hadoop Distributed File System), l’ensemble des composants de HDInsight peut fonctionner directement sur les données structurées ou non structurées dans le stockage d’objets blob.

Le stockage de données dans le stockage d’objets blob vous permet de supprimer les clusters HDInsight servant aux calculs, sans perte de données utilisateur.

> [!IMPORTANT]
> HDInsight prend uniquement en charge les objets blob de blocs. Il ne prend en charge les objets blob de pages ou d’ajout.
> 
> 

Pour plus d'informations sur la création d'un cluster HDInsight, consultez la rubrique [Prise en main de HDInsight][hdinsight-get-started] ou [Création de clusters HDInsight][hdinsight-creation].

## <a name="hdinsight-storage-architecture"></a>Architecture de stockage HDInsight
Le schéma suivant résume l'architecture de stockage HDInsight :

![Les clusters Hadoop utilisent l’API HDFS pour accéder aux données structurées et non structurées et les stocker dans le stockage d’objets blob.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage Architecture")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet, par exemple :

    hdfs://<namenodehost>/<path>

De plus, HDInsight permet d'accéder aux données du stockage d'objets blob Azure. La syntaxe est :

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

> [!NOTE]
> Dans les versions antérieures à 3.0, HDInsight `asv://` a été utilisé à la place de `wasb://`. `asv://` ne doit pas être utilisé avec les clusters HDInsight 3.0 ou versions ultérieures, car cela entraînerait une erreur.
> 
> 

Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut implique un schéma et une autorité par défaut. Il peut également être utilisé pour résoudre les chemins d'accès relatifs. Au cours de la création de HDInsight, un compte de stockage Azure et un conteneur de stockage d'objets blob Azure spécifique de ce compte sont désignés en tant que système de fichiers par défaut.

Outre ce compte de stockage, vous pouvez en ajouter d’autres à partir du même abonnement Azure ou à partir d’autres abonnements Azure pendant le processus de création ou à l’issue de la création d’un cluster. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Création de clusters HDInsight][hdinsight-creation].

* **Conteneurs dans les comptes de stockage connectés à un cluster :** comme le nom et la clé du compte sont associés au cluster durant la création, vous disposez d'un accès complet aux objets blob de ces conteneurs.
* **Conteneurs publics ou objets blob publics dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous avez l’autorisation en lecture seule pour les objets blob dans les conteneurs.
  
  > [!NOTE]
  > Des conteneurs publics vous permettent d'obtenir une liste de tous les objets blob disponibles, ainsi que ses métadonnées. Vous pouvez accéder aux objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Limiter l'accès aux conteneurs et aux objets blob</a>.
  > 
  > 
* **Conteneurs privés dans les comptes de stockage qui ne sont PAS connectés à un cluster :** vous ne pouvez pas accéder aux objets blob se trouvant dans les conteneurs, sauf si vous définissez le compte de stockage quand vous envoyez des travaux WebHCat. Une explication sera fournie plus loin dans cet article.

Les comptes de stockage définis durant la création et leurs clés sont stockés dans %HADOOP_HOME%/conf/core-site.xml sur les nœuds du cluster. Le comportement par défaut de HDInsight consiste à utiliser les comptes de stockage définis dans le fichier core-site.xml. Il est déconseillé de modifier le fichier core-site.xml, car le nœud principal du cluster peut à tout moment être recréé ou migrer, et toutes les modifications apportées à ces fichiers seront perdues.

Plusieurs tâches WebHCat, notamment Hive, MapReduce, la diffusion en continu Hadoop et Pig, peuvent véhiculer avec elles une description des comptes de stockage et des métadonnées. (cela fonctionne actuellement pour Pig, pour les comptes de stockage, mais pas pour les métadonnées.) La section [Accès aux objets blob avec Azure PowerShell](#powershell) de cet article contient un exemple de cette fonctionnalité. Pour plus d'informations, consultez la page [Utilisation d'un cluster HDInsight avec des comptes de stockage et des metastores secondaires](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Le stockage d’objets blob peut être utilisé pour les données structurées et non structurées. Les conteneurs de stockage d'objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser la barre oblique (« / ») dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire *input* , mais la barre oblique figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.

### <a name="a-idbenefitsabenefits-of-blob-storage"></a><a id="benefits"></a>Avantages du stockage d’objets blob
La réduction des performances entraînée par la séparation des clusters de calcul et des ressources de stockage est compensée par le fait que les clusters de calcul sont créés à proximité des ressources du compte de stockage dans la région Azure, où le réseau à haut débit permet aux nœuds de calcul d'accéder plus efficacement aux données dans le stockage d'objets blob Azure.

Voici les avantages offerts par le stockage de données dans un stockage d'objets blob Azure au lieu d'un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage blob Azure en utilisant les API HDFS ou les [API REST de stockage blob][blob-storage-restAPI]. Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.
* **Archivage des données** : le stockage de données dans le stockage d'objets blob Azure permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur.
* **Coût de stockage des données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans un stockage d'objets blob Azure, car le coût d'un cluster de calcul est plus élevé que celui d'un conteneur de stockage d'objets blob Azure. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.
* **Montée en charge élastique :** même si le système HDFS offre un système de fichiers monté en charge, cette capacité est déterminée par le nombre de nœuds que vous créez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d'évolution flexible que vous obtenez automatiquement dans le stockage d’objets blob Azure.
* **Géo-réplication :** vous pouvez géo-répliquer vos conteneurs de stockage d’objets blob Azure. Si cette fonctionnalité permet la récupération géographique et la redondance des données, un basculement vers un emplacement géo-répliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Nous vous recommandons donc de peser sérieusement le pour et le contre avant de choisir la géo-réplication.

Certains packages et tâches MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un stockage d'objets blob Azure. Dans ce cas, vous pouvez choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d'autres processus.

> [!NOTE]
> La plupart des commandes HDFS (par exemple <b>ls</b>, <b>copyFromLocal</b> et <b>mkdir</b>) fonctionnent toujours comme prévu. Seules les commandes propres à l'implémentation HDFS native (nommée DFS), telles que <b>fschk</b> et <b>dfsadmin</b> se comporteront différemment dans le stockage d'objets blob Azure.
> 
> 

## <a name="create-blob-containers"></a>Création de conteneurs d’objets blob
Pour utiliser des objets blob, commencez par créer un [compte de stockage Azure][azure-storage-create]. Durant cette opération, vous devez indiquer une région Azure qui stockera les objets que vous créez en utilisant ce compte. Le cluster et le compte de stockage doivent être hébergés dans la même région. La base de données SQL Server de metastore Hive et la base de données SQL Server de metastore Oozie doivent également se trouver dans la même région.

Où qu’il réside, chaque objet blob que vous créez appartient à un conteneur de votre compte de stockage Azure. Ce conteneur peut être un objet blob existant créé hors de HDInsight ou un conteneur créé pour un cluster HDInsight.

Le conteneur d’objets blob par défaut stocke les informations spécifiques de cluster telles que l’historique et les journaux des travaux. Ne partagez pas un conteneur d’objets blob par défaut avec plusieurs clusters HDInsight. Cela peut endommager l’historique des travaux et modifier le comportement du cluster. Il est recommandé d’utiliser un conteneur différent pour chaque cluster et de placer des données partagées sur un compte de stockage lié spécifié dans le déploiement de tous les clusters pertinents plutôt que d’utiliser le compte de stockage par défaut. Pour plus d'informations sur la configuration des comptes de stockage liés, consultez la rubrique [Création de clusters HDInsight][hdinsight-creation]. Vous pouvez, toutefois, réutiliser un conteneur de stockage par défaut une fois le cluster HDInsight d'origine supprimé. Pour les clusters HBase, vous pouvez conserver le schéma de la table HBase et les données en créant un nouveau cluster HBase à l'aide du conteneur de stockage d'objets blob par défaut qui est utilisé par un cluster HBase qui a été supprimé.

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure
Lorsque vous créez un cluster HDInsight à partir du portail, vous avez la possibilité d’utiliser un compte de stockage existant ou de créer un nouveau compte de stockage :

![source de données de création hadoop hdinsight](./media/hdinsight-hadoop-use-blob-storage/hdinsight.provision.data.source.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Si vous avez [installé et configuré l’interface de ligne de commande Azure](../xplat-cli-install.md), la commande suivante peut être utilisée sur un compte de stockage et un conteneur.

    azure storage account create <storageaccountname> --type LRS

> [!NOTE]
> Le paramètre `--type` indique la méthode de réplication du compte de stockage. Pour plus d'informations, consultez [Réplication Azure Storage](../storage/storage-redundancy.md). N'utilisez pas ZRS, car ZRS ne prend en charge d'objets blob de pages, de fichiers, de tables ou de files d'attente.
> 
> 

Vous devez spécifier la région géographique dans laquelle se trouve le compte de stockage. Vous devez créer le compte de stockage dans la région où vous envisagez de créer votre cluster HDInsight.

Une fois le compte de stockage créé, utilisez la commande suivante pour récupérer les clés du compte de stockage :

    azure storage account keys list <storageaccountname>

Pour créer un conteneur, utilisez la commande suivante :

    azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

### <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Si vous avez [installé et configuré Azure PowerShell][powershell-install], vous pouvez utiliser la commande suivante dans l’invite Azure PowerShell pour créer un compte de stockage et un conteneur :

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS 

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

## <a name="address-files-in-blob-storage"></a>Adressage des fichiers dans le stockage d'objets blob
Le modèle d’URI pour accéder aux fichiers du stockage d’objets blob à partir de HDInsight est le suivant :

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


Le modèle d'URI offre à la fois un accès non chiffré (avec le préfixe *wasb:*) et un accès chiffré SSL (avec *wasbs*). Dans la mesure du possible, nous vous recommandons d’utiliser *wasbs* , même lorsqu’il s’agit d’accéder à des données qui résident dans la même région Azure.

Le &lt;BlobStorageContainerName&gt; identifie le nom du conteneur de stockage d'objets blob Azure.
Le &lt;StorageAccountName&gt; identifie le nom de compte de stockage Azure. Un nom de domaine complet (FQDN) est requis.

Si ni &lt;BlobStorageContainerName&gt; ni &lt;StorageAccountName&gt; n'a été spécifié, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, le fichier *hadoop-mapreduce-examples.jar* fourni avec les clusters HDInsight peut être désigné pour l'une des utilisations suivantes :

    wasbs://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasbs:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Le nom du fichier est <i>hadoop-examples.jar</i> sur les clusters HDInsight version 2.1 et 1.6.
> 
> 

Le &lt;path&gt; correspond au nom du chemin d'accès du fichier ou du répertoire HDFS. Comme les conteneurs dans le stockage d'objets blob Azure constituent simplement un magasin de clé-valeur, il n'y a pas de système de fichiers hiérarchique. Une barre oblique (« / ») à l'intérieur d'une clé d'objet blob est interprétée comme un séparateur de répertoire. Par exemple, le nom d'objet blob pour *hadoop-mapreduce-examples.jar* est :

    example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> Lorsque vous utilisez des objets blob hors de HDInsight, la plupart des utilitaires ne reconnaissent pas le format WASB et attendent plutôt un format de chemin d’accès basique, comme `example/jars/hadoop-mapreduce-examples.jar`.
> 
> 

## <a name="access-blobs-using-azure-cli"></a>Accès aux objets blob avec l’interface de ligne de commande Azure
Utilisez la commande suivante pour répertorier les commandes relatives aux objets blob :

    azure storage blob

**Exemple d’utilisation de l’interface de ligne de commande Azure pour charger un fichier**

    azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemple d’utilisation de l’interface de ligne de commande Azure pour télécharger un fichier**

    azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemple d’utilisation de l’interface de ligne de commande Azure pour supprimer un fichier**

    azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Exemple d’utilisation de l’interface de ligne de commande Azure pour répertorier des fichiers**

    azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

## <a name="access-blobs-using-azure-powershell"></a>Accès aux objets blob avec Azure PowerShell
> [!NOTE]
> Les commandes de cette section présentent des exemples basiques d’utilisation de PowerShell pour accéder aux données stockées dans des objets blob. Pour un exemple plus complet personnalisé pour une utilisation avec HDInsight, consultez la section [Outils HDInsight](https://github.com/Blackmist/hdinsight-tools).
> 
> 

Utilisez la commande suivante pour répertorier les cmdlets relatives aux objets blob :

    Get-Command *blob*

![Liste des cmdlets PowerShell relatives aux objets blob.][img-hdi-powershell-blobcommands]

### <a name="upload-files"></a>Charger des fichiers
Consultez la page [Téléchargement de données vers HDInsight][hdinsight-upload-data].

### <a name="download-files"></a>Téléchargement de fichiers
Le script suivant télécharge un objet blob de blocs vers le dossier actuel. Avant d'exécuter le script, remplacez le répertoire par un dossier sur lequel vous disposez d'accès en écriture.

    $resourceGroupName = "<AzureResourceGroupName>"
    $storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at creation.
    $containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    # Use Add-AzureAccount if you haven't connected to your Azure subscription
    Login-AzureRmAccount 
    Select-AzureRmSubscription -SubscriptionID "<Your Azure Subscription ID>"

    Write-Host "Create a context object ... " -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force

    Write-Host "List the downloaded file ..." -ForegroundColor Green
    cat "./$blob"

Vous pouvez utiliser le code suivant pour fournir le nom de groupe de ressources et le nom du cluster :

    $resourceGroupName = "<AzureResourceGroupName>"
    $clusterName = "<HDInsightClusterName>"
    $blob = "example/data/sample.log" # The name of the blob to be downloaded.

    $cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    $defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount)[0].Value
    $defaultStorageContainer = $cluster.DefaultStorageContainer
    $storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 

    Write-Host "Download the blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob $blob -Context $storageContext -Force

### <a name="delete-files"></a>Supprimer des fichiers
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob

### <a name="list-files"></a>Énumérer des fichiers
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix "example/data/"

### <a name="run-hive-queries-using-an-undefined-storage-account"></a>Exécution de requêtes Hive à l'aide d'un compte de stockage non défini
Cet exemple montre comment répertorier le contenu d’un dossier d’un compte de stockage non défini pendant le processus de création.
$clusterName = "<HDInsightClusterName>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureRmHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-AzureRmHDInsightHiveJob -Defines $defines -Query "dfs -ls wasbs://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment utiliser le stockage d’objets blob Azure compatible avec HDFS avec HDInsight, mais aussi, que le stockage d’objets blob Azure est un élément essentiel de HDInsight. Ceci vous permet de créer des solutions à long terme et évolutives pour acquérir des données d’archivage avec le stockage d’objets blob Azure et d’utiliser HDInsight pour déverrouiller les informations des données structurées et non structurées stockées.

Pour plus d'informations, consultez les pages suivantes :

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utiliser des signatures d’accès partagé Stockage Azure pour restreindre l’accès aux données avec HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: ../powershell-install-configure.md
[hdinsight-creation]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  



<!--HONumber=Nov16_HO2-->


