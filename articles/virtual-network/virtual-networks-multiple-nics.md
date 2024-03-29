---
title: Créer une machine virtuelle avec plusieurs cartes d’interface réseau
description: Découvrir comment créer et configurer des machines virtuelles avec plusieurs cartes réseau
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management,azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial

---
# Créer une machine virtuelle avec plusieurs cartes d’interface réseau
Vous pouvez créer des machines virtuelles (VM) dans Azure et joindre plusieurs cartes d’interface réseau (NIC) à chacune d’elles. Il s’agit d’une exigence pour de nombreuses appliances virtuelles réseau, telles que les solutions de distribution d’applications et d’optimisation de réseau étendu (WAN). La fonctionnalité Multi-NIC contribue également à renforcer la gestion du trafic réseau en permettant notamment d’isoler le trafic entre une NIC frontale et une ou plusieurs NIC principales, ou de séparer le trafic du plan de données de celui du plan de gestion.

![Fonctionnalité Multi-NIC pour machine virtuelle](./media/virtual-networks-multiple-nics/IC757773.png)

La figure ci-dessus illustre une machine virtuelle équipée de trois NIC connectées à différents sous-réseaux.

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

* Une adresse IP virtuelle (déploiements classiques) accessible via Internet n’est prise en charge que sur la NIC « par défaut ». Il n’existe qu’une seule adresse IP virtuelle pour l’adresse IP de la NIC par défaut.
* Pour l’instant, les adresses IP publiques de niveau d’instance (LPIP) (déploiements classiques) ne sont pas prises en charge pour les machines virtuelles à plusieurs NIC.
* L’ordre des NIC à l’intérieur de la machine virtuelle est aléatoire et peut changer lors des mises à jour de l’infrastructure Azure. Toutefois, les adresses IP et les adresses MAC Ethernet correspondantes restent identiques. Par exemple, supposons qu’**Eth1** comporte l’adresse IP 10.1.0.100 et l’adresse MAC 00-0D-3A-B0-39-0D. Après une mise à jour et un redémarrage de l’infrastructure Azure, il se peut qu’Eth1 devienne **Eth2**, mais l’association d’adresses IP et MAC ne change pas. Lorsqu’un redémarrage est effectué par le client, l’ordre des NIC reste identique.
* L’adresse de chaque NIC équipant chacune des machines virtuelles doit figurer dans un sous-réseau, et les différentes NIC d’une même machine virtuelle peuvent recevoir des adresses situées dans le même sous-réseau.
* La taille de machine virtuelle détermine le nombre de NIC que vous pouvez créer pour une machine virtuelle. Voir les articles sur les tailles de machine virtuelles [Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) et [Linux](../virtual-machines/virtual-machines-linux-sizes.md) afin de déterminer le nombre de cartes réseau que chaque taille de machine virtuelle prend en charge.

## Groupes de sécurité réseau (NSG)
Dans un déploiement avec gestionnaire de ressources, les NIC d’une machine virtuelle peuvent être associées à un groupe de sécurité réseau (NSG), y compris les NIC d’une machine virtuelle sur laquelle la fonctionnalité Multi-NIC est activée. Si une NIC reçoit une adresse d’un sous-réseau associé à un NSG, les règles qui régissent le NSG du sous-réseau s’appliquent également à cette NIC. Outre l’association de sous-réseaux à des NSG, vous pouvez également associer une NIC à un NSG.

Si un sous-réseau est associé à un NSG,et qu’une NIC de ce sous-réseau est liée individuellement à un NSG, les règles du NSG associé sont appliquées dans l’« **ordre du flux de trafic** » en fonction de la direction du trafic entrant ou sortant de la NIC :

* Le **trafic entrant** dont la destination est la NIC en question passe d’abord par le sous-réseau, en déclenchant les règles du NSG du sous-réseau, puis transite par la NIC et déclenche les règles du NSG de la NIC.
* Le **trafic sortant** dont la source est la NIC en question commence par sortir de la NIC, en déclenchant les règles du NSG du sous-réseau, puis transite par le sous-réseau, et déclenche alors les règles du NSG du sous-réseau.

En savoir plus sur les [groupes de sécurité réseau](virtual-networks-nsg.md) et leur application selon les associations de sous-réseaux, de machines virtuelles et de cartes réseau.

## Configuration d’une machine virtuelle avec plusieurs NIC dans un déploiement classique
Les instructions ci-dessous expliquent comment créer une machine virtuelle multi-NIC contenant 3 NIC : une NIC par défaut et deux NIC supplémentaires. Cette procédure de configuration crée une machine virtuelle qui sera configurée en fonction du fragment de fichier de configuration de service ci-dessous :

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


La configuration requise pour l’exécution des commandes PowerShell de cet exemple est la suivante :

* Un abonnement Azure.
* Un réseau virtuel configuré. Pour plus d’informations sur les réseaux virtuels, voir l’article [Présentation du réseau virtuel](virtual-networks-overview.md).
* La dernière version d’Azure PowerShell téléchargée et installée. Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Pour créer une machine virtuelle avec plusieurs cartes réseau, suivez la procédure ci-dessous :

1. Sélectionnez une image de machine virtuelle dans la galerie d’images de machine virtuelle Azure. Notez que les images changent fréquemment et sont disponibles par région. L’image indiquée dans l’exemple ci-dessous étant susceptible de changer ou de ne pas être disponible dans votre région, veillez à spécifier l’image dont vous avez besoin.
   
        $image = Get-AzureVMImage `
            -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
2. Créez une configuration de machine virtuelle.
   
        $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
            -Image $image.ImageName –AvailabilitySetName "MyAVSet"
3. Créez la connexion d’administrateur par défaut.
   
        Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
            -Password "<YourAdminPassword>"
4. Ajoutez des NIC à la configuration de machine virtuelle.
   
        Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
            -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
        Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
            -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
5. Spécifiez le sous-réseau et l’adresse IP de la NIC par défaut.
   
        Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
        Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
6. Créez la machine virtuelle dans votre réseau virtuel.
   
        New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

> [!NOTE]
> Le réseau virtuel que vous spécifiez ici doit déjà exister (comme indiqué dans la configuration requise). L’exemple ci-dessous définit un réseau virtuel nommé **MultiNIC-VNet**.
> 
> 

## Limitations
Les limitations suivantes s’appliquent lors de l’utilisation de la fonctionnalité Multi-NIC :

* Les machines virtuelles à plusieurs NIC doivent être créées dans des réseaux virtuels Azure. Les machines virtuelles n’appartenant pas à un réseau virtuel ne peuvent pas être configurées avec plusieurs cartes d’interface réseau.
* Toutes les machines virtuelles dans un ensemble de disponibilité doivent utiliser plusieurs cartes réseau ou une NIC unique. Il ne peut pas y avoir un mélange de machines virtuelles avec plusieurs cartes réseau et de machines virtuelles avec une carte réseau unique au sein d’un groupe de disponibilité. Les mêmes règles s’appliquent pour les machines virtuelles dans un service cloud.
* Une machine virtuelle avec une seule carte réseau ne peut pas être configurée avec plusieurs cartes d’interface réseau (et inversement) une fois qu’elle est déployée. Il faudra la supprimer et la recréer.

## Accès des cartes réseau secondaires à d’autres sous-réseaux
Par défaut, les cartes réseau secondaires ne sont pas configurées avec une passerelle par défaut. Par conséquent, le flux de trafic sur les cartes réseau secondaires sera limité à l’intérieur du même sous-réseau. Si les utilisateurs souhaitent activer les cartes réseau secondaires afin de communiquer en dehors de leur propre sous-réseau, il leur faudra ajouter une entrée dans la table de routage afin de configurer la passerelle, tel que décrit ci-dessous.

> [!NOTE]
> Les machines virtuelles créées avant juillet 2015 peuvent avoir une passerelle par défaut configurée pour toutes les cartes réseau. La passerelle par défaut pour les cartes réseau secondaires ne sera pas supprimée tant que ces machines virtuelles ne seront pas redémarrés. Dans les systèmes d’exploitation qui utilisent le modèle de routage d’hôte faible comme Linux, la connectivité Internet sera interrompue si les trafic entrants et sortants utilisent différentes cartes réseau.
> 
> 

### Configurer les machines virtuelles Windows
Supposons que vous disposiez d’une machine virtuelle Windows avec deux cartes réseau, comme suit :

* Adresse IP de la carte réseau principale : 192.168.1.4
* Adresse IP de la carte réseau secondaire : 192.168.2.5

La table de routage IPv4 associée à cette machine virtuelle ressemblera à ceci :

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

Notez que l’itinéraire par défaut (0.0.0.0) est disponible uniquement sur la carte réseau principale. Vous ne serez pas en mesure d’accéder aux ressources à l’extérieur du sous-réseau avec la carte réseau secondaire, comme représenté ci-dessous :

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

Pour ajouter un itinéraire par défaut à la carte réseau secondaire, suivez la procédure ci-dessous :

1. À partir d’une invite de commande, exécutez la commande ci-dessous afin d’identifier le numéro d’index associé à la carte réseau secondaire :
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. Vous remarquerez la seconde entrée de la table, avec un index de 27 (dans cet exemple).
3. À partir de l’invite de commande, exécutez la commande **route add**, comme indiqué ci-dessous. Dans cet exemple, vous définissez 192.168.2.1 en tant que passerelle par défaut pour la carte réseau secondaire :
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. Pour tester la connectivité, revenez à l’invite de commande et tentez d’exécuter une commande ping sur un sous-réseau différent à partir de la carte réseau secondaire, comme représenté dans l’exemple ci-dessous :
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. Vous pouvez également examiner votre table de routage afin d’étudier l’itinéraire nouvellement ajouté, comme représenté ci-dessous :
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Configurer des machines virtuelles Linux
Pour les machines virtuelles Linux, dans la mesure où le comportement par défaut valorise un modèle de routage d’hôte faible, nous vous recommandons de limiter les cartes réseau secondaires au flux de trafic au sein du même sous-réseau. Toutefois, si certains scénarios nécessitent une connectivité à l’extérieur du sous-réseau, les utilisateurs doivent configurer un routage basé sur une stratégie afin de garantir que les trafics entrant et sortant utilisent la même carte réseau.

## Étapes suivantes
* Déploiement de [machines virtuelles MultiNIC dans un scénario d’application à 2 niveaux pour un déploiement Resource Manager](virtual-network-deploy-multinic-arm-template.md).
* Déploiement de [machines virtuelles MultiNIC dans un scénario d’application à 2 niveaux pour un déploiement classique](virtual-network-deploy-multinic-classic-ps.md).

<!---HONumber=AcomDC_0824_2016-->