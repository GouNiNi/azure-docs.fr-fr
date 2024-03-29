---
title: Utilisation d’Elasticsearch en tant que magasin de trace d’applications Service Fabric | Microsoft Docs
description: Cet article décrit comment les applications Service Fabric peuvent utiliser Elasticsearch et Kibana pour le stockage, l’indexation et la recherche dans les traces d’applications (journaux).
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: adegeo
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2016
ms.author: karolz@microsoft.com

---
# Utiliser Elasticsearch en tant que magasin de trace d’applications Service Fabric
## Introduction
Cet article décrit comment les applications [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) peuvent utiliser **Elasticsearch** et **Kibana** pour le stockage, l’indexation et la recherche de traces d’applications. [Elasticsearch](https://www.elastic.co/guide/index.html) est un moteur de recherche et d’analyse en temps réel open source, distribué et évolutif, qui convient parfaitement à cette tâche. Il peut être installé sur des machines virtuelles Windows ou Linux exécutées dans Microsoft Azure. Elasticsearch peut traiter efficacement les traces *structurées* produites à l’aide de technologies telles que **Event Tracing for Windows (ETW)**.

ETW est utilisé par le runtime Service Fabric pour sourcer les informations de diagnostic (traces). C’est la méthode recommandée pour le sourçage des informations de diagnostics des applications Service Fabric. L’utilisation du même mécanisme garantit une corrélation entre les traces fournies par le runtime et celles fournies par l’application, tout en facilitant la résolution des problèmes. Les modèles de projet Service Fabric dans Visual Studio incluent une API de journalisation (basée sur la classe .NET **EventSource**) qui émet des traces ETW par défaut. Pour obtenir une vue d’ensemble du traçage d’applications Service Fabric à l’aide d’ETW, consultez [Surveillance et diagnostic des services dans une configuration de développement d’ordinateur local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Pour que les traces s’affichent dans Elasticsearch, elles doivent être capturées en temps réel sur les nœuds de cluster Service Fabric (pendant l’exécution de l’application) et envoyées à un point de terminaison Elasticsearch. Il existe deux options principales pour la capture de traces :

* **Capture de traces dans le processus** :  
  l’application, ou plus précisément, le processus de service est responsable de l’envoi des données de diagnostic dans le magasin de trace (ElasticSearch).
* **Capture de traces hors processus** :  
  un agent séparé capture les traces à partir du ou des processus de service et les envoie au magasin de trace.

Ci-après, nous expliquons comment configurer Elasticsearch sur Azure, présentons les avantages et inconvénients pour les deux options de capture, et expliquons comment configurer un service Service Fabric pour envoyer des données à Elasticsearch.

## Configurer Elasticsearch sur Azure
La façon la plus simple de configurer le service Elasticsearch sur Azure consiste à passer par les [**modèles Azure Resource Manager**](../resource-group-overview.md). Un [modèle Azure Resource Manager de démarrage rapide pour Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) est disponible à partir du référentiel de modèles de démarrage rapide Azure. Ce modèle utilise des comptes de stockage distincts pour les unités d’échelle (groupes de nœuds). Il peut également approvisionner des nœuds client et serveur distincts avec des configurations différentes et divers nombres de disques de données associés.

Ici, nous utilisons un autre modèle appelé **ES-MultiNode** à partir du [référentiel d’outils de diagnostic Azure](https://github.com/Azure/azure-diagnostics-tools). Ce modèle est plus facile à utiliser ; il crée un cluster Elasticsearch protégé par l’authentification de base HTTP. Avant de continuer, téléchargez le référentiel sur GitHub sur votre ordinateur (soit en clonant le référentiel, soit en téléchargeant un fichier zip). Le modèle ES-MultiNode se trouve dans le dossier du même nom.

### Préparer un ordinateur pour l’exécution des scripts d’installation d’Elasticsearch
Le moyen le plus simple d’exploiter le modèle ES-MultiNode consiste à utiliser un script Azure PowerShell déjà fourni, appelé `CreateElasticSearchCluster`. Pour utiliser ce script, vous devez installer les modules PowerShell ainsi qu’un outil appelé **openssl**. Ce dernier est indispensable pour la création d’une clé SSH que vous pouvez utiliser pour administrer votre cluster Elasticsearch à distance.

Le script `CreateElasticSearchCluster` est conçu pour être facilement utilisable avec le modèle ES-MultiNode à partir d’un ordinateur Windows. Il est possible d’utiliser le modèle sur un ordinateur non-Windows, mais nous n’aborderons pas ce scénario dans cet article.

1. Si vous ne l’avez pas déjà fait, installez les [**modules Azure PowerShell**](http://aka.ms/webpi-azps). À l’invite, cliquez sur **Exécuter**, puis sur **Installer**. PowerShell Azure 1.3 (ou une version ultérieure) est requis.
2. L’outil **openssl** est inclus dans la distribution de [**Git pour Windows**](http://www.git-scm.com/downloads). Si vous ne l’avez pas déjà fait, installez [Git pour Windows](http://www.git-scm.com/downloads) maintenant. (Les options d’installation par défaut sont appropriées.)
3. En supposant que Git a bien été installé mais n’a pas été inclus dans le chemin système, ouvrez une fenêtre Microsoft Azure PowerShell et exécutez les commandes suivantes :
   
    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```
   
    Remplacez `<Git installation folder>` par l’emplacement de Git sur votre ordinateur ; la valeur par défaut est **C:\\Program Files\\Git**. Notez le point-virgule au début du premier chemin.
4. Vérifiez que vous êtes connecté à Azure (avec l’applet de commande [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx)) et que vous avez sélectionné l’abonnement que vous devez utiliser pour créer votre cluster Elasticsearch. Vous pouvez vérifier que l’abonnement sélectionné est correct à l’aide des applets de commande `Get-AzureRmContext` et `Get-AzureRmSubscription`.
5. Si vous ne l’avez pas déjà fait, accédez au répertoire actuel dans le dossier ES-MultiNode.

### Exécuter le script CreateElasticSearchCluster
Avant d’exécuter le script, ouvrez le fichier `azuredeploy-parameters.json` et vérifiez ou renseignez les valeurs pour les paramètres du script. Les paramètres suivants sont fournis :

| Nom du paramètre | Description |
| --- | --- |
| dnsNameForLoadBalancerIP |Nom utilisé pour créer le nom DNS du cluster Elasticsearch visible publiquement (en ajoutant le domaine de la région Azure au nom fourni). Par exemple, si la valeur de ce paramètre est « myBigCluster » et que la région Azure choisie est l’Ouest des États-Unis, le nom DNS généré pour le cluster est myBigCluster.westus.cloudapp.azure.com. <br /><br />Ce nom sert également de nom racine pour de nombreux artefacts associés au cluster Elasticsearch, tels que les noms de nœuds de données. |
| adminUsername |Nom du compte administrateur chargé de la gestion du cluster Elasticsearch (les clés SSH correspondantes sont générées automatiquement). |
| dataNodeCount |Nombre de nœuds dans le cluster Elastic Search. La version actuelle du script ne distingue pas les nœuds de données des nœuds de requête ; tous les nœuds jouent les deux rôles. Défini par défaut sur la valeur 3 nœuds. |
| dataDiskSize |Taille des disques de données (en Go) allouée pour chaque nœud de données. Chaque nœud reçoit quatre disques de données, exclusivement dédiés au service Elasticsearch. |
| region |Nom de la région Azure où le cluster Elastic Search doit se trouver. |
| esUserName |Nom de l’utilisateur configuré pour avoir accès au cluster ES (selon l’authentification de base HTTP). Le mot de passe ne fait pas partie du fichier des paramètres et doit être fourni lorsque le script `CreateElasticSearchCluster` est appelé. |
| vmSizeDataNodes |La taille de la machine virtuelle Azure pour les nœuds du cluster Elastic Search. Par défaut, Standard\_D2. |

Vous êtes maintenant prêt à exécuter le script. Exécutez la commande suivante :

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

où

| Nom du paramètre de script | Description |
| --- | --- |
| `<es-group-name>` |Nom du groupe de ressources Azure qui contiendra toutes les ressources du cluster Elasticsearch. |
| `<azure-region>` |Nom de la région Azure où le cluster Elasticsearch doit être créé. |
| `<es-password>` |Mot de passe de l’utilisateur Elasticsearch. |

> [!NOTE]
> Si vous obtenez un NullReferenceException à partir de l’applet de commande Test-AzureResourceGroup, cela signifie que vous avez oublié d’ouvrir une session sur Azure (`Add-AzureRmAccount`).
> 
> 

Si vous obtenez une erreur d’exécution du script et que vous constatez que l’erreur est due à une valeur de paramètre de modèle incorrecte, corrigez le fichier de paramètres et réexécutez le script avec un autre nom de groupe de ressources. Vous pouvez également réutiliser le même nom de groupe de ressources et nettoyer l’ancien à l’aide du script en ajoutant le paramètre `-RemoveExistingResourceGroup` à l’appel du script.

### Résultat de l’exécution du script CreateElasticSearchCluster
Une fois que vous avez exécuté le script `CreateElasticSearchCluster`, les principaux artefacts suivants sont créés. Pour cet exemple, nous supposons que vous avez utilisé « myBigCluster » comme valeur du paramètre `dnsNameForLoadBalancerIP` et que la région où vous avez créé le cluster est l’Ouest des États-Unis.

| Artefact | Nom, emplacement et remarques |
| --- | --- |
| Clé SSH d’administration à distance |Fichier myBigCluster.key (dans le répertoire à partir duquel CreateElasticSearchCluster a été exécuté). <br /><br />Ce fichier de clé peut être utilisé pour vous connecter au nœud admin et (avec le nœud admin) aux nœuds de données du cluster. |
| Nœud admin |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Une machine virtuelle dédiée à l’administration à distance du cluster Elasticsearch, la seule qui autorise les connexions SSH externes. Elle s’exécute sur le même réseau virtuel que tous les nœuds du cluster Elasticsearch, mais elle n’exécute pas tous les services Elasticsearch. |
| Nœuds de données |myBigCluster1... myBigCluster*N*<br /><br />Nœuds de données qui exécutent les services Elasticsearch et Kibana. Vous pouvez vous connecter à chaque nœud en utilisant le protocole SSH, mais uniquement via le nœud admin. |
| Cluster Elasticsearch |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Le point de terminaison principal du cluster Elasticsearch (notez le suffixe /es). Elle est protégée par l’authentification HTTP de base (les informations d’identification ont été spécifiées par les paramètres esUserName/esPassword du modèle ES-MultiNode). Le plug-in principal (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) est également installé sur le cluster pour l’administration de base. |
| Service Kibana |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Le service Kibana est configuré pour afficher des données à partir du cluster Elasticsearch créé. Il est protégé par les mêmes informations d’identification d’authentification que celles du cluster lui-même. |

## Comparaison de la collecte de traces dans le processus et hors processus
Dans l’introduction, nous avons mentionné deux méthodes fondamentales de la collecte de données de diagnostic : dans le processus et hors processus. Chacune de ces méthodes présente ses propres avantages et inconvénients.

La **collecte de traces dans le processus** comporte les avantages suivants :

1. *Simplicité de configuration et de déploiement*
   
   * La configuration de la collecte de données de diagnostic s’inscrit simplement dans le cadre de la configuration de l’application. Elle reste toujours facilement « synchronisée » avec le reste de l’application.
   * Vous pouvez facilement réaliser une configuration par application ou par service.
   * La capture de traces hors processus implique généralement d’effectuer séparément le déploiement et la configuration de l’agent de diagnostic, ce qui représente une tâche administrative supplémentaire source d’erreurs potentielle. La technologie d’agent n’autorise souvent qu’une seule instance de l’agent par machine virtuelle (nœud). Cela signifie que la configuration de la collecte de la configuration du diagnostic est partagée entre l’ensemble des applications et services qui s’exécutent sur ce nœud.
2. *Flexibilité*
   
   * L’application peut envoyer les données partout où elles sont nécessaires, tant qu’il existe une bibliothèque cliente capable de prendre en charge le système de stockage ciblé. Vous pouvez ajouter de nouveaux récepteurs si vous le souhaitez.
   * Vous pouvez également implémenter des règles de capture, de filtrage et d’agrégation de données complexes.
   * La méthode de collecte de traces hors processus est souvent limitée par les récepteurs de données pris en charge par l’agent. Certains agents sont extensibles.
3. *Accès aux données d’application internes et contexte*
   
   * Le sous-système de diagnostic exécuté à l’intérieur du processus d’application/service peut facilement enrichir les traces d’informations contextuelles.
   * Dans l’approche hors processus, les données doivent être envoyées à un agent par le biais d’un mécanisme de communication inter-processus du type Event Tracing for Windows. Ce mécanisme peut imposer des limites supplémentaires.

La **collecte de traces hors processus** comporte les avantages suivants :

1. *Possibilité de surveiller l’application et de collecter les vidages sur incident*
   
   * La méthode de collecte de traces dans le processus peut échouer si l’application ne parvient pas à démarrer ou si elle se bloque. Un agent indépendant a beaucoup plus de chance de capturer des informations de dépannage cruciales.<br /><br />
2. *Maturité, fiabilité et performances éprouvées*
   
   * Un agent développé par un fournisseur de plateformes (tel qu’un agent de diagnostics Microsoft Azure) a été soumis à des tests rigoureux et difficiles.
   * Avec la méthode de collecte de traces dans le processus, il convient de veiller à ce que l’activité d’envoi de données de diagnostic à partir d’un processus d’application n’interfère pas avec les tâches principales de l’application ou n’introduise pas de problèmes de synchronisation ou de performances. Un agent exécuté indépendamment est moins sujet à ces problèmes et est conçu spécialement pour limiter l’impact sur le système.

Il est possible de combiner ces deux approches et de tirer parti de leurs avantages respectifs. En effet, cela peut constituer la meilleure solution pour de nombreuses applications.

Ici, nous utilisons la **bibliothèque Microsoft.Diagnostic.Listeners** et la méthode de collecte de traces dans le processus pour transférer des données entre une application Service Fabric et un cluster Elasticsearch.

## Utiliser la bibliothèque Listeners pour l’envoi de données de diagnostic à Elasticsearch
La bibliothèque Microsoft.Diagnostic.Listeners fait partie de l’application Service Fabric de l’exemple de PartyCluster. Pour l’utiliser :

1. Téléchargez [l’exemple PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) sur GitHub.
2. Copiez les projets Microsoft.Diagnostics.Listeners et Microsoft.Diagnostics.Listeners.Fabric (dossiers entiers) du répertoire de l’exemple PartyCluster dans le dossier de solution de l’application supposée envoyer les données à Elasticsearch.
3. Ouvrez la solution cible, cliquez avec le bouton droit de la souris sur le nœud de solution dans l’Explorateur de solutions et choisissez **Ajouter un projet existant**. Ajoutez le projet Microsoft.Diagnostics.Listeners à la solution. Répétez la même procédure pour le projet Microsoft.Diagnostics.Listeners.Fabric.
4. Ajoutez aux deux projets ajoutés une référence de projet à partir de vos projets de service. (Chaque service censé envoyer des données à Elasticsearch devrait référencer les projets Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric).
   
    ![Références de projet aux bibliothèques Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric][1]

### Disponibilité générale de la version de Service Fabric et package NuGet Microsoft.Diagnostics.Tracing
Les applications créées avec version à disponibilité générale de Service Fabric (2.0.135, publiée le 31 mars 2016) ciblent **.NET Framework 4.5.2**. Il s’agit de la version la plus récente de .NET Framework prise en charge par Azure au moment du lancement de la disponibilité générale de la version. Malheureusement, cette version du framework ne dispose pas de certaines API EventListener dont a besoin la bibliothèque Microsoft.Diagnostics.Listeners. Étant donné qu’EventSource (le composant de base des API de journalisation dans les applications Fabric) et EventListener sont étroitement couplés, chaque projet qui utilise la bibliothèque Microsoft.Diagnostics.Listeners doit utiliser une autre implémentation d’EventSource. Cette implémentation est fournie par le **package NuGet Microsoft.Diagnostics.Tracing** créé par Microsoft. Le package est entièrement rétrocompatible avec la version d’EventSource incluse dans le framework, ce qui signifie qu’aucune modification de code n’est nécessaire à l’exception des modifications de l’espace de noms référencé.

Pour commencer à utiliser l’implémentation Microsoft.Diagnostics.Tracing de la classe EventSource, procédez comme suit pour chaque projet de service qui doit envoyer des données à Elasticsearch :

1. Cliquez avec le bouton droit sur le projet de service et sélectionnez **Gérer les packages NuGet**.
2. Basculez vers la source du package nuget.org (le cas échéant) et recherchez « **Microsoft.Diagnostics.Tracing** ».
3. Installez le package `Microsoft.Diagnostics.Tracing.EventSource` (et ses dépendances).
4. Ouvrez le fichier **ServiceEventSource.cs** ou **ActorEventSource.cs** dans votre projet de service et remplacez la directive `using System.Diagnostics.Tracing` sur le fichier par la directive `using Microsoft.Diagnostics.Tracing`.

Ces étapes ne seront pas nécessaires dès lors que **.NET Framework 4.6** est pris en charge par Microsoft Azure.

### Instanciation et configuration de l’écouteur Elasticsearch
La dernière étape pour envoyer des données de diagnostic à Elasticsearch consiste à créer une instance de `ElasticSearchListener` et à la configurer avec les données de connexion Elasticsearch. L’écouteur capture automatiquement tous les événements déclenchés avec les classes EventSource définies dans le projet de service. Il doit être actif pendant la durée de vie du service ; autrement dit, l’idéal est de le créer dans le code d’initialisation du service. Voici comment se présenterait le code d’initialisation d’un service sans état une fois les modifications nécessaires introduites (les ajouts sont signalés dans les commentaires commençant par `****`) :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Les données de connexion Elasticsearch doivent être placées dans une section distincte du fichier de configuration de service (**PackageRoot\\Config\\Settings.xml**). Le nom de la section doit correspondre à la valeur transmise au constructeur `FabricConfigurationProvider`, par exemple :

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Les valeurs des paramètres `serviceUri`, `userName` et `password` correspondent respectivement à l’adresse du point de terminaison du cluster Elasticsearch, au nom d’utilisateur et au mot de passe Elasticsearch. `indexNamePrefix` désigne le préfixe des index Elasticsearch ; la bibliothèque Microsoft.Diagnostics.Listeners crée chaque jour un index pour ses données.

### Vérification
Et voilà ! Chaque fois que le service est exécuté, il commence à envoyer des traces au service Elasticsearch spécifié dans la configuration. Vous pouvez le vérifier en ouvrant l’UI Kibana associé à l’instance Elasticsearch cible. Dans notre exemple, l’adresse de la page est http://myBigCluster.westus.cloudapp.azure.com/. Vérifiez que les index possédant le préfixe du nom choisi pour l’instance `ElasticSearchListener` ont effectivement été créés et remplis avec des données.

![Kibana affichant des événements d’application du cluster Party][2]

## Étapes suivantes
* [En savoir plus sur le diagnostic et la surveillance d’un service Fabric Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0817_2016-->