---
title: "Prise en main d’Event Hubs en C# | Microsoft Docs"
description: "Suivez ce didacticiel pour commencer à utiliser Azure Event Hubs, à envoyer des événements en C# et à en recevoir en Java à l’aide d’EventProcessorHost."
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 059fb733-a397-400e-8e43-0c7ea5930b8b
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/27/2016
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 51d880650ab8059f3346b5c1272c232b49be33e9


---
# <a name="get-started-with-event-hubs"></a>Prise en main des hubs d’événements
[!INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduction
Event Hubs constitue un service qui traite de grandes quantités de données d'événement (télémétrie) à partir de périphériques et d'applications connectés. Après avoir collecté des données dans les concentrateurs d’événements, vous pouvez les stocker à l’aide d’un cluster de stockage ou les transformer à l’aide d’un fournisseur d’analyses en temps réel. Cette fonctionnalité de collecte et de traitement d’événements à grande échelle représente un élément clé des architectures d’applications modernes, notamment l’Internet des objets (IoT).

Ce didacticiel montre comment utiliser le portail Azure Classic pour créer un concentrateur d’événements. Il montre également comment collecter les messages dans un Event Hub à l’aide d’une application console en C# et comment les récupérer en parallèle en utilisant la bibliothèque de l’hôte du processeur d’événements en Java.

Pour réaliser ce didacticiel, vous aurez besoin des éléments suivants :

* [Microsoft Visual Studio](http://visualstudio.com)
* Un compte Azure actif. <br/>Si vous n’en avez pas, vous pouvez créer un compte gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[!INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[!INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Exécution des applications
Vous êtes maintenant prêt à exécuter les applications.

1. Exécutez le projet **Récepteur** .
   
   ![][21]
2. Exécutez le projet **Expéditeur** .
   
   ![][22]

## <a name="next-steps"></a>Étapes suivantes
Vous avez conçu une application opérationnelle qui crée un hub d’événements et envoie et reçoit des données. Vous pouvez à présent passer aux scénarios suivants :

* Un [exemple d'application complet qui utilise des Event Hubs][exemple d'application complet qui utilise des Event Hubs].
* Exemple de [montée en puissance du traitement des événements avec Event Hubs][montée en puissance du traitement des événements avec Event Hubs].
* [Vue d'ensemble d’Event Hubs][Vue d'ensemble d’Event Hubs]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Portail Azure Classic]: https://manage.windowsazure.com/
[Vue d'ensemble d’Event Hubs]: event-hubs-overview.md
[exemple d'application complet qui utilise des Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[montée en puissance du traitement des événements avec Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Nov16_HO2-->


