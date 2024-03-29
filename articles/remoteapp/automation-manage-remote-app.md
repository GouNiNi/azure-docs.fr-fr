---
title: Gérer Azure RemoteApp avec Azure Automation | Microsoft Docs
description: Découvrez comment le service Azure Automation peut être utilisé pour gérer Azure RemoteApp.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: ''

ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: magoedte;csand

---
# Gestion d'Azure RemoteApp à l'aide d'Azure Automation
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Ce guide vous présente le service Azure Automation et la manière de l'utiliser pour gérer plus simplement Azure RemoteApp.

## Qu'est-ce qu'Azure Automation ?
[Azure Automation](../automation/automation-intro.md) est un service Azure destiné à simplifier la gestion du cloud via l'automatisation des processus. Il automatise les tâches manuelles, répétitives, fastidieuses et sources d’erreurs pour accroître la fiabilité, l’efficacité et le retour sur investissement de votre organisation.

Azure Automation fournit un moteur d’exécution de workflow à haute fiabilité et à haute disponibilité, qui s’adapte à vos besoins. Dans Azure Automation, les processus peuvent être lancés manuellement, par des systèmes tiers ou à des intervalles planifiés, afin que les tâches interviennent exactement au moment opportun.

Réduisez les coûts opérationnels et libérez du temps pour que votre équipe d’informaticiens et de développeurs puisse se concentrer sur des tâches qui génèrent une valeur ajoutée pour l’entreprise, en déléguant l’exécution automatique de vos tâches de gestion de Cloud à Azure Automation.

## Comment Azure Automation peut-il aider à gérer Azure RemoteApp ?
RemoteApp peut être géré dans Azure Automation à l'aide des applets de commande PowerShell qui sont disponibles dans les [outils Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Azure Automation dispose dès le départ de ces applets de commande PowerShell pour RemoteApp, de sorte que vous pouvez effectuer toutes vos tâches de gestion de RemoteApp au sein du service. Dans Azure Automation, vous pouvez également associer ces applets de commande à des applets de commande d'autres services Azure, afin d'automatiser des tâches complexes entre des services Azure et des systèmes tiers.

## Étapes suivantes
Maintenant que vous avez appris les bases d'Azure Automation et la manière de l'utiliser pour gérer Azure RemoteApp, cliquez sur ces liens pour en savoir plus sur Azure Automation.

* Consultez le [Didacticiel de prise en main](../automation/automation-first-runbook-graphical.md) d'Azure Automation

<!---HONumber=AcomDC_0817_2016-->