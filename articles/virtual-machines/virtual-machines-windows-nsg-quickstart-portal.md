---
title: Ouvrir des ports sur une machine virtuelle Linux à l’aide du Portail Azure | Microsoft Docs
description: Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Resource Manager dans le Portail Azure
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/08/2016
ms.author: iainfou

---
# Ouverture de ports sur une machine virtuelle dans Azure à l’aide du Portail Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## Commandes rapides
Vous pouvez également [effectuer ces étapes à l’aide d’Azure PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

Créez d’abord votre Groupe de sécurité réseau. Sélectionnez un groupe de ressources dans le portail, cliquez sur « Ajouter », puis recherchez et sélectionnez « groupe de sécurité de réseau » :

![Ajouter un groupe de sécurité réseau](./media/virtual-machines-windows-nsg-quickstart-portal/add-nsg.png)

Entrez un nom pour votre groupe de sécurité réseau et sélectionnez un emplacement :

![Création d'un groupe de sécurité réseau](./media/virtual-machines-windows-nsg-quickstart-portal/create-nsg.png)

Sélectionnez un nouveau groupe de sécurité réseau. Vous créez maintenant une règle de trafic entrant :

![Ajouter une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/add-inbound-rule.png)

Entrez un nom pour votre nouvelle règle. Le port 80 est déjà entré par défaut. Sur ce panneau, vous pouvez modifier la source, le protocole et la destination lors de l’ajout de règles supplémentaires à votre groupe de sécurité réseau :

![Créer une règle de trafic entrant](./media/virtual-machines-windows-nsg-quickstart-portal/create-inbound-rule.png)

L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique à votre groupe de sécurité réseau. Associons le groupe de sécurité réseau à un sous-réseau :

![Associer un groupe de sécurité réseau à un sous-réseau](./media/virtual-machines-windows-nsg-quickstart-portal/associate-subnet.png)

Sélectionnez votre réseau virtuel, puis sélectionnez le sous-réseau approprié :

![Association d’un groupe de sécurité réseau à un réseau virtuel](./media/virtual-machines-windows-nsg-quickstart-portal/select-vnet-subnet.png)

Vous avez maintenant créé un groupe de sécurité réseau, créé une règle de trafic entrant qui autorise le trafic sur le port 80 et l’avez associé à un sous-réseau. Toute machine virtuelle que vous connectez à ce sous-réseau est accessible sur le port 80.

## En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md)

## Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Présentation d’Azure Resource Manager](../load-balancer/load-balancer-arm.md)

<!---HONumber=AcomDC_0907_2016-->