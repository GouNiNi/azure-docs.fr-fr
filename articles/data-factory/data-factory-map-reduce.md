---
title: Appeler le programme MapReduce à partir d'Azure Data Factory
description: Learn how to process data by running MapReduce programs on an Azure HDInsight cluster from an Azure data factory.
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
ms.date: 09/12/2016
ms.author: spelluru

---
# Appeler des programmes MapReduce à partir de Data Factory
L’activité MapReduce de HDInsight dans un [pipeline](data-factory-create-pipelines.md) Data Factory exécute des programmes MapReduce sur votre cluster HDInsight [propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) sous Windows ou Linux. Cet article s'appuie sur l'article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

## Introduction
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l'utilisation de l’activité MapReduce de HDInsight.

Consultez [Pig](data-factory-pig-activity.md) et [Hive](data-factory-hive-activity.md) pour plus d’informations sur l’exécution de scripts Pig/Hive sur un cluster HDInsight sous Windows ou Linux à partir d’un pipeline à l’aide des activités Pig et Hive de HDInsight.

## JSON pour l’activité MapReduce de HDInsight
Dans la définition JSON de l’activité HDInsight :

1. Affectez au **type** de l’**activité** la valeur **HDInsight**.
2. Spécifiez le nom de la classe pour la propriété **className**.
3. Spécifiez le chemin d’accès du fichier JAR, ainsi que le nom de fichier, pour la propriété **jarFilePath**.
4. Spécifiez le service lié qui fait référence au stockage d’objets blob Azure contenant le fichier JAR pour la propriété **jarLinkedService**.
5. Spécifiez les arguments du programme MapReduce dans la section **arguments**. Lors de l’exécution, vous verrez quelques arguments supplémentaires (par exemple : mapreduce.job.tags) à partir de l’infrastructure MapReduce. Pour différencier vos arguments avec les arguments MapReduce, envisagez d’utiliser l’option et la valeur en tant qu’arguments comme indiqué dans l’exemple suivant (-s, --entrée, --sortie etc. sont des options immédiatement suivies par leurs valeurs).
   
        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }

Vous pouvez utiliser l’activité MapReduce de HDInsight pour exécuter un fichier jar MapReduce dans un cluster HDInsight. Dans l'exemple suivant de définition JSON d'un pipeline, l'activité HDInsight est configurée pour exécuter un fichier JAR Mahout.

## Exemple sur GitHub
Vous pouvez télécharger un exemple d’utilisation de l’activité MapReduce HDInsight à l’emplacement suivant : [Exemples Data Factory sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).

## Exécution du programme de nombre de mots
Le pipeline dans cet exemple exécute le programme Map/Reduce du nombre de mots sur votre cluster Azure HDInsight.

### Services liés
Tout d'abord, vous créez un service lié pour lier le stockage Azure qui est utilisé par le cluster Azure HDInsight à la fabrique de données Azure. Si vous copiez/collez le code suivant, n'oubliez pas de remplacer **nom de compte** et **clé de compte** par le nom et la clé de votre stockage Azure.

#### Service lié Azure Storage
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### Service lié Azure HDInsight
Tout d'abord, vous créez un service lié pour lier le cluster Azure HDInsight à la fabrique de données Azure. Si vous copiez/collez le code suivant, remplacez **le nom du cluster HDInsight** par le nom de votre cluster HDInsight et modifiez le nom d’utilisateur et le mot de passe.

    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### Groupes de données
#### Jeu de données de sortie
Le pipeline de cet exemple n’accepte pas d’entrées. Vous spécifiez un jeu de données de sortie pour l’activité MapReduce HDInsight. Il s’agit simplement d’un ensemble de données factice qui est nécessaire au fonctionnement de la planification de pipeline.

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### Pipeline
Le pipeline de cet exemple n'a qu'une seule activité de type : HDInsightMapReduce. Certaines propriétés importantes dans le JSON sont :

| Propriété | Remarques |
|:--- |:--- |
| type |Le type doit être défini sur **HDInsightMapReduce**. |
| className |Le nom de la classe est : **wordcount** |
| jarFilePath |Chemin d’accès au fichier jar contenant la classe. Si vous copiez/collez le code suivant, n'oubliez pas de modifier le nom du cluster. |
| jarLinkedService |Service Azure Storage lié qui contient le fichier jar. Ce service lié fait référence au stockage associé au cluster HDInsight. |
| arguments |Le programme de nombre de mots accepte deux arguments, une entrée et une sortie. Le fichier d'entrée est le fichier davinci.txt. |
| frequency/interval |Les valeurs de ces propriétés correspondent au jeu de données de sortie. |
| linkedServiceName |fait référence au service HDInsight lié créé précédemment. |

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## Exécuter des programmes Spark
Vous pouvez utiliser l'activité MapReduce pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez la page [Appeler des programmes Spark à partir d'Azure Data Factory](data-factory-spark.md) pour plus d'informations.

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## Voir aussi
* [Activité Hive](data-factory-hive-activity.md)
* [Activité pig](data-factory-pig-activity.md)
* [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
* [Appeler des programmes Spark](data-factory-spark.md)
* [Appeler des scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0914_2016-->