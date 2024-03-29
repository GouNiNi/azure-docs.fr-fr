---
title: Création d'un package de support StorSimple | Microsoft Docs
description: Apprenez à créer, déchiffrer et modifier un package de support pour votre appareil StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli

---
# Création et gestion d’un package de prise en charge StorSimple
## Vue d'ensemble
Un package de support StorSimple est un mécanisme facile à utiliser qui collecte tous les journaux appropriés afin d’aider le support Microsoft à résoudre tout problème lié à votre appareil StorSimple. Les fichiers journaux collectés sont chiffrés et compressés.

Ce didacticiel inclut des instructions détaillées pour créer et gérer le package de support.

## Créer et charger un package de support dans le portail Azure Classic
Vous pouvez créer et télécharger un package de support sur le site de support technique de Microsoft à l’aide de la page **Maintenance** du service dans le portail Azure Classic.

> [!NOTE]
> Le chargement nécessite une clé de sécurité de support. Votre ingénieur du support technique doit vous la fournir par e-mail.
> 
> 

Un package de support chiffré et compressé (fichier .cab) est créé et chargé sur le site de support. L’ingénieur du support technique peut ensuite récupérer ce package à partir du site de support pour résoudre le problème.

Procédez comme suit dans le portail classique pour créer un package de support.

#### Pour créer un package de support dans le portail Azure Classic
1. Sélectionnez **Appareils** > **Maintenance**.
2. Dans la section **Package de support**, sélectionnez **Créer et télécharger le package de support**.
3. Dans la boîte de dialogue **Créer et télécharger le package de support**, procédez comme suit :
   
    ![Créer un package de support](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * Dans la zone de texte **Clé de sécurité de support**, entrez la clé de sécurité. Votre ingénieur de support technique Microsoft doit vous envoyer cette clé de sécurité par e-mail.
   * Cochez la case pour accepter de télécharger automatiquement le package de support sur le site de support de Microsoft.
   * Cliquez sur l’icône en forme de coche ![Icône en forme de coche](./media/storsimple-create-manage-support-package/IC740895.png).

## Créer manuellement un package de support
Dans certains cas, vous devez créer manuellement le package de support via Windows PowerShell pour StorSimple. Par exemple :

* Si vous devez supprimer des informations sensibles de vos fichiers journaux avant de le partager avec le support Microsoft.
* Si vous rencontrez des difficultés à charger le package en raison de problèmes de connectivité.

Vous pouvez partager votre package de support généré manuellement avec le support Microsoft par e-mail. Procédez comme suit pour créer un package de support dans Windows PowerShell pour StorSimple.

#### Pour créer un package de support dans Windows PowerShell pour StorSimple
1. Pour démarrer une session Windows PowerShell en tant qu’administrateur sur l’ordinateur distant utilisé pour la connexion à votre appareil StorSimple, utilisez la commande suivante :
   
    `Start PowerShell`
2. Dans la session Windows PowerShell, connectez-vous à la console SSAdmin de votre appareil :
   
   * À l’invite de commandes, tapez :
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * Dans la boîte de dialogue qui s’affiche, saisissez votre mot de passe administrateur. Le mot de passe par défaut est :
     
      `Password1`
     
      ![Boîte de dialogue des informations d’identification PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)
   * Sélectionnez **OK**.
   * À l’invite de commandes, tapez :
     
      `Enter-PSSession $MS`
3. Dans la session qui s’ouvre, saisissez la commande appropriée.
   
   * Pour les partages réseau protégés par un mot de passe, saisissez :
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       Vous serez invité à entrer un mot de passe, le chemin d’accès au dossier réseau partagé et une phrase secrète de chiffrement (car le package de support est chiffré). Un package de support est ensuite créé dans le dossier spécifié.
   * Pour les partages qui ne sont pas protégés par un mot de passe, vous n’avez pas besoin du paramètre `-Credential`. Entrez les informations suivantes :
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       Le package de support est créé pour les deux contrôleurs dans le dossier réseau partagé spécifié. Il s’agit d’un fichier compressé et chiffré qui peut être envoyé au support technique de Microsoft à des fins de dépannage. Pour plus d'informations, consultez [Contacter le support technique de Microsoft](storsimple-contact-microsoft-support.md).

### Les paramètres de l’applet de commande Export-HcsSupportPackage
Vous pouvez utiliser les paramètres suivants avec l’applet de commande Export-HcsSupportPackage.

| Paramètre | Obligatoire ou facultatif | Description |
| --- | --- | --- |
| `-Path` |Requis |Permet d’indiquer l’emplacement du dossier réseau partagé dans lequel le package de support est placé. |
| `-EncryptionPassphrase` |Requis |Permet de fournir une phrase secrète permettant de chiffrer le package de support. |
| `-Credential` |Facultatif |Permet de fournir des informations d’identification d’accès pour le dossier réseau partagé. |
| `-Force` |Facultatif |Permet d'ignorer l'étape de confirmation de la phrase secrète de chiffrement. |
| `-PackageTag` |Facultatif |Permet de spécifier un répertoire sous *Chemin d’accès* dans lequel le package de support est placé. La valeur par défaut est [nom de l’appareil]-[date et heure actuelles : aaaa-MM-jj-HH-mm-ss]. |
| `-Scope` |Facultatif |Définir sur **Cluster** (valeur par défaut) pour créer un package de support pour les deux contrôleurs. Si vous souhaitez créer un package uniquement pour le contrôleur actuel, spécifiez **Contrôleur**. |

## Modification d'un package de support
Une fois que vous avez généré un package de support, vous devrez peut-être modifier le package pour en supprimer les informations sensibles. Cela peut inclure des noms de volume, les adresses IP d’appareil et les noms des sauvegardes des fichiers journaux.

> [!IMPORTANT]
> Vous pouvez uniquement modifier un package de support qui a été généré à l'aide de Windows PowerShell pour StorSimple. Vous ne pouvez pas modifier un package créé dans le portail Azure Classic avec le service StorSimple Manager.
> 
> 

Pour modifier un package de support avant de le télécharger sur le site de support technique de Microsoft, vous devez déchiffrer le package de support, modifier les fichiers et le chiffrer de nouveau. Procédez comme suit.

#### Pour modifier un package de support dans Windows PowerShell pour StorSimple
1. Générez un package de support, comme décrit dans la section [Création d’un package de support dans Windows PowerShell pour StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).
2. [Téléchargez le script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre ordinateur client.
3. Importez le module Windows PowerShell. Spécifiez le chemin d’accès au dossier local dans lequel vous avez téléchargé le script. Pour importer le module, entrez :
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. Tous les fichiers sont des fichiers *.aes* compressés et chiffrés. Pour décompresser et déchiffrer les fichiers, entrez :
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    Sachez que les extensions sont maintenant affichées pour tous les fichiers.
   
    ![Modifier un package de support](./media/storsimple-create-manage-support-package/IC750706.png)
5. Lorsque vous êtes invité à entrer la phrase secrète de chiffrement, tapez la phrase secrète utilisée lors de la création du package de support.
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. Accédez au dossier qui contient les fichiers journaux. Étant donné que les fichiers journaux sont désormais décompressés et déchiffrés, leurs extensions d’origine sont affichées. Modifiez ces fichiers pour supprimer toutes les informations spécifiques au client, comme les noms de volumes et les adresses IP d’appareils, puis enregistrez les fichiers.
7. Fermez les fichiers pour les compresser au format gzip et les chiffrer avec AES-256. Cette opération est exécutée à des fins de sécurité et de rapidité lors du transfert du package de support sur un réseau. Pour compresser et chiffrer les fichiers, entrez les informations suivantes :
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![Modifier un package de support](./media/storsimple-create-manage-support-package/IC750707.png)
8. Lorsque vous y êtes invité, fournissez une phrase secrète de chiffrement pour le package de support modifié.
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. Notez la nouvelle phrase secrète afin de pouvoir la partager avec le support technique de Microsoft si nécessaire.

### Exemple : Modification de fichiers dans un package de support sur un partage protégé par mot de passe
L’exemple suivant illustre comment déchiffrer, modifier et re-chiffrer un package de support.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## Étapes suivantes
* Découvrez comment [utiliser les packages de support et les journaux de l’appareil pour dépanner votre déploiement](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).
* Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0817_2016-->