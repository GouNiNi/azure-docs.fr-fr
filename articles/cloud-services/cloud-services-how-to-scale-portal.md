---
title: Mise à l’échelle automatique d’un service cloud dans le portail | Microsoft Docs
description: Découvrez comment utiliser le portail pour configurer des règles de mise à l’échelle automatique pour le rôle web ou de travail d’un service cloud dans Azure.
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
ms.date: 09/06/2016
ms.author: adegeo

---
# Mise à l’échelle automatique d’un service cloud
> [!div class="op_single_selector"]
> * [Portail Azure](cloud-services-how-to-scale-portal.md)
> * [Portail Azure Classic](cloud-services-how-to-scale.md)
> 
> 

Des conditions peuvent être définies pour un rôle de travail de service cloud qui déclenchent une opération de diminution et d’augmentation de la taille des instances. Les conditions pour le rôle peuvent être basées sur le processeur, le disque ou la charge réseau du rôle. Vous pouvez également définir une condition basée sur une file d’attente de messages ou sur des mesures d’une autre ressource Azure associée à votre abonnement.

> [!NOTE]
> Cet article porte essentiellement sur les rôles web et de travail d’un service cloud. Lorsque vous créez directement une machine virtuelle (Classic), elle est hébergée dans un service cloud. Vous pouvez mettre à l’échelle un ordinateur virtuel standard en l’associant à un [groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-classic-configure-availability.md) et en l’activant ou la désactivant manuellement.
> 
> 

## Considérations
Vous devez tenir compte des informations suivantes avant de configurer la mise à l'échelle de votre application :

* L'utilisation des cœurs a une incidence sur la mise à l'échelle. Le nombre de cœurs utilisés varie en fonction de la taille des instances de rôle. Vous pouvez mettre à l’échelle une application uniquement dans la limite des cœurs de votre abonnement. Par exemple, si la limite de votre abonnement est de vingt cœurs et que vous exécutez une application avec deux services cloud de taille moyenne (quatre cœurs au total), vous pouvez faire monter en charge les autres déploiements de service cloud de votre abonnement de seize cœurs seulement. Pour plus d’informations sur les tailles, consultez [Tailles de services cloud](cloud-services-sizes-specs.md).
* Vous pouvez mettre à l’échelle en fonction d’un seuil de messages de file d’attente. Pour plus d’informations sur l’utilisation des files d’attente, consultez la rubrique sur [l’utilisation du service Queue Storage](../storage/storage-dotnet-how-to-use-queues.md).
* Vous pouvez également mettre à l’échelle d’autres ressources associées à votre abonnement.
* Pour activer la fonction de haute disponibilité de votre application, vous devez vous assurer qu’elle est déployée avec plusieurs instances de rôle. Pour plus d'informations, consultez la page [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

## Emplacement de la mise à l’échelle
Une fois votre service cloud sélectionné, le panneau du service cloud doit s’afficher.

1. Dans le panneau du service cloud, sélectionnez le nom du service cloud dans la vignette **Rôles et instances**. **IMPORTANT**: veillez à cliquer sur le rôle de service cloud, non sur l’instance de rôle qui se trouve sous le rôle.
   
    ![](./media/cloud-services-how-to-scale-portal/roles-instances.png)
2. Sélectionnez la vignette **Mise à l’échelle**.
   
    ![](./media/cloud-services-how-to-scale-portal/scale-tile.png)

## Mise à l’échelle automatique
Vous pouvez configurer les paramètres de mise à l’échelle d’un rôle avec deux modes : **manuel** ou **automatique**. Le mode Manuel permet définir le nombre absolu d’instances. Le mode Automatique vous permet toutefois de définir des règles qui déterminent le type et le volume de la mise à l’échelle.

Définissez l’option **Mettre à l’échelle selon** sur **Règles de planification et de performance**.

![Paramètres de mise à l’échelle de services cloud avec profil et règle](./media/cloud-services-how-to-scale-portal/schedule-basics.png)

1. Un profil existant.
2. Ajouter une règle pour le profil parent.
3. Ajoutez un autre profil.

Sélectionnez **Ajouter un profil**. Le profil détermine le mode que vous souhaitez utiliser pour la mise à l’échelle : **toujours**, **périodicité**, **date fixe**.

Après avoir configuré le profil et les règles, sélectionnez l’icône **Enregistrer** en haut.

#### Profil
Le profil définit un nombre minimum et maximum d’instance pour la mise à l’échelle, et également lorsque cette plage de mise à l’échelle est active.

* **Toujours**
  
    Toujours conserver cette plage d’instances disponible.
  
    ![Service cloud toujours mis à l’échelle](./media/cloud-services-how-to-scale-portal/select-always.png)
* **Périodicité**
  
    Choisissez un ensemble de jours de la semaine pour la mise à l’échelle.
  
    ![Mise à l’échelle du service cloud avec une planification périodique](./media/cloud-services-how-to-scale-portal/select-recurrence.png)
* **Date fixe**
  
    Une plage de dates fixe pour la mise à l’échelle du rôle.
  
    ![Mise à l’échelle du service cloud avec une date fixe](./media/cloud-services-how-to-scale-portal/select-fixed.png)

Après avoir configuré le profil, sélectionnez le bouton **OK** en bas du panneau du profil.

#### Règle
Des règles sont ajoutées à un profil et représentent une condition de déclenchement de la mise à l’échelle.

Le déclencheur de la règle est basé sur une mesure du service cloud (utilisation de l’UC, activité du disque ou activité réseau) à laquelle vous pouvez ajouter une valeur conditionnelle. Vous pouvez également définir le déclencheur en fonction d’une file d’attente de messages ou de mesures d’une autre ressource Azure associée à votre abonnement.

![](./media/cloud-services-how-to-scale-portal/rule-settings.png)

Après avoir configuré la règle, sélectionnez le bouton **OK** en bas du panneau de la règle.

## Retour à la mise à l’échelle manuelle
Accédez aux [Paramètres de mise à l’échelle](#where-scale-is-located) et définissez l’option **Mettre à l’échelle selon** sur **un nombre d’instances que j’entre manuellement**.

![Paramètres de mise à l’échelle de services cloud avec profil et règle](./media/cloud-services-how-to-scale-portal/manual-basics.png)

Cela supprime la mise à l’échelle automatique du rôle, et vous pouvez définir le nombre d’instances directement.

1. L’option de mise à l’échelle (manuelle ou automatique).
2. Un curseur de nombre d’instances de rôle pour définir les instances de mise à l’échelle.
3. Nombre d’instances pour la mise à l’échelle du rôle.

Après avoir configuré les paramètres de mise à l’échelle, sélectionnez l’icône **Enregistrer** en haut.

<!---HONumber=AcomDC_0914_2016-->