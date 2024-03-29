---
title: Utilisation d’Azure Media Analytics pour convertir le contenu texte de fichiers vidéo en texte numérique | Microsoft Docs
description: Azure Media Analytics OCR (reconnaissance optique de caractères) vous permet de convertir le contenu texte de fichiers vidéo en un texte numérique modifiable et pouvant faire l’objet d’une recherche.  Vous pouvez ainsi automatiser l’extraction de métadonnées explicites à partir du signal vidéo de votre contenu multimédia.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Utilisation d’Azure Media Analytics pour convertir le contenu texte de fichiers vidéo en texte numérique
## <a name="overview"></a>Vue d'ensemble
Si vous devez extraire le contenu texte de vos fichiers vidéo et générer un texte numérique modifiable et pouvant faire l’objet d’une recherche, vous devez utiliser Azure Media Analytics OCR (reconnaissance optique de caractères). Ce processeur multimédia Azure détecte le contenu texte de vos fichiers vidéo et génère les fichiers texte à utiliser. La reconnaissance optique de caractères vous permet d’automatiser l’extraction de métadonnées explicites à partir du signal vidéo de votre contenu multimédia.

Lorsque vous l’utilisez conjointement avec un moteur de recherche, vous pouvez facilement indexer vos données multimédia par texte et améliorer ainsi la détectabilité du contenu. Cela est particulièrement utile dans une vidéo contenant beaucoup de texte comme un enregistrement vidéo ou une capture d’écran de diaporama. Le processeur multimédia Azure OCR est optimisé pour le texte numérique.

Le processeur multimédia **Azure Media OCR** est actuellement en version préliminaire.

Cette rubrique donne des informations détaillées sur **Azure Media OCR** et montre comment l’utiliser avec le SDK Media Services pour .NET. Pour plus d’informations et d’exemples, consultez [ce blog](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Fichiers d'entrée OCR
Fichiers vidéo. Les formats suivants sont actuellement pris en charge : MP4, MOV et WMV.

## <a name="task-configuration"></a>Configuration de la tâche
Configuration de la tâche (préconfiguration). Lors de la création d’une tâche **Azure Media OCR**, vous devez spécifier une présélection de configuration avec JSON ou XML. 

### <a name="attribute-descriptions"></a>Descriptions des attributs
| Nom de l’attribut | Description |
| --- | --- |
| Langage |(facultatif) indique la langue du texte à rechercher. Une des valeurs suivantes : AutoDetect (par défaut), Arabic, ChineseSimplified, ChineseTraditional, Czech, Danish, Dutch, English, Finnish, French, German, Greek, Hungarian, Italian, Japanese, Korean, Norwegian, Polish, Portuguese, Romanian, Russian, SerbianCyrillic, SerbianLatin, Slovak, Spanish, Swedish, Turkish. |
| TextOrientation |(facultatif) indique l’orientation du texte à rechercher.  « Left » signifie que la partie supérieure de toutes les lettres pointe vers la gauche.  Par défaut, le texte peut être affiché en mode « Up » (comme dans un livre).  Une des valeurs suivantes : AutoDetect (par défaut), Up, Right, Down, Left. |
| TimeInterval |(facultatif) décrit le taux d’échantillonnage.  La valeur par défaut est chaque demie seconde.<br/>Format JSON – HH:mm:ss.SSS (par défaut : 00:00:00.500)<br/>Format XML – durée de la primitive W3C XSD (par défaut PT0.5) |
| DetectRegions |(facultatif) Tableau d’objets DetectRegion spécifiant des zones au sein de l’image vidéo dans lesquelles détecter le texte.<br/>Un objet DetectRegion est constitué de quatre valeurs entières suivantes :<br/>Gauche – pixels à partir de la marge de gauche<br/>Haut – pixels à partir de la marge supérieure<br/>Largeur – largeur de la zone en pixels<br/>Height – hauteur de la zone en pixels |

#### <a name="json-preset-example"></a>Exemple de présélection JSON
    {
        'Version':'1.0', 
        'Options': 
        {
        'Language':'English', 
            'TimeInterval':'00:00:01.5',
            'DetectRegions': 
             [
                {'Left':'1','Top':'1','Width':'1','Height':'1'},
                {'Left':'2','Top':'2','Width':'2','Height':'2'}
             ],
            'TextOrientation':'Up'
        }
    }

#### <a name="xml-preset-example"></a>Exemple de présélection XML
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>1</Left>
                   <Top>1</Top>
                   <Width>1</Width>
                   <Height>1</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>

## <a name="ocr-output-files"></a>Fichiers de sortie OCR
La sortie du processeur multimédia OCR est un fichier JSON.

### <a name="elements-of-the-output-json-file"></a>Éléments du fichier de sortie JSON
La sortie vidéo OCR fournit des données temporelles segmentées sur les caractères présents dans votre vidéo.  Vous pouvez utiliser des attributs tels que la langue ou l’orientation pour cibler exactement les mots que vous souhaitez analyser. 

La sortie contient les attributs suivants :

| Élément | Description |
| --- | --- |
| Échelle de temps |« cycles » par seconde de la vidéo |
| Offset |décalage des horodatages. Cette valeur sera toujours 0 dans la version 1.0 des API vidéo. |
| Framerate |Images par seconde de la vidéo |
| width |largeur de la vidéo en pixels |
| height |hauteur de la vidéo en pixels |
| Fragments |tableau de segments de temps de vidéo dans lequel les métadonnées sont mémorisées en bloc |
| start |heure de début d’un fragment en « cycles » |
| duration |durée d’un fragment en « cycles » |
| interval |intervalle de chaque événement dans le fragment donné |
| événements |tableau contenant des régions |
| region |objet représentant des mots ou expressions détectés |
| Langage |langue du texte détecté dans une région |
| orientation |orientation du texte détecté dans une région |
| lignes |tableau de lignes de texte détecté dans une région |
| texte |le texte réel |

### <a name="json-output-example"></a>Exemple de sortie JSON
L’exemple de sortie suivant contient des informations générales sur la vidéo et plusieurs fragments vidéo. Chaque fragment vidéo contient toutes les régions détectées par OCR MP selon la langue et l’orientation du texte choisies. La région contient également chaque ligne de mot dans cette région, avec le texte et la position de cette ligne, et toutes les informations sur ce mot (contenu, position et niveau de confiance du mot). En voici un exemple (avec mes commentaires).

    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }

## <a name="sample-code"></a>Exemple de code
Le programme suivant montre comment effectuer les tâches suivantes :

1. Créer un élément multimédia et charger un fichier multimédia dans l’élément multimédia.
2. Créer une tâche avec un fichier de configuration/présélection OCR.
3. Télécharger les fichiers JSON de sortie. 
   
        using System;
        using System.Configuration;
        using System.IO;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;
        using System.Threading;
        using System.Threading.Tasks;
   
        namespace OCR
        {
            class Program
            {
                // Read values from the App.config file.
                private static readonly string _mediaServicesAccountName =
                    ConfigurationManager.AppSettings["MediaServicesAccountName"];
                private static readonly string _mediaServicesAccountKey =
                    ConfigurationManager.AppSettings["MediaServicesAccountKey"];
   
                // Field for service context.
                private static CloudMediaContext _context = null;
                private static MediaServicesCredentials _cachedCredentials = null;
   
                static void Main(string[] args)
                {
   
                    // Create and cache the Media Services credentials in a static class variable.
                    _cachedCredentials = new MediaServicesCredentials(
                                    _mediaServicesAccountName,
                                    _mediaServicesAccountKey);
                    // Used the cached credentials to create CloudMediaContext.
                    _context = new CloudMediaContext(_cachedCredentials);
   
                    // Run the OCR job.
                    var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                                @"C:\supportFiles\OCR\config.json");
   
                    // Download the job output asset.
                    DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
                }
   
                static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
                {
                    // Create an asset and upload the input media file to storage.
                    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                        "My OCR Input Asset",
                        AssetCreationOptions.None);
   
                    // Declare a new job.
                    IJob job = _context.Jobs.Create("My OCR Job");
   
                    // Get a reference to Azure Media OCR.
                    string MediaProcessorName = "Azure Media OCR";
   
                    var processor = GetLatestMediaProcessorByName(MediaProcessorName);
   
                    // Read configuration from the specified file.
                    string configuration = File.ReadAllText(configurationFile);
   
                    // Create a task with the encoding details, using a string preset.
                    ITask task = job.Tasks.AddNew("My OCR Task",
                        processor,
                        configuration,
                        TaskOptions.None);
   
                    // Specify the input asset.
                    task.InputAssets.Add(asset);
   
                    // Add an output asset to contain the results of the job.
                    task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);
   
                    // Use the following event handler to check job progress.  
                    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
   
                    // Launch the job.
                    job.Submit();
   
                    // Check job execution and wait for job to finish.
                    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
   
                    progressJobTask.Wait();
   
                    // If job state is Error, the event handling
                    // method for job progress should log errors.  Here we check
                    // for error state and exit if needed.
                    if (job.State == JobState.Error)
                    {
                        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                        Console.WriteLine(string.Format("Error: {0}. {1}",
                                                        error.Code,
                                                        error.Message));
                        return null;
                    }
   
                    return job.OutputMediaAssets[0];
                }
   
                static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
                {
                    IAsset asset = _context.Assets.Create(assetName, options);
   
                    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                    assetFile.Upload(filePath);
   
                    return asset;
                }
   
                static void DownloadAsset(IAsset asset, string outputDirectory)
                {
                    foreach (IAssetFile file in asset.AssetFiles)
                    {
                        file.Download(Path.Combine(outputDirectory, file.Name));
                    }
                }
   
                static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors
                        .Where(p => p.Name == mediaProcessorName)
                        .ToList()
                        .OrderBy(p => new Version(p.Version))
                        .LastOrDefault();
   
                    if (processor == null)
                        throw new ArgumentException(string.Format("Unknown media processor",
                                                                   mediaProcessorName));
   
                    return processor;
                }
   
                static private void StateChanged(object sender, JobStateChangedEventArgs e)
                {
                    Console.WriteLine("Job state changed event:");
                    Console.WriteLine("  Previous state: " + e.PreviousState);
                    Console.WriteLine("  Current state: " + e.CurrentState);
   
                    switch (e.CurrentState)
                    {
                        case JobState.Finished:
                            Console.WriteLine();
                            Console.WriteLine("Job is finished.");
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
                            // LogJobStop(job.Id);
                            break;
                        default:
                            break;
                    }
                }
   
            }
        }

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Liens connexes
[Vue d’ensemble d’Azure Media Services Analytics](media-services-analytics-overview.md)

<!--HONumber=Oct16_HO2-->


