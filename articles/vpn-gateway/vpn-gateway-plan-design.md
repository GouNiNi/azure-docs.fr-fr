---
title: Planification et conception de la passerelle VPN | Microsoft Docs
description: En savoir plus sur la planification et la conception de la passerelle VPN pour les connexions entre locaux, hybrides et entre réseaux virtuels
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: cherylmc

---
# <a name="planning-and-design-for-vpn-gateway"></a>Planification et conception de la passerelle VPN
La planification et la conception de vos configurations intersites et entre réseaux virtuels peuvent être simples ou complexes, selon vos besoins de mise en réseau. Cet article présente diverses considérations relatives à la conception et à la planification de base.

## <a name="planning"></a>Planification
### <a name="<a-name="compare"></a>cross-premises-connectivity-options"></a><a name="compare"></a>Options de connectivité intersite
Si vous voulez connecter vos sites locaux en toute sécurité à un réseau virtuel, vous disposez de trois options : Site à site, Point à site et ExpressRoute. Comparer les différentes connexions entre locaux qui sont disponibles. L’option choisie peut dépendre de la réponse à différentes questions, notamment :

* Quel est le type de débit requis par votre solution ?
* Souhaitez-vous communiquer sur l’Internet public par le biais d’un VPN sécurisé ou sur une connexion privée ?
* Disposez-vous d’une adresse IP publique utilisable ?
* Envisagez-vous d’utiliser un périphérique VPN ? Si tel est le cas, est-il compatible ?
* Ne voulez-vous connecter qu’un petit nombre d’ordinateurs, ou souhaitez-vous mettre en place une connexion permanente pour votre site ?
* Quel est le type de passerelle VPN requis par la solution que vous souhaitez créer ?
* Quelle référence SKU de passerelle devez-vous utiliser ?

Le tableau suivant peut vous aider à déterminer la meilleure option de connectivité pour votre solution.

[!INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="<a-name="gwrequire"></a>gateway-requirements-by-vpn-type-and-sku"></a><a name="gwrequire"></a>Configuration requise de la passerelle par type de VPN et SKU
[!INCLUDE [vpn-gateway-gwsku](../../includes/vpn-gateway-gwsku-include.md)]

Pour plus d’informations sur les références (SKU) de passerelle, consultez [Paramètres de passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

<br>
 Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Le débit total estimé peut être un facteur décisif pour votre conception.
La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[!INCLUDE [vpn-gateway-table-gwtype-aggtput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="<a-name="wf"></a>workflow"></a><a name="wf"></a>Flux de travail
La liste suivante présente le flux de travail courant pour la connectivité au cloud :

1. Concevez et planifiez la topologie de votre connectivité et répertoriez les espaces d’adressage pour tous les réseaux auxquels vous souhaitez vous connecter.
2. Créez un réseau virtuel Azure. 
3. Créez une passerelle VPN pour le réseau virtuel.
4. Créez et configurez les connexions aux réseaux locaux ou à d’autres réseaux virtuels (en fonction des besoins).
5. Créez et configurez une connexion Point-à-Site pour votre passerelle VPN Azure (en fonction des besoins).

## <a name="design"></a>Conception
### <a name="<a-name="topologies"></a>connection-topologies"></a><a name="topologies"></a>Topologies de connexion
Commencez par consulter les diagrammes dans l’article [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md) . Cet article contient des schémas de base, les modèles de déploiement de chaque topologie (Resource Manager ou classique) et les outils de déploiement que vous pouvez utiliser pour déployer votre configuration.   

### <a name="<a-name="designbasics"></a>design-basics"></a><a name="designbasics"></a>Concepts de base
Les sections suivantes présentent les principes de base d’une passerelle VPN. Voir également [Limitations des services de mise en réseau](../azure-subscription-service-limits.md#networking-limits).

#### <a name="<a-name="subnets"></a>about-subnets"></a><a name="subnets"></a>À propos des sous-réseaux
Lorsque vous créez des connexions, vous devez prendre en compte vos plages de sous-réseau. Il ne peut pas avoir de chevauchement entre des plages d’adresses de sous-réseau. On parle de chevauchement de sous-réseau lorsqu’un réseau virtuel ou un emplacement local contient le même espace d’adressage que l’autre emplacement. Cela signifie que vos ingénieurs réseau de vos réseaux locaux doivent rechercher une plage que vous pourrez utiliser pour votre espace d’adressage IP/vos sous-réseaux Azure. Vous devez disposer d’un espace d’adressage qui n’est pas utilisé sur le réseau local. 

Il est également important d’éviter le chevauchement des sous-réseaux lorsque vous travaillez avec des connexions entre réseaux virtuels. Si vos sous-réseaux se chevauchent et si une adresse IP existe à la fois dans les réseaux virtuel expéditeur et destinataire, les connexions entre réseaux virtuels échouent. Azure ne peut pas router les données vers l’autre réseau virtuel, car l’adresse de destination fait partie du réseau virtuel expéditeur. 

Les passerelles VPN nécessitent un sous-réseau spécifique appelé sous-réseau de passerelle. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet afin de fonctionner correctement. Faites attention à ne pas donner un autre nom au sous-réseau de votre passerelle et ne déployez pas de machines virtuelles ou tout autre élément sur le sous-réseau de passerelle. Voir [Sous-réseaux de passerelle](vpn-gateway-about-vpn-gateway-settings.md#gwsub).

#### <a name="<a-name="local"></a>about-local-network-gateways"></a><a name="local"></a>À propos des passerelles de réseau local
La passerelle de réseau local fait généralement référence à votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local était appelée site de réseau local. Lorsque vous configurez une passerelle de réseau local, vous lui donnez un nom, définissez l’adresse IP publique du périphérique VPN local, et spécifiez les préfixes d’adresse qui se trouvent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour la passerelle de réseau local, et route les paquets en conséquence. Vous pouvez modifier ces préfixes d’adresse en fonction des besoins. Pour plus d’informations, voir [Passerelles de réseau local](vpn-gateway-about-vpn-gateway-settings.md#lng).

#### <a name="<a-name="gwtype"></a>about-gateway-types"></a><a name="gwtype"></a>À propos des types de passerelles
Il est essentiel de sélectionner le type de passerelle approprié pour votre topologie. Si vous sélectionnez un type de passerelle erroné, votre passerelle ne fonctionne pas correctement. Le type de passerelle spécifie comment la passerelle se connecte ; il s’agit d’un paramètre de configuration requis pour le modèle de déploiement Resource Manager.

Les types de passerelles sont les suivants :

* Vpn
* ExpressRoute

#### <a name="<a-name="connectiontype"></a>about-connection-types"></a><a name="connectiontype"></a>À propos des types de connexions
Chaque configuration nécessite un type de connexion spécifique. Les types de connexion sont les suivants :

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

#### <a name="<a-name="vpntype"></a>about-vpn-types"></a><a name="vpntype"></a>À propos des types de VPN
Chaque configuration nécessite un type de VPN spécifique. Si vous combinez deux configurations, telles que la création d’une connexion de site à site et une connexion de point-à-site au même réseau virtuel, vous devez utiliser un type de réseau privé virtuel qui satisfait les exigences des deux types de connexion.

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Les tableaux suivants indiquent le type de VPN en fonction de chaque configuration de connexion. Assurez-vous que le type de VPN pour votre passerelle correspond à la configuration que vous souhaitez créer. 

[!INCLUDE [vpn-gateway-table-vpntype](../../includes/vpn-gateway-table-vpntype-include.md)]

### <a name="<a-name="devices"></a>vpn-devices-for-site-to-site-connections"></a><a name="devices"></a>Périphériques VPN et connexions de site à site
Pour configurer une connexion de site à site, quel que soit le modèle de déploiement, vous avez besoin des éléments suivants :

* un périphérique VPN compatible avec les passerelles VPN Azure
* une adresse IPv4 publique qui ne se trouve pas derrière un NAT

Vous devez disposer d’une expérience dans la configuration de votre périphérique VPN, ou disposer d’une personne capable de configurer l’appareil pour vous. Pour plus d’informations sur les périphériques VPN, voir [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). L’article sur les périphériques VPN contient des informations sur les périphériques validés, la configuration requise pour les périphériques qui n’ont pas été validés, ainsi que des liens vers d’éventuels documents relatifs à la configuration de périphérique.

### <a name="<a-name="forcedtunnel"></a>consider-forced-tunnel-routing"></a><a name="forcedtunnel"></a>Envisager le routage avec tunneling forcé
Dans la plupart des configurations, vous pouvez configurer le tunneling forcé. Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise. 

Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fera toujours à partir de l’infrastructure du réseau Azure directement vers Internet, sans vous laisser inspecter ou vérifier le trafic. L’accès Internet non autorisés est susceptible d’entraîner la divulgation d’informations ou tout autre type de violation de sécurité.

**Diagramme du tunneling forcé**

![Connexion de tunneling forcé](./media/vpn-gateway-plan-design/forced-tunnel.png "forced tunneling")

Une connexion de tunneling forcé peut être configurée dans les deux modèles de déploiement et à l’aide de différents outils. Pour plus d’informations, consultez le tableau suivant. Nous mettons à jour ce tableau à mesure que de nouveaux articles, de nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.

[!INCLUDE [vpn-gateway-table-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations susceptibles de vous aider dans votre conception, voir [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md) et [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).

Pour plus d’informations sur les paramètres de passerelle spécifiques, voir [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).

<!--HONumber=Oct16_HO2-->


