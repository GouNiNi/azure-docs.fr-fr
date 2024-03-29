---
title: "Sauvegarder et restaurer les machines virtuelles chiffrées à l’aide de Sauvegarde Azure"
description: "Cet article présente l’expérience de sauvegarde et de restauration de machines virtuelles chiffrées à l’aide d’Azure Disk Encryption (ADE)."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 8387f186-7d7b-400a-8fc3-88a85403ea63
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/25/2016
ms.author: markgal; jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd65e7acc10b3e750025279820bddbdef5de5498


---
# <a name="backup-and-restore-encrypted-vms-using-azure-backup"></a>Sauvegarder et restaurer les machines virtuelles chiffrées à l’aide de Sauvegarde Azure
Cet article présente les étapes pour sauvegarder et restaurer des machines virtuelles à l’aide de Sauvegarde Azure. Il fournit également des détails sur les scénarios pris en charge, les composants requis et les étapes de dépannage en cas d’erreur.

## <a name="supported-scenarios"></a>Scénarios pris en charge
> [!NOTE]
> 1. La sauvegarde et la restauration de machines virtuelles chiffrées sont prises en charge uniquement pour les machines virtuelles déployées dans Resource Manager. Ces opérations ne sont pas prises en charge par les machines virtuelles classiques. <br>
> 2. Elles sont prises en charge uniquement pour les machines virtuelles chiffrées en même temps à l’aide de la clé de chiffrement BitLocker (BEK, BitLocker Encryption Key) et de la clé KEK (Key Encryption Key, clé de chiffrement de clé). Elles ne sont pas prises en charge pour les machines virtuelles chiffrées à l’aide de la clé de chiffrement BitLocker uniquement. <br>
> 
> 

## <a name="pre-requisites"></a>Conditions préalables
1. Les machines virtuelles ont été chiffrées à l’aide d’[Azure Disk Encryption](../security/azure-security-disk-encryption.md). Elles doivent être chiffrées en même temps à l’aide de la clé de chiffrement BitLocker et de la clé KEK (Key Encryption Key, clé de chiffrement de clé).
2. Le coffre Recovery Services a été créé et la réplication du stockage a été définie à l’aide des étapes mentionnées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md).

## <a name="backup-encrypted-vm"></a>Sauvegarde de machines virtuelles chiffrées
Utilisez les étapes suivantes pour définir l’objectif de sauvegarde, définir une stratégie, configurer les éléments et déclencher une sauvegarde.

### <a name="configure-backup"></a>Configurer une sauvegarde
1. Si l’un de vos coffres Recovery Services est déjà ouvert, passez à l’étape suivante. Si vous n’avez aucun coffre Recovery Services ouvert, mais que vous vous trouvez dans le portail Azure, cliquez sur **Parcourir**dans le menu Hub.
   
   * Dans la liste des ressources, tapez **Recovery Services**.
   * Au fur et à mesure de la saisie, la liste est filtrée. Lorsque vous voyez **Coffres Recovery Services**, cliquez dessus.
     
      ![Créer un archivage de Recovery Services - Étape 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>
     
     La liste des archivages de Recovery Services s’affiche. Dans la liste des archivages de Recovery Services, sélectionnez un archivage.
     
     Le tableau de bord de l’archivage sélectionné s'ouvre.
2. Dans la liste d’éléments qui s’affiche sous le coffre, cliquez sur **Sauvegarder** pour ouvrir le panneau Sauvegarde.
   
      ![Ouvrir le panneau Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png) 
3. Dans le panneau Sauvegarde, cliquez sur **Objectif de sauvegarde** pour ouvrir le panneau Objectif de sauvegarde.
   
      ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png) 
4. Dans le panneau Objectif de sauvegarde, définissez **Où s’exécute votre charge de travail ?** sur Azure et **Que souhaitez-vous sauvegarder ?** sur Machine virtuelle, puis cliquez sur **OK**.
   
   Le panneau Backup Goal (Objectif de la sauvegarde) se ferme et le panneau Stratégie de sauvegarde s’ouvre.
   
   ![Ouvrir le panneau Scénario](./media/backup-azure-vms-encryption/select-backup-goal-two.png) 
5. Dans le panneau Stratégie de sauvegarde, sélectionnez la stratégie de sauvegarde à appliquer au coffre, puis cliquez sur **OK**.
   
      ![Sélectionner la stratégie de sauvegarde](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png) 
   
    Les détails de la stratégie par défaut sont indiqués à l’écran. Pour créer une stratégie, sélectionnez **Créer** dans le menu déroulant. Lorsque vous cliquez sur **OK**, la stratégie de sauvegarde est associée au coffre.
   
    Ensuite, choisissez les machines virtuelles à associer à l'archivage.
6. Sélectionnez les machines virtuelles chiffrées à associer à la stratégie spécifiée, puis cliquez sur **OK**.
   
      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
7. Cette page affiche un message sur le coffre de clés associé aux machines virtuelles chiffrées sélectionnées. Le service de sauvegarde requiert l’accès en lecture seule aux clés et clés secrètes dans le coffre de clés. Il utilise ces autorisations pour sauvegarder la clé et la clé secrète, ainsi que les machines virtuelles associées. 
   
      ![Message Machines virtuelles chiffrées](./media/backup-azure-vms-encryption/encrypted-vm-message.png)
   
      Maintenant que vous avez défini tous les paramètres du coffre, dans le panneau Sauvegarde, cliquez sur Activer la sauvegarde en bas de la page. Le fait d’activer la sauvegarde déploie la stratégie dans le coffre et les machines virtuelles.
8. La prochaine phase de préparation est l’installation de l’Agent de machine virtuelle ou s’assurer que l’Agent de machine virtuelle est installé. Pour faire de même, utilisez les étapes présentées dans l’article [Préparation de l’environnement pour la sauvegarde](backup-azure-arm-vms-prepare.md). 

### <a name="triggering-backup-job"></a>Déclenchement d’un travail de sauvegarde
Utilisez les étapes présentées dans l’article [Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](backup-azure-arm-vms.md) pour déclencher le travail de sauvegarde.

## <a name="restore-encrypted-vm"></a>Restaurer une machine virtuelle chiffrée
L’expérience de restauration est la même, que les machines virtuelles soient chiffrées ou non. Utilisez les étapes présentées dans [Restaurer des machines virtuelles dans le portail Azure](backup-azure-arm-restore-vms.md) pour restaurer la machine virtuelle chiffrée. Au cas où vous devez restaurer les clés et clés secrètes, vous devez vérifier que le coffre de clés permettant de les restaurer existe déjà.

## <a name="troubleshooting-errors"></a>Résolution des erreurs
| Opération | Détails de l’erreur | Résolution : |
| --- | --- | --- |
| Sauvegarde |La validation a échoué, car la machine virtuelle est chiffrée avec une clé BEK uniquement. Les sauvegardes peuvent être activées uniquement pour les machines virtuelles chiffrées aussi bien avec des clés BEK qu’avec des clés KEK. |La machine virtuelle doit être chiffrée simultanément à l’aide de clés BEK et KEK. Après l’application de ce chiffrement, la sauvegarde doit être activée. |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car le coffre de clés associé à cette machine virtuelle n’existe pas. |Pour créer un coffre de clés, voir [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md). Consultez [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |
| Restauration |Vous ne pouvez pas restaurer cette machine virtuelle chiffrée, car la clé et la clé secrète associées n’existent pas. |Consultez [Restore key vault key and secret using Azure Backup](backup-azure-restore-key-secret.md) (Restaurer la clé et le secret de coffre de clés à l’aide de Sauvegarde Azure) pour restaurer la clé et la clé secrète si celles-ci n’existent pas. |




<!--HONumber=Nov16_HO3-->


