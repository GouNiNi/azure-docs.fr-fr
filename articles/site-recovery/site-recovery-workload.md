---
title: "Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?"
description: "Azure Site Recovery protège vos charges de travail et applications en coordonnant la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques locaux sur Azure, ou sur un site local secondaire."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/10/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: ddeadcf741eb099f7c14a554dd4f31561a408aa1


---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?
Cet article décrit les charges de travail et les applications que vous pouvez répliquer avec le service Azure Site Recovery.

Publier des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d'ensemble
Les organisations ont besoin d’une stratégie de continuité des activités et de récupération d’urgence pour que les charges de travail et les données demeurent en sécurité et disponibles pendant les temps d’arrêt prévus et imprévus, et pour rétablir au plus vite des conditions de travail normales.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence. À l’aide de Site Recovery, vous pouvez déployer une réplication compatible avec les applications vers le cloud ou un site secondaire. Qu’il s’agisse d’applications Windows ou Linux s’exécutant sur des serveurs physiques ou des machines virtuelles VMware ou Hyper-V, vous pouvez utiliser Site Recovery pour orchestrer la réplication, effectuer un test de récupération d’urgence et exécuter des opérations de basculement et de restauration automatique.

Site Recovery s’intègre avec les applications Microsoft, y compris SharePoint, Exchange, Dynamics, SQL Server et Active Directory. Par ailleurs, Microsoft travaille en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. Vous pouvez personnaliser les solutions de réplication application par application.

## <a name="why-use-site-recovery-for-application-replication"></a>Pourquoi utiliser Site Recovery pour la réplication des applications ?
Site Recovery contribue à la protection des applications et à la récupération comme suit :

* Service indépendant de l’application qui assure la réplication de n’importe quelle charge de travail exécutée sur une machine prise en charge.
* Réplication presque synchrone avec des RPO de 30 secondes pour répondre aux besoins des applications professionnelles les plus critiques.
* Captures instantanées cohérentes de l’application pour les applications uniques ou multiniveau.
* Intégration avec SQL Server AlwaysOn et partenariat avec d’autres technologies de réplication au niveau des applications, y compris la réplication AD, SQL AlwaysOn, les groupes de disponibilité de base de données (DAG) Exchange et Oracle Data Guard.
* Plans de récupération flexibles qui vous permettent de récupérer une pile de l’application entière en un seul clic, et incluent les scripts externes et des actions manuelles.
* Gestion réseau avancée dans Site Recovery et Azure simplifiant la configuration réseau requise pour une application, notamment la réservation d’adresses IP, la configuration de l’équilibrage de charge et l’intégration avec Azure Traffic Manager pour les commutations réseau à RTO faible.
* Une bibliothèque d’automatisation avancée qui fournit des scripts spécifiques d’application prêts pour la production, qui peuvent être téléchargés et intégrés avec des plans de récupération.

## <a name="workload-summary"></a>Synthèse relative aux charges de travail
Site Recovery permet de répliquer n’importe quelle application exécutée sur une machine prise en charge. En outre, nous collaborons avec les équipes produit pour effectuer des tests supplémentaires spécifiques à l’application.

| **Charge de travail** | **Réplication de machines virtuelles Hyper-V vers un site secondaire** | **Réplication de machines virtuelles Hyper-V dans Azure** | **Réplication de machines virtuelles VMware vers un site secondaire** | **Réplication de machines virtuelles VMware dans Azure** |
| --- | --- | --- | --- | --- |
| Active Directory, DNS |O |O |O |O |
| Applications web (IIS, SQL) |O |O |O |O |
| System Center Operations Manager |O |O |O |O |
| SharePoint |O |O |O |O |
| SAP<br/><br/>Réplication d’un site SAP vers Microsoft Azure (aucun cluster) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |
| Microsoft Exchange (aucun DAG) |O |Bientôt disponible |O |O |
| Bureau à distance/VDI |O |O |O |N/A |
| Linux (système d’exploitation et applications) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |
| Dynamics AX |O |O |O |O |
| Dynamics CRM |O |Bientôt disponible |O |Bientôt disponible |
| Oracle |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |O (opération testée par Microsoft) |
| Serveur de fichiers Windows |O |O |O |O |

## <a name="replicate-active-directory-and-dns"></a>Répliquer Active Directory et DNS
Une infrastructure DNS et Active Directory sont essentiels pour la plupart des applications d’entreprise. Dans le cadre d’une récupération d’urgence, vous devez protéger et récupérer ces composants d’infrastructure avant de récupérer vos charges de travail et applications.

À l’aide de Site Recovery, vous pouvez créer un plan de récupération d’urgence automatisé complet pour Active Directory et DNS. Par exemple, si vous souhaitez basculer SharePoint et SAP d’un site principal vers un site secondaire, vous pouvez configurer un plan de récupération qui bascule Active Directory dans un premier temps, puis un plan de récupération spécifique à l’application supplémentaire pour le basculement des autres applications reposant sur Active Directory.

[En savoir plus](site-recovery-active-directory.md) sur la protection d’Active Directory et DNS

## <a name="protect-sql-server"></a>Protéger SQL Server
SQL Server fournit une base pour les services de données pour de nombreuses applications professionnelles dans un centre de données local.  Site Recovery peut être utilisé avec les technologies SQL Server HA/DR pour protéger les applications d’entreprise multiniveau faisant appel à SQL Server. Site Recovery présente les caractéristiques suivantes :

* Solution de récupération d’urgence simple et économique pour SQL Server. Réplication de plusieurs versions et éditions de serveurs et clusters autonomes SQL Server vers Azure ou un site secondaire.  
* Intégration avec les groupes de disponibilité SQL AlwaysOn pour gérer le basculement et la restauration automatique avec les plans de récupération Azure Site Recovery.
* Plans de récupération de bout en bout pour tous les niveaux d’une application, y compris les bases de données SQL Server.
* Mise à l’échelle de SQL Server pour les charges maximales à l’aide de Site Recovery par « éclatement » en tailles de machines virtuelles IaaS plus volumineuses dans Azure.
* Test facile de récupération d’urgence de SQL Server. Vous pouvez effectuer des basculements tests pour l’analyse et les vérifications de conformité des données, sans impact sur votre environnement de production.

[En savoir plus](site-recovery-sql.md) sur la protection de SQL server.

## <a name="protect-sharepoint"></a>Protéger SharePoint
Azure Site Recovery vous aide à protéger vos déploiements SharePoint comme suit :

* Élimine le besoin et les coûts de l’infrastructure associée à une batterie de secours pour la récupération d’urgence. Utilisez Site Recovery, pour répliquer la protection de la batterie entière (niveaux web, application et base de données) sur Azure ou sur un site secondaire.
* Simplifie la gestion et le déploiement de l’application. Les mises à jour déployées sur le site principal sont automatiquement répliquées et disponibles après le basculement et la récupération d’une batterie de serveurs sur un site secondaire. Cela réduit également la complexité de gestion et les coûts associés au maintien d’une batterie de secours à jour.
* Simplifie le développement et le test d’applications SharePoint en créant un environnement de réplica de copie à la demande de type production pour le test et le débogage.
* Simplifie la transition vers le cloud en utilisant Site Recovery pour migrer les déploiements SharePoint vers Azure.

[En savoir plus](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) sur la protection de SharePoint.

## <a name="protect-dynamics-ax"></a>Protéger Dynamics AX
Azure Site Recovery vous permet de protéger votre solution Dynamics AX ERP à travers :

* L’orchestration de la réplication de l’ensemble de votre environnement Dynamics AX (niveaux web et AOS, niveaux bases de données, SharePoint) sur Azure ou sur un site secondaire.
* La simplification de la migration des déploiements de Dynamics AX dans le cloud (Azure).
* La simplification du développement et du test d’applications Dynamics AX en créant une copie de type production à la demande pour le test et le débogage.

[En savoir plus](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) sur la protection de Dynamic AX.

## <a name="protect-rds"></a>Protéger les services Bureau à distance
RDS active l’infrastructure de bureau virtuel (VDI), les bureaux basés sur session et les applications, permettant aux utilisateurs de travailler n’importe où. Avec Azure Site Recovery, vous pouvez :

* Répliquer des bureaux virtuels en pool gérés ou non gérés sur un site secondaire, ainsi que des applications et des sessions à distance sur un site secondaire ou sur Azure.
* Voici ce que vous pouvez répliquer :

| **RDS** | **Réplication de machines virtuelles Hyper-V vers un site secondaire** | **Réplication de machines virtuelles Hyper-V dans Azure** | **Réplication de machines virtuelles VMware vers un site secondaire** | **Réplication de machines virtuelles VMware dans Azure** | **Réplication de serveurs physiques vers un site secondaire** | **Répliquer des serveurs physiques dans Azure** |
| --- | --- | --- | --- | --- | --- | --- |
| **Bureau virtuel en pool (non géré)** |Oui |Non |Oui |Non |Oui |Non |
| **Bureau virtuel en pool (géré et sans UPD)** |Oui |Non |Oui |Non |Oui |Non |
| **Applications à distance et sessions de bureau (sans UPD)** |Oui |Oui |Oui |Oui |Oui |Oui |

[En savoir plus](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) sur la protection de RDS.

## <a name="protect-exchange"></a>Protéger Exchange
Site Recovery vous permet de protéger Exchange comme suit :

* Site Recovery peut répliquer et basculer les déploiements Exchange de petite taille, par exemple des serveurs uniques ou autonomes, vers Azure ou un site secondaire.
* Pour les déploiements plus importants, Site Recovery s’intègre avec les groupes de disponibilité de base de données (DAG) Exchange.
* Les groupes de disponibilité de base de données (DAG) Exchange sont la solution recommandée pour la récupération d’urgence Exchange dans une entreprise.  Les plans de récupération Site Recovery peuvent inclure des groupes de disponibilité de base de données (DAG) pour orchestrer le basculement de ceux-ci entre les sites.

[En savoir plus](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) sur la protection d’Exchange.

## <a name="protect-sap"></a>Protéger SAP
Utilisez Site Recovery pour protéger votre déploiement SAP comme suit :

* Assurez la protection du déploiement SAP entier en répliquant plusieurs couches de déploiement vers Azure ou un site secondaire.
* Simplifiez la migration vers le cloud en utilisant Site Recovery pour migrer votre déploiement SAP vers Azure.
* Simplifiez le développement et le test SAP en créant une copie de type production à la demande pour le test et le débogage des applications.

[En savoir plus](http://aka.ms/asr-sap) sur la protection de SAP.

## <a name="next-steps"></a>Étapes suivantes
[Préparer le déploiement de Site Recovery](site-recovery-best-practices.md) 




<!--HONumber=Nov16_HO2-->


