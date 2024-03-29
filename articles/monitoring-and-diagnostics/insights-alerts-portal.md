---
title: Utiliser le Portail Azure pour créer des alertes pour les services Azure | Microsoft Docs
description: Utilisez le Portail Azure pour créer des alertes Azure, qui peuvent déclencher des notifications ou Automation lorsque les conditions spécifiées sont remplies.
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: robb

---
# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Utiliser le Portail Azure pour créer des alertes pour les services Azure
> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](../azure-portal/insights-alerts-command-line-interface.md) 
> 
> 

## <a name="overview"></a>Vue d'ensemble
Cet article vous montre comment définir des alertes Azure avec le Portail Azure.   

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou uniquement lorsqu’un certain nombre d’événements se produisent.

Vous pouvez configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement : 

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook
* démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure) 

Vous pouvez configurer et obtenir des informations sur les règles d’alerte avec 

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md) 
* [interface de ligne de commande (CLI)](../azure-portal/insights-alerts-command-line-interface.md) 
* [API REST Azure Insights](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Créer une règle d’alerte sur une métrique avec le Portail Azure
1. Sur le [portail](https://portal.azure.com/), localisez la ressource que vous souhaitez surveiller et sélectionnez-la.
2. Sélectionnez **Alertes** ou **Règles d’alerte** dans la section SURVEILLANCE. Le texte et l’icône peuvent varier légèrement pour les différentes ressources.  
   
    ![Analyse](./media/insights-alerts-portal/AlertRulesButton.png)
3. Sélectionnez la commande **Ajouter une alerte** et renseignez les champs.
   
    ![Ajouter une alerte](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)
4. **Nommez** votre règle d’alerte, puis choisissez une **Description** qui indique également les adresses électroniques de notification.
5. Sélectionnez la **Métrique** que vous souhaitez surveiller, puis choisissez une **Condition** et une valeur de **Seuil** pour la métrique. Choisissez également la **Période** de temps pendant laquelle la règle de métrique doit être satisfaite pour que l’alerte se déclenche. Par exemple, si vous utilisez la période « PT5M » et que vous alerte recherche l’UC au-dessus de 80 %, elle se déclenche quand l’UC a été constamment au-dessus de 80 % pendant cinq minutes. Après le premier déclenchement, elle se déclenche à nouveau lorsque l’UC reste au-dessous de 80 % pendant cinq minutes. La mesure de l’UC se produit toutes les minutes.   
6. Cochez **Propriétaires de messagerie...** si vous souhaitez que les administrateurs et les coadministrateurs reçoivent un courrier électronique lorsque l’alerte se déclenche.
7. Si vous souhaitez que d’autres adresses électroniques reçoivent une notification lorsque l’alerte se déclenche, ajoutez-les dans le champ **Adresse(s) de messagerie d’administrateur(s) supplémentaire(s)** . Séparez les adresses électroniques par des points-virgules : *email@contoso.com;email2@contoso.com* 
8. Insérez un URI valide dans le champ **Webhook** si vous souhaitez qu’il soit appelé lorsque l’alerte se déclenche.
9. Si vous utilisez Azure Automation, vous pouvez sélectionner un Runbook à exécuter lorsque l’alerte se déclenche. 
10. Quand vous avez terminé, sélectionnez **OK** pour créer l’alerte.   

Après quelques minutes, l’alerte est active et se déclenche comme décrit précédemment.

## <a name="managing-your-alerts"></a>Gestion de vos alertes
Une fois que vous avez créé une alerte, vous pouvez la sélectionner et :

* Afficher un graphique indiquant le seuil de la métrique et les valeurs réelles du jour précédent. 
* La modifier ou la supprimer. 
* La **Désactiver** ou l’**Activer** si vous voulez arrêter temporairement ou reprendre l’envoi de notifications pour cette alerte. 

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md) , notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détails la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) et collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](../azure-portal/insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.

<!--HONumber=Oct16_HO2-->


