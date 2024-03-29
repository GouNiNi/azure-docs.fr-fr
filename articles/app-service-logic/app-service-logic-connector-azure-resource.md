---
title: Utilisation du connecteur de ressources Azure dans des applications logiques | Microsoft Docs
description: Comment créer et configurer le connecteur de ressources Azure ou une application API et l’utiliser dans une application logique d’Azure App Service
services: logic-apps
documentationcenter: .net,nodejs,java
author: stepsic-microsoft-com
manager: erikre
editor: ''

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/01/2016
ms.author: stepsic

---
# Utilisation et ajout du connecteur de ressources Azure dans votre application logique
> [!NOTE]
> Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.
> 
> 

Utilisez le connecteur de ressources Azure pour gérer facilement des ressources Azure à l’intérieur de votre application logique.

## Création du connecteur de ressources Azure
Pour utiliser l’application API du connecteur de ressources Azure, vous devez commencer par en créer une instance. Vous pouvez effectuer cette opération inline lorsque vous créez une application logique ou en sélectionnant l’application API du connecteur de ressources Azure à partir d’Azure Marketplace.

Pour la configurer, vous devez définir un principal du service doté d’autorisations pour toutes les tâches que vous souhaitez exécuter dans Azure. Tous les appels seront effectués pour le compte du principal du service que vous avez configuré. Cette opération vous permet de limiter la portée du connecteur aux seules tâches que vous voulez qu’il effectue.

David Ebbo a rédigé un [formidable billet de blog](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sur la configuration de ce principal. Suivez toutes les instructions qui y figurent et obtenez votre **ID de locataire**, **ID client** et **Clé secrète**. Ces trois champs, ainsi que l’**ID d’abonnement**, définissent les informations requises pour la configuration du connecteur.

## Utilisation du connecteur de ressources Azure dans le concepteur d’applications logiques
### Déclencheur
Deux déclencheurs sont pris en charge dans le connecteur :

| Nom | Description |
| --- | --- |
| Occurrence d’événement |Déclenchement lorsqu’un événement se produit sur une ressource de votre abonnement. |
| Seuil de métrique atteint |Déclenchement lorsqu’un métrique atteint un certain seuil. |

### Action
De la même façon, vous pouvez spécifier un grand nombre d’actions dans votre abonnement Azure :

Pour **Groupes de ressources**, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Répertorier les groupes de ressources |Répertorie tous les groupes de ressources de l’abonnement. |
| Obtenir un groupe de ressources |Obtient un groupe de ressources par son ID. |
| Créer un groupe de ressources |Crée ou met à jour un groupe de ressources. |
| Supprimer un groupe de ressources |Supprime un groupe de ressources. |

Pour **Ressources**, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Répertorier les ressources |Répertorie les ressources de votre abonnement selon différents types de filtre. |
| Obtenir une ressource |Obtient une ressource unique par son ID. |
| Créer ou mettre à jour une ressource |Crée une ressource ou met à jour une ressource existante. Vous devez fournir toutes les propriétés de cette ressource. |
| Exécuter une action de ressource |Effectue toute autre action sur une ressource. Vous devez connaître le nom de l’action et la charge utile de cette action (le cas échéant). |
| Supprimer une ressource |Supprime une ressource. |

Pour **Fournisseurs de ressources**, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Répertorier les fournisseurs de ressources |Répertorie tous les fournisseurs de ressources disponibles dans l’abonnement. |

Pour **Déploiements de groupe de ressources**, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Répertorier les déploiements |Répertorie tous les déploiements d’un groupe de ressources. |
| Obtenir un déploiement |Obtient un déploiement de modèle par son ID. |
| Créer un déploiement |Crée un déploiement de groupe de ressources en fournissant un modèle. |

Pour **Événements** concernant les ressources, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Obtenir les événements |Obtient les événements d’un abonnement ou d’une ressource. |

Pour **Métriques** concernant les ressources, vous pouvez définir les actions suivantes :

| Nom | Description |
| --- | --- |
| Obtenir les métriques |Obtient un métrique pour un ID de ressource. |

## En faire plus avec votre connecteur
Le connecteur étant créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure Logic Apps avant d’ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0907_2016-->