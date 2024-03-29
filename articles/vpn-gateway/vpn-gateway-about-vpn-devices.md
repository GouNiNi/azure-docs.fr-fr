---
title: "À propos des périphériques VPN pour les connexions des passerelles VPN site à site pour les réseaux virtuels Azure | Microsoft Docs"
description: "Cet article présente les périphériques VPN et les paramètres IPsec pour les connexions de passerelle VPN site à site (S2S) et contient des liens vers des instructions et des exemples de configuration."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: ba449333-2716-4b7f-9889-ecc521e4d616
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/13/2016
ms.author: yushwang;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 019395d76a1e2d8e75b4ac1474abe45ce1b8aeb1


---
# <a name="about-vpn-devices-for-sitetosite-vpn-gateway-connections"></a>À propos des périphériques VPN pour les connexions de la passerelle VPN de site à site
La configuration d’une connexion VPN site à site (S2S) nécessite un périphérique VPN. Vous pouvez utiliser des connexions site à site pour créer une solution hybride, ou chaque fois que vous souhaitez disposer d'une connexion sécurisée entre votre réseau local et votre réseau virtuel. Cet article traite des périphériques VPN compatibles et des paramètres de configuration associés. 

> [!NOTE]
> Lorsque vous configurez une connexion site à site, une adresse IPv4 publique est requise pour votre périphérique VPN.                                                                                                                                                                               
> 
> 

Si votre périphérique n’apparaît pas dans le tableau [Périphériques VPN validés](#devicetable), consultez la section [Périphériques VPN non validés](#additionaldevices) de cet article. Il est possible que votre périphérique fonctionne toujours avec Azure. Pour plus d’informations sur la prise en charge des périphériques VPN, contactez le fabricant de votre périphérique.

**Éléments à noter lorsque vous affichez les tables :**

* Une modification de la terminologie a eu lieu pour le routage statique et dynamique. Vous rencontrerez probablement les deux termes. Seuls les noms ont été modifiés, pas la fonctionnalité.
  * Routage statique = basé sur des stratégies
  * Routage dynamique = basé sur un itinéraire
* Sauf indication contraire, les spécifications des passerelles VPN hautes performances sont identiques à celles des passerelles VPN basées sur itinéraire. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN basées sur itinéraire sont également compatibles avec la passerelle VPN hautes performances Azure. 

## <a name="a-namedevicetableavalidated-vpn-devices"></a><a name="devicetable"></a>Périphériques VPN validés
Nous avons validé un ensemble de périphériques VPN standard en partenariat avec des fournisseurs de périphériques. Tous les périphériques des familles de périphériques figurant dans la liste suivante doivent fonctionner avec les passerelles VPN Azure. Consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle dont vous avez besoin pour créer la solution que vous souhaitez configurer. 

Pour configurer plus facilement votre périphérique VPN, reportez-vous aux liens qui correspondent à la famille de périphériques appropriée. Pour plus d’informations sur la prise en charge des périphériques VPN, contactez le fabricant de votre périphérique.

| **Fournisseur** | **Famille de périphériques** | **Version de système d’exploitation minimale** | **PolicyBased** | **RouteBased** |
| --- | --- | --- | --- | --- |
| Allied Telesis |Routeurs VPN série AR |2.9.2 |Bientôt disponible |Non compatible |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série F |Basé sur des stratégies : 5.4.3, basé sur un itinéraire : 6.2.0 |[Instructions de configuration](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Instructions de configuration](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall série X |Pare-feu Barracuda 6.5 |[Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Non compatible |
| Brocade |Routeur virtuel Vyatta 5400 |Routeur virtuel 6.6R3 GA |[Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html) |Non compatible |
| Check Point |Passerelle de sécurité |R75.40, R75.40VS |[Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco |ASA |8.3 |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA) |Non compatible |
| Cisco |ASR |IOS 15.1 (basé sur des stratégies), IOS 15.2 (basé sur un itinéraire) |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR) |
| Cisco |ISR |IOS 15.0 (basé sur des stratégies), IOS 15.1 (basé sur un itinéraire*) |[Exemples Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |[Exemples Cisco*](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR) |
| Citrix |NetScaler MPX, SDX, VPX |10.1 et versions ultérieures |[Instructions d’intégration](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Non compatible |
| Dell SonicWALL |Série TZ, Série NSA, Série SuperMassive, Série NSA classe E |SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646) |[Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |[Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850) |
| F5 |Série BIG-IP |12.0 |[Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Instructions de configuration](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.2.7 |[Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) |[Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure) |
| Internet Initiative Japan (IIJ) |Série SEIL |SEIL/X 4.60, SEIL/B1 4.60, SEIL/x86 3.20 |[Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Non compatible |
| Juniper |SRX |JunOS 10.2 (basé sur des stratégies), JunOS 11.4 (basé sur un itinéraire) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX) |
| Juniper |Série J |JunOS 10.4r9 (basé sur des stratégies), JunOS 11.4 (basé sur un itinéraire) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries) |
| Juniper |ISG |ScreenOS 6.3 (basé sur des stratégies et basé sur un itinéraire) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG) |
| Juniper |SSG |ScreenOS 6.2 (basé sur des stratégies et basé sur un itinéraire) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |[Exemples Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG) |
| Microsoft |Service de routage et d’accès à distance |Windows Server 2012 |Non compatible |[Exemples Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761) |
| Open Systems AG |Passerelle Mission Control Security |N/A |[Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |[Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan |Openswan |2.6.32 |(Bientôt disponible) |Non compatible |
| Palo Alto Networks |Tous les périphériques exécutant PAN-OS |PAN-OS 6.1.5 ou version ultérieure (basé sur des stratégies), PAN-OS 7.0.5 ou version ultérieure (basé sur un itinéraire) |[Instructions de configuration](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Instructions de configuration](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340) |
| Watchguard |Tout |Fireware XTM v11.x |[Instructions de configuration](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/) |Non compatible |

(*) Les routeurs de la série ISR 7200 prennent uniquement en charge les VPN basés sur des stratégies.

## <a name="a-nameadditionaldevicesanonvalidated-vpn-devices"></a><a name="additionaldevices"></a>Périphériques VPN non validés
Si votre appareil n’est pas répertorié dans le tableau des périphériques VPN validés, il peut tout de même fonctionner avec une connexion site à site. Vérifiez que votre périphérique VPN répond à la configuration minimale requise décrite dans la section de configuration de la passerelle de l'article [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Les périphériques qui répondent à la configuration minimale requise sont censés fonctionner également avec les passerelles VPN. Contactez le fabricant de votre périphérique pour obtenir une prise en charge et des instructions de configuration supplémentaires.

## <a name="editing-device-configuration-samples"></a>Modification des exemples de configuration de périphérique
Après avoir téléchargé l’exemple de configuration de périphérique VPN fourni, vous devrez remplacer certaines des valeurs spécifiées pour qu’elles reflètent les paramètres de votre environnement. 

**Pour modifier un exemple :**

1. Ouvrez l’exemple à l’aide du Bloc-notes. 
2. Recherchez et remplacez toutes les chaînes au format <*texte*> par les valeurs qui correspondent à votre environnement. Prenez soin d’inclure < et >. Lorsque vous sélectionnez un nom, assurez-vous qu’il est unique. Si une commande ne fonctionne pas, consultez la documentation du fabricant du périphérique.

| **Texte de l’exemple** | **Valeur de substitution** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Nom que vous choisissez pour cet objet. Exemple : MonRéseauLocal |
| &lt;RP_AzureNetwork&gt; |Nom que vous choisissez pour cet objet. Exemple : MonRéseauAzure |
| &lt;RP_AccessList&gt; |Nom que vous choisissez pour cet objet. Exemple : MaListeAccèsAzure |
| &lt;RP_IPSecTransformSet&gt; |Nom que vous choisissez pour cet objet. Exemple : MonJeuTransformationsIPSec |
| &lt;RP_IPSecCryptoMap&gt; |Nom que vous choisissez pour cet objet. Exemple : MaCarteChiffrementIPSec |
| &lt;SP_AzureNetworkIpRange&gt; |Spécifiez une plage. Exemple : 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Spécifiez un masque de sous-réseau. Exemple : 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Spécifiez une plage locale. Exemple : 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Spécifiez un masque de sous-réseau local. Exemple : 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Ces informations sont propres à votre réseau virtuel et figurent dans le portail de gestion sous l’intitulé **Adresse IP de la passerelle**. |
| &lt;SP_PresharedKey&gt; |Ces informations sont propres à votre réseau virtuel et figurent dans le Portail de gestion sous l’intitulé Gérer la clé. |

## <a name="ipsec-parameters"></a>Paramètres IPsec
> [!NOTE]
> Même si les valeurs répertoriées dans le tableau ci-dessous sont prises en charge par la passerelle VPN Azure, il n’existe pour le moment aucun moyen de spécifier ou de sélectionner une combinaison spécifique de la passerelle VPN Azure. Vous devez spécifier des contraintes à partir du périphérique VPN local. En outre, vous devez définir MSS sur 1350.
> 
> 

### <a name="ike-phase-1-setup"></a>Configuration IKE Phase 1
| **Propriété** | **PolicyBased** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
| --- | --- | --- |
| Version IKE |IKEv1 |IKEv2 |
| Groupe Diffie-Hellman |Groupe 2 (1 024 bits) |Groupe 2 (1 024 bits) |
| Méthode d'authentification |Clé prépartagée |Clé prépartagée |
| Algorithmes de chiffrement |AES256 AES128 3DES |AES256 3DES |
| Algorithme de hachage |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Durée de vie d’association de sécurité de phase 1 (temps) |28 800 secondes |10 800 secondes |

### <a name="ike-phase-2-setup"></a>Configuration IKE Phase 2
| **Propriété** | **PolicyBased** | **Basé sur un itinéraire et passerelle VPN standard ou hautes performances** |
| --- | --- | --- |
| Version IKE |IKEv1 |IKEv2 |
| Algorithme de hachage |SHA1(SHA128) |SHA1(SHA128) |
| Durée de vie d’association de sécurité de phase 2 (temps) |3 600 secondes |3 600 secondes |
| Durée de vie d’association de sécurité de phase 2 (débit) |102 400 000 Ko |- |
| Offres d’authentification et de chiffrement d’association de sécurité IPsec (par ordre de préférence) |1. ESP-AES256 2. ESP-AES128 3. ESP-3DES 4. N/A |Voir *Offres d’association de sécurité IPsec pour passerelle basée sur un itinéraire* (ci-dessous) |
| PFS (Perfect Forward Secrecy) |Non |Non (*) |
| Détection d’homologue mort |Non pris en charge |Pris en charge |

(*) La passerelle Azure en tant que répondeur IKE peut accepter le groupe DH PFS 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Offres d’association de sécurité IPsec pour passerelle basée sur un itinéraire
Le tableau suivant répertorie les offres d’authentification et de chiffrement d’association de sécurité IPsec. Les offres sont énumérées dans l’ordre de préférence dans lequel elles sont présentées ou acceptées.

| **Offres d’authentification et de chiffrement d’association de sécurité IPsec** | **Passerelle Azure en tant qu’initiateur** | **Passerelle Azure en tant que répondeur** |
| --- | --- | --- |
| 1 |ESP AES_256 SHA |ESP AES_128 SHA |
| 2 |ESP AES_128 SHA |ESP 3_DES MD5 |
| 3 |ESP 3_DES MD5 |ESP 3_DES SHA |
| 4 |ESP 3_DES SHA |AH SHA1 avec ESP AES_128 et HMAC Null |
| 5 |AH SHA1 avec ESP AES_256 et HMAC Null |AH SHA1 avec ESP 3_DES et HMAC Null |
| 6 |AH SHA1 avec ESP AES_128 et HMAC Null |AH MD5 avec ESP 3_DES et HMAC Null, aucune durée de vie proposée |
| 7 |AH SHA1 avec ESP 3_DES et HMAC Null |AH SHA1 avec ESP 3_DES SHA1, aucune durée de vie |
| 8 |AH MD5 avec ESP 3_DES et HMAC Null, aucune durée de vie proposée |AH MD5 avec ESP 3_DES MD5, aucune durée de vie |
| 9 |AH SHA1 avec ESP 3_DES SHA1, aucune durée de vie |ESP DES MD5 |
| 10 |AH MD5 avec ESP 3_DES MD5, aucune durée de vie |ESP DES SHA1, aucune durée de vie |
| 11 |ESP DES MD5 |AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 12 |ESP DES SHA1, aucune durée de vie |AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée |
| 13. |AH SHA1 avec ESP DES et HMAC Null, aucune durée de vie proposée |AH SHA1 avec ESP DES SHA1, aucune durée de vie |
| 14 |AH MD5 avec ESP DES et HMAC Null, aucune durée de vie proposée |AH MD5 avec ESP DES MD5, aucune durée de vie |
| 15 |AH SHA1 avec ESP DES SHA1, aucune durée de vie |ESP SHA, aucune durée de vie |
| 16 |AH MD5 avec ESP DES MD5, aucune durée de vie |ESP MD5, aucune durée de vie |
| 17 |- |AH SHA, aucune durée de vie |
| 18 |- |AH MD5, aucune durée de vie |

* Vous pouvez spécifier le chiffrement IPsec ESP NULL avec les passerelles VPN basées sur un itinéraire et hautes performances. Le chiffrement Null ne fournit pas de protection des données en transit. Il doit être utilisé uniquement lorsqu’un débit maximal et une latence minimale sont requis.  Les clients peuvent choisir de l’utiliser dans les scénarios de communication entre les réseaux virtuels ou lorsque le chiffrement est appliqué ailleurs dans la solution.
* Pour les connexions entre locaux par le biais d’Internet, utilisez les paramètres de passerelle VPN Azure par défaut avec les algorithmes de chiffrement et de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications cruciales.




<!--HONumber=Nov16_HO2-->


