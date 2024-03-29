---
title: Surveiller un cluster Azure Container Service avec Sysdig | Microsoft Docs
description: Surveillez un cluster Azure Container Service avec Sysdig.
services: container-service
documentationcenter: 
author: rbitia
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Conteneurs, DC/OS, Azure
ms.assetid: 91d9a28a-3a52-4194-879e-30f2fa3d946b
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2016
ms.author: t-ribhat
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4407bc49bf11cada012015f340a9995a26374d00


---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Surveiller un cluster Azure Container Service avec Sysdig
Dans cet article, nous allons déployer des agents Sysdig sur tous les nœuds d’agent de votre cluster Azure Container Service. Vous avez besoin d’un compte Sysdig pour cette configuration. 

## <a name="prerequisites"></a>Composants requis
[Déployez](container-service-deployment.md) et [connectez](container-service-connect.md) un cluster configuré par Azure Container Service. Explorez [l’interface utilisateur Marathon](container-service-mesos-marathon-ui.md). Accéder à [http://app.sysdigcloud.com](http://app.sysdigcloud.com) pour configurer un compte cloud Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig est un service qui vous permet de surveiller vos conteneurs au sein de votre cluster. Sysdig est connu pour aider à résoudre les problèmes, mais il propose également des mesures de surveillance de base pour le processeur, la mise en réseau, la mémoire et les E/S. Sysdig permet de voir facilement quels conteneurs travaillent le plus ou utilisent essentiellement le plus de mémoire et de ressources processeur. Cette vue se trouve dans la section Overview (Vue d’ensemble), actuellement disponible en version bêta. 

![IU Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurer un déploiement Sysdig avec Marathon
Ces étapes vous expliquent comment configurer et déployer des applications Sysdig dans votre cluster avec Marathon. 

Accédez à l’IU DC/OS via [http://localhost:80/](http://localhost:80/). Une fois dans l’IU DC/OS, accédez à Universe (Univers) en bas à gauche de la page, puis recherchez « Sysdig ».

![Sysdig dans l’Univers DC/OS](./media/container-service-monitoring-sysdig/sysdig1.png)

Pour terminer la configuration, vous avez besoin d’un compte cloud ou d’un compte d’essai gratuit Sysdig. Une fois connecté au site web du cloud Sysdig, cliquez sur votre nom d’utilisateur. La page qui s’affiche comporte votre clé d’accès (Access Key). 

![Clé d’API Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Entrez votre clé d’accès dans la configuration de Sysdig dans l’Univers DC/OS. 

![Configuration de Sysdig dans l’Univers DC/OS](./media/container-service-monitoring-sysdig/sysdig3.png)

Définissez le nombre d’instances sur 10000000 pour que chaque fois qu’un nouveau nœud est ajouté au cluster, Sysdig déploie automatiquement un agent sur ce nouveau nœud. Cette solution temporaire permet de garantir le déploiement de Sysdig sur tous les nouveaux agents au sein du cluster. 

![Configuration de Sysdig dans l’Univers DC/OS : instances](./media/container-service-monitoring-sysdig/sysdig4.png)

Une fois le package installé, revenez à l’IU Sysdig. Vous devez être en mesure d’explorer les différentes mesures d’utilisation pour les conteneurs de votre cluster. 




<!--HONumber=Nov16_HO2-->


