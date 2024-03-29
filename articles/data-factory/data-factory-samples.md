---
title: Azure Data Factory - Exemples
description: Fournit des détails sur les exemples fournis avec le service Azure Data Factory.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: spelluru

---
# Azure Data Factory - Exemples
## Exemples sur GitHub
Le [dépôt GitHub Azure-DataFactory](https://github.com/azure/azure-datafactory) contient plusieurs exemples qui vous aideront à prendre en main rapidement le service Azure Data Factory (ou) à modifier les scripts et à utiliser ce service dans votre propre application. Le dossier Samples\\JSON contient des extraits de code JSON pour les scénarios courants.

| Exemple | Description |
|:--- |:--- |
| [Procédure pas à pas Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) |Cet exemple fournit une procédure pas à pas de traitement de journaux à l’aide d’Azure Data Factory pour transformer les données des fichiers journaux en informations. <br/><br/>Dans cette procédure, le pipeline Data Factory collecte des exemples de journaux, traite et enrichit les données des journaux avec des données de référence, puis transforme les données pour évaluer l’efficacité d’une campagne marketing lancée dernièrement. |
| [Exemples JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) |Cet exemple fournit des exemples JSON pour des scénarios courants. |
| [Exemple de téléchargeur de données HTTP](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) |Cet exemple montre le téléchargement des données à partir d’un point de terminaison HTTP vers Azure Blob Storage, à l’aide d’une activité .NET personnalisée. |
| [Exemple d’activité .NET entre AppDomains](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Cet exemple vous montre comment créer une activité .NET personnalisée qui ne soit pas limitée aux versions d’assembly utilisées par le lanceur d’Azure Data Factory (par exemple, WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.). |
| [Exécuter un script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Cet exemple inclut l’activité personnalisée Data Factory qui peut être utilisée pour appeler RScript.exe. Cet exemple fonctionne uniquement avec votre propre cluster HDInsight (et non celui à la demande) sur lequel est déjà installé le script R. |
| [Appeler des travaux Spark sur un cluster HDInsight Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) |Cet exemple montre comment utiliser l’activité MapReduce pour appeler un programme Spark. Le programme Spark copie simplement les données d'un conteneur d'objets blob Azure vers un autre. |
| [Analyse de Twitter à l’aide d’une activité de notation par lot d’Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) |Cet exemple montre comment utiliser AzureMLBatchScoringActivity pour appeler un modèle Azure Machine Learning qui effectue une analyse de sentiments, une notation, des prédictions, etc. pour Twitter. |
| [Analyse de Twitter à l’aide d’une activité personnalisée](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Cet exemple montre comment utiliser une activité .NET personnalisée pour appeler un modèle Azure Machine Learning qui effectue une analyse de sentiments, une notation, des prédictions, etc. pour Twitter. |
| [Pipelines paramétrés pour Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) |Cet exemple fournit du code C# de bout en bout permettant de déployer des pipelines N, puis d’en effectuer la notation et le recyclage avec un paramètre de région différent pour chacun. La liste des régions provient du fichier parameters.txt qui est inclus avec cet exemple. |
| [Actualisation des données de référence pour les travaux Azure Stream Analytics](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |Cet exemple montre comment combiner Azure Data Factory et Azure Stream Analytics pour exécuter des requêtes avec des données de référence et planifier l’actualisation des données de référence. |
| [Pipeline hybride avec une instance locale d’Hadoop Hortonworks](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) |Cet exemple utilise un cluster Hadoop local comme cible de calcul pour exécuter des travaux dans Data Factory. La procédure est la même que pour ajouter d’autres cibles de calcul, telles qu’un cluster Hadoop HDInsight, dans le cloud. |
| [Outil de conversion JSON](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) |Cet outil vous permet de convertir des fichiers JSON depuis les versions antérieures à 2015-07-01-preview vers la version 2015-07-01-preview, qui est la plus récente (par défaut). |
| [Exemple de fichier d’entrée U-SQL](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |Il s’agit d’un exemple de fichier utilisé par une activité U-SQL. |

## Exemples dans le portail Azure
Vous pouvez utiliser la vignette **exemples de pipelines** figurant dans la page d’accueil de votre fabrique de données pour déployer des exemples de pipelines et les entités associées (ensembles de données et services liés) dans votre fabrique de données.

1. Créez une nouvelle fabrique de données ou ouvrez une fabrique de données existante. Consultez la page [Prise en main d’Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) pour connaître les étapes de la création d’une fabrique de données.
2. Dans le panneau **DATA FACTORY** pour la fabrique de données, cliquez sur la vignette **Exemples de pipelines**.
   
    ![Vignette Exemples de pipelines](./media/data-factory-samples/SamplePipelinesTile.png)
3. Dans le panneau **Exemples de pipelines**, cliquez sur l’**exemple** que vous souhaitez déployer.
   
    ![Panneau Exemples de pipelines](./media/data-factory-samples/SampleTile.png)
4. Spécifiez les paramètres de configuration de l’exemple. Par exemple, votre clé de compte et votre nom de compte de stockage Azure, le nom du serveur SQL Azure, la base de données, l’ID d’utilisateur, le mot de passe, etc.
   
    ![Panneau Exemple](./media/data-factory-samples/SampleBlade.png)
5. Une fois que vous avez fini de spécifier les paramètres de configuration, cliquez sur **Créer** pour créer/déployer les exemples de pipelines et les tables/services liés utilisés par les pipelines.
6. L’état du déploiement s’affiche sur l’exemple de vignette sur laquelle vous avez cliqué dans le panneau **Exemples de pipelines**.
   
    ![État du déploiement](./media/data-factory-samples/DeploymentStatus.png)
7. Lorsque vous voyez le message **Déploiement réussi** sur la vignette de l’exemple, fermez le panneau **Exemples de pipelines**.
8. Dans le panneau **DATA FACTORY**, vous pouvez voir que les services liés, les jeux de données et les pipelines sont ajoutés à votre fabrique de données.
   
    ![Panneau Data Factory](./media/data-factory-samples/DataFactoryBladeAfter.png)

## Exemples dans Visual Studio
### Composants requis
Les composants suivants doivent être installés sur votre ordinateur :

* Visual Studio 2013 ou Visual Studio 2015
* Téléchargez le Kit de développement logiciel (SDK) Azure pour Visual Studio 2013 ou Visual Studio 2015. Accédez à la [page de téléchargement d’Azure](https://azure.microsoft.com/downloads/), puis cliquez sur **VS 2013** ou **VS 2015** dans la section **.NET**.
* Téléchargez le dernier plug-in Azure Data Factory pour Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) ou [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si vous utilisez Visual Studio 2013, vous pouvez également mettre à jour le plug-in de la manière suivante : dans le menu, cliquez sur **Outils** -> **Extensions et mises à jour** -> **En ligne** -> **Galerie Visual Studio** -> **Outils Microsoft Azure Data Factory pour Visual Studio** -> **Mettre à jour**.

### Utilisation de modèles de fabrique de données
1. Cliquez sur **Fichier** dans le menu, pointez sur **Nouveau**, puis cliquez sur **Projet**.
2. Dans la boîte de dialogue **Nouveau projet**, procédez comme suit :
   
   1. Sous **Modèles**, sélectionnez **DataFactory**.
   2. Dans le volet droit, sélectionnez **Modèles de fabrique de données**.
   3. Entrez un **nom** pour le projet.
   4. Sélectionnez un **emplacement** pour le projet.
   5. Cliquez sur **OK**.
      
      ![Boîte de dialogue Nouveau projet](./media/data-factory-samples/vs-new-project-adf-templates.png)
3. Dans la boîte de dialogue **Modèles de fabrique de données**, sélectionnez l’exemple de modèle dans la section **Modèles de cas d’utilisation**, puis cliquez sur **Suivant**. La procédure suivante décrit l’utilisation du modèle **Analyse des profils des clients**. Les étapes sont similaires pour les autres exemples.
   
    ![Boîte de dialogue Modèles de fabrique de données](./media/data-factory-samples/vs-data-factory-templates-dialog.png)
4. Dans la boîte de dialogue **Configuration de Data Factory**, cliquez sur **Suivant** dans la page **Principes de base de Data Factory**.
5. Dans la page **Configurer une fabrique de données**, procédez comme suit :
   1. Pour les besoins de cette procédure pas à pas, sélectionnez **Créer une fabrique de données**. Vous pouvez également sélectionner **Utiliser une fabrique de données existante**.
   2. Entrez un **nom** pour la fabrique de données.
   3. Sélectionnez l’**abonnement Azure** dans lequel vous voulez créer la fabrique de données.
   4. Sélectionnez le **groupe de ressources** pour la fabrique de données.
   5. Sélectionnez **États-Unis de l’Ouest**, **États-Unis de l’Est** ou **Europe du Nord** pour la **région**.
   6. Cliquez sur **Next**.
6. Dans la page **Configurer des banques de données**, spécifiez une **base de données SQL Azure** et un **compte de stockage Azure** existants, ou créez-en des nouveaux, puis cliquez sur Suivant.
7. Dans la page **Configurer le calcul**, sélectionnez les valeurs par défaut, puis cliquez sur **Suivant**.
8. Dans la page **Résumé**, passez en revue tous les paramètres, puis cliquez sur **Suivant**.
9. Dans la page **État du déploiement** , patientez jusqu’à ce que le déploiement soit terminé, puis cliquez sur **Terminer**.
10. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**.
11. Si la boîte de dialogue **Connectez-vous à votre compte Microsoft** s'affiche, entrez vos informations d'identification pour le compte qui dispose de l'abonnement Azure, puis cliquez sur **Se connecter**.
12. La boîte de dialogue suivante doit s’afficher :
    
    ![Boîte de dialogue Publier](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
13. Dans la page **Configurer une fabrique de données**, procédez comme suit :
    
    1. Confirmez l’option **Utiliser une fabrique de données existante**.
    2. Sélectionnez la **fabrique de données** que vous aviez sélectionnée en utilisant le modèle ci-dessus.
    3. Cliquez sur **Suivant** pour basculer vers la page **Publier des éléments**. (utilisez la touche **TABULATION** pour passer au champ Nom si le bouton **Suivant** est désactivé.)
14. Dans la page **Publier des éléments**, vérifiez que toutes les entités de fabriques de données sont sélectionnées, puis cliquez sur **Suivant** pour basculer vers la page **Résumé**.
15. Passez en revue le résumé, puis cliquez sur **Suivant** pour démarrer le processus de déploiement et afficher l’**état du déploiement**.
16. Dans la page **État du déploiement**, vous devez voir l’état du processus de déploiement. Une fois le déploiement terminé, cliquez sur Terminer.

Pour plus d’informations sur l’utilisation de Visual Studio pour créer des entités Data Factory et les publier sur Azure, consultez [Créer votre première fabrique de données (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md).

<!---HONumber=AcomDC_0810_2016-->