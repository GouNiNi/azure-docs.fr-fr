---
title: Abonnement Microsoft Azure et limites, quotas et contraintes du service
description: Fournit une liste des abonnements Azure et des limites, quotas et contraintes de service habituels. Cela inclut des informations sur la façon d’augmenter les limites ainsi que les valeurs maximales.
services: ''
documentationcenter: ''
author: rothja
manager: jeffreyg
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: btardif

---
# <a name="azure-subscription-and-service-limits,-quotas,-and-constraints"></a>Abonnement Azure et limites, quotas et contraintes de service
## <a name="overview"></a>Vue d'ensemble
Ce document présente quelques-unes des limites Microsoft Azure les plus communes. À l’heure actuelle, notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

Pour en savoir plus sur la tarification Azure, consultez [Tarification Azure](https://azure.microsoft.com/pricing/) . Vous pouvez y estimer vos coûts à l’aide de la [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/) ou en consultant la page de détails sur la tarification d’un service (par exemple, les [machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)).

> [!NOTE]
> Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, vous pouvez [ouvrir gratuitement un incident auprès du service client](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.
> 
> 

## <a name="limits-and-the-azure-resource-manager"></a>Limites et Azure Resource Manager
Il est désormais possible de combiner plusieurs ressources Azure en un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, les limites qui étaient auparavant mondiales sont désormais gérées au niveau régional avec Azure Resource Manager. Pour plus d’informations sur les groupes de ressources, consultez la [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).

Dans les limites ci-dessous, un nouveau tableau a été ajouté pour tenir compte des différences de limites lorsque vous utilisez Azure Resource Manager. Par exemple, vous disposez d’une table **Limites d’abonnement** et d’une table **Limites d’abonnement – Azure Resource Manager**. Lorsqu'une limite s'applique aux deux scénarios, elle apparaît uniquement dans le premier tableau. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [!NOTE]
> Il est important de souligner que les quotas de ressources dans les groupes de ressources Azure sont accessibles par région par le biais de votre abonnement et non par abonnement, comme les quotas de gestion des services. Nous allons utiliser des quotas de base à titre d'exemple. Si vous avez besoin de demander une augmentation du quota avec la prise en charge de cœurs, vous devez choisir le nombre de noyaux souhaité et les régions concernées, et effectuer une demande spécifique de quotas de base de groupes de ressources Azure pour les volumes et régions choisis. Par conséquent, si vous avez besoin de 30 cœurs en Europe de l'Ouest pour exécuter votre application, vous devez demander spécifiquement 30 cœurs en Europe de l'Ouest. Néanmoins, il n’y aura pas d’augmentation du quota de base dans les autres régions. Seule la région Europe de l'Ouest disposera du quota de 30 cœurs.
> <!-- -->
> Par conséquent, il peut s’avérer utile de décider de vos quotas de groupes de ressources Azure pour votre charge de travail dans une région, puis de demander ce volume dans chaque région dans laquelle vous envisagez d’effectuer un déploiement. Pour vous aider à découvrir vos quotas actuels pour des régions spécifiques, consultez la page [Dépannage de problèmes de déploiement](resource-manager-common-deployment-errors.md)
> 
> 

## <a name="service-specific-limits"></a>Limites de service spécifique
* [Active Directory](#active-directory-limits)
* [Gestion des API](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Cache Redis Azure](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Sauvegarde](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [DNS](#dns-limits)
* [Base de données de documents](#documentdb-limits)
* [Hubs d'événements](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [Surveillance](#monitoring-limits)
* [Azure Multi-Factor Authentication](#multi-factor-authentication)
* [Mise en réseau](#networking-limits)
* [Service de hub de notification](#notification-hub-service-limits)
* [Operational Insights](#operational-insights-limits)
* [Groupe de ressources](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [action](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de données SQL](#sql-database-limits)
* [Stockage](#storage-limits)
* [Système StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Abonnement](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Machines virtuelles](#virtual-machines-limits)
* [Jeux de mise à l’échelle de machine virtuelle](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Limites d’abonnement
#### <a name="subscription-limits"></a>Limites d’abonnement
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Limites d’abonnement – Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

Pour plus d’informations sur la gestion des limites sur les demandes Resource Manager, consultez [Limitation des requêtes Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limites de groupe de ressources
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Limites de machines virtuelles
#### <a name="virtual-machine-limits"></a>Limites de machine virtuelle
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Limites de machines virtuelles - Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="virtual-machine-scale-sets-limits"></a>Limites des jeux de mise à l’échelle de machine virtuelle
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="networking-limits"></a>Limites de mise en réseau
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="networking-limits"></a>Limites de mise en réseau
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="traffic-manager-limits"></a>Limites de Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="dns-limits"></a>Limites de DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

### <a name="storage-limits"></a>Limites de stockage
Pour plus d'informations sur les limites des comptes de stockage, consultez [Objectifs de performance et d’extensibilité Azure Storage](storage/storage-scalability-targets.md).

#### <a name="storage-service-limits"></a>Limites de service de stockage
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### <a name="virtual-machine-disk-limits"></a>Limites du nombre de disques de machine virtuelle
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d'informations, consultez [Tailles des machines virtuelles](virtual-machines/virtual-machines-linux-sizes.md) .

**Comptes de stockage standard**

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Comptes de stockage Premium**

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### <a name="storage-resource-provider-limits"></a>Limites de fournisseur de ressources de stockage
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="cloud-services-limits"></a>Limites de services cloud
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="app-service-limits"></a>Limites App Service
Les limites App Service suivantes incluent des limites pour les applications Web, les applications mobiles, les applications d'API et les applications logiques.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="scheduler-limits"></a>Limites de planificateur
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Limites Azure Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Limites de BizTalk Services
Le tableau suivant indique les limites d’Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="documentdb-limits"></a>Limites de DocumentDB
[!INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]

Les quotas marqués d'un astérisque (*) [peuvent être ajustés en contactant le support Azure](documentdb/documentdb-increase-limits.md).

### <a name="mobile-engagement-limits"></a>Limites Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

### <a name="search-limits"></a>Limites Azure Search
Les niveaux tarifaires déterminent la capacité et les limites de votre service de recherche. Les niveaux sont les suivants :

* *Gratuit* : service mutualisé, partagé avec d’autres abonnés Azure, destiné à des projets d’évaluation et de développement de petite taille.
* *De base* : fournit des ressources de calcul dédiées pour des charges de travail de production à plus petite échelle, avec jusqu’à trois réplicas pour les charges de travail de requête hautement disponible.
* *Standard (S1, S2, S3, S3 haute densité)* est approprié pour des charges de travail de production de plus grande taille. Plusieurs niveaux existent dans la couche standard pour vous permettre de choisir une configuration de ressources pour des scénarios spécifiques.

**Limites par abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites par service de recherche**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Pour plus d’informations granulaires sur les autres limites, notamment la taille du document, les requêtes par seconde, les clés, les requêtes et les réponses, référez-vous à la rubrique [Limites de service d’Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Limites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="cdn-limits"></a>Limites de CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Limites Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="monitoring-limits"></a>Limites de surveillance
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

### <a name="notification-hub-service-limits"></a>Limites du service Notification Hub
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Limites de concentrateurs d’événements
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Limites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Limites de hub IoT (IoT Hub)
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="data-factory-limits"></a>Limites de Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Limites Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="stream-analytics-limits"></a>Limites Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Limites d'Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="azure-remoteapp-limits"></a>Limites Azure RemoteApp
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### <a name="storsimple-system-limits"></a>Limites du système StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="operational-insights-limits"></a>Limites Operational Insights
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### <a name="backup-limits"></a>Limites Azure Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="site-recovery-limits"></a>Limites Azure Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="application-insights-limits"></a>Limites d’Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### <a name="api-management-limits"></a>Limites Gestion des API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-redis-cache-limits"></a>Limite du service Cache Redis Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Limites du coffre de clés
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication"></a>Azure Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Limites du service Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="sql-database-limits"></a>Limites de base de données SQL
Pour connaître les limites de la base de données SQL, consultez [Limites de ressources de base de données SQL](sql-database/sql-database-resource-limits.md).

## <a name="see-also"></a>Voir aussi
[Présentation des limites et des augmentations Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tailles de machines virtuelles et services cloud pour Microsoft Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tailles de services cloud](cloud-services/cloud-services-sizes-specs.md)

<!--HONumber=Oct16_HO2-->


