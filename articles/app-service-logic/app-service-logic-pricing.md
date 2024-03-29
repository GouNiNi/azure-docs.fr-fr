---
title: Modèle de tarification de Logic Apps | Microsoft Docs
description: Informations sur le fonctionnement de la tarification dans Logic Apps
author: kevinlam1
manager: dwrede
editor: ''
services: logic-apps
documentationcenter: ''

ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: klam

---
# Modèle de tarification de Logic Apps
Azure Logic Apps vous permet de mettre à l’échelle et d’exécuter les flux de travail d’intégration dans le cloud. Vous trouverez ci-dessous plus d’informations sur les plans de facturation et les niveaux de tarification de Logic Apps.

## Tarification de la consommation
La plateforme Logic Apps récemment créée utilise un plan de consommation. Avec le modèle de tarification de la consommation de Logic Apps, vous payez uniquement ce que vous utilisez. La plateforme Logic Apps n’est pas limitée lors de l’utilisation d’un plan de consommation. Toutes les actions effectuées lors de l’exécution d’une instance de l’application logique sont mesurées.

### Que sont les exécutions d’action ?
Chaque étape d’une définition d’application logique est une action. Cela inclut les déclencheurs, les étapes de flux de contrôle comme les conditions, les étendues, les boucles foreach et do until, ainsi que les appels aux connecteurs et aux actions natives. Les déclencheurs sont des actions spéciales qui sont conçues pour instancier une nouvelle instance d’une application logique lorsqu’un événement particulier se produit. Les déclencheurs peuvent avoir des comportements différents, qui peuvent affecter la façon dont l’application logique est mesurée.

* **Déclencheur d’interrogation** : ce déclencheur interroge constamment un point de terminaison jusqu’à ce qu’il reçoive un message répondant aux critères de création d’une instance d’une application logique. L’intervalle d’interrogation peut être configuré dans le déclencheur dans le concepteur d’applications logiques. Chaque requête d’interrogation compte comme une exécution d’action même si elle ne crée pas d’instance d’une application logique.
* **Déclencheur webhook** : ce déclencheur attend qu’un client lui envoie une requête sur un point de terminaison particulier. Chaque requête envoyée au point de terminaison webhook est considérée comme une exécution d’action. Le déclencheur de requête et le déclencheur Webhook HTTP sont tous deux des déclencheurs webhook.
* **Déclencheur de périodicité** : ce déclencheur crée une instance de l’application logique en fonction de l’intervalle de périodicité configuré dans le déclencheur. Par exemple, un déclencheur de périodicité peut être configuré pour s’exécuter tous les 3 jours ou toutes les minutes.

Les exécutions de déclencheur sont visibles dans le panneau des ressources de Logic Apps dans la partie Historique du déclencheur.

Toutes les actions exécutées, qu’il s’agisse de réussites ou d’échecs, sont considérées comme des exécutions d’action. Les actions qui ont été ignorées en raison d’une condition non remplie ou celles qui n’ont pas été exécutées, car l’application logique s’est arrêtée avant son achèvement ne sont pas considérées comme des exécutions d’action.

Les actions exécutées dans des boucles sont comptabilisées par itération de la boucle. Par exemple, une seule et même action d’une boucle foreach en itération dans une liste de 10 éléments est comptabilisée comme le nombre d’éléments de la liste (10) multiplié par le nombre d’actions de la boucle (1) plus une correspondant à l’initiation de la boucle, ce qui, dans cet exemple, représente (10 * 1) + 1 = 11 exécutions d’action.

Il n’est pas possible de créer de nouvelles instances des applications logiques qui sont désactivées. Par conséquent, pendant leur période de désactivation, elles ne peuvent pas être facturées. N’oubliez pas qu’une fois une application logique désactivée, la suspension de ses instances, avant leur désactivation complète, peut prendre un certain temps.

## Plans App Service
Les plans App Service ne sont plus nécessaires pour créer une application logique. Vous pouvez également référencer un plan App Service avec une application logique existante. Les applications logiques précédemment créées avec un plan App Service continueront d’avoir le même comportement : en fonction du plan choisi, elles seront limitées après qu’un nombre d’exécutions quotidiennes aura été dépassé et ne seront pas facturées à l’aide de l’indicateur d’exécutions d’action.

Plans App Service et exécutions d’action quotidiennes autorisées :

|  | Gratuit/Partagé/De base | Standard | Premium |
| --- | --- | --- | --- |
| Exécutions d’action quotidiennes |200 |10 000 |50 000 |

### Convertir la tarification de type Consommation en tarification de plan App Service
Pour référencer un plan App Service pour une application logique de consommation, vous pouvez simplement [exécuter le script PowerShell ci-dessous](https://github.com/logicappsio/ConsumptionToAppServicePlan). Assurez-vous que vous disposez bien des [outils Azure PowerShell](https://github.com/Azure/azure-powershell).

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### Convertir la tarification de plan App Service en tarification de type Consommation
Pour modifier une application logique à laquelle un plan App Service est associé et la définir sur un modèle de consommation, supprimez la référence au plan App Service dans la définition de l’application logique. Ce peut être fait via un appel à une applet de commande PowerShell :

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## Tarification
Pour plus d’informations sur la tarification, voir la page [Tarification de Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

## Étapes suivantes
* [An overview of Logic Apps (Vue d’ensemble de Logic Apps)][whatis]
* [Créez votre première application logique][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0810_2016-->