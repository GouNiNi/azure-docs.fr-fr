---
title: "Didacticiel : Créer un pipeline avec l’activité de copie à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Dans ce didacticiel, vous créez un pipeline Azure Data Factory avec une activité de copie à l’aide d’Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a4121f8857fa9eaeb1cf1bca70e29666f6a04f63
ms.openlocfilehash: 457a5c758923a0741ab0baeebd2f22c43930f71d


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-powershell"></a>Didacticiel : Créer un pipeline avec l'activité de copie à l'aide d'Azure PowerShell
> [!div class="op_single_selector"]
> * [Vue d’ensemble et étapes préalables requises](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistant de copie](data-factory-copy-data-wizard-tutorial.md)
> * [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modèle Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Dans ce didacticiel, vous créez et surveillez une fabrique de données Azure à l’aide d’applets de commande Azure PowerShell. Le pipeline de la fabrique de données que vous créez dans ce didacticiel utilise une activité de copie pour copier les données d’un objet blob Azure vers une base de données SQL Azure.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) .   

> [!IMPORTANT]
> Lisez l’article [Vue d’ensemble du didacticiel](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) et effectuez les **étapes préalables requises** avant de suivre ce didacticiel.
> 
> cet article ne couvre pas toutes les applets de commande Data Factory. Consultez la [Référence des applets de commande Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) pour obtenir une documentation complète sur les applets de commande Data Factory.
> 
> 

## <a name="prerequisites"></a>Composants requis
Outre les étapes préalables répertoriées dans la rubrique Vue d’ensemble du didacticiel, vous devez installer **Azure PowerShell**. Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer Azure PowerShell sur votre ordinateur.

## <a name="in-this-tutorial"></a>Dans ce didacticiel
Le tableau suivant répertorie les étapes que vous allez exécuter dans le cadre de ce didacticiel et leurs descriptions. 

| Étape | Description |
| --- | --- |
| [créer une fabrique de données Azure](#create-data-factory) |Dans cette étape, vous créez une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**. |
| [créer des services liés](#create-linked-services) |Dans cette étape, vous créez deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. StorageLinkedService relie un stockage Azure et AzureSqlLinkedService relie une base de données SQL Azure à ADFTutorialDataFactoryPSH. |
| [Créer des jeux de données d'entrée et de sortie](#create-datasets) |Dans cette étape, vous définissez deux jeux de données (**EmpTableFromBlob** et **EmpSQLTable**). Ils sont utilisés comme tables d’entrée et de sortie de l’ **activité de copie** du pipeline ADFTutorialPipeline que vous allez créer à l’étape suivante. |
| [créer et exécuter un pipeline](#create-pipeline) |Dans cette étape, vous créez un pipeline nommé **ADFTutorialPipeline** dans la fabrique de données : **ADFTutorialDataFactoryPSH**. Le pipeline effectue une **activité de copie** qui se charge de copier des données d’objet blob Azure dans une table de base de données Azure de sortie. |
| [surveiller les jeux de données et le pipeline](#monitor-pipeline) |Dans cette étape, vous surveillez les jeux de données et le pipeline à l’aide d’Azure PowerShell. |

## <a name="create-data-factory"></a>Créer une fabrique de données
Dans cette étape, vous utilisez Azure PowerShell pour créer une fabrique de données Azure nommée **ADFTutorialDataFactoryPSH**.

1. Lancez **PowerShell**. Conservez Azure PowerShell ouvert jusqu’à la fin de ce didacticiel. Si vous fermez puis rouvrez Azure PowerShell, vous devez réexécuter ces commandes.
   
   1. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure :
      
           Login-AzureRmAccount   
   2. Exécutez la commande suivante pour afficher tous les abonnements de ce compte :
      
           Get-AzureRmSubscription 
   3. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **&lt;NameOfAzureSubscription**&gt; par le nom de votre abonnement Azure :
      
           Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
2. Créez un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante :
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
   
    Certaines étapes de ce didacticiel supposent que vous utilisez le groupe de ressources nommé **ADFTutorialResourceGroup**. Si vous utilisez un autre groupe de ressources, vous devrez l’utiliser à la place d’ADFTutorialResourceGroup dans ce didacticiel. 
3. Exécutez l’applet de commande **New-AzureRmDataFactory** pour créer une fabrique de données nommée **ADFTutorialDataFactoryPSH** :  
   
        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

Notez les points suivants :

* Le nom de la fabrique de données Azure doit être un nom global unique. Si l’erreur suivante s’affiche, changez le nom (par exemple, votrenomADFTutorialDataFactoryPSH). Utilisez ce nom à la place d’ADFTutorialFactoryPSH quand vous effectuez les étapes de ce didacticiel. Consultez la rubrique [Data Factory - Règles d’affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory.
  
        Data factory name “ADFTutorialDataFactoryPSH” is not available
* Pour créer des instances de fabrique de données, vous devez avoir le statut d’administrateur/collaborateur de l’abonnement Azure
* Le nom de la fabrique de données pourra être enregistré en tant que nom DNS et devenir ainsi visible publiquement.
* Si vous recevez le message d’erreur : «**L’abonnement n’est pas inscrit pour utiliser l’espace de noms Microsoft.DataFactory**», effectuez l’une des opérations suivantes et essayez de relancer la publication : 
  
  * Dans Azure PowerShell, exécutez la commande suivante pour enregistrer le fournisseur Data Factory : 
    
          Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
      Exécutez la commande suivante pour confirmer l’enregistrement du fournisseur Data Factory : 
    
          Get-AzureRmResourceProvider
  * Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de l’abonnement Azure et accédez à un panneau Data Factory (ou) créez une fabrique de données dans le portail Azure. Cette action enregistre automatiquement le fournisseur.

## <a name="create-linked-services"></a>Créer des services liés
Les services liés se chargent de lier des magasins de données ou des services de calcul à une fabrique de données Azure. Un magasin de données peut être de type Stockage Azure, Azure SQL Database ou une base de données SQL Server locale qui contient des données d’entrée ou qui stocke les données de sortie d’un pipeline Data Factory. Un service de calcul est un service qui traite les données d’entrée et génère des données de sortie. 

Dans cette étape, vous créez deux services liés : **StorageLinkedService** et **AzureSqlLinkedService**. Le service lié StorageLinkedService relie un compte de stockage Azure Storage et AzureSqlLinkedService relie une base de données Azure SQL à la fabrique de données : **ADFTutorialDataFactoryPSH**. Vous allez créer un pipeline plus loin dans ce didacticiel pour copier les données d’un conteneur d’objets blob dans StorageLinkedService vers une table SQL dans AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Créer un service lié pour un compte de stockage Azure
1. Créez un fichier JSON nommé **StorageLinkedService.json** dans **C:\ADFGetStartedPSH** avec le contenu suivant. Créez le dossier ADFGetStartedPSH s’il n’existe pas déjà.
   
         {
               "name": "StorageLinkedService",
               "properties": {
                 "type": "AzureStorage",
                 "typeProperties": {
                       "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                 }
               }
         }
   
   Remplacez **accountname** et **accountkey** par le nom de votre compte Stockage Azure et par sa clé.
2. Dans **Azure PowerShell**, basculez vers le dossier **ADFGetStartedPSH**. 
3. Vous pouvez utiliser l’applet de commande **New-AzureRmDataFactoryLinkedService** pour créer un service lié. Cette applet de commande et d’autres applets de commande Data Factory que vous utilisez dans ce didacticiel vous obligent à transmettre des valeurs aux paramètres **ResourceGroupName** et **DataFactoryName**. Vous pouvez également utiliser **Get-AzureRmDataFactory** pour obtenir un objet DataFactory et transmettre l’objet sans avoir à saisir ResourceGroupName et DataFactoryName chaque fois que vous exécutez une applet de commande. Exécutez la commande suivante pour affecter le résultat de l’applet de commande **Get-AzureRmDataFactory** à une variable **$df** : 
   
```   
$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
```

4. À présent, exécutez l’applet de commande **New-AzureRmDataFactoryLinkedService** pour créer le service lié **StorageLinkedService**. 
   
```
New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
```

Si vous n’avez pas exécuté l’applet de commande **Get-AzureRmDataFactory** et affecté la sortie à la variable **$df**, il vous faut spécifier des valeurs pour les paramètres ResourceGroupName et DataFactoryName, comme suit.   
   
```
New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
```

Si vous fermez Azure PowerShell au milieu du didacticiel, il vous faut exécuter l’applet de commande Get-AzureRmDataFactory au prochain lancement d’Azure PowerShell pour terminer le didacticiel.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Créer un service lié pour une base de données SQL Azure
1. Créez un fichier JSON nommé AzureSqlLinkedService.json avec le contenu suivant :
   
         {
             "name": "AzureSqlLinkedService",
             "properties": {
                 "type": "AzureSqlDatabase",
                 "typeProperties": {
                       "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                 }
               }
         }
   
   Remplacez **servername**, **databasename**, **username@servername**, et **password** par les noms de votre serveur SQL Azure, de la base de données, du compte d’utilisateur et par le mot de passe.
2. Exécutez la commande suivante pour créer un service lié : 
   
```
New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
```

   Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé sur votre serveur SQL Azure. Pour vérifier et l’activer, procédez comme suit :
   
   1. Cliquez sur le hub **PARCOURIR** situé à gauche, puis sur **Serveurs SQL**.
   2. Sélectionnez votre serveur, puis cliquez sur **PARAMÈTRES** dans le panneau SQL SERVER.
   3. Dans le panneau **PARAMÈTRES**, cliquez sur **Pare-feu**.
   4. Dans le panneau **Paramètres de pare-feu**, cliquez sur **ACTIVER** pour **Autoriser l’accès aux services Azure**.
   5. Cliquez sur le concentrateur **ACTIVE** à gauche pour basculer vers le panneau **Data Factory** où vous étiez.

## <a name="create-datasets"></a>Créer des jeux de données
À l’étape précédente, vous avez créé les services liés **StorageLinkedService** et **AzureSqlLinkedService** pour lier un compte Stockage Azure et la base de données SQL Azure à la fabrique de données : **ADFTutorialDataFactoryPSH**. Dans cette étape, vous créez des jeux de données qui représentent des données d’entrée et de sortie pour l’activité de copie dans le pipeline que vous créez à l’étape suivante. 

Une table est un jeu de données rectangulaire. C’est le seul type de jeu de données pris en charge pour l’instant. La table d’entrée dans ce didacticiel fait référence à un conteneur d’objets blob dans le stockage Azure identifié par StorageLinkedService. La table de sortie fait référence à une table SQL de la base de données SQL Azure, identifiée par AzureSqlLinkedService.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Préparer le stockage d'objets blob Azure et la base de données SQL Azure pour le didacticiel
Ignorez cette étape si vous avez suivi le didacticiel de l’article [Copie de données Blob Storage vers une base de données SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 

Effectuez les étapes suivantes pour préparer le stockage d’objets blob Azure et la base de données SQL Azure pour ce didacticiel. 

* Créez un conteneur d’objets blob nommé **adftutorial** dans le stockage d’objets blob Azure vers lequel pointe **StorageLinkedService**. 
* Créez et téléchargez un fichier texte, **emp.txt**, en tant qu’objet blob vers le conteneur **adftutorial**. 
* Créez une table nommée **emp** dans la base de données SQL Azure vers laquelle pointe **AzureSqlLinkedService**.

1. Lancez le Bloc-notes, collez le texte suivant, puis enregistrez-le sous le nom **emp.txt** dans le dossier **C:\ADFGetStartedPSH** sur votre disque dur. 
   
        John, Doe
        Jane, Doe
2. Utilisez des outils tels que [l’Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et télécharger le fichier **emp.txt** vers ce dernier.
   
    ![Azure Storage Explorer](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
3. Utilisez le script SQL suivant pour créer la table **emp** dans votre base de données SQL Azure.  

        CREATE TABLE dbo.emp 
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

    Si vous avez installé SQL Server 2014 sur votre ordinateur : suivez les instructions de l’article [Étape 2 : se connecter à la base de données SQL de la gestion de base de données SQL à l’aide de SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) pour vous connecter à votre serveur SQL Azure et exécuter le script SQL.

    Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu pour votre serveur SQL Azure afin d’autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez [cet article](../sql-database/sql-database-configure-firewall-settings.md) pour savoir comment configurer le pare-feu de votre serveur SQL Azure.

### <a name="create-input-dataset"></a>Créer le jeu de données d’entrée
Une table est un jeu de données rectangulaire qui dispose d'un schéma. Dans cette étape, vous allez créer une table nommée **EmpBlobTable** qui pointe vers un conteneur d’objets blob dans l’emplacement Stockage Azure représenté par le service lié **StorageLinkedService**. Ce conteneur d’objets blob (**adftutorial**) contient les données d’entrée dans le fichier : **emp.txt**. 

1. Créez un fichier JSON nommé **EmpBlobTable.json** dans le dossier **C:\ADFGetStartedPSH** avec le contenu suivant :
   
         {
           "name": "EmpTableFromBlob",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureBlob",
             "linkedServiceName": "StorageLinkedService",
             "typeProperties": {
               "fileName": "emp.txt",
               "folderPath": "adftutorial/",
               "format": {
                 "type": "TextFormat",
                 "columnDelimiter": ","
               }
             },
             "external": true,
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }
   
   Notez les points suivants : 
   
   * Le **type** de jeu de données est défini sur **AzureBlob**.
   * **linkedServiceName** a la valeur **StorageLinkedService**. 
   * **folderPath** est défini sur le conteneur **adftutorial**. 
   * **fileName** est défini sur **emp.txt**. Si vous ne spécifiez pas le nom de l’objet blob, les données provenant de tous les objets blob du conteneur sont considérées comme des données d’entrée.  
   * Le **type** de format a la valeur **TextFormat**.
   * Le fichier texte contient deux champs, **FirstName** et **LastName**, séparés par une virgule (**columnDelimiter**).    
   * Le paramètre **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval**, la valeur **1**). Le service Data Factory recherche les données d’entrée toutes les heures dans le dossier racine du conteneur d’objets blob (**adftutorial**).
   
   Si vous ne spécifiez pas de nom **fileName** pour une **table** d’**entrée**, tous les fichiers/objets blobs du dossier d’entrée (**folderPath**) sont considérés comme des entrées. Si vous spécifiez un fileName dans le JSON, seul le fichier/objet blob spécifié est considéré comme une entrée. 
   
   Si vous ne spécifiez pas de nom (**fileName**) pour une **table de sortie**, les fichiers générés dans le **folderPath** sont nommés selon le format suivant : Data.<Guid\>.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt).
   
   Pour affecter une valeur à **folderPath** et **fileName** de manière dynamique en fonction de l’heure de **SliceStart**, utilisez la propriété **partitionedBy**. Dans l’exemple suivant, folderPath utilise les valeurs Year, Month et Day à partir de SliceStart (heure de début de la partie en cours de traitement), alors que fileName utilise la valeur Hour à partir de SliceStart. Par exemple, si une partie est produite pour 2016-10-20T08:00:00, la valeur folderName est wikidatagateway/wikisampledataout/2016/10/20, alors que la valeur de fileName est 08.csv. 
   
         "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
         "fileName": "{Hour}.csv",
         "partitionedBy": 
         [
             { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
             { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
             { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
             { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
         ],
   
   Pour plus d'informations sur les propriétés JSON, consultez [Référence sur la création de scripts JSON](data-factory-data-movement-activities.md) .
2. Exécutez la commande suivante pour créer le jeu de données Data Factory :
   
```  
New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
```

### <a name="create-output-dataset"></a>Créer un jeu de données de sortie
Dans cette étape, vous créez un jeu de données de sortie nommé **EmpSQLTable**. Ce jeu de données pointe vers une table SQL (**emp**) dans la base de données SQL Azure représentée par **AzureSqlLinkedService**. Le pipeline copie les données à partir de l’objet blob d’entrée vers la table **emp** . 

1. Créez un fichier JSON nommé **EmpSQLTable.json** dans le dossier **C:\ADFGetStartedPSH** avec le contenu suivant :
   
         {
           "name": "EmpSQLTable",
           "properties": {
             "structure": [
               {
                 "name": "FirstName",
                 "type": "String"
               },
               {
                 "name": "LastName",
                 "type": "String"
               }
             ],
             "type": "AzureSqlTable",
             "linkedServiceName": "AzureSqlLinkedService",
             "typeProperties": {
               "tableName": "emp"
             },
             "availability": {
               "frequency": "Hour",
               "interval": 1
             }
           }
         }
   
   Notez les points suivants : 
   
   * Le **type** de jeu de données est défini sur **AzureSQLTable**.
   * **linkedServiceName** est défini sur **AzureSqlLinkedService**.
   * **tablename** est défini sur **emp**.
   * La table emp de la base de données contient trois colonnes : **ID**, **FirstName** et **LastName**. ID étant une colonne d’identité, il vous suffit de spécifier **FirstName** et **LastName**.
   * **availability** est défini sur **hourly** (**frequency** a la valeur **hour** et **interval** est défini sur **1**).  Le service Data Factory génère une tranche de données de sortie toutes les heures dans la table **emp** de la base de données SQL Azure.
2. Exécutez la commande suivante pour créer le jeu de données Data Factory : 

```   
New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
```

## <a name="create-pipeline"></a>Création d’un pipeline
Dans cette étape, vous créez un pipeline avec une **activité de copie** qui utilise **EmpTableFromBlob** en entrée et **EmpSQLTable** en sortie.

1. Créez un fichier JSON nommé **ADFTutorialPipeline.json** dans le dossier **C:\ADFGetStartedPSH** avec le contenu suivant : 
   
          {
           "name": "ADFTutorialPipeline",
           "properties": {
             "description": "Copy data from a blob to Azure SQL table",
             "activities": [
               {
                 "name": "CopyFromBlobToSQL",
                 "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                 "type": "Copy",
                 "inputs": [
                   {
                     "name": "EmpTableFromBlob"
                   }
                 ],
                 "outputs": [
                   {
                     "name": "EmpSQLTable"
                   }
                 ],
                 "typeProperties": {
                   "source": {
                     "type": "BlobSource"
                   },
                   "sink": {
                     "type": "SqlSink"
                   }
                 },
                 "Policy": {
                   "concurrency": 1,
                   "executionPriorityOrder": "NewestFirst",
                   "style": "StartOfInterval",
                   "retry": 0,
                   "timeout": "01:00:00"
                 }
               }
             ],
             "start": "2016-08-09T00:00:00Z",
             "end": "2016-08-10T00:00:00Z",
             "isPaused": false
           }
         }
   
   Notez les points suivants :
   
   * Dans la section des activités, il existe une seule activité dont le **type** a la valeur **Copy**.
   * L’entrée de l’activité a la valeur **EmpTableFromBlob** et la sortie de l’activité a la valeur **EmpSQLTable**.
   * Dans la section **transformation**, **BlobSource** est spécifié en tant que type de source et **SqlSink** est spécifié en tant que type de récepteur.
   
   Remplacez la valeur de la propriété **start** par le jour actuel et la valeur **end** par le jour suivant. Les dates/heures de début et de fin doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2016-10-14T16:32:41Z. L’heure de fin ( **end** ) est facultative, mais nous allons l’utiliser dans ce didacticiel. 
   
   Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9/9/9999** comme valeur pour la propriété **end**.
   
   Dans l’exemple ci-dessus, il existe 24 tranches de données, car une tranche est générée par heure.
   
   Pour plus d'informations sur les propriétés JSON, consultez [Référence sur la création de scripts JSON](data-factory-data-movement-activities.md) .
2. Exécutez la commande suivante pour créer la table Data Factory : 

```   
New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
```

**Félicitations !**  Vous avez correctement créé une fabrique de données Azure, les services liés, les tables et un pipeline et avez planifié le pipeline.

## <a name="monitor-pipeline"></a>Surveillance d’un pipeline
Au cours de cette étape, vous utilisez Azure PowerShell pour surveiller ce qui se passe dans une fabrique de données Azure.

1. Exécutez **Get-AzureRmDataFactory** et affectez le résultat à une variable $df.

```  
$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
```

2. Exécutez **Get-AzureRmDataFactorySlice** pour obtenir des détails sur toutes les tranches de **EmpSQLTable**, qui correspond à la table de sortie du pipeline.  

```   
Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
```

   Remplacez les parties correspondant à l’année, au mois et au jour dans le paramètre **StartDateTime** par l’année, le mois et le jour en cours. Ce paramètre doit correspondre à la valeur **Start** dans le pipeline JSON. 
   
   Vous devez voir 24 tranches, une pour chaque heure entre 12: 00 du jour actuel et 12: 00 le lendemain. 
   
   **Exemple de sortie :**

```   
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
```
3. Exécutez **Get-AzureRmDataFactoryRun** pour obtenir les détails d’exécution d’activité pour une tranche **spécifique**. Modifiez la valeur du paramètre **StartDateTime** pour qu’elle corresponde à l’heure **Start** de la tranche dans la sortie. La valeur de **StartDateTime** doit être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). 

```  
Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
```

   Le résultat doit ressembler à ce qui suit :

```   
     Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
     ResourceGroupName   : ADFTutorialResourceGroup
     DataFactoryName     : ADFTutorialDataFactoryPSH
     TableName           : EmpSQLTable
     ProcessingStartTime : 8/9/2016 11:03:28 PM
     ProcessingEndTime   : 8/9/2016 11:04:36 PM
     PercentComplete     : 100
     DataSliceStart      : 8/9/2016 10:00:00 PM
     DataSliceEnd        : 8/9/2016 11:00:00 PM
     Status              : Succeeded
     Timestamp           : 8/9/2016 11:03:28 PM
     RetryAttempt        : 0
     Properties          : {}
     ErrorMessage        :
     ActivityName        : CopyFromBlobToSQL
     PipelineName        : ADFTutorialPipeline
     Type                : Copy
```

Consultez la [référence des applets de commande Data Factory][cmdlet-reference] pour obtenir une documentation complète sur les applets de commande Data Factory. 

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez créé une fabrique de données Azure pour copier des données d'objet blob Azure dans une base de données SQL Azure. Vous avez utilisé PowerShell pour créer la fabrique de données, les services liés, les jeux de données et un pipeline. Voici les étapes de premier niveau que vous avez effectuées dans ce didacticiel :  

1. Création d’une **fabrique de données**Azure.
2. Création de **services liés**:
   1. Un service lié **Stockage Azure** pour lier votre compte Stockage Azure contenant des données d’entrée.     
   2. Un service lié **Azure SQL** pour lier votre base de données Azure SQL contenant les données de sortie. 
3. Création de **jeux de données** qui décrivent les données d’entrée et de sortie des pipelines.
4. Création d’un **pipeline** avec une **activité de copie** avec **BlobSource** en tant que source et **SqlSink** en tant que récepteur. 

## <a name="see-also"></a>Voir aussi
| Rubrique | Description |
|:--- |:--- |
| [Activités de déplacement des données](data-factory-data-movement-activities.md) |Cet article fournit une description détaillée de l’activité de copie que vous avez utilisée dans ce didacticiel. |
| [Planification et exécution](data-factory-scheduling-and-execution.md) |Cet article explique les aspects de la planification et de l’exécution du modèle d’application Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) |Cet article vous aide à comprendre les pipelines et les activités dans Azure Data Factory |
| [Groupes de données](data-factory-create-datasets.md) |Cet article vous aide à comprendre les jeux de données dans Azure Data Factory. |
| [Surveiller et gérer les pipelines Azure Data Factory à l’aide de la nouvelle application de surveillance et gestion.](data-factory-monitor-manage-app.md) |Cet article décrit comment surveiller, gérer et déboguer les pipelines à l’aide de l’application de surveillance et gestion. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md



<!--HONumber=Nov16_HO2-->


