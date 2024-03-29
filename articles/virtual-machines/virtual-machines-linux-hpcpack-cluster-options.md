---
title: Options de cluster HPC Pack Linux dans le cloud | Microsoft Docs
description: Découvrez les options de Microsoft HPC Pack pour créer et gérer un cluster HPC (High Performance Computing) Linux dans le cloud Azure
services: virtual-machines-linux,cloud-services
documentationcenter: ''
author: dlepow
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep

---
# Options de HPC Pack pour créer et gérer un cluster HPC dans Azure pour des charges de travail Linux
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Cet article traite des options d’utilisation de HPC Pack pour exécuter des charges de travail Linux. Il existe également des options d’exécution [de charges de travail Windows HPC avec HPC Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## Configuration d’un cluster HPC Pack dans Azure
### Modèles Azure
* (Place de marché) [Cluster HPC Pack pour les charges de travail Linux](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* (Démarrage rapide) [Créer un cluster HPC avec des nœuds de calcul Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### Script de déploiement PowerShell
* [Créer un cluster HPC Linux avec le script de déploiement HPC Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### Didacticiels
* [Didacticiel : prise en main des nœuds de calcul Linux dans un cluster HPC Pack dans Azure](virtual-machines-linux-classic-hpcpack-cluster.md)
* [Didacticiel : exécution de NAMD avec Microsoft HPC Pack sur des nœuds de calcul Linux dans Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [Didacticiel : Exécuter STAR-CCM+ avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### Gestion de cluster
* [Envoyer des travaux à un cluster HPC Pack dans Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
* [Gestion des travaux dans HPC Pack](https://technet.microsoft.com/library/jj899585.aspx)

## Créer des clusters RDMA pour des charges de travail MPI
* [Didacticiel : Exécuter OpenFOAM avec Microsoft HPC Pack sur un cluster Linux RDMA dans Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
* [Configuration d’un cluster Linux RDMA pour exécuter des applications MPI](virtual-machines-linux-classic-rdma-cluster.md)

<!---HONumber=AcomDC_0928_2016-->