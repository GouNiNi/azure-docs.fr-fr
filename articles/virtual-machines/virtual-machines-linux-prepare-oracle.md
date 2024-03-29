---
title: Préparation d’une machine virtuelle Oracle Linux pour Azure | Microsoft Docs
description: Configuration étape par étape d’une machine virtuelle Oracle exécutant Linux dans Microsoft Azure.
services: virtual-machines-linux
author: bbenz
manager: timlt
documentationcenter: virtual-machines
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2015
ms.author: bbenz

---
# Préparation d’une machine virtuelle Linux Oracle pour Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

* [Préparation d’une machine virtuelle Oracle Linux 6.4+ pour Azure](virtual-machines-linux-oracle-create-upload-vhd.md)
* [Préparation d’une machine virtuelle Oracle Linux 7.0+ pour Azure](virtual-machines-linux-oracle-create-upload-vhd.md)

## Conditions préalables
Cet article suppose que vous avez déjà installé un système d'exploitation Oracle Linux dans un disque dur virtuel. Il existe de multiples outils dédiés à la création de fichiers .vhd, comme la solution de virtualisation Hyper-V. Pour obtenir des instructions, consultez la page [Installation de Hyper-V et création d’une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx).

**Notes générales d’installation d’Oracle Linux**

* Le noyau Oracle compatible Red Hat et son noyau UEK3 (Unbreakable Enterprise Kernel) sont tous les deux pris en charge sur Hyper-V et Azure. Pour de meilleurs résultats, veillez à mettre à jour le noyau lorsque vous préparez votre disque dur virtuel Oracle Linux.
* Le noyau UEK2 d'Oracle n'est pas pris en charge sur Hyper-V et Azure, car il ne comporte pas les pilotes nécessaires.
* Azure ne prend pas en charge le nouveau format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de l’applet de commande convert-vhd.
* Lors de l’installation du système Linux, il est recommandé d’utiliser les partitions standard plutôt que LVM (qui est souvent le choix par défaut pour de nombreuses installations). Ceci permettra d'éviter les conflits de noms avec des machines virtuelles clonées, notamment si un disque de système d'exploitation doit être relié à une autre machine virtuelle pour la dépanner. Les techniques LVM ou [RAID](virtual-machines-linux-configure-raid.md) sont utilisables sur les disques de données si vous le souhaitez.
* NUMA n'est pas pris en charge pour les tailles de machines virtuelles plus élevées en raison d'un bogue dans les versions du noyau Linux inférieures à 2.6.37. Ce problème touche spécialement les distributions qui utilisent le noyau Red Hat 2.6.32 en amont. L'installation manuelle de l'Agent Linux Azure (waagent) désactive automatiquement NUMA dans la configuration GRUB pour le noyau Linux. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* Ne configurez pas une partition d’échange sur le disque du système d’exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes ci-dessous fournissent plus d’informations à ce sujet.
* La taille des disques durs virtuels doit être un multiple de 1 Mo.
* Assurez-vous que le référentiel `Addons` est activé. Modifiez le fichier `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux), modifiez la ligne `enabled=0` en `enabled=1` sous **[ol6\_addons]** ou **[ol7\_addons]** dans ce fichier.

## Oracle Linux 6.4+
Vous devez suivre des étapes de configuration spécifiques dans le système d'exploitation afin que la machine virtuelle fonctionne sur Azure.

1. Dans le panneau central de Hyper-V Manager, sélectionnez la machine virtuelle.
2. Cliquez sur **Connect** pour ouvrir la fenêtre de la machine virtuelle.
3. Exécutez la commande suivante pour désinstaller NetworkManager :
   
        # sudo rpm -e --nodeps NetworkManager
   
   > [!NOTE]
   > si le package n'est pas déjà installé, la commande échoue et un message d'erreur s'affiche. Ceci est normal.
   > 
   > 
4. Créez un fichier nommé **network** dans le répertoire /etc/sysconfig/ contenant le texte suivant :
   
    `NETWORKING=yes` `HOSTNAME=localhost.localdomain`
5. Créez un fichier nommé **ifcfg-eth0** dans le répertoire /etc/sysconfig/network-scripts/ contenant le texte suivant :
   
       DEVICE=eth0
       ONBOOT=yes
       BOOTPROTO=dhcp
           TYPE=Ethernet
           USERCTL=no
           PEERDNS=yes
       IPV6INIT=no
6. Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Azure ou Hyper-V :
   
       # sudo mkdir -m 0700 /var/lib/waagent
       # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
       # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null
7. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :
   
       # chkconfig network on
8. Saisissez la commande suivante pour installer python-pyasn1 :
   
       # sudo yum install python-pyasn1
9. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez le fichier « /boot/grub/menu.lst » dans un éditeur de texte et vérifiez que le noyau par défaut comprend les paramètres suivants :
   
       console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Ce permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cela désactive NUMA en raison d'une erreur dans le noyau compatible Red Hat d'Oracle.
   
   Outre les précautions ci-dessus, nous recommandons de *supprimer* les paramètres suivants :
   
       rhgb quiet crashkernel=auto
   
   Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.
   
   L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus. Cela peut s’avérer problématique sur les machines virtuelles de petite taille.
10. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.
11. Installez l'agent linux Azure en exécutant la commande suivante :
    
    # sudo yum install WALinuxAgent
    L'installation du package WALinuxAgent entraîne la suppression des packages NetworkManager et NetworkManager-gnome, s'ils n'avaient pas déjà été supprimés comme indiqué à l'étape 2.
12. Ne créez pas d'espace swap sur le disque du système d'exploitation.
    
    L’agent Linux Azure peut configurer automatiquement un espace swap à l’aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Lorsque vous avez installé l’agent Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :
    
       ResourceDisk.Format=y
    
       ResourceDisk.Filesystem=ext4
    
       ResourceDisk.MountPoint=/mnt/resource
    
       ResourceDisk.EnableSwap=y
    
       ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
13. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
14. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## Oracle Linux 7.0+
**Modifications dans Oracle Linux 7**

La préparation d’une machine virtuelle Oracle Linux 7 pour Azure est très similaire au processus pour Oracle Linux 6. Cependant, certaines différences importantes méritent d’être notées :

* Le noyau compatible Red Hat et le noyau UEK3 d'Oracle sont pris en charge dans Azure. Nous recommandons le noyau UEK3.
* Le package NetworkManager n'est plus en conflit avec l'agent Azure Linux. Ce package est installé par défaut ; nous recommandons de ne pas le supprimer.
* GRUB2 est maintenant utilisé comme chargeur de démarrage (bootloader) par défaut ; la modification des paramètres du noyau a donc changé (voir ci-dessous).
* XFS est maintenant le système de fichiers par défaut. Le système de fichiers ext4 est toujours utilisable si vous le souhaitez.

**Configuration**

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.
2. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.
3. Créez un fichier nommé **network** dans le répertoire /etc/sysconfig/ contenant le texte suivant :
   
       NETWORKING=yes
       HOSTNAME=localhost.localdomain
4. Créez un fichier nommé **ifcfg-eth0** dans le répertoire /etc/sysconfig/network-scripts/ contenant le texte suivant :
   
       DEVICE=eth0
       ONBOOT=yes
       BOOTPROTO=dhcp
       TYPE=Ethernet
       USERCTL=no
           PEERDNS=yes
       IPV6INIT=no
5. Déplacez (ou supprimez) les règles udev afin d'éviter la génération de règles statiques pour l'interface Ethernet. Ces règles entraînent des problèmes lors du clonage de machines virtuelles dans Microsoft Azure ou Hyper-V.
   
       # sudo mkdir -m 0700 /var/lib/waagent
       # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
       # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null
6. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :
   
       # sudo chkconfig network on
7. Exécutez la commande suivante pour installer le package python-pyasn1 :
   
       # sudo yum install python-pyasn1
8. Exécutez la commande suivante pour effacer les métadonnées yum actuelles et installer les mises à jour le cas échéant :
   
       # sudo yum clean all
       # sudo yum -y update
9. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour cela, ouvrez /etc/default/grub dans un éditeur de texte et modifiez le paramètre GRUB\_CMDLINE\_LINUX, par exemple :
   
       GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Ce permet également d'assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d'Azure. Outre les précautions ci-dessus, nous recommandons de *supprimer* les paramètres suivants :
   
       rhgb quiet crashkernel=auto
   
   Le démarrage graphique et transparent n'est pas utile dans un environnement cloud où nous voulons que tous les journaux soient envoyés au port série.
   
   L’option `crashkernel` peut éventuellement être conservée. Notez cependant que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus. Cela peut s’avérer problématique sur les machines virtuelles de petite taille.
10. Lorsque vous avez fini de modifier le fichier « /etc/default/grub », exécutez la commande suivante pour régénérer la configuration grub :
    
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. C'est généralement le cas par défaut.
12. Installez l'agent linux Azure en exécutant la commande suivante :
    
    # sudo yum install WALinuxAgent
13. Ne créez pas d'espace swap sur le disque du système d'exploitation.
    
    L’agent Linux Azure peut configurer automatiquement un espace swap à l’aide du disque local de ressources connecté à la machine virtuelle après déploiement sur Azure. Notez que le disque de ressources local est un disque *temporaire* et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Lorsque vous avez installé l’agent Linux Azure (voir l’étape précédente), modifiez les paramètres suivants dans le fichier /etc/waagent.conf :
    
       ResourceDisk.Format=y
       ResourceDisk.Filesystem=ext4
       ResourceDisk.MountPoint=/mnt/resource
       ResourceDisk.EnableSwap=y
       ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.
14. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :
    
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
15. Cliquez sur **Action -> Arrêter** dans le Gestionnaire Hyper-V. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

<!---HONumber=AcomDC_0824_2016-->