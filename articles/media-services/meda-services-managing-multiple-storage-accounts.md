---
title: Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage | Microsoft Docs
description: Cet article vous donne des conseils sur la gestion des éléments multimédias Media Services dans plusieurs comptes de stockage.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="managing-media-services-assets-across-multiple-storage-accounts"></a>Gestion des éléments multimédias Media Services sur plusieurs comptes de stockage
À partir de Microsoft Azure Media Services 2.2, vous pouvez associer plusieurs comptes de stockage à un seul compte Media Services. L’option d’association de plusieurs comptes de stockage à un compte Media Services offre les avantages suivants :

* Gestion de vos éléments multimédias sur plusieurs comptes de stockage.
* Mais aussi, mise à l'échelle de Media Services pour traiter de grandes quantités de contenu (car actuellement un compte de stockage unique est limité à un maximum de 500 To). 

Cette rubrique montre comment associer plusieurs comptes de stockage à un compte Media Services à l’aide de l’API REST de gestion des services Azure. Elle montre également comment spécifier différents comptes de stockage lors de la création d'éléments multimédias à l'aide du Kit de développement logiciel (SDK) Media Services. 

## <a name="considerations"></a>Considérations
Quand vous associez plusieurs comptes de stockage à votre compte Media Services, tenez compte des points suivants :

* Tous les comptes de stockage associés à un compte Media Services doivent être dans le même centre de données que le compte Media Services.
* Actuellement, une fois qu'un compte de stockage est associé au compte Media Services spécifié, il ne peut pas en être dissocié.
* Le compte de stockage principal est celui indiqué au moment de la création du compte Media Services. Actuellement, vous ne pouvez pas modifier le compte de stockage par défaut. 

Autres points à considérer :

Media Services utilise la valeur de la propriété **IAssetFile.Name** durant la génération d’URL pour le contenu de diffusion en streaming (par exemple, http://{WAMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Pour cette raison, l’encodage par pourcentage n’est pas autorisé. La valeur de la propriété Name ne peut pas comporter les [caractères réservés à l’encodage en pourcentage](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) suivants : !*'();:@&=+$,/?%#[]". En outre, il ne peut y avoir qu’un « . » pour l’extension de nom de fichier.

## <a name="to-attach-a-storage-account-with-azure-service-management-rest-api"></a>Pour associer un compte de stockage à l'aide de l'API REST de gestion des services Azure
Actuellement, le seul moyen d'associer plusieurs comptes de stockage consiste à utiliser l' [API REST de gestion des services Azure](http://msdn.microsoft.com/library/azure/dn167014.aspx). L’exemple de code indiqué dans la rubrique [Procédure : utiliser l’API REST de gestion de Media Services](https://msdn.microsoft.com/library/azure/dn167656.aspx) définit la méthode **AttachStorageAccountToMediaServiceAccount** qui associe un compte de stockage au compte Media Services spécifié. Le code indiqué dans cette même rubrique définit la méthode **ListStorageAccountDetails** qui répertorie tous les comptes de stockage associés au compte Media Services spécifié.

## <a name="to-manage-media-services-assets-across-multiple-storage-accounts"></a>Pour gérer des éléments multimédias Media Services dans plusieurs comptes de stockage
Le code suivant utilise le dernier Kit de développement logiciel (SDK) Media Services pour effectuer les tâches suivantes :

1. Affichage de tous les comptes de stockage associés au compte Media Services spécifié.
2. Récupération du nom du compte de stockage par défaut.
3. Création d'un élément multimédia dans le compte de stockage par défaut.
4. Création d'un élément multimédia de sortie de la tâche d'encodage dans le compte de stockage spécifié.
   
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System;
        using System.Collections.Generic;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace MultipleStorageAccounts
        {
            class Program
            {
                // Location of the media file that you want to encode. 
                private static readonly string _singleInputFilePath =
                    Path.GetFullPath(@"../..\supportFiles\multifile\interview2.wmv");
   
                private static readonly string MediaServicesAccountName = 
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string MediaServicesAccountKey = 
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                private static CloudMediaContext _context;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    MediaServicesAccountName,
                                    MediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);

                    // Display the storage accounts associated with 
                    // the specified Media Services account:
                    foreach (var sa in _context.StorageAccounts)
                        Console.WriteLine(sa.Name);

                    // Retrieve the name of the default storage account.
                    var defaultStorageName = _context.StorageAccounts.Where(s => s.IsDefault == true).FirstOrDefault();
                    Console.WriteLine("Name: {0}", defaultStorageName.Name);
                    Console.WriteLine("IsDefault: {0}", defaultStorageName.IsDefault);

                    // Retrieve the name of a storage account that is not the default one.
                    var notDefaultStroageName = _context.StorageAccounts.Where(s => s.IsDefault == false).FirstOrDefault();
                    Console.WriteLine("Name: {0}", notDefaultStroageName.Name);
                    Console.WriteLine("IsDefault: {0}", notDefaultStroageName.IsDefault);

                    // Create the original asset in the default storage account.
                    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, 
                        defaultStorageName.Name, _singleInputFilePath);
                    Console.WriteLine("Created the asset in the {0} storage account", asset.StorageAccountName);

                    // Create an output asset of the encoding job in the other storage account.
                    IAsset outputAsset = CreateEncodingJob(asset, notDefaultStroageName.Name, _singleInputFilePath);
                    if(outputAsset!=null)
                        Console.WriteLine("Created the output asset in the {0} storage account", outputAsset.StorageAccountName);

                }

                static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string storageName, string singleFilePath)
                {
                    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();

                    // If you are creating an asset in the default storage account, you can omit the StorageName parameter.
                    var asset = _context.Assets.Create(assetName, storageName, assetCreationOptions);

                    var fileName = Path.GetFileName(singleFilePath);

                    var assetFile = asset.AssetFiles.Create(fileName);

                    Console.WriteLine("Created assetFile {0}", assetFile.Name);

                    assetFile.Upload(singleFilePath);

                    Console.WriteLine("Done uploading {0}", assetFile.Name);

                    return asset;
                }

                static IAsset CreateEncodingJob(IAsset asset, string storageName, string inputMediaFilePath)
                {
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My encoding job");
                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My encoding task",
                        processor,
                        "H264 Multiple Bitrate 720p",
                        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(asset);
                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew("Output asset", storageName,
                        AssetCreationOptions.None);

                    // Use the following event handler to check job progress.  
                    job.StateChanged += new
                            EventHandler<JobStateChangedEventArgs>(StateChanged);

                    // Launch the job.
                    job.Submit();

                    // Check job execution and wait for job to finish. 
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
                    progressJobTask.Wait();

                    // Get an updated job reference.
                    job = GetJob(job.Id);

                    // If job state is Error the event handling 
                    // method for job progress should log errors.  Here we check 
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        Console.WriteLine("\nExiting method due to job error.");
                        return null;
                    }

                    // Get a reference to the output asset from the job.
                    IAsset outputAsset = job.OutputMediaAssets[0];

                    return outputAsset;
                }


                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }

                private static void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);

                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("********************");
                            Console.WriteLine("Job is finished.");
                            Console.WriteLine("Please wait while local tasks or downloads complete...");
                            Console.WriteLine("********************");
                            Console.WriteLine();
                            Console.WriteLine();
                            break;
                        case JobState.Canceling:
                        case JobState.Queued:
                        case JobState.Scheduled:
                        case JobState.Processing:
                            Console.WriteLine("Please wait...\n");
                            break;
                        case JobState.Canceled:
                        case JobState.Error:
                            // Cast sender as a job.
                            IJob job = (IJob)sender;
                            // Display or log error details as needed.
                            Console.WriteLine("An error occurred in {0}", job.Id);
                            break;
                        default:
                            break;
                    }
                }

                static IJob GetJob(string jobId)
                {
                    // Use a Linq select query to get an updated 
                    // reference by Id. 
                    var jobInstance =
                        from j in _context.Jobs
                        where j.Id == jobId
                        select j;
                    // Return the job reference as an Ijob. 
                    IJob job = jobInstance.FirstOrDefault();

                    return job;
                }
            }
        }


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO2-->


