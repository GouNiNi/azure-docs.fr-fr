---
title: "Configuration de connexions VPN Expressroute et site à site pouvant coexister pour le modèle de déploiement Resource Manager | Microsoft Docs"
description: "Cet article vous guide tout au long de la configuration d’une connexion ExpressRoute et d’une connexion VPN de site à site pouvant coexister pour le modèle de déploiement Resource Manager."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charleywen
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 44f6761b3c3680af923f2a3b16671ca19672e281


---
# <a name="configure-expressroute-and-sitetosite-coexisting-connections-for-the-resource-manager-deployment-model"></a>Configurer la coexistence de connexions de site à site et ExpressRoute pour le modèle de déploiement Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell - Classique](expressroute-howto-coexist-classic.md)
> 
> 

La possibilité de configurer des connexions VPN de site à site et ExpressRoute présente plusieurs avantages. Vous pouvez configurer un VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne sont pas connectés via ExpressRoute. Dans cet article, nous décrirons les étapes de configuration des deux scénarios. Cet article s'applique au modèle de déploiement Resource Manager. Cette configuration n'est pas disponible dans le portail Azure.

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Les circuits ExpressRoute doivent être préconfigurés avant que vous suiviez les instructions ci-dessous. Assurez-vous que vous avez suivi les guides [Créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [Configurer le routage](expressroute-howto-routing-arm.md) avant de suivre les étapes ci-dessous.
> 
> 

## <a name="limits-and-limitations"></a>Limites et limitations
* **Le routage de transit n’est pas pris en charge.** Vous ne pouvez effectuer de routage (via Azure) entre votre réseau local connecté via le réseau VPN de site à site et votre réseau local connecté via ExpressRoute.
* **La passerelle de référence de base n’est pas prise en charge.** Vous devez utiliser une passerelle de référence SKU autre que De base pour la [passerelle ExpressRoute](expressroute-about-virtual-network-gateways.md) et la [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Seule la passerelle VPN basée sur un itinéraire est prise en charge.** Vous devez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) basée sur un itinéraire.
* L’**itinéraire statique doit être configuré pour votre passerelle VPN.** Si votre réseau local est connecté à la fois à ExpressRoute et à un VPN de site à site, vous devez avoir configuré un itinéraire statique sur votre réseau local pour acheminer la connexion VPN de site à site vers l’Internet public.
* La **passerelle ExpressRoute doit être configurée en premier.** Vous devez commencer par créer la passerelle ExpressRoute avant d’ajouter la passerelle VPN de site à site.

## <a name="configuration-designs"></a>Modèles de configuration
### <a name="configure-a-sitetosite-vpn-as-a-failover-path-for-expressroute"></a>Configurer un réseau VPN de site à site comme un chemin d’accès de basculement pour ExpressRoute
Vous pouvez configurer une connexion VPN de site à site en tant que sauvegarde pour ExpressRoute. Cela s’applique uniquement aux réseaux virtuels liés au chemin d’homologation privé Azure. Il n’existe aucune solution de basculement basée sur des réseaux VPN pour les services accessibles via les homologations Azure public et Microsoft. Le circuit ExpressRoute est toujours le lien principal. Les données circulent via le chemin d’accès du réseau VPN de site à site uniquement si le circuit ExpressRoute échoue.

> [!NOTE]
> Bien que le circuit ExpressRoute soit préférable par rapport au réseau VPN de site à site lorsque les deux itinéraires sont identiques, Azure utilise la correspondance de préfixe la plus longue pour choisir l’itinéraire vers la destination du paquet.
> 
> 

![Coexister](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-sitetosite-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Configurer un réseau VPN de site à site pour se connecter à des sites non connectés via ExpressRoute
Vous pouvez configurer votre réseau là où certains sites se connectent directement à Azure via des réseaux VPN de site à site ou via ExpressRoute. 

![Coexister](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Vous ne pouvez pas configurer un réseau virtuel comme un routeur de transit.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Sélection des étapes à suivre
Vous pouvez choisir entre deux procédures différentes pour configurer vos connexions afin qu’elles coexistent. La procédure de configuration que vous sélectionnez varie selon que vous disposez déjà d’un réseau virtuel auquel vous connecter ou que vous voulez créer un réseau virtuel.

* Je n’ai pas de réseau virtuel et dois en créer un
  
    Si vous ne disposez pas déjà d’un réseau virtuel, cette procédure vous guide dans la création d’un réseau virtuel en utilisant le modèle de déploiement Resource Manager et dans l’établissement de nouvelles connexions ExpressRoute et VPN de site à site. Pour procéder à la configuration, suivez les étapes décrites dans la section [Créer un réseau virtuel et des connexions qui coexistent](#new).
* J’ai déjà un réseau virtuel répondant au modèle de déploiement Resource Manager.
  
    Vous disposez peut-être déjà d’un réseau virtuel avec une connexion VPN de site à site existante ou une connexion ExpressRoute. La section [Configurer des connexions qui coexistent pour un réseau virtuel existant](#add) vous guide tout au long des étapes de suppression de la passerelle et de création de connexions ExpressRoute et VPN de site à site. Notez que vous devez effectuer les étapes dans un ordre très spécifique lorsque vous créez les connexions. N’utilisez pas les instructions contenues dans d’autres articles pour créer des connexions et des passerelles.
  
    Lors de cette procédure, si vous créez des connexions pouvant coexister, vous devez supprimer votre passerelle, puis configurer de nouvelles passerelles. En d’autres termes, vous subissez un temps d’arrêt pour les connexions entre différents locaux lorsque vous supprimez et recréez la passerelle et les connexions, mais vous ne devez pas migrer les ordinateurs virtuels ou les services vers un nouveau réseau virtuel. Les machines virtuelles et les services sont toujours en mesure de communiquer via l’équilibreur de charge lorsque vous configurez votre passerelle s’ils sont configurés pour ce faire.

## <a name="a-namenewato-create-a-new-virtual-network-and-coexisting-connections"></a><a name="new"></a>Créer un réseau virtuel et des connexions qui coexistent
Cette procédure vous guide dans la création d’un réseau virtuel et dans l’établissement de nouvelles connexions de site à site et ExpressRoute appelées à coexister.

1. Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) . Les applets de commande que vous utiliserez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions.
2. Connectez-vous à votre compte et configurez l'environnement.
   
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
3. Créez un réseau virtuel et un sous-réseau de passerelle. Pour plus d’informations sur la configuration d'un réseau virtuel, consultez la rubrique [Configuration du réseau virtuel Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).
   
   > [!IMPORTANT]
   > Le sous-réseau de la passerelle doit être défini sur /27 ou un préfixe plus court (comme /26 ou /25).
   > 
   > 
   
    Créez un nouveau réseau virtuel.
   
        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 
   
    Ajoutez des sous-réseaux.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Enregistrez la configuration du réseau virtuel.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
4. <a name="gw"></a>Créez une passerelle ExpressRoute. Pour plus d'informations sur la configuration de la passerelle ExpressRoute, consultez la rubrique [Configuration de la passerelle ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). La valeur de GatewaySKU doit être *Standard*, *HighPerformance*, ou *UltraPerformance*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 
5. Liez la passerelle ExpressRoute au circuit ExpressRoute. Une fois cette étape terminée, la connexion entre votre réseau local et Azure est établie via ExpressRoute. Pour plus d'informations sur l'opération de liaison, voir l'article [Liaison de réseaux virtuels à ExpressRoute](expressroute-howto-linkvnet-arm.md).
   
        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
6. <a name="vpngw"></a>Créez ensuite la passerelle VPN de site à site. Pour plus d’informations sur la configuration de la passerelle VPN, consultez la rubrique [Configuration d’un réseau virtuel avec une connexion de site à site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). La valeur de GatewaySKU doit être *Standard*, *HighPerformance*, ou *UltraPerformance*. La valeur VpnType doit être *RouteBased*.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
   
    La passerelle VPN Azure prend en charge le protocole BGP. Vous pouvez spécifier - EnableBgp dans la commande suivante.
   
        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true
   
    Vous pouvez trouver l’IP d’homologation BGP et le numéro AS qu’Azure utilise pour la passerelle VPN dans $azureVpn.BgpSettings.BgpPeeringAddress et $azureVpn.BgpSettings.Asn. Pour plus d’informations, consultez [Configurer BGP](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) pour la passerelle VPN Azure.
7. Créez une entité de passerelle VPN de site local. Cette commande ne configure pas votre passerelle VPN locale. Elle vous permet d’indiquer les paramètres de la passerelle locale, par exemple l’adresse IP publique et l’espace d’adressage local afin que la passerelle VPN Azure puisse s’y connecter.
   
    Si votre périphérique VPN local prend uniquement en charge le routage statique, vous pouvez configurer des itinéraires statiques de la façon suivante.
   
        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   
    Si votre périphérique VPN local prend en charge le protocole BGP et que vous souhaitez activer le routage dynamique, vous devez connaître l’IP d’homologation BGP et le numéro AS utilisé par votre périphérique VPN local.
   
        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
8. Configurez votre périphérique VPN local à connecter à la nouvelle passerelle VPN Azure. Pour plus d’informations sur la configuration du périphérique VPN, consultez la rubrique [Configuration de périphérique VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Liez la passerelle VPN de site à site dans Azure à la passerelle locale.
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>

## <a name="a-nameaddato-configure-coexsiting-connections-for-an-already-existing-vnet"></a><a name="add"></a>Configurer des connexions coexistantes pour un réseau virtuel existant
Si vous disposez déjà d’un réseau virtuel, vérifiez la taille du sous-réseau de passerelle. Si le sous-réseau de passerelle est /28 ou /29, vous devez tout d’abord supprimer la passerelle de réseau virtuel et augmenter la taille du sous-réseau de passerelle. Les étapes décrites dans cette section vous indiquent la procédure à suivre.

Si le sous-réseau de passerelle est défini sur/27 ou plus et si le réseau virtuel est connecté via ExpressRoute, vous pouvez ignorer les étapes ci-dessous et passer à [« Étape 6 : créer une passerelle VPN de site à site »](#vpngw) dans la section précédente. 

> [!NOTE]
> Lorsque vous supprimez la passerelle existante, votre site local perdra la connexion à votre réseau virtuel lorsque vous effectuerez cette configuration. 
> 
> 

1. Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) . Les applets de commande que vous utiliserez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions. 
2. Supprimez la passerelle VPN ExpressRoute ou de site à site existante. 
   
        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
3. Supprimez le sous-réseau de la passerelle.
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
4. Ajoutez un sous-réseau de passerelle défini sur /27 ou plus.
   
   > [!NOTE]
   > S’il ne vous reste pas suffisamment d’adresses IP dans votre réseau virtuel pour augmenter la taille du sous-réseau de passerelle, vous devez augmenter l’espace d’adresses IP.
   > 
   > 
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Enregistrez la configuration du réseau virtuel.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
5. À ce stade, vous disposez d’un réseau virtuel sans passerelles. Pour créer de nouvelles passerelles et finaliser vos connexions, vous pouvez passer à l’ [Étape 4 : Créer une passerelle ExpressRoute](#gw), dans les étapes qui précèdent.

## <a name="to-add-pointtosite-configuration-to-the-vpn-gateway"></a>Pour ajouter une configuration point à site à la passerelle VPN
Vous pouvez suivre les étapes ci-dessous pour ajouter une configuration point à site à votre passerelle VPN dans une configuration de coexistence.

1. Ajoutez le pool d’adresses des clients VPN. 
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
2. Téléchargez le certificat racine VPN dans Azure pour votre passerelle VPN. Dans cet exemple, nous supposons que le certificat racine est stocké dans l'ordinateur local où sont exécutées les applets de commande PowerShell suivantes. 
   
        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Pour plus d’informations sur le réseau VPN point à site, consultez la rubrique [Configuration d’une connexion point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).




<!--HONumber=Nov16_HO2-->


