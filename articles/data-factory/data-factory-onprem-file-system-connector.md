---
title: Déplacer des données vers et depuis un système de fichiers | Microsoft Docs
description: Apprenez à déplacer des données vers et depuis un système de fichiers local à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jingwang

---
# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Déplacer des données vers et depuis un système de fichiers local à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie Azure Data Factory pour déplacer des données vers et depuis un système de fichiers local. Consultez la page [Sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores) pour obtenir une liste des magasins de données pouvant être utilisés comme sources ou récepteurs avec le système de fichiers local. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Data Factory prend en charge la connexion vers et depuis un système de fichiers local via la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle.

> [!NOTE]
> En dehors de la passerelle de gestion des données, aucun autre fichier binaire n’a besoin d’être installé pour communiquer vers et depuis un système de fichiers local.
> 
> Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.
> 
> 

## <a name="linux-file-share"></a>Partage de fichiers Linux
Effectuez les deux étapes suivantes pour utiliser un partage de fichiers Linux avec le service lié de serveur de fichiers :

* Installez [Samba](https://www.samba.org/) sur votre serveur Linux.
* Installez et configurez la passerelle de gestion des données sur un serveur Windows. L’installation de la passerelle de gestion des données sur un serveur Linux n'est pas prise en charge.

## <a name="copy-wizard"></a>Assistant de copie
Le moyen le plus simple de créer un pipeline qui copie les données vers et depuis un système de fichiers local consiste à utiliser l’Assistant Copie. Pour obtenir une description rapide, consultez [Didacticiel : créer un pipeline à l’aide de l’Assistant Copie](data-factory-copy-data-wizard-tutorial.md).

Les exemples suivants présentent des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide [du Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [de Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données vers et depuis un système de fichiers local et Stockage Blob Azure. Toutefois, vous pouvez copier les données *directement* à partir de n’importe quelle source vers l’un des récepteurs répertoriés dans [Sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores) à l’aide de l’activité de copie dans Azure Data Factory.

## <a name="sample:-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Exemple : Copie de données depuis un système de fichiers local vers Stockage Blob Azure
Cet exemple indique comment copier des données depuis un système de fichiers local vers Stockage Blob Azure.

L’exemple contient les entités Data Factory suivantes :

* Un service lié de type [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
* Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
* Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
* Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Le [pipeline](data-factory-create-pipelines.md) avec l’activité de copie qui utilise [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple suivant copie toutes les heures des données d’une série horaire d’un système de fichiers local vers Stockage Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Pour commencer, configurez la passerelle de gestion des données selon les instructions décrites dans [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md).

**Service lié de serveur de fichiers local :**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Nous vous recommandons d’utiliser la propriété **encryptedCredential** plutôt que les propriétés **userid** et **password**. Consultez la page [Service lié de serveur de fichiers](#onpremisesfileserver-linked-service-properties) pour plus d’informations sur ce service lié.

**Service lié Azure Storage :**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Jeu de données d’entrée de système de fichiers local :**

Les données sont récupérées à partir d’un nouveau fichier toutes les heures. Les propriétés folderPath et fileName sont déterminées en fonction de l’heure de début de la tranche.  

La définition de `"external": "true"` informe Data Factory que le jeu de données est externe à la Data Factory et non produit par une activité dans la Data Factory.

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Jeu de données de sortie Stockage Blob Azure :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **FileSystemSource** et le type **sink** est défini sur **BlobSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "FileSystemSource"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="sample:-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Exemple : Copie de données depuis Azure SQL Database vers un système de fichiers local
L’exemple suivant montre :

* Un service lié de type AzureSqlDatabase.
* Un service lié de type OnPremisesFileServer.
* Un jeu de données d'entrée de type AzureSqlTable.
* Un jeu de données de sortie de type FileShare.
* Un pipeline avec une activité de copie qui utilise SqlSource et FileSystemSink.

L’exemple copie chaque heure des données de série horaire à partir d’une table SQL Azure vers un système de fichiers local. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié SQL Azure :**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Service lié de serveur de fichiers local :**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Nous vous recommandons d’utiliser la propriété **encryptedCredential** plutôt que les propriétés **userid** et **password**. Consultez la page [Service lié de système de fichiers](#onpremisesfileserver-linked-service-properties) pour plus d’informations sur ce service lié.

**Jeu de données d'entrée SQL Azure :**

L’exemple suppose que vous avez créé une table « MyTable » dans SQL Azure et qu’elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de ``“external”: ”true”`` informe Data Factory que le jeu de données est externe à la Data Factory et non produit par une activité dans la Data Factory.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Jeu de données de sortie de système de fichiers local :**

Les données sont copiées vers un nouveau fichier toutes les heures. Les paramètres folderPath et fileName pour l’objet Blob sont déterminés en fonction de l’heure de début de la tranche.

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
          "partitionedBy": [
            {
              "name": "Year",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyy"
              }
            },
            {
              "name": "Month",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ]
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Pipeline avec une activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **SqlSource** et le type **sink** est défini sur **FileSystemSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="on-premises-file-server-linked-service-properties"></a>Propriétés du service lié de serveur de fichiers local
Vous pouvez lier un système de fichiers local à une fabrique de données Azure avec le service lié Serveur de fichiers local. Le tableau suivant décrit les éléments JSON spécifiques au service lié Serveur de fichiers local.

| Propriété | Description | Requis |
| --- | --- | --- |
| type |Vérifiez que la propriété type est définie sur **OnPremisesFileServer**. |Oui |
| host |Spécifie le chemin d’accès racine du dossier que vous souhaitez copier. Utilisez le caractère d’échappement « \ » pour les caractères spéciaux contenus dans la chaîne. Consultez la section [Exemples de définitions de jeux de données et de service liés](#sample-linked-service-and-dataset-definitions) pour obtenir des exemples. |Oui |
| userId |Spécifiez l’ID de l’utilisateur qui a accès au serveur. |Non (si vous choisissez encryptedcredential) |
| password |Spécifiez le mot de passe de l’utilisateur (userid). |Non (si vous choisissez encryptedcredential) |
| Encryptedcredential |Spécifiez les informations d’identification chiffrées que vous pouvez obtenir en exécutant l’applet de commande New-AzureRmDataFactoryEncryptValue. |Non (si vous choisissez de spécifier un nom d'utilisateur et un mot de passe en texte brut) |
| gatewayName |Spécifie le nom de la passerelle que Data Factory doit utiliser pour se connecter au serveur de fichiers local. |Oui |

Pour plus d’informations sur la définition des informations d’identification pour une source de données d’un système de fichiers local, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)

### <a name="sample-linked-service-and-dataset-definitions"></a>Exemples de définitions de jeux de données et de service liés
| Scénario | Hôte dans la définition du service lié | folderPath dans la définition du jeu de données |
| --- | --- | --- |
| Dossier local sur l’ordinateur passerelle de gestion des données :  <br/><br/>Exemples : D:\\\* ou D:\dossier\sous-dossier\\* |D:\\\\ (pour la passerelle de gestion des données 2.0 et versions ultérieures) <br/><br/> hôte local (pour les versions de la passerelle de gestion des données antérieures à 2.0) |.\\\\ ou dossier\\\\sous-dossier (pour la passerelle de gestion des données 2.0 et versions ultérieures) <br/><br/>D:\\\\ ou D:\\\\dossier\\\\sous-dossier (pour les versions de la passerelle antérieures à 2.0) |
| Dossier partagé distant :  <br/><br/>Exemples : \\\\myserver\\share\\\* ou \\\\myserver\\share\\dossier\\sous-dossier\\* |\\\\\\\\myserver\\\\share |.\\\\ ou dossier\\\\sous-dossier |

**Pour trouver la version d’une passerelle :**

1. Lancez le [Gestionnaire de configuration de la passerelle de gestion des données](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) sur votre ordinateur.
2. Basculez vers l’onglet **Aide** .

> [!NOTE]
> Il est recommandé que vous [mettiez à niveau votre passerelle vers la version 2.0 ou une version ultérieure](data-factory-data-management-gateway.md#update-data-management-gateway) pour tirer parti des dernières fonctionnalités et des derniers correctifs.
> 
> 

**Exemple : utilisation d’un nom d'utilisateur et d’un mot de passe en texte brut**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**Exemple : utilisation de l’élément encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "D:\\",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## <a name="on-premises-file-system-dataset-type-properties"></a>Propriétés du type de jeu de données de système de fichiers local
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données.

La section typeProperties est différente pour chaque type de jeu de données. Elle fournit des informations telles que l’emplacement et le format des données dans la banque de données. La section typeProperties pour le jeu de données de type **FileShare** a les propriétés suivantes :

| Propriété | Description | Requis |
| --- | --- | --- |
| folderPath |Spécifie le sous-chemin vers le dossier. Utilisez le caractère d’échappement « \ » pour les caractères spéciaux contenus dans la chaîne. Consultez la section [Exemples de définitions de jeux de données et de service liés](#sample-linked-service-and-dataset-definitions) pour obtenir des exemples.<br/><br/>Vous pouvez également effectuer une combinaison avec la propriété **partitionBy** pour que les chemins d’accès de dossier soient basés sur les dates et heures de démarrage et d’arrêt de la tranche. |Oui |
| fileName |Spécifiez le nom du fichier dans l’élément **folderPath** si vous souhaitez que la table se réfère à un fichier spécifique du dossier. Si vous ne spécifiez aucune valeur pour cette propriété, le tableau pointe vers tous les fichiers du dossier.<br/><br/>Lorsque fileName n’est pas spécifié pour un jeu de données de sortie, le nom du fichier généré est au format suivant : <br/><br/>`Data.<Guid>.txt` (Exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |Non |
| partitionedBy |partitionedBy peut être utilisé pour spécifier un folderPath/fileName dynamique pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. |Non |
| Format |Les types de formats suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** et **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour plus d’informations, consultez [Définition de TextFormat](#specifying-textformat), [Définition d’AvroFormat](#specifying-avroformat), [Définition de JsonFormat](#specifying-jsonformat), [Définition d’OrcFormat](#specifying-orcformat) et [Définition de ParquetFormat](#specifying-parquetformat). Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), vous pouvez ignorer la section Format dans les deux définitions de jeu de données d’entrée et de sortie. |Non |
| fileFilter |Spécifiez un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers. <br/><br/>Les valeurs autorisées sont : `*` (plusieurs caractères) et `?` (caractère unique).<br/><br/>Exemple 1 : « fileFilter » : « *.log »<br/>Exemple 2 : « fileFilter » : « 2014-1-?.txt »<br/><br/>Remarque : fileFilter s’applique à un jeu de données FileShare d’entrée. |Non |
| compression |Spécifiez le type et le niveau de compression pour les données. Les types pris en charge sont **GZip**, **Deflate** et **BZip2**. Les niveaux pris en charge sont **Optimal** et **Fastest**. Pour l’instant, les paramètres de compression ne sont pas pris en charge pour les données au format **AvroFormat** ou **OrcFormat**. Pour plus d’informations, consultez la section [Prise en charge de la compression](#compression-support). |Non |

> [!NOTE]
> Vous ne pouvez pas utiliser fileName et fileFilter simultanément.
> 
> 

### <a name="using-partitionedby-property"></a>Utilisation de la propriété partitionedBy
Comme mentionné dans la section précédente, vous pouvez spécifier un folderPath et un fileName dynamiques pour les données de série chronologique avec partitionedBy. Vous pouvez le faire avec les macros Data Factory et les variables système SliceStart et SliceEnd, qui indiquent la période logique d’une tranche de données spécifique.

Consultez les articles [Création de jeux de données](data-factory-create-datasets.md), [Planification et exécution](data-factory-scheduling-and-execution.md) et [Création de pipelines](data-factory-create-pipelines.md) pour mieux comprendre les jeux de données de série chronologique, la planification et les segments.

#### <a name="sample-1:"></a>Exemple 1 :
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple, {Slice} est remplacé par la valeur de la variable système Data Factory SliceStart au format (AAAAMMJJHH). SliceStart fait référence à l’heure de début de la tranche. folderPath est différent pour chaque segment. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2:"></a>Exemple 2 :
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

Dans cet exemple, l’année, le mois, le jour et l’heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="file-share-copy-activity-type-properties"></a>Propriétés du type d’activité de copie de partage de fichiers
**FileSystemSource** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| recursive |Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. |True, False (par défaut) |Non |

**FileSystemSink** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| copyBehavior |Cette propriété définit le comportement de copie lorsque la source est BlobSource ou FileSystem. |**PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible. Le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.<br/><br/>**FlattenHierarchy**: tous les fichiers du dossier source sont créés dans le premier niveau du dossier cible. Les fichiers cibles sont créés avec un nom généré automatiquement.<br/><br/>**MergeFiles** : fusionne tous les fichiers du dossier source dans un même fichier. Si le nom d’objet blob ou le nom de fichier est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, il s’agit d’un nom de fichier généré automatiquement. |Non |

### <a name="recursive-and-copybehavior-examples"></a>exemples de valeurs recursive et copyBehavior
Cette section décrit le comportement résultant de l’opération de copie pour différentes combinaisons de valeurs pour les propriétés recursive et copyBehavior.

| valeur recursive | valeur copyBehavior | Comportement résultant |
| --- | --- | --- |
| true |preserveHierarchy |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible nommé Dossier1 est créé et structuré de la même manière que la source :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5 |
| true |flattenHierarchy |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier5 |
| true |mergeFiles |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible Dossier1 est créé et structuré comme suit : <br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 + Fichier3 + Fichier4 + Fichier5 est fusionné dans un fichier avec le nom de fichier généré automatiquement. |
| false |preserveHierarchy |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible nommé Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |flattenHierarchy |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible nommé Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nom généré automatiquement pour Fichier2<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |
| false |mergeFiles |Pour un dossier source nommé Dossier1 et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fichier2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sousdossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fichier5<br/><br/>le dossier cible nommé Dossier1 est créé et structuré comme suit :<br/><br/>Dossier1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Le contenu de Fichier1 + Fichier2 est fusionné dans un fichier avec un nom de fichier généré automatiquement.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nom généré automatiquement pour Fichier1<br/><br/>Sous-dossier1, où Fichier3, Fichier4 et Fichier5 ne sont pas sélectionnés. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
 Consultez le [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!--HONumber=Oct16_HO2-->


