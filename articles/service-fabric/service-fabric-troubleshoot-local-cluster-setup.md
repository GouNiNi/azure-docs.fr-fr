---
title: Dépanner votre configuration de cluster Service Fabric locale | Microsoft Docs
description: Cet article aborde un ensemble de suggestions relatives à la résolution des problèmes de votre cluster de développement local
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/08/2016
ms.author: seanmck

---
# Résoudre les problèmes d'installation de votre cluster de développement local
Si vous rencontrez un problème en interagissant avec votre cluster de développement Azure Service Fabric local, examinez les suggestions suivantes de résolution.

## Échecs de configuration du cluster
### Impossible de nettoyer les journaux de Service Fabric
#### Problème
Lors de l’exécution du script DevClusterSetup, une erreur de ce type peut s’afficher :

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devriez pouvoir exécuter le script.

## Échecs de connexion au cluster
### Applets de commande PowerShell de Service Fabric non reconnues dans Azure PowerShell
#### Problème
Si vous essayez d’exécuter l’une des applets de commande PowerShell de Service Fabric, par exemple `Connect-ServiceFabricCluster`, dans une fenêtre Azure PowerShell, l’applet de commande échoue avec un message indiquant qu’elle n’est pas reconnue. Cela est dû au fait qu’Azure PowerShell utilise la version 32 bits de Windows PowerShell (même sur les versions 64 bits du système d’exploitation), tandis que les applets de commande Service Fabric fonctionnent uniquement dans des environnements 64 bits.

#### Solution
Exécutez toujours les applets de commande Service Fabric directement à partir de Windows PowerShell.

> [!NOTE]
> La dernière version d'Azure PowerShell ne crée aucun raccourci spécial, et cela ne devrait donc plus se reproduire.
> 
> 

### Exception durant l’initialisation de type
#### Problème
Quand vous êtes connecté au cluster dans PowerShell, l’erreur TypeInitializationException apparaît pour System.Fabric.Common.AppTrace.

#### Solution
Votre variable PATH n’a pas été correctement définie durant l’installation. Déconnectez-vous de Windows, puis reconnectez-vous. Cela permettra d’actualiser votre variable.

### La connexion au cluster est mise en échec avec une indication de fermeture de l’objet
#### Problème
Un appel à Connect-ServiceFabricCluster est mis en échec avec une erreur de ce type :

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Solution
Fermez la fenêtre PowerShell active et ouvrez une nouvelle fenêtre PowerShell en tant qu’administrateur. Vous devez être maintenant en mesure de vous connecter.

### Exception Connexion Fabric refusée
#### Problème
Pendant le débogage à partir de Visual Studio, vous obtenez une erreur FabricConnectionDeniedException.

#### Solution
Cette erreur se produit généralement lorsque vous essayez de démarrer manuellement un processus hôte de service, sans recourir au runtime de Service Fabric.

Assurez-vous de ne pas disposer de projets de service définis en tant que projets de démarrage dans votre solution. Seuls les projets d’application Service Fabric doivent être définis en tant que projets de démarrage.

> [!TIP]
> Si, après l’installation, votre cluster local commence à se comporter de manière anormale, vous pouvez le réinitialiser à l’aide de l’application de barre d’état système de gestionnaire de cluster local. Cela supprimera le cluster existant et en installera un nouveau. Notez que toutes les applications déployées et les données associées seront supprimées.
> 
> 

## Étapes suivantes
* [Comprendre votre cluster et résoudre les problèmes à l’aide des rapports d’intégrité système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!---HONumber=AcomDC_0713_2016-->