---
title: Tâches communes de gestion du service cloud | Microsoft Docs
description: Découvrez comment gérer des services cloud dans le portail Azure. Ces exemples utilisent le portail Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: adegeo

---
# Gestion des services cloud
> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-how-to-manage-portal.md)
> * [Portail Azure Classic](cloud-services-how-to-manage.md)
> 
> 

Votre service cloud est géré dans la zone **Cloud Services (classique)** du portail Azure. Cet article décrit certaines actions courantes disponibles lors de la gestion de vos services cloud. Cela inclut la mise à jour, la suppression, la mise à l’échelle et la promotion d’un déploiement intermédiaire vers la production.

Vous trouverez plus d’informations sur la mise à l’échelle de votre service cloud [ici](cloud-services-how-to-scale-portal.md).

## Mise à jour d’un rôle ou d’un déploiement de service cloud
Si vous devez mettre à jour le code de l'application pour votre service cloud, utilisez **Update** dans le panneau de service cloud. Vous pouvez mettre à jour un ou plusieurs rôles. Pour effectuer la mise à jour, vous pouvez charger un nouveau package de service ou un nouveau fichier de configuration de service.

1. Dans le [portail Azure][portail Azure], sélectionnez le service cloud que vous souhaitez mettre à jour. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Update**.
   
    ![Bouton Update](./media/cloud-services-how-to-manage-portal/update-button.png)
3. Mettez à jour le déploiement avec un nouveau fichier de package de service (.cspkg) et un fichier de configuration de service (.cscfg).
   
    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)
4. Mettez **éventuellement** à jour l'étiquette de déploiement et le compte de stockage.
5. Si l’un des rôles ne comporte qu’une seule instance, sélectionnez **Déployer même si un ou plusieurs rôles contiennent une seule instance** afin de permettre à la mise à niveau de continuer.
   
    Azure ne peut garantir 99,95 % de disponibilité du service pendant la mise à jour du service cloud que si chaque rôle dispose d'au moins deux instances de rôle (machines virtuelles). Avec deux instances de rôle, une machine virtuelle traitera les demandes du client pendant que l'autre est mise à jour.
6. Cochez la case **Démarrer le déploiement** pour appliquer la mise à jour après le téléchargement du package.
7. Cliquez sur **OK** pour démarrer la mise à jour du service.

## Inversion de déploiements pour faire passer un déploiement intermédiaire en production
Lorsque vous décidez de déployer une nouvelle version d'un service cloud, créez un déploiement intermédiaire et testez la nouvelle version dans l'environnement intermédiaire de votre service cloud. Utilisez **Swap** pour inverser les URL qui permettent d'accéder aux deux déploiements et de faire passer une nouvelle version en production.

Vous pouvez inverser les déploiements à partir de la page **Cloud Services** ou du tableau de bord.

1. Dans le [portail Azure][portail Azure], sélectionnez le service cloud que vous souhaitez mettre à jour. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Swap**.
   
    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-button.png)
3. L'invite de confirmation suivante s'affiche.
   
    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/swap-prompt.png)
4. Une fois les informations de déploiement vérifiées, cliquez sur **OK** pour inverser les déploiements.
   
    L'inversion des déploiements se fait rapidement, car la seule chose qui change est l'adresse IP virtuelle (VIP) des déploiements.
   
    Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez.

## Liaison d’une ressource à un service cloud
Le portail Azure ne lie pas les ressources comme le fait le portail Azure Classic actuel. Déployez plutôt des ressources supplémentaires vers le même groupe de ressources que celui utilisé par le service cloud.

## Suppression de déploiements et d’un service cloud
Avant de pouvoir supprimer un service cloud, vous devez supprimer tous les déploiements existants.

Afin de réduire les coûts liés au calcul, vous pouvez supprimer le déploiement intermédiaire une fois que vous êtes certain que votre déploiement de production se comporte comme vous le souhaitez. Vous êtes facturé pour les coûts de calcul des instances de rôle déployées qui ont été arrêtées.

Utiliser la procédure suivante pour supprimer un déploiement ou un service cloud.

1. Dans le [portail Azure][portail Azure], sélectionnez le service cloud que vous souhaitez supprimer. Cette étape ouvre le panneau d'instance de service cloud.
2. Dans le panneau, cliquez sur le bouton **Delete**.
   
    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-button.png)
3. Vous pouvez supprimer tout le service cloud en cochant **Cloud service and its deployments** ou choisir le **déploiement de production** ou le **déploiement intermédiaire**.
   
    ![Inverser les services cloud](./media/cloud-services-how-to-manage-portal/delete-blade.png)
4. Cliquez sur le bouton **Delete** en bas de la page.
5. Pour supprimer le service cloud, cliquez sur **Delete cloud service**. Ensuite, à l'invite de confirmation, cliquez sur **Yes**.

> [!NOTE]
> Lorsqu’un service cloud est supprimé et que la surveillance détaillée est configurée, vous devez supprimer manuellement les données de votre compte de stockage. Pour plus d'informations sur les tables de mesures, consultez [cet](cloud-services-how-to-monitor.md) article.
> 
> 

[portail Azure]: https://portal.azure.com

## Étapes suivantes
* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* Configurez des [certificats SSL](cloud-services-configure-ssl-certificate-portal.md).

<!---HONumber=AcomDC_0810_2016-->