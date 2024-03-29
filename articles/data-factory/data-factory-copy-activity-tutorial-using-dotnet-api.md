---
title: "Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API .NET | Microsoft Docs"
description: "Dans ce didacticiel, vous allez créer un pipeline Azure Data Factory avec une activité de copie à l’aide de l’API .NET."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/27/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 629ff68b11df0d17629ca101e5a80a396cfd0fb9


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’API .NET
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

Ce didacticiel vous montre comment créer et surveiller une fabrique de données Azure à l’aide de l’API .NET. Le pipeline de la fabrique de données utilise une activité de copie pour copier les données d’un objet blob Azure vers une base de données SQL Azure.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Elle est mise en œuvre par un service disponible dans le monde entier, capable de copier des données entre différents magasins de données de façon sécurisée, fiable et évolutive. Pour plus d’informations sur l’activité de copie, consultez l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) .   

> [!NOTE]
> Cet article ne couvre pas toutes les API .NET Data Factory. Pour plus d’informations sur le Kit de développement logiciel (SDK) .NET de Data Factory, consultez [Fabrique de données .NET](https://msdn.microsoft.com/library/mt415893.aspx) . 
> 
> 

## <a name="prerequisites"></a>Composants requis
* Consultez [Vue d’ensemble et étapes préalables requises](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir une vue d’ensemble du didacticiel et effectuer les **étapes préalables requises** . 
* Visual Studio 2012, 2013 ou 2015
* Téléchargez et installez le [Kit de développement logiciel (SDK) Azure .NET](http://azure.microsoft.com/downloads/)
* Azure PowerShell. Suivez les instructions de l’article [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour installer Azure PowerShell sur votre ordinateur. Vous utilisez Azure PowerShell pour créer une application Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Créer une application dans Azure Active Directory
Créez une application Azure Active Directory, créez un principal de service pour l’application et attribuez-lui le rôle **Contributeurs de Data Factory** .  

1. Lancez **PowerShell**. 
2. Exécutez la commande suivante, puis saisissez le nom d’utilisateur et le mot de passe que vous avez utilisés pour la connexion au portail Azure.
   
        Login-AzureRmAccount   
3. Exécutez la commande suivante pour afficher tous les abonnements de ce compte.
   
        Get-AzureRmSubscription 
4. Exécutez la commande suivante pour sélectionner l’abonnement que vous souhaitez utiliser. Remplacez **&lt;NameOfAzureSubscription**&gt; par le nom de votre abonnement Azure. 
   
        Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
   
   > [!IMPORTANT]
   > Notez les éléments **SubscriptionId** et **TenantId** dans la sortie de cette commande. 
   > 
   > 
5. Créez un groupe de ressources Azure nommé **ADFTutorialResourceGroup** en exécutant la commande suivante dans PowerShell.  
   
        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
   
    Si le groupe de ressources existe, indiquez s’il faut le mettre à jour (Y) ou le conserver tel quel (N). 
   
    Si vous utilisez un autre groupe de ressources, vous devez remplacer ADFTutorialResourceGroup par le nom de votre groupe de ressources dans ce didacticiel.
6. Créez une application Azure Active Directory. 
   
        $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
   
    Si vous obtenez l’erreur suivante, spécifiez une autre URL et relancez la commande. 
   
        Another object with the same value for property identifierUris already exists.
7. Créez le principal du service AD. 
   
        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
8. Ajoutez le principal du service au rôle **Contributeurs de Data Factory** . 
   
        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
9. Récupérez l’ID de l’application.
   
        $azureAdApplication
   
    Notez l’ID d’application (**applicationID** dans la sortie).

Vous devez avoir les quatre valeurs suivantes après ces étapes : 

* ID client
* Identifiant d’abonnement
* ID de l'application 
* Mot de passe (spécifié dans la première commande)   

## <a name="walkthrough"></a>Procédure pas à pas
1. À l’aide de Visual Studio 2012/2013/2015, créez une application console C# .NET.
   1. Lancez **Visual Studio** 2012/2013/2015.
   2. Cliquez sur **Fichier**, pointez le curseur de la souris sur **Nouveau**, puis cliquez sur **Projet**.
   3. Développez **Modèles**, puis sélectionnez **Visual C#**. Dans cette procédure pas à pas, vous utilisez C#, mais vous pouvez utiliser un autre langage .NET.
   4. Sélectionnez **Application console** dans la liste des types de projet située sur la droite.
   5. Entrez **DataFactoryAPITestApp** dans le champ Nom.
   6. Sélectionnez **C:\ADFGetStarted** dans le champ Emplacement.
   7. Cliquez sur **OK** pour créer le projet.
2. Cliquez sur **Outils**, pointez sur **Gestionnaire de package NuGet**, puis cliquez sur **Console du Gestionnaire de package**.
3. Dans la **Console du Gestionnaire de package**, effectuez les étapes suivantes : 
   1. Exécutez la commande suivante pour installer le package Data Factory : `Install-Package Microsoft.Azure.Management.DataFactories`        
   2. Exécutez la commande suivante pour installer le package Azure Active Directory (vous utilisez l’API Active Directory dans le code) : `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Ajoutez la section **appSettings** suivante au fichier **App.config**. Ces valeurs sont utilisées par la méthode d’assistance **GetAuthorizationHeader**. 
   
    Remplacez les valeurs de **&lt;ID d’Application&gt;**, **&lt;Mot de passe&gt;**, **&lt;ID d’abonnement&gt;**, et **&lt;ID client&gt;** par vos propres valeurs. 
   
        <?xml version="1.0" encoding="utf-8" ?>
        <configuration>
            <startup> 
                <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
            </startup>
            <appSettings>
                <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
                <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
                <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
   
                <add key="ApplicationId" value="your application ID" />
                <add key="Password" value="Password you used while creating the AAD application" />
                <add key="SubscriptionId" value= "Subscription ID" />
                <add key="ActiveDirectoryTenantId" value="Tenant ID" />
            </appSettings>
        </configuration>
5. Ajoutez les instructions **using** suivantes au fichier source (Program.cs) dans le projet.
   
        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
   
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
   
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Ajoutez à la méthode **Main** le code suivant, qui crée une instance de la classe **DataPipelineManagementClient**. Cet objet vous permet de créer une fabrique de données, un service lié, des jeux de données d’entrée et de sortie, ainsi qu’un pipeline. Il vous permet également d’analyser les tranches d’un jeu de données lors de l’exécution.    
   
            // create data factory management client
            string resourceGroupName = "ADFTutorialResourceGroup";
            string dataFactoryName = "APITutorialFactory";
   
            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());
   
            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);
   
            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
   
   > [!IMPORTANT]
   > Remplacez la valeur de **resourceGroupName** par le nom de votre groupe de ressources Azure. 
   > 
   > Mettez à jour le nom de la fabrique de données (**dataFactoryName**) pour le rendre unique. Le nom de la fabrique de données doit être un nom global unique. Consultez la rubrique [Data Factory - Règles d'affectation des noms](data-factory-naming-rules.md) pour savoir comment nommer les artefacts Data Factory. 
   > 
   > 
7. Ajoutez à la méthode **Main** le code suivant, qui crée une **fabrique de données**.
   
            // create a data factory
            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties() { }
                    }
                }
            );
8. Ajoutez le code suivant pour créer un **service lié Azure Storage** dans la méthode **Main**. 
   
   > [!IMPORTANT]
   > Remplacez **storageaccountname** et **accountkey** par le nom et la clé de votre compte Azure Storage. 
   > 
   > 
   
            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Ajoutez le code suivant pour créer un **service lié Azure SQL** dans la méthode **Main**.
   
   > [!IMPORTANT]
   > Remplacez **servername**, **databasename**, **username** et **password** par le nom de votre serveur SQL Azure, le nom de la base de données, le nom de l’utilisateur et le mot de passe.  
   > 
   > 
   
            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Ajoutez à la méthode **Main** le code suivant, qui crée des **jeux de données d’entrée et de sortie**. 
    
            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";
    
            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
                            },

                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

1. Ajoutez à la méthode **Main** le code suivant, qui **crée et active un pipeline**. Ce pipeline dispose d’une fonction **CopyActivity** qui accepte **BlobSource** en tant que source et **BlobSink** en tant que récepteur.
   
           // create a pipeline
           Console.WriteLine("Creating a pipeline");
           DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
           DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
           string PipelineName = "ADFTutorialPipeline";
   
           client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
               new PipelineCreateOrUpdateParameters()
               {
                   Pipeline = new Pipeline()
                   {
                       Name = PipelineName,
                       Properties = new PipelineProperties()
                       {
                           Description = "Demo Pipeline for data transfer between blobs",
   
                   // Initial value for pipeline's active period. With this, you won't need to set slice status
                   Start = PipelineActivePeriodStartTime,
                           End = PipelineActivePeriodEndTime,
   
                           Activities = new List<Activity>()
                           {
                               new Activity()
                               {
                                   Name = "BlobToAzureSql",
                                   Inputs = new List<ActivityInput>()
                                   {
                                       new ActivityInput() {
                                           Name = Dataset_Source
                                       }
                                   },
                                   Outputs = new List<ActivityOutput>()
                                   {
                                       new ActivityOutput()
                                       {
                                           Name = Dataset_Destination
                                       }
                                   },
                                   TypeProperties = new CopyActivity()
                                   {
                                       Source = new BlobSource(),
                                       Sink = new BlobSink()
                                       {
                                           WriteBatchSize = 10000,
                                           WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                       }
                                   }
                               }
                           },
                       }
                   }
               });    
2. Ajoutez le code suivant à la méthode **Main** pour obtenir l’état d’une tranche de données du jeu de données de sortie. Une seule tranche est attendue dans cet exemple.   
   
           // Pulling status within a timeout threshold
           DateTime start = DateTime.Now;
           bool done = false;
   
           while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
           {
               Console.WriteLine("Pulling the slice status");
               // wait before the next status check
               Thread.Sleep(1000 * 12);
   
               var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                   new DataSliceListParameters()
                   {
                       DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                       DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                   });
   
               foreach (DataSlice slice in datalistResponse.DataSlices)
               {
                   if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                   {
                       Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                       done = true;
                       break;
                   }
                   else
                   {
                       Console.WriteLine("Slice status is: {0}", slice.State);
                   }
               }
           }
3. Ajoutez à la méthode **Main** le code suivant pour obtenir les détails d’exécution d’une tranche de données.
   
           Console.WriteLine("Getting run details of a data slice");
   
           // give it a few minutes for the output slice to be ready
           Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
           Console.ReadKey();
   
           var datasliceRunListResponse = client.DataSliceRuns.List(
                   resourceGroupName,
                   dataFactoryName,
                   Dataset_Destination,
                   new DataSliceRunListParameters()
                   {
                       DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                   }
               );
   
           foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
           {
               Console.WriteLine("Status: \t\t{0}", run.Status);
               Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
               Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
               Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
               Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
               Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
               Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
           }
   
           Console.WriteLine("\nPress any key to exit.");
           Console.ReadKey();
4. Ajoutez à la classe **Program** la méthode d’assistance suivante utilisée par la méthode **Main**.  
   
       public static string GetAuthorizationHeader()
       {
           AuthenticationResult result = null;
           var thread = new Thread(() =>
           {
               try
               {
                   var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                   ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                   result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
               }
               catch (Exception threadEx)
               {
                   Console.WriteLine(threadEx.Message);
               }
           });
   
           thread.SetApartmentState(ApartmentState.STA);
           thread.Name = "AcquireTokenThread";
           thread.Start();
           thread.Join();
   
           if (result != null)
           {
               return result.AccessToken;
           }
   
           throw new InvalidOperationException("Failed to acquire token");
       }  
5. Dans l’Explorateur de solutions, développez le projet (**DataFactoryAPITestApp**), cliquez avec le bouton droit sur **Références**, puis cliquez sur **Ajouter une référence**. Cochez la case de l’assemblage **System.Configuration** et cliquez sur **OK**. 
6. Générez l'application console. Dans le menu, cliquez sur **Générer**, puis sur **Générer la solution**. 
7. Vérifiez qu’il existe au moins un fichier dans le conteneur **adftutorial** de votre stockage d’objets blob Azure. Si tel n’est pas le cas, créez le fichier **Emp.txt** dans le bloc-notes avec le contenu suivant, puis chargez-le dans le conteneur adftutorial.
   
       John, Doe
       Jane, Doe
8. Exécutez l’exemple en cliquant dans le menu sur **Déboguer** -> **Démarrer le débogage**. Si **Obtention des détails d’exécution d’une tranche de données** s’affiche, patientez quelques minutes, puis appuyez sur **Entrée**. 
9. Utilisez le portail Azure pour vérifier que la fabrique de données **APITutorialFactory** est créée avec les artefacts suivants : 
   * Service lié : **LinkedService_AzureStorage** 
   * Jeu de données : **DatasetBlobSource** et **DatasetBlobDestination**.
   * Pipeline : **PipelineBlobSample** 
10. Vérifiez que les deux enregistrements d’employés sont créés dans la table « **emp** » de la base de données Azure SQL spécifiée.

## <a name="next-steps"></a>Étapes suivantes
* Lisez l’article [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md) qui fournit une description détaillée de l’activité de copie que vous avez utilisée dans ce didacticiel.
* Pour plus d’informations sur le Kit de développement logiciel (SDK) .NET de Data Factory, consultez [Fabrique de données .NET](https://msdn.microsoft.com/library/mt415893.aspx) . Cet article ne couvre pas toutes les API .NET Data Factory. 




<!--HONumber=Nov16_HO2-->


