---
title: Présentation des journaux de diagnostic Azure | Microsoft Docs
description: Découvrez les journaux de diagnostic Azure et comment les utiliser pour comprendre les événements qui se produisent au sein d’une ressource Azure.
author: johnkemnetz
manager: rboucher
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: johnkem; magoedte

---
# <a name="overview-of-azure-diagnostic-logs"></a>Présentation des journaux de diagnostic Azure
Les **journaux de diagnostic Azure** sont des journaux émis par une ressource qui fournissent des informations riches et fréquentes relatives à l’opération de cette ressource. Le contenu de ces journaux varie selon le type de ressource (p. ex. les journaux d’événements Windows du système sont une catégorie de journaux de diagnostic pour les machines virtuelles et les objets blob ; les journaux de files d’attente et de tables sont des catégories de journaux de diagnostic pour les comptes de stockage) et diffère du [journal d’activité (autrefois appelé journal d’audit ou journal des opérations)](monitoring-overview-activity-logs.md), qui fournit des informations sur les opérations qui ont été effectuées sur les ressources de votre abonnement. Toutes les ressources ne prennent pas en charge le nouveau type de journal de diagnostic décrit ici. La liste des services pris en charge ci-dessous indique quels types de ressources prennent en charge les nouveaux journaux de diagnostic.

![Positionnement logique des journaux de diagnostic](./media/monitoring-overview-of-diagnostic-logs/logical-placement-chart.png)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Ce que vous pouvez faire avec les journaux de diagnostic
Voici ce que vous pouvez faire avec les journaux de diagnostic :

* Enregistrez-les dans un **compte de stockage** pour l’audit ou l’inspection manuelle. Vous pouvez spécifier la durée de rétention (en jours) à l’aide des **paramètres de diagnostic**.
* [Diffusez-les en streaming sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md) pour qu’un service tiers ou une solution d’analyse personnalisée (comme PowerBI) les ingère.
* Analysez-les avec [OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)

## <a name="diagnostic-settings"></a>paramètres de diagnostic
Les journaux de diagnostic pour les ressources non liées au calcul sont configurés à l’aide des paramètres de diagnostic. **Paramètres de diagnostic** pour un contrôle de ressource :

* L’emplacement où les journaux de diagnostic sont envoyés (compte de stockage, Event Hubs et/ou OMS Log Analytics).
* Les catégories de journal qui sont envoyées.
* La durée de rétention de chaque catégorie de journal dans un compte de stockage ; une durée de rétention de zéro jour signifie que les journaux sont conservés indéfiniment. Dans le cas contraire, cette valeur peut être comprise entre 1 et 2147483647. Si des stratégies de rétention sont définies, mais que le stockage des journaux dans un compte de stockage est désactivé (par exemple si seules les options Event Hubs ou OMS sont sélectionnées), les stratégies de rétention n’ont aucun effet.

Ces paramètres sont facilement configurés via le panneau Diagnostics pour une ressource dans le portail Azure, via les commandes d’interface de ligne de commande et Azure PowerShell ou via [l’API REST Insights](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Les mesures et les journaux de diagnostic des ressources Compute (comme les machines virtuelles ou Service Fabric) utilisent [un mécanisme distinct pour la configuration et la sélection des sorties](../azure-diagnostics.md).
> 
> 

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Comment activer la collecte des journaux de diagnostic
La collecte des journaux de diagnostic peut être activée dans le cadre de la création d’une ressource, ou après avoir créé une ressource via le panneau des ressources dans le portail. Vous pouvez également activer les journaux de diagnostic à tout moment via les commandes d’interface de ligne de commande ou Azure PowerShell ou via l’API REST Insights.

> [!TIP]
> Ces instructions peuvent ne pas s’appliquer directement à toutes les ressources. Consultez les liens de schéma en bas de cette page pour comprendre les étapes spécifiques qui peuvent concerner certains types de ressources.
> 
> 

[Cet article vous explique comment utiliser un modèle de ressource pour activer les paramètres de diagnostic lors de la création d’une ressource](monitoring-enable-diagnostic-logs-using-template.md)

### <a name="enable-diagnostic-logs-in-the-portal"></a>Activation des journaux de diagnostic dans le portail
Vous pouvez activer les journaux de diagnostic dans le portail Azure lorsque vous créez certains types de ressources en procédant comme suit :

1. Accédez à **Nouveau** et choisissez la ressource qui vous intéresse.
2. Après avoir configuré les paramètres de base et sélectionné une taille, dans le panneau **Paramètres**, sous **Analyse**, sélectionnez **Activé** et choisissez un compte de stockage dans lequel vous souhaitez stocker les journaux de diagnostic. Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.
   
   ![Activer les journaux de diagnostic lors de la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-new.png)
3. Cliquez sur **OK** et créez la ressource.

Pour activer les journaux de diagnostic dans le portail Azure après la création d’une ressource, procédez comme suit :

1. Accédez au panneau de la ressource et ouvrez le panneau **Diagnostics** .
2. Cliquez sur **On** (Activé) et choisissez un compte de stockage et/ou un Event Hub.
   
   ![Activer les journaux de diagnostic après la création de ressources](./media/monitoring-overview-of-diagnostic-logs/enable-portal-existing.png)
3. Sous **Journaux**, sélectionnez les **catégories de journaux** que vous souhaitez collecter ou diffuser en streaming.
4. Cliquez sur **Save**.

### <a name="enable-diagnostic-logs-programmatically"></a>Activation des journaux de diagnostic par programme
Pour activer les journaux de diagnostic via les applets de commande Azure PowerShell, utilisez les commandes suivantes.

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true

L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu des journaux de diagnostic vers un Event Hub, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true

L’ID de règle Service Bus est une chaîne au format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

    Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [log analytics workspace id] -Enabled $true

> [!NOTE]
> Le paramètre WorkspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.
> 
> 

Vous pouvez obtenir votre ID d’espace de travail de journal Log Analytics dans le portail Azure.

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

Pour activer les journaux de diagnostic via l’interface de ligne de commande Azure, utilisez les commandes suivantes :

Pour activer le stockage des journaux de diagnostic dans un compte de stockage, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true

L’ID de compte de stockage est l’ID de ressource pour le compte de stockage auquel vous souhaitez envoyer les journaux.

Pour activer la diffusion en continu des journaux de diagnostic vers un Event Hub, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true

L’ID de règle Service Bus est une chaîne au format : `{service bus resource ID}/authorizationrules/{key name}`.

Pour activer l’envoi des journaux de diagnostic à un espace de travail Log Analytics, utilisez cette commande :

    azure insights diagnostic set --resourceId <resourceId> --workspaceId <workspaceId> --enabled true

> [!NOTE]
> Le paramètre workspaceId n’est pas disponible dans la version d’octobre. Il sera disponible dans la version de novembre.
> 
> 

Vous pouvez obtenir votre ID d’espace de travail de journal Log Analytics dans le portail Azure.

Vous pouvez combiner ces paramètres pour activer plusieurs options de sortie.

Pour modifier les paramètres de diagnostic via l’API REST Insights, consultez [ce document](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-diagnostic-settings-in-the-portal"></a>Gérer les paramètres de diagnostic dans le portail
Pour vérifier que toutes vos ressources sont correctement configurées avec les paramètres de diagnostic, vous pouvez accéder au panneau **Analyse** du portail et ouvrir le panneau **Journaux de diagnostic**.

![Panneau Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-nav.png)

Vous devrez peut-être cliquer sur « Plus de services » pour trouver le panneau Analyse.

Dans ce panneau, vous pouvez afficher et filtrer toutes les ressources qui prennent en charge les journaux de diagnostic pour voir s’ils ont des diagnostics activés et le compte de stockage, hub d’événement et/ou espace de travail Log Analytics vers lesquels ces journaux circulent.

![Résultats du panneau Journaux de diagnostic dans le portail](./media/monitoring-overview-of-diagnostic-logs/manage-portal-blade.png)

Cliquer sur une ressource affiche tous les journaux qui ont été stockés dans le compte de stockage et vous donne la possibilité de désactiver ou modifier les paramètres de diagnostic. Cliquez sur l’icône de téléchargement pour télécharger les journaux sur une période donnée.

![Panneau Journaux de diagnostic pour une ressource](./media/monitoring-overview-of-diagnostic-logs/manage-portal-logs.png)

> [!NOTE]
> Les journaux de diagnostic s’affichent dans cette vue uniquement et sont disponibles pour téléchargement si vous avez configuré les paramètres de diagnostic pour les enregistrer dans un compte de stockage.
> 
> 

## <a name="supported-services-and-schema-for-diagnostic-logs"></a>Schéma et services pris en charge pour les journaux de diagnostic
Le schéma pour les journaux de diagnostic varie en fonction de la ressource et de la catégorie de journal. Vous trouverez ci-dessous les services pris en charge et leur schéma.

| de diffusion en continu | Schéma et documentation |
| --- | --- |
| Équilibrage de charge Azure |[Analyse des journaux de l’équilibreur de charge Azure (version préliminaire)](../load-balancer/load-balancer-monitor-log.md) |
| Groupes de sécurité réseau |[Analyse de journaux pour les groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md) |
| Passerelles d’application |[Journalisation des diagnostics pour Application Gateway](../application-gateway/application-gateway-diagnostics.md) |
| Key Vault |[Journalisation d’Azure Key Vault](../key-vault/key-vault-logging.md) |
| Azure Search |[Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](../search/search-traffic-analytics.md) |
| Data Lake Store |[Accès aux journaux de diagnostic d’Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| Data Lake Analytics |[Accès aux journaux de diagnostic d’Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Logic Apps |Aucun schéma disponible. |
| Azure Batch |[Journalisation des diagnostics Azure Batch](../batch/batch-diagnostics.md) |
| Azure Automation |[Log Analytics pour Azure Automation](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Hub d’événements |Aucun schéma disponible. |
| SERVICE BUS |Aucun schéma disponible. |
| Stream Analytics |Aucun schéma disponible. |

## <a name="supported-log-categories-per-resource-type"></a>Catégories de journaux prises en charge par type de ressource
| Type de ressource | Catégorie | Nom d’affichage de la catégorie |
| --- | --- | --- |
| Microsoft.Automation/automationAccounts |JobLogs |Journaux de travail |
| Microsoft.Automation/automationAccounts |JobStreams |Flux de travail |
| Microsoft.Batch/batchAccounts |ServiceLog |Journaux de service |
| Microsoft.DataLakeAnalytics/accounts |Audit |Journaux d’audit |
| Microsoft.DataLakeAnalytics/accounts |Requêtes |Journaux de requête |
| Microsoft.DataLakeStore/accounts |Audit |Journaux d’audit |
| Microsoft.DataLakeStore/accounts |Requêtes |Journaux de requête |
| Microsoft.EventHub/namespaces |ArchiveLogs |Journaux d’archivage |
| Microsoft.EventHub/namespaces |OperationalLogs |Journaux des opérations |
| Microsoft.KeyVault/vaults |AuditEvent |Journaux d’audit |
| Microsoft.Logic/workflows |WorkflowRuntime |Événements de diagnostic de runtime de workflow |
| Microsoft.Network/networksecuritygroups |NetworkSecurityGroupEvent |Événement de groupe de sécurité réseau |
| Microsoft.Network/networksecuritygroups |NetworkSecurityGroupRuleCounter |Compteur de règle de groupe de sécurité réseau |
| Microsoft.Network/networksecuritygroups |NetworkSecurityGroupFlowEvent |Événement de flux de règle de groupe de sécurité réseau |
| Microsoft.Network/loadBalancers |LoadBalancerAlertEvent |Événements d’alerte d’équilibrage de charge |
| Microsoft.Network/loadBalancers |LoadBalancerProbeHealthStatus |État d’intégrité de la sonde d’équilibrage de charge |
| Microsoft.Network/applicationGateways |ApplicationGatewayAccessLog |Journal d’accès à la passerelle d’application |
| Microsoft.Network/applicationGateways |ApplicationGatewayPerformanceLog |Journal de performance de la passerelle d’application |
| Microsoft.Network/applicationGateways |ApplicationGatewayFirewallLog |Journal de pare-feu de la passerelle d’application |
| Microsoft.Search/searchServices |OperationLogs |Journaux des opérations |
| Microsoft.ServerManagement/nodes |RequestLogs |Journaux de requête |
| Microsoft.ServiceBus/namespaces |OperationalLogs |Journaux des opérations |
| Microsoft.StreamAnalytics/streamingjobs |Exécution |Exécution |
| Microsoft.StreamAnalytics/streamingjobs |Création |Création |

## <a name="next-steps"></a>Étapes suivantes
* [Diffuser en streaming les journaux de diagnostic sur **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Modifier les paramètres de diagnostic via l’API REST Insights](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analyser les journaux avec OMS Log Analytics](../log-analytics/log-analytics-azure-storage-json.md)

<!--HONumber=Oct16_HO2-->


