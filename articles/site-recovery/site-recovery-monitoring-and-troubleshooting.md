---
title: Surveiller et résoudre les problèmes de protection pour les machines virtuelles et les serveurs physiques | Microsoft Docs
description: Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles hébergées sur des serveurs locaux ou sur un centre de données secondaire. Cet article permet de surveiller et de résoudre les problèmes de protection d’un site VMM ou Hyper-V.
services: site-recovery
documentationcenter: ''
author: anbacker
manager: mkjain
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2016
ms.author: rajanaki

---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Surveiller et résoudre les problèmes de protection pour les machines virtuelles et les serveurs physiques
Ce guide de surveillance et de résolution des problèmes présente des techniques de suivi d’intégrité de réplication et de résolution des problèmes pour Azure Site Recovery.

## <a name="understanding-the-components"></a>Vue d’ensemble des composants
### <a name="vmware/physical-site-deployment-for-replication-between-on-premises-and-azure."></a>Déploiement du site VMware/physique pour la réplication entre des sites locaux et Azure.
Pour configurer DR entre les machines VMware/physiques locales, le serveur de configuration, la cible maître et le serveur de traitement doivent être configurés. Lors de l'activation de la protection pour le serveur source, Azure Site Recovery installera le service de mobilité. Après une défaillance locale, le serveur source bascule sur Azure et les clients doivent configurer un serveur de traitement dans Azure et un serveur cible maître local pour protéger le serveur source et le reconstruire localement. 

![Déploiement du site VMware/physique pour la réplication entre des sites locaux et Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site."></a>Déploiement du site VMM pour la réplication entre des sites locaux
Dans le cadre de la configuration de la récupération d’urgence entre deux emplacements locaux ; le fournisseur Azure Site Recovery doit être téléchargé et installé sur le serveur VMM. Le fournisseur a besoin d’une connexion à Internet pour s’assurer que toutes les opérations déclenchées à partir du portail Azure sont transmises sur des opérations locales telles que l’activation de la protection, l’arrêt des machines virtuelles du côté principal dans le cadre de basculements, etc.

![Déploiement du site VMM pour la réplication entre des sites locaux](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-&-azure."></a>Déploiement du site VMM pour la réplication entre des sites locaux et Azure
Dans le cadre de la configuration de la récupération d’urgence entre des sites locaux et Azure ; le fournisseur Azure Site Recovery doit être téléchargé et installé sur le serveur VMM avec l’agent Azure Recovery Services qui doit être installé sur chaque hôte Hyper-V. Pour plus d’informations, consultez [Présentation de la protection Site vers Azure](site-recovery-understanding-site-to-azure-protection.md) .

![Déploiement du site VMM pour la réplication entre des sites locaux et Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-&-azure"></a>Déploiement du site Hyper-V pour la réplication entre des sites locaux et Azure
Ceci est similaire à celle du déploiement VMM. La seule différence étant que le fournisseur et l’agent sont installés sur l’hôte Hyper-V lui-même. Pour plus d’informations, consultez [Présentation de la protection Site vers Azure](site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration,-protection-and-recovery-operations"></a>Opérations de surveillance de la configuration, de protection et de récupération
Chaque opération ASR est auditée et suivie sous l’onglet « TÂCHES ». Si une erreur de configuration, de protection ou de récupération survient, accédez à l’onglet TÂCHES et vérifiez s’il y a une défaillance.

![Opérations de surveillance de la configuration, de protection et de récupération](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Si vous trouvez des défaillances sous la vue TÂCHES, sélectionnez la TÂCHE et cliquez sur DÉTAILS DE L’ERREUR pour cette tâche.

![Opérations de surveillance de la configuration, de protection et de récupération](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Les détails des erreurs permettent d’identifier les causes possibles et des recommandations relatives au problème.

![Opérations de surveillance de la configuration, de protection et de récupération](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Dans le cas ci-dessus, il semble qu’une autre opération soit en cours, à cause de laquelle la configuration de la protection a échoué. Veuillez résoudre le problème en suivant les recommandations affichées, puis cliquez sur REDÉMARRER pour réinitialiser l’opération.

![Opérations de surveillance de la configuration, de protection et de récupération](media/site-recovery-monitoring-and-troubleshooting/image6.png)

L’option REDÉMARRER n’est pas disponible pour toutes les opérations : dans ce cas, revenez à l’objet et relancez l’opération. Vous pouvez annuler chaque TÂCHE à tout moment lors de son exécution en utilisant le bouton ANNULER.

![Opérations de surveillance de la configuration, de protection et de récupération](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Surveillance de l’intégrité de la réplication de la machine virtuelle
Une surveillance centralisée et à distance du fournisseur ASR est disponible via le portail Azure pour chacune des entités protégées. Accédez à ÉLÉMENTS PROTÉGÉS, puis sélectionnez CLOUDS VMM ou GROUPES DE PROTECTION. L’onglet CLOUDS VMM est uniquement disponible pour les déploiements VMM, tandis que tous les autres scénarios ont des entités protégées, disponibles sous l’onglet GROUPES de PROTECTION.

![Surveillance de l’intégrité de la réplication de la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Sélectionnez ensuite l’entité protégée sous le cloud ou le groupe de protection correspondant. Une fois que vous avez sélectionné l’entité protégée, toutes les opérations autorisées sont affichées dans le volet inférieur.

![Surveillance de l’intégrité de la réplication de la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Comme indiqué ci-dessus, au cas où l’INTÉGRITÉ de la machine virtuelle est critique, vous pouvez cliquer sur le bouton DÉTAILS DE L’ERREUR en bas de la page pour afficher l’erreur. En fonction des « causes possibles » et de la « recommandation », résolvez le problème.

![Surveillance de l’intégrité de la réplication de la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Surveillance de l’intégrité de la réplication de la machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Remarque : si une opération active est en cours ou a échoué, accédez à la vue TÂCHES comme indiqué précédemment pour afficher l’erreur propre à la TÂCHE.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Résolution des problèmes Hyper-V locaux
Connectez-vous à la console du gestionnaire Hyper-V, sélectionnez la machine virtuelle et vérifiez l’intégrité de la réplication.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Dans ce cas, l’*Intégrité de la réplication* est indiquée comme Critique : cliquez sur *Afficher l’intégrité de la réplication* pour afficher les détails.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Dans les cas où la réplication est suspendue pour la machine virtuelle, cliquez avec le bouton droit et sélectionnez *Réplication*->*Reprendre la réplication*
![Troubleshoot on-premises Hyper-V issues (Résoudre les problèmes Hyper-V locaux)](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Dans le cas où la machine virtuelle migre un nouvel hôte Hyper-V (dans le cluster ou un ordinateur autonome) qui a été configuré via ASR, la réplication de la machine virtuelle n’est pas affectée. Assurez-vous que le nouvel hôte Hyper-V satisfait à toutes les conditions requises et qu’il est configuré à l’aide d’ASR.

### <a name="event-log"></a>Journal des événements
| Sources d’événement | Détails |
| --- |:--- |
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (serveur VMM) |Fournit une journalisation utile à la résolution de nombreux problèmes liés à VMM. |
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (hôte Hyper-V) |Fournit une journalisation utile à la résolution de nombreux problèmes liés à Microsoft Azure Recovery Services Agent. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (hôte Hyper-V) |Fournit une journalisation utile à la résolution de nombreux problèmes liés à Microsoft Azure Site Recovery Service. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (hôte Hyper-V) |Fournit une journalisation utile à la résolution de nombreux problèmes liés à la gestion des machines virtuelles Hyper-V. <br/> ![Source d’événement pour l’hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Options de journalisation de réplication Hyper-V
Tous les événements liés au réplica Hyper-V sont consignés dans le journal Hyper-V-VMMS\\Admin situé sous **Journaux des applications et des services\\Microsoft\\Windows**. De plus, un journal d’analyse peut être activé pour Hyper-V-VMMS. Pour activer ce journal, commencez par afficher les journaux d’analyse et de débogage dans l’Observateur d’événements. Ouvrez l’Observateur d’événements, puis dans le **menu Affichage**, cliquez sur **Afficher les journaux d’analyse et de débogage**.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un journal d’analyse s’affiche sous Hyper-V-VMMS

![Résolution des problèmes Hyper-V locaux](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Dans le volet **Actions**, cliquez sur **Activer le journal**. Une fois activé, il apparaît dans **Analyseur de performances** comme une session de suivi d’événement située sous **Ensembles de collecteurs de données**.

![Résolution des problèmes Hyper-V locaux](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Pour afficher les informations collectées, commencez par arrêter la session de suivi en désactivant le journal, puis enregistrez le journal et rouvrez-le dans l’Observateur d’événements ou en utilisant d’autres outils pour le convertir selon vos besoins.

## <a name="reaching-out-for-microsoft-support"></a>Contacter le support Microsoft
### <a name="log-collection"></a>Collection de journaux
Pour la protection de site VMM, consultez la page [Collecte de journaux ASR à l’aide d’un outil SDP (Support Diagnostics Platform, plateforme de diagnostics de support)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) pour collecter les journaux requis.

Pour la protection de site Hyper-V, téléchargez l’[outil](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) et exécutez-le sur l’hôte Hyper-V pour collecter les journaux.

Pour les scénarios VMware/physiques, consultez la page [Collecte de journaux Azure Site Recovery pour la protection de sites VMware et physiques](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) pour collecter les journaux requis.

L’outil collecte les journaux localement, dans un sous-dossier au nom aléatoire et situé sous **%LocalAppData%\ElevatedDiagnostics**.

![Exemples d’étapes provenant de la protection du site Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Ouverture d’un ticket de support
Pour déclencher un ticket de support pour ASR, accédez au support Azure en utilisant l’URL disponible sur <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Articles de la Base de connaissances
* [Comment conserver la lettre de lecteur pour les machines virtuelles protégées qui ont basculé ou migré sur Azure](http://support.microsoft.com/kb/3031135)
* [Comment gérer l’utilisation de la bande passante réseau par la protection Local vers Azure](https://support.microsoft.com/kb/3056159)
* [ASR : Erreur « La ressource de cluster est introuvable » quand vous essayez d’activer la protection d’une machine virtuelle](http://support.microsoft.com/kb/3010979)
* [Guide de présentation et de résolution des problèmes relatifs à la réplication Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Les erreurs ASR courantes et leur résolution
Vous trouverez ci-dessous les erreurs courantes qui peuvent être testées et leur résolution. Chaque erreur est documentée sur une page WIKI à part.

### <a name="general"></a>Généralités
* <span style="color:green;">NOUVEAU</span> [Échec de travaux avec l’erreur « Une opération est en cours ». Erreur 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">NOUVEAU</span> [Échec de travaux avec l’erreur « Le serveur n’est pas connecté à Internet ». Erreur 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Paramétrage
* [Impossible d’inscrire le serveur VMM en raison d’une erreur interne. Reportez-vous à la vue tâches dans le portail Site Recovery pour plus d'informations sur l'erreur. Exécutez de nouveau le programme d'installation pour inscrire le serveur.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [Impossible d’établir une connexion au coffre Hyper-V Recovery Manager. Vérifiez les paramètres de proxy ou réessayez ultérieurement.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuration
* [Impossible de créer un groupe de protection : une erreur est survenue lors de la récupération de la liste de serveurs.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Le cluster hôte Hyper-V contient au moins une carte réseau statique ou aucune carte connectée n'est configurée pour utiliser DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [VMM n'a pas les autorisations nécessaires pour effectuer une action](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [Impossible de sélectionner le compte de stockage dans l’abonnement lors de la configuration de la protection](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protection
* <span style="color:green;">NOUVEAU</span> [Échec de l’activation de la protection avec l’erreur « Impossible de configurer la protection pour la machine virtuelle ». Erreur 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">NOUVEAU</span> [Échec de l’activation de la protection avec l’erreur « Impossible d’activer la protection pour la machine virtuelle ». Erreur 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">NOUVEAU</span> [Erreur de migration en direct 23848 : La machine virtuelle va être déplacée avec le type En direct. Ceci peut endommager l’état de protection de la récupération de la machine virtuelle.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
* [Échec de l'activation de la protection, car l'agent n’est pas installé sur la machine hôte](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [Impossible de trouver un hôte approprié pour la machine virtuelle de réplication, en raison des faibles ressources de calcul](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [Impossible de trouver un hôte approprié pour la machine virtuelle de réplication, en raison de l'absence de réseau logique attaché](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [Impossible de se connecter à la machine hôte de réplication : impossible d'établir une connexion](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Récupérer
* Impossible pour VMM de terminer l'opération de l'hôte :
  * [Basculement vers le point de récupération sélectionné pour la machine virtuelle : accès général refusé.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Échec du basculement d’Hyper-V vers le point de récupération sélectionné pour la machine virtuelle : opération abandonnée, essayez un point de récupération plus récent. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * Impossible d’établir une connexion avec le serveur (0x00002EFD)
    * [Impossible pour Hyper-V d'activer la réplication inverse pour la machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Impossible pour Hyper-V d'activer la réplication pour la machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [Impossible de valider le basculement de machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [Le plan de récupération contient des machines virtuelles qui ne sont pas prêtes pour un basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [La machine virtuelle n'est pas prête pour un basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [La machine virtuelle ne fonctionne pas et n'est pas hors tension](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [Une opération hors bande s’est produite sur une machine virtuelle et la validation du basculement a échoué](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* Test Failover
  * [Impossible de lancer le basculement : test de basculement en cours](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">NOUVEAU</span> Le basculement expire avec le message « PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout » en raison des paramètres de configuration du groupe de sécurité réseau associé à la machine virtuelle ou au sous-réseau auquel la machine appartient. Consultez [« PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout »](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) pour plus de détails.

### <a name="configuration-server,-process-server,-master-target"></a>Serveur de configuration, Serveur de traitement, Serveur maître
Serveur de configuration, Serveur de traitement, Serveur maître

* [Échec de l'hôte ESXi sur lequel le serveur de traitement/configuration est hébergé comme machine virtuelle avec un écran violet de la mort.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Résolution des problèmes après un basculement de bureau à distance
* Suite à un basculement, de nombreux clients ont été confrontés à des problèmes de connexion à la machine virtuelle dans Azure. [Utiliser le document de résolution des problèmes pour le protocole RDP dans la machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Ajout d’une adresse IP publique sur une machine virtuelle du Gestionnaire des ressources
Si le bouton **Connecter** du portail est grisé et que vous n’êtes pas connecté à Azure avec une connexion Express Route ou VPN de site à site, vous devez créer votre machine virtuelle et lui attribuer une adresse IP publique pour pouvoir utiliser le protocole RDP/SSH. Suivez les étapes ci-dessous pour ajouter une adresse IP publique sur l’interface réseau de la machine virtuelle.  

![Ajout d’une adresse IP publique sur l’interface réseau de la machine virtuelle basculée](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)

<!--HONumber=Oct16_HO2-->


