---
title: Affichage des journaux de diagnostic d’Azure Data Lake Analytics | Microsoft Docs
description: 'Comprendre comment configurer les journaux de diagnostic et y accéder pour Azure Data Lake Analytics '
services: data-lake-analytics
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/11/2016
ms.author: larryfr

---
# Accès aux journaux de diagnostic d’Azure Data Lake Analytics
Découvrez comment activer la journalisation de diagnostic pour votre compte Data Lake Analytics et comment afficher les journaux collectés pour votre compte.

Les organisations peuvent activer la journalisation de diagnostic pour leur compte Azure Data Lake Analytics, afin de collecter des pistes d’audit d’accès aux données. Ces journaux fournissent des informations comme :

* Une liste des utilisateurs qui ont accédé aux données.
* La fréquence à laquelle les données sont consultées.
* La quantité de données stockées dans le compte.

## Composants requis
* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Activation de votre abonnement Azure** pour la version préliminaire publique de Data Lake Analytics. Consultez les [instructions](data-lake-analytics-get-started-portal.md#signup).
* **Compte Azure Data Lake Analytics**. Suivez les instructions de [Prise en main du service Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

## Activation de la journalisation
1. Inscrivez-vous au nouveau [portail Azure](https://portal.azure.com).
2. Ouvrez votre compte Data Lake Analytics et dans le panneau de votre compte Data Lake Analytics, cliquez sur **Paramètres**, puis sur **Paramètres de diagnostic**.
3. Dans le panneau **Diagnostic**, apportez les modifications suivantes pour configurer la journalisation de diagnostic.
   
    ![Activation de la journalisation de diagnostic](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Activer la journalisation des diagnostics")
   
   * Définissez **État** sur **Activé** pour activer la journalisation de diagnostic.
   * Vous pouvez choisir de stocker/traiter les données de deux manières différentes.
     * Sélectionnez **Exporter vers Event Hub** pour transmettre les données de journal à un concentrateur d’événements Azure. Utilisez cette option si vous disposez d’un pipeline de traitement en aval pour analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les informations relatives au concentrateur d’événements Azure que vous souhaitez utiliser.
     * Sélectionnez **Exporter vers un compte de stockage** pour stocker les journaux sur un compte Azure Storage. Utilisez cette option si vous souhaitez archiver les données. Si vous sélectionnez cette option, vous devez fournir un compte Azure Storage sur lequel enregistrer les journaux.
   * Spécifiez si vous souhaitez obtenir des journaux d’audit ou des journaux de demande ou les deux.
   * Spécifiez le nombre de jours pendant lesquels les données doivent être conservées.
   * Cliquez sur **Save**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez consulter les journaux dans l’onglet **Journaux de diagnostic**.

## afficher les journaux ;
Il existe deux manières d’afficher les données de journal de votre compte Data Lake Analytics :

* à partir des paramètres de compte Data Lake Analytics ;
* à partir du compte Azure Storage dans lequel les données sont stockées.

### Utilisation de la vue des paramètres Data Lake Analytics
1. Dans le panneau **Paramètres** de votre compte Data Lake Analytics, cliquez sur **Journaux de diagnostic**.
   
    ![Afficher la journalisation de diagnostic](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic")
2. Dans le panneau **Journaux de diagnostic**, vous devez voir les journaux classés par **journaux d’audit** et **journaux de demande**.
   
   * Les journaux de demande capturent chaque demande d’API effectuée sur le compte Data Lake Analytics.
   * Les journaux d’audit sont similaires aux journaux de demande, mais ils fournissent une analyse beaucoup plus détaillée des opérations effectuées sur le compte Data Lake Analytics. Par exemple, un simple appel d’API de chargement dans les journaux de demande peut entraîner plusieurs opérations « Ajouter » dans les journaux d’audit.
3. Cliquez sur le lien **Télécharger** d’une entrée de journal pour le télécharger.

### À partir du compte Azure Storage qui contient des données de journal
1. Ouvrez le panneau du compte Azure Storage associé à Data Lake Analytics pour la journalisation, puis cliquez sur Objets blob. Le panneau **Service BLOB** répertorie deux conteneurs.
   
    ![Afficher la journalisation de diagnostic](./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")
   
   * Le conteneur **insights-logs-audit** contient les journaux d’audit.
   * Le conteneur **insights-logs-requests** contient les journaux de demande.
2. Les journaux sont stockés dans ces conteneurs selon la structure suivante.
   
        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
   
   > [!NOTE]
   > Les entrées `##` dans le chemin d’accès contiennent l’année, le mois, le jour et l’heure auxquels le journal a été créé. Data Lake Analytics crée un fichier toutes les heures, par conséquent, `m=` contient toujours une valeur de `00`.
   > 
   > 
   
    Par exemple, le chemin d’accès complet à un journal d’audit peut être :
   
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json
   
    De même, le chemin d’accès complet à un journal de demande peut être :
   
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## Structure journal
Les journaux d’audit et de demande sont au format JSON. Dans cette section, nous examinons la structure JSON des journaux de demande et d’audit.

### Journaux de demande
Voici un exemple d’entrée dans le journal de demande au format JSON. Chaque objet blob a un objet racine appelé **enregistrements** qui contient un tableau d’objets du journal.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### Schéma du journal de requête
| Name | Type | Description |
| --- | --- | --- |
| time |Chaîne |L’horodatage (heure UTC) du journal. |
| resourceId |Chaîne |L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category |Chaîne |La catégorie du journal. Par exemple, **Demandes**. |
| operationName |Chaîne |Le nom de l’opération qui est journalisée. Par exemple, GetAggregatedJobHistory. |
| resultType |Chaîne |L’état de l’opération. Par exemple, 200. |
| callerIpAddress |Chaîne |L’adresse IP du client qui a effectué la demande. |
| correlationId |Chaîne |L’ID du journal. Cette valeur peut être utilisée pour regrouper un ensemble d’entrées de journal associées |
| identité |Object |L’identité qui a généré le journal. |
| properties |JSON |Consultez la section suivante (Schéma des propriétés de journal de demande) pour plus d’informations |

#### Schéma des propriétés de journal de demande
| Name | Type | Description |
| --- | --- | --- |
| HttpMethod |Chaîne |La méthode HTTP utilisée pour l’opération. Par exemple, GET. |
| Chemin |Chaîne |Le chemin d’accès vers l’emplacement où l’opération a eu lieu. |
| RequestContentLength |int |La longueur du contenu de la demande HTTP. |
| ClientRequestId |Chaîne |L’ID qui identifie de façon unique la demande. |
| StartTime |Chaîne |L’heure à laquelle le serveur a reçu la demande. |
| EndTime |Chaîne |L’heure à laquelle le serveur a envoyé une réponse. |

### Journaux d’audit
Voici un exemple d’entrée dans le journal d’audit au format JSON. Chaque objet blob a un objet racine appelé **records** qui contient un tableau d’objets du journal

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### Schéma du journal d’audit
| Name | Type | Description |
| --- | --- | --- |
| time |Chaîne |L’horodatage (heure UTC) du journal. |
| resourceId |Chaîne |L’ID de la ressource sur laquelle l’opération a eu lieu. |
| category |Chaîne |La catégorie du journal. Par exemple, **Audit**. |
| operationName |Chaîne |Le nom de l’opération qui est journalisée. Par exemple, JobSubmitted. |
| resultType |Chaîne |Un sous-état de l’état de la tâche (operationName). |
| resultSignature |Chaîne |Informations supplémentaires sur l’état de la tâche (operationName). |
| identité |Chaîne |L’utilisateur qui a demandé l’opération. Par exemple, susan@contoso.com. |
| properties |JSON |Consultez la section suivante (Schéma des propriétés de journal d’audit) pour plus d’informations |

> [!NOTE]
> **resultType** et **resultSignature** fournissent des informations sur le résultat d’une opération et contiennent uniquement une valeur si une opération est terminée. Par exemple, ils contiennent une valeur lorsque **operationName** contient une valeur de **JobStarted** ou **JobEnded**.
> 
> 

#### Schéma des propriétés de journal d’audit
| Name | Type | Description |
| --- | --- | --- |
| JobId |Chaîne |L’ID affecté à la tâche. |
| JobName |Chaîne |Le nom fourni pour la tâche. |
| JobRunTime |Chaîne |Le runtime utilisé pour traiter la tâche. |
| SubmitTime |Chaîne |L’heure (UTC) à laquelle la tâche a été envoyée. |
| StartTime |Chaîne |L’heure à laquelle l’exécution de la tâche a commencé après la soumission (UTC). |
| EndTime |Chaîne |L’heure à laquelle la tâche s’est terminée. |
| Parallélisme |Chaîne |Le nombre d’unités Data Lake Analytics demandées pour cette tâche lors de l’envoi. |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** et **Parallélisme** fournissent des informations sur une opération et contiennent uniquement une valeur si une opération a démarré ou s’est terminée. Par exemple, **SubmitTime** contient une valeur après **qu’operationName** indique **JobSubmitted**.
> 
> 

## Traiter les données de journal
Azure Data Lake Analytics fournit un exemple de traitement et d’analyse des données de journal. Vous trouverez l’exemple à l’adresse [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## Étapes suivantes
* [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md)

<!---HONumber=AcomDC_0914_2016-->