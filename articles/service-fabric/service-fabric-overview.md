---
title: Vue d’ensemble de Service Fabric | Microsoft Docs
description: Présentation de Service Fabric, où les applications sont composées de nombreux microservices pour fournir une mise à l’échelle et une résilience. Service Fabric est une plateforme de systèmes distribués qui permet de créer des applications évolutives, fiables et faciles à gérer pour le cloud.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/22/2016
ms.author: mfussell

---
# <a name="overview-of-service-fabric"></a>Vue d'ensemble de Service Fabric
Service Fabric est une plateforme de systèmes distribués qui facilite le packaging, le déploiement et la gestion de microservices extensibles et fiables. Service Fabric résout également les problèmes non négligeables du développement et de la gestion des applications cloud. Les développeurs et administrateurs n’ont plus à résoudre les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques exigeantes, sachant qu’elles sont évolutives, fiables et faciles à gérer. Service Fabric représente la plateforme middleware de nouvelle génération pour la création et la gestion de ces applications d’entreprise de niveau 1 à l’échelle du cloud.

## <a name="applications-composed-of-microservices"></a>Applications composées de microservices
Service Fabric vous permet de créer et gérer des applications évolutives et fiables, composées de microservices s’exécutant à très haute densité sur un pool partagé d’ordinateurs (appelé cluster). Il fournit un runtime sophistiqué pour générer des microservices avec et sans état distribués et évolutifs. Il fournit également des fonctionnalités complètes de gestion d’application pour la configuration, le déploiement, l’analyse, la mise à niveau/mise à jour corrective et la suppression d’applications déployées.

Pourquoi est-il important d’adopter une approche microservices ? Voici les deux principales raisons :

1. Ils permettent de mettre à l’échelle différentes parties de votre application en fonction de ses besoins.
2. Les équipes de développement sont plus agiles dans le déploiement de modifications et fournissent ainsi plus vite et plus souvent des fonctionnalités à vos clients.

Service Fabric alimente de nombreux services Microsoft aujourd’hui, notamment la base de données SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT, Skype Entreprise et de nombreux services Azure principaux.

Service Fabric est adapté à la création de services « nés dans le cloud » qui peuvent commencer petit, si nécessaire, et évoluer à grande échelle avec des centaines ou des milliers d'ordinateurs.

Actuellement, les services Internet sont composés de microservices. Les microservices sont, par exemple, les passerelles de protocole, les profils utilisateur, les paniers d’achat, le traitement des stocks, les files d’attente et les caches. Service Fabric est une plateforme de microservices qui attribue à chaque microservice un nom unique pouvant être avec ou sans état.

Service Fabric fournit des fonctionnalités complètes de gestion du runtime et du cycle de vie pour les applications constituées de ces microservices. Il héberge les microservices dans des conteneurs déployés et activés sur le cluster Service Fabric. Le déplacement des machines virtuelles aux conteneurs rend possible une multiplication par dix de la densité. De même, une nouvelle multiplication par dix de la densité devient possible en déplaçant des conteneurs vers les microservices. Par exemple, un seul cluster de bases de données SQL Azure comprend des centaines d’ordinateurs exécutant des dizaines de milliers de conteneurs qui hébergent au total des centaines de milliers de bases de données. Chaque base de données est un microservice Service Fabric avec état. Il en est de même pour les autres services mentionnés précédemment ; c’est pourquoi le terme « hyperscale » est utilisé pour décrire les capacités de Service Fabric. Si les conteneurs vous fournissent une haute densité, les microservices vous fournissent une hyper-extensibilité.

Pour plus d’informations sur l’approche microservices, consultez [Pourquoi adopter une approche microservices de la création d’applications ?](service-fabric-overview-microservices.md)

## <a name="create-service-fabric-clusters-anywhere"></a>Créer des clusters Service Fabric n'importe où
Vous pouvez créer des clusters Service Fabric dans de nombreux environnements, notamment Azure ou locaux, sous Windows Server ou sous Linux. En outre, l’environnement de développement dans le Kit de développement logiciel (SDK) est identique à l’environnement de production, sans aucun émulateur impliqué. En d’autres termes, s’il s’exécute sur votre cluster de développement local, il se déploie sur le même cluster dans d’autres environnements.

Pour en savoir plus sur la création de clusters en local, voir [Création d’un cluster sous Windows Server ou Linux](service-fabric-deploy-anywhere.md) ou, pour créer un cluster Azure par l’intermédiaire du portail Azure, voir [cette rubrique](service-fabric-cluster-creation-via-portal.md).

![Plateforme Service Fabric][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Microservices Service Fabric avec et sans état
Service Fabric vous permet de créer des applications composées de microservices. Les microservices sans état (par exemple, les passerelles de protocole, les proxys web, etc.) ne conservent pas un état mutable en dehors des demandes du service et de leur réponse. Les rôles de travail Azure Cloud Services sont un exemple de service sans état. Les microservices avec état (par exemple, les comptes d’utilisateur, les bases de données, les appareils, les paniers d’achat, les files d’attente, etc.) conservent un état mutable faisant autorité au-delà de la demande et la réponse. Actuellement, les applications Internet sont constituées d’une combinaison de microservices avec et sans état.

Pourquoi utiliser des microservices avec état et sans état ? Voici les deux principales raisons :

1. La possibilité de créer des services de traitement transactionnel en ligne (OLTP) à débit élevé, faible latence et tolérance de panne en conservant le code proche des données sur le même ordinateur. Quelques exemples : vitrines interactives, recherche, systèmes Internet des objets (IoT), systèmes commerciaux, systèmes de détection des fraudes et de traitement des cartes de crédit et gestion des enregistrements personnels.
2. Simplification de la conception des applications. Les microservices avec état permettent de supprimer les caches et files d’attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d’une application purement sans état. Les services avec état sont naturellement hautement disponibles et à faible latence, ce qui réduit le nombre d’éléments mobiles à gérer au sein de votre application.

Pour plus d’informations sur les modèles d’application avec Service Fabric, consultez [Scénarios d’application](service-fabric-application-scenarios.md) et [Choix d’une infrastructure de modèle de programmation](service-fabric-choose-framework.md) pour votre service.

## <a name="application-lifecycle-management"></a>Gestion du cycle de vie des applications
Service Fabric offre une excellente prise en charge de la gestion du cycle de vie complet des applications cloud : du développement au retrait éventuel, en passant par le déploiement, la gestion quotidienne et la maintenance.

Les fonctionnalités Service Fabric de gestion du cycle de vie des applications permettent aux administrateurs d'application / opérateurs informatiques d'utiliser des flux de travail simples et automatisés pour configurer, déployer, corriger et surveiller les applications. Ces flux de travail intégrés réduisent considérablement la charge pesant sur les opérateurs informatiques pour que vos applications soient disponibles en permanence.

La plupart des applications sont constituées d’une combinaison de microservices avec et sans état et d’autres exécutables/runtimes déployés ensemble. Service Fabric, grâce à des types forts sur les applications et aux microservices packagés, permet le déploiement de plusieurs instances d’applications. Chaque instance est gérée et mise à niveau indépendamment. Plus important encore, Service Fabric peut déployer *n’importe quel* exécutable ou runtime et le rendre fiable. Par exemple, Service Fabric déploie ASP.NET Core 1, node.js, des machines virtuelles Java, des scripts ou tout autre élément qui compose votre application.

Pour plus d’informations sur la gestion du cycle de vie des applications, consultez [Cycle de vie des applications](service-fabric-application-lifecycle.md), et sur le déploiement de n’importe quel code, consultez [Déployer un exécutable invité](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Fonctionnalités clés
Service Fabric vous permet d'effectuer les opérations suivantes :

* Développer des applications hautement évolutives avec réparation spontanée.
* Développer des applications composées de microservices à l’aide du modèle de programmation Service Fabric. Ou simplement héberger des exécutables invités et d’autres infrastructures d’applications de votre choix, par exemple ASP.NET Core 1 ou Node.js.
* Développer des microservices avec et sans état très fiables.
* Simplifier la conception de votre application, en utilisant les microservices avec état à la place des files d’attente et des caches.
* Déployer dans Azure ou dans des clouds locaux qui exécutent Windows Server ou Linux sans aucune modification de code. Écrire une fois, puis déployer sur n'importe quel cluster Service Fabric.
* Développer une approche de « centre de données sur votre ordinateur ». L’environnement de développement local utilise le même code exécuté dans le centre de données Azure.
* Déployer des applications en quelques secondes.
* Déployer des applications avec une densité supérieure à celle des ordinateurs virtuels, en déployant des centaines ou des milliers d’applications par ordinateur.
* Déployer des versions différentes de la même application côte à côte, chacune pouvant être mise à niveau indépendamment.
* Gérer le cycle de vie de vos applications avec état sans interruption de service, y compris les mises à niveau avec rupture et sans rupture.
* Gérer des applications à l’aide de l’interface des API .NET, de Powershell ou de REST.
* Mettre à niveau et corriger des microservices dans les applications indépendamment.
* Surveiller et diagnostiquer l’intégrité de vos applications et définir des stratégies pour effectuer des réparations automatiques.
* Monter ou descendre en puissance votre cluster Service Fabric facilement, sachant que les applications s’adaptent en fonction des ressources disponibles.
* Observer l’équilibreur de ressources à auto-adaptation orchestrer la redistribution des applications au sein du cluster. Service Fabric récupère après des défaillances et optimise la distribution de la charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations :
  * [Pourquoi une approche de microservices pour la conception d’applications ?](service-fabric-overview-microservices.md)
  * [Vue d'ensemble de la terminologie](service-fabric-technical-overview.md)
* Configuration de votre [environnement de développement](service-fabric-get-started.md)  
* [Choix d’une infrastructure de modèle de programmation](service-fabric-choose-framework.md) pour votre service.

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png



<!--HONumber=Oct16_HO2-->


