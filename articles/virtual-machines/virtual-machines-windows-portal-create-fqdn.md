---
title: Créer un nom de domaine complet pour une machine virtuelle dans le portail Azure | Microsoft Docs
description: Apprenez à créer un nom de domaine complet pour une machine virtuelle Resource Manager dans le portail Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/24/2016
ms.author: iainfou

---
# Créer un nom de domaine complet dans le portail Azure
Lorsque vous créez une machine virtuelle dans le [portail Azure](https://portal.azure.com) à l’aide du modèle de déploiement Resource Manager, une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) par défaut, vous pouvez en créer un une fois la machine virtuelle créée. Cet article explique les étapes pour créer un nom DNS ou un nom de domaine complet.

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant vous connecter à distance à la machine virtuelle à l’aide de ce nom DNS comme pour Remote Desktop Protocol (RDP).

## Étapes suivantes
Maintenant que votre machine virtuelle a un nom DNS et une IP publique, vous pouvez déployer des infrastructures d’applications courantes ou des services tels qu’IIS, SQL ou SharePoint.

Vous pouvez également lire un autre article sur [l’utilisation de Resource Manager](../resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.

<!---HONumber=AcomDC_0831_2016-->