---
title: Surveiller un cluster Azure Container Service avec Datadog | Microsoft Docs
description: Surveillez un cluster Azure Container Service avec Datadog. Utilisez l’interface utilisateur web du contrôleur de domaine/système d’exploitation pour déployer les agents Datadog sur votre cluster.
services: container-service
documentationcenter: ''
author: rbitia
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Conteneurs, contrôleur de domaine/système d’exploitation, Docker Swarm, Azure

ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: t-ribhat

---
# Surveiller un cluster Azure Container Service avec Datadog
Dans cet article, nous allons déployer des agents Datadog sur tous les nœuds d’agent de votre cluster Azure Container Service. Vous aurez besoin d’un compte Datadog pour cette configuration.

## Composants requis
[Déployez](container-service-deployment.md) et [connectez](container-service-connect.md) un cluster configuré par Azure Container Service. Explorez [l’interface utilisateur Marathon](container-service-mesos-marathon-ui.md). Accédez à [http://datadoghq.com](http://datadoghq.com) pour configurer un compte Datadog.

## Datadog
Datadog est un service de surveillance qui regroupe les données de surveillance provenant de vos conteneurs dans votre cluster Azure Container Service. Datadog intègre un tableau de bord Docker Integration qui affiche des mesures spécifiques dans vos conteneurs. Les mesures recueillies à partir de vos conteneurs sont classées par processeur, mémoire, réseau et E/S. Datadog fractionne les mesures en conteneurs et images. Vous trouverez ci-dessous un exemple d’interface utilisateur pour l’utilisation du processeur.

![Interface utilisateur Datadog](./media/container-service-monitoring/datadog4.png)

## Configurer un déploiement Datadog avec Marathon
Ces étapes vous expliquent comment configurer et déployer des applications Datadog dans votre cluster avec Marathon.

Accédez à l'interface utilisateur de votre contrôleur de domaine/système d’exploitation via [http://localhost:80/](http://localhost:80/). Une fois dans l’interface utilisateur, accédez à Universe « Univers » en bas à gauche de la page, recherchez « Datadog », puis cliquez sur « Installer ».

![Package Datadog dans l’Univers DC/OS](./media/container-service-monitoring/datadog1.png)

Pour terminer la configuration, vous aurez besoin d’un compte Datadog ou d’un compte d’essai gratuit Datadog. Une fois que vous êtes connecté au site web Datadog, sélectionnez Integrations -> API, à gauche de l'écran.

![Clé d’API Datadog](./media/container-service-monitoring/datadog2.png)

Entrez votre clé API dans la configuration de Datadog dans l’Univers DC/OS.

![Configuration de Datadog dans l’Univers DC/OS](./media/container-service-monitoring/datadog3.png)

Dans la configuration ci-dessus, le nombre d’instances est défini sur 10000000 pour que chaque fois qu’un nouveau nœud est ajouté au cluster, Datadog déploie automatiquement un agent sur ce nœud. Il s’agit d’une solution temporaire. Une fois que vous avez installé le package, vous devez naviguer vers le site web Datadog et recherchez « Dashboards » (tableaux de bord). Vous y verrez des tableaux de bord personnalisés (Custom) et d'intégration (Integration). Le tableau de bord Docker Integration contient toutes les mesures de conteneur dont vous avez besoin pour surveiller votre cluster.

<!---HONumber=AcomDC_0810_2016-->