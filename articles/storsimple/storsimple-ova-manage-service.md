---
title: Déployer le service StorSimple Manager pour un tableau virtuel StorSimple| Microsoft Docs
description: Explique comment créer et supprimer le service StorSimple Manager dans le portail Azure Classic et décrit comment gérer la clé d’inscription du service.
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
ms.date: 05/19/2016
ms.author: alkohli

---
# Déployer le service StorSimple Manager pour StorSimple Virtual Array
## Vue d'ensemble
Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Après avoir créé le service, vous pouvez l’utiliser pour gérer les appareils à partir du portail Microsoft Azure Classic s’exécutant dans un navigateur. Vous pouvez ainsi surveiller tous les appareils qui sont connectés au service StorSimple Manager à partir d’un emplacement central et unique, ce qui réduit la charge administrative.

La page d’accueil StorSimple Manager répertorie tous les services StorSimple Manager que vous pouvez utiliser pour gérer vos dispositifs de stockage StorSimple. Pour chaque service StorSimple Manager, les informations suivantes s’affichent sur la page StorSimple Manager :

* **Nom** : le nom affecté à votre service StorSimple Manager lors de sa création. Impossible de modifier le nom du service une fois que le service a été créé.
* **État** : l’état du service, qui peut être **Actif**, **Création en cours** ou **En ligne**.
* **Emplacement** : l’emplacement géographique sur lequel l’appareil StorSimple sera déployé.
* **Abonnement** : l’abonnement de facturation associé à votre service.

Les tâches courantes qui peuvent être effectuées via la page StorSimple Manager sont les suivantes :

* Créer un service
* Supprimer un service
* Obtenir la clé d’inscription du service
* Régénérer la clé d’inscription du service

Le didacticiel explique comment effectuer chacune de ces tâches. Les informations contenues dans cet article s’appliquent uniquement aux tableaux virtuels StorSimple. Pour plus d’informations sur la gamme StorSimple 8000, consultez la page [déployer un service StorSimple Manager](storsimple-manage-service.md).

## Créer un service
Utilisez l’option **Création rapide** pour créer un service StorSimple Manager si vous souhaitez déployer votre appareil StorSimple. Pour créer un service, vous avez besoin des éléments suivants :

* Un abonnement avec un contrat Entreprise
* Un compte de stockage Microsoft Azure actif
* Les informations de facturation utilisées pour la gestion des accès

Vous pouvez également choisir de générer un compte de stockage par défaut lorsque vous créez le service.

Un seul service peut gérer plusieurs appareils. Cependant, un appareil ne peut pas couvrir plusieurs services. Une grande entreprise peut avoir plusieurs instances de service pour utiliser différents abonnements, organisations ou même emplacements de déploiement.

> [!NOTE]
> Vous devez créer des instances distinctes du service StorSimple Manager pour gérer les appareils de la gamme StorSimple 8000 et les tableaux virtuels StorSimple.
> 
> 

Procédez comme suit pour créer un service.

[!INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## Supprimer un service
Avant de supprimer un service, assurez-vous qu’aucun appareil connecté ne l’utilise. Si le service est en cours d’utilisation, désactivez les appareils connectés. L’opération de désactivation rompt la connexion entre l’appareil et le service, mais conserve les données de l’appareil dans le cloud.

> [!IMPORTANT]
> Après qu’un service a été supprimé, l’opération ne peut pas être annulée.
> 
> 

Pour supprimer un service, procédez comme suit.

### Pour supprimer un service
1. Dans la page **Service StorSimple Manager**, sélectionnez le service que vous souhaitez supprimer.
2. Cliquez sur **Supprimer** en bas de la page.
3. Cliquez sur **Oui** dans la notification de confirmation. La suppression du service peut nécessiter quelques minutes.

## Obtenir la clé d’inscription du service
Une fois que vous avez créé un service, vous devez inscrire votre appareil StorSimple auprès du service. Pour inscrire votre premier appareil StorSimple, vous avez besoin de la clé d’inscription du service. Pour inscrire des appareils supplémentaires avec un service StorSimple existant, vous avez besoin de la clé d’inscription et de la clé de chiffrement des données du service (générée sur le premier appareil lors de l’inscription). Pour plus d’informations sur la clé de chiffrement des données de service, consultez [Obtenir la clé de chiffrement des données de service à partir de l’interface utilisateur web locale](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

Procédez comme suit pour obtenir la clé d’inscription du service.

[!INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Conservez la clé d’inscription du service dans un emplacement sûr. Vous aurez besoin de cette clé, ainsi que de la clé de chiffrement des données du service, pour enregistrer des appareils supplémentaires auprès du service. Après avoir obtenu la clé d’inscription du service, vous devez configurer votre appareil via l’interface Windows PowerShell pour StorSimple.

## Régénérer la clé d’inscription du service
Vous devez régénérer une clé d’inscription du service si vous êtes amené à effectuer une rotation des clés ou si la liste des administrateurs du service a changé. Lorsque vous régénérez la clé, la nouvelle clé est utilisée uniquement pour l’enregistrement des appareils suivants. Les appareils déjà enregistrés ne sont pas affectés par ce processus.

Procédez comme suit pour régénérer une clé d’inscription du service.

### Pour régénérer la clé d’inscription du service
1. Dans la page **Service StorSimple Manager**, cliquez sur **Clé d’inscription**.
2. Dans la boîte de dialogue **Clé d’inscription du service**, cliquez sur **Régénérer**.
3. Un message de confirmation s’affiche. Cliquez sur **OK** pour poursuivre la régénération.
4. Une nouvelle clé d’inscription du service s’affiche.
5. Copiez cette clé et sauvegardez-la pour enregistrer tout nouvel appareil auprès de ce service.
6. Cliquez sur l’icône de coche ![Icône en forme de coche](./media/storsimple-ova-manage-service/image7.png) pour fermer cette boîte de dialogue.

## Étapes suivantes
* Découvrez comment [prendre en main](storsimple-ova-deploy1-portal-prep.md) StorSimple Virtual Array.
* Découvrez comment [gérer votre appareil StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0525_2016-->