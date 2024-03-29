---
title: Linux et informatique open-source sur Azure | Microsoft Docs
description: Répertorie des articles sur Linux et l’informatique open source dans Azure, notamment sur l’utilisation de base de Linux, certains concepts essentiels sur l’exécution ou le téléchargement d’images Linux dans Azure, ainsi que d’autres indications sur des technologies et optimisations spécifiques.
services: virtual-machines-linux
documentationcenter: ''
author: squillace
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management

ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/27/2016
ms.author: rasquill

---
# Linux et informatique open-source sur Azure
Recherchez tous les documents dont vous avez besoin pour créer et gérer des machines virtuelles Linux dans le modèle de déploiement Classic.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## Prise en main
* [Présentation de Linux sous Azure](virtual-machines-linux-intro-on-azure.md)
* [Forum aux questions sur les machines virtuelles Azure créées avec le modèle de déploiement classique](virtual-machines-linux-classic-faq.md)
* [À propos des images pour les machines virtuelles](virtual-machines-linux-classic-about-images.md)
* [Chargement de votre propre image de distribution](virtual-machines-linux-classic-create-upload-vhd.md) (et instructions d’utilisation d’une [distribution approuvée dans Azure](virtual-machines-linux-endorsed-distros.md))
* [Connexion à une machine virtuelle exécutant Linux à l’aide du Portail Azure Classic](virtual-machines-linux-mac-create-ssh-keys.md)

## Installation
* [Installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md)

## Didacticiels
* [Installation de la pile LAMP sur une machine virtuelle Linux dans Azure](virtual-machines-linux-create-lamp-stack.md)
* [Application Web Ruby on Rails sur une machine virtuelle Azure](virtual-machines-linux-classic-ruby-rails-web-app.md)
* [Installation d’Apache Qpid Proton-C pour AMQP et Service Bus](../service-bus-messaging/service-bus-amqp-apache.md)

### Bases de données
* [optimisation des performances de MySQL dans Azure](virtual-machines-linux-classic-optimize-mysql.md)
* [clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
* [exécution de Cassandra avec Linux sur Azure et accès au cluster depuis Node.js](virtual-machines-linux-classic-cassandra-nodejs.md)
* [création d'un cluster multimaîtres de bases de données MariaDb](virtual-machines-linux-classic-mariadb-mysql-cluster.md)

### HPC
* [Prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
* [Exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

### Docker
* [Utilisation de l’extension Docker VM à partir de l’interface de ligne de commande Microsoft Azure](virtual-machines-linux-classic-cli-use-docker.md)
* [Utilisation de l’extension Docker VM avec le portail Azure](virtual-machines-linux-classic-portal-use-docker.md)
* [Comment utiliser docker-machine sur Azure](virtual-machines-linux-docker-machine.md)

### Ubuntu
* [Clusters MySQL](virtual-machines-linux-classic-mysql-cluster.md)
* [Node.js et Cassandra](virtual-machines-linux-classic-cassandra-nodejs.md)

### OpenSUSE
* [Installation et exécution de MySQL](virtual-machines-linux-classic-mysql-on-opensuse.md)

### CoreOS
* [Utilisation de CoreOS dans Azure](https://coreos.com/os/docs/latest/booting-on-azure.html)

## Planification
* [Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-linux-infrastructure-subscription-accounts-guidelines.md)
* [sélection de noms d'utilisateurs Linux](virtual-machines-linux-usernames.md)
* [Configuration d’un groupe à haute disponibilité pour des machines virtuelles dans le modèle de déploiement classique](virtual-machines-linux-classic-configure-availability.md)
* [Planification d’une maintenance planifiée sur des machines virtuelles Azure](virtual-machines-linux-planned-maintenance-schedule.md)
* [Gestion de la disponibilité des machines virtuelles](virtual-machines-linux-manage-availability.md)
* [Maintenance planifiée des machines virtuelles Linux dans Azure](virtual-machines-linux-planned-maintenance.md)

## Déploiement
* [Création d’une machine virtuelle personnalisée exécutant Linux](virtual-machines-linux-classic-createportal.md)
* [Concepts de base : capture d'une machine virtuelle Linux pour en faire un modèle](virtual-machines-linux-classic-capture-image.md)
* [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md)

## Gestion
* [SSH](virtual-machines-linux-mac-create-ssh-keys.md)
* [comment réinitialiser un mot de passe ou des propriétés SSH pour Linux](virtual-machines-linux-classic-reset-access.md)
* [utilisation de la racine](virtual-machines-linux-use-root-privileges.md)

## Ressources Azure
* [agent Linux Azure](virtual-machines-linux-agent-user-guide.md)
* [Fonctionnalités et extensions de machine virtuelle Azure](virtual-machines-windows-extensions-features.md)
* [injection de données personnalisées dans une machine virtuelle à utiliser avec Cloud-init](virtual-machines-windows-classic-inject-custom-data.md)

## Storage
* [association d'un disque de données à une machine virtuelle Linux](virtual-machines-linux-classic-attach-disk.md)
* [détachement d'un disque de données d'une machine virtuelle Linux](virtual-machines-linux-classic-detach-disk.md)
* [RAID](virtual-machines-linux-configure-raid.md)

## Mise en réseau
* [Comment configurer des points de terminaison sur une machine virtuelle classique dans Azure](virtual-machines-linux-classic-setup-endpoints.md)

## Résolution de problèmes
* [Résolution des problèmes des connexions SSH avec une machine virtuelle Azure Linux](virtual-machines-linux-troubleshoot-ssh-connection.md)
* [Résoudre les problèmes de déploiement Classic liés à la création d’une machine virtuelle Linux dans Azure](virtual-machines-linux-classic-troubleshoot-deployment-new-vm.md)
* [Résoudre les problèmes de déploiement Classic liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure](virtual-machines-linux-classic-restart-resize-error-troubleshooting.md)

## Référence
* [Commandes de l’interface de ligne de commande Azure en mode Azure Service Management (ASM)](../virtual-machines-command-line-tools.md)
* [API REST de gestion de service Microsoft Azure](https://msdn.microsoft.com/library/azure/ee460799.aspx)

## Liens généraux
Les liens ci-après se rapportent aux blogs Microsoft, aux pages Technet et à des sites externes, et non plus à la documentation Azure.com indiquée ci-dessus. Les univers d’Azure et de l’open source évoluant assez vite, les liens suivants sont probablement obsolètes, *bien que* nous nous efforcions constamment d’ajouter de nouvelles rubriques et de supprimer celles qui ne sont plus d’actualité. Si vous constatez qu’une rubrique obsolète n’a pas été supprimée, n’hésitez pas à laisser un commentaire ou envoyez une demande d’extraction à notre [référentiel Github](https://github.com/Azure/azure-content/).

* [Exécution d'ASP.NET 5 sur Linux à l'aide de conteneurs Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
* [déploiement d'une image de machine virtuelle CentOS à partir d'OpenLogic](https://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
* [SUSE Update Infrastructure (Infrastructure de mise à jour SUSE)](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
* [SUSE Linux Enterprise Server pour SAP Cloud Appliance Library](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)
* [génération d'une distribution Linux à haute disponibilité dans Azure en 12 étapes](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
* [Automate Provisioning Linux on Azure with Azure CLI, node.js, jhawk (Automatisation de l’approvisionnement de Linux dans Azure avec l’interface de ligne de commande Azure, node.js et jhawk)](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
* [GlusterFS sur Azure](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

### FreeBSD
* [exécution de FreeBSD dans Azure](https://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
* [déploiement facile de FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
* [déploiement d'une image FreeBSD personnalisée](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
* [Kaspersky Antivirus pour Linux File Server](https://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

### Nosql
* [8 sources de données NoSql open source pour Azure](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
* [Slideshare (MSOpenTech) : Expériences d’utilisation de CouchDb dans Azure](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
* [exécution de CouchDB-as-a-Service avec node.js, CORS et Grunt](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
* [Redis sur Windows dans le service de mise en cache Redis Azure](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
* [annonce du fournisseur d'état de session ASP.NET pour la version d'aperçu de Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
* [Blog : RavenHQ désormais disponible dans Azure Marketplace](https://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### Données volumineuses (« Big Data »)
* [installation de Hadoop sur des machines virtuelles Linux Azure](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
* [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)

### Base de données relationnelle
* [Architecture de haute disponibilité MySQL dans Microsoft Azure](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
* [Installation de Postgres avec corosync, pg\_bouncer en utilisant ILB](https://github.com/chgeuer/postgres-azure)

### Calcul haute performance (HPC) Linux
* [Modèle de démarrage rapide : Spin up a SLURM cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/slurm) (et [billet de blog](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx))
* [Modèle de démarrage rapide : Création d'un cluster HPC avec des nœuds de calcul Linux](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

### DevOps, gestion et optimisation
Étant donné que les domaines des DevOps, de la gestion et de l’optimisation sont assez vastes et évoluent rapidement, il convient de considérer la liste ci-dessous comme un point de départ.

* [Vidéo : Azure Virtual Machines : Using Chef, Puppet and Docker for managing Linux VMs (Utilisation de Chef, Puppet et Docker pour la gestion des machines virtuelles Linux)](https://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)
* [Guide complet du déploiement automatisé de cluster Kubernetes avec CoreOS et Weave](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
* [Visualiseur Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Plug-in Jenkins Slave pour Azure](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
* [Référentiel GitHub : Plug-in Jenkins Storage pour Azure](https://github.com/jenkinsci/windows-azure-storage-plugin)
* [Tiers : Plug-in Hudson Slave pour Azure](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
* [Tiers : Plug-in Hudson Storage pour Azure](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Vidéo : Description et fonctionnement de Chef](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)
* [Vidéo : Comment utiliser Azure Automation avec des machines virtuelles Linux](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* [Blog : Configuration d’état souhaité PowerShell pour Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
* [GitHub : Configuration d’état souhaité du client Docker](https://github.com/anweiss/DockerClientDSC)
* [Plug-in Packer pour Azure](https://github.com/msopentech/packer-azure)

<!---HONumber=AcomDC_0928_2016-->