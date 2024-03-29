---
title: Concepts de base du service Azure Batch | Microsoft Docs
description: "Découvrez l’utilisation d’Azure Batch pour les charges de travail HPC et parallèles à grande échelle"
services: batch
documentationcenter: 
author: mmacy
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/22/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2559aae752f319805c2c9f87a2e2a81d9b37e4b2


---
# <a name="basics-of-azure-batch"></a>Notions de base d’Azure Batch
Azure Batch vous permet d’exécuter efficacement dans le cloud des applications de calcul haute performance (HPC) en parallèle et à grande échelle. Ce service de plateforme planifie les travaux nécessitant une grande quantité de ressources système à exécuter sur une collection gérée de machines virtuelles. Il peut mettre automatiquement à l’échelle les ressources de calcul pour répondre aux besoins du travail.

Avec le service Batch, vous définissez des ressources de calcul Azure pour exécuter vos applications en parallèle et à grande échelle. Vous pouvez exécuter des travaux à la demande ou selon un calendrier précis, sans avoir à créer, configurer et gérer manuellement un cluster HPC, des machines virtuelles individuelles, des réseaux virtuels ou une infrastructure complexe de planification des tâches et des travaux.

## <a name="use-cases-for-batch"></a>Exemples d’utilisation du service Batch
Batch est un service Azure géré qui est utilisé pour le *traitement par lots* ou *Batch Computing*, et qui exécute un grand nombre de tâches similaires afin d’obtenir les résultats souhaités. Le traitement par lots est couramment utilisé dans les entreprises qui sont régulièrement amenées à traiter, transformer et analyser d’importants volumes de données.

Le service Batch fonctionne parfaitement avec les applications et charges de travail intrinsèquement parallèles (ou « massivement parallèles »). Les charges de travail intrinsèquement parallèles peuvent être facilement fractionnées en plusieurs tâches exécutées simultanément sur plusieurs ordinateurs.

![Tâches parallèles][1]<br/>

Voici quelques exemples de charges de travail communément traitées à l’aide de cette technique :

* Modélisation de risques financiers
* Analyse des données hydrologiques et météorologiques
* Rendu, analyse et traitement d’images
* Encodage et transcodage multimédia
* Analyse de séquence génétique
* Analyse des contraintes en ingénierie
* Test de logiciels

Batch peut également effectuer des calculs parallèles avec une opération de réduction à la fin, et des charges de travail HPC plus complexes telles que les applications [MPI (Interface de transfert de message)](batch-mpi.md) .

Pour obtenir une comparaison entre Batch et d’autres solutions HPC utilisées dans Azure, consultez la page [Solutions Batch et HPC](batch-hpc-solutions.md).

## <a name="developing-with-batch"></a>Développement avec Batch
Le traitement des charges de travail parallèles avec le traitement par lots est généralement effectué par programme en utilisant une des [API Batch](#batch-development-apis). Les API Batch vous permettent de créer et gérer des pools de nœuds de calcul (machines virtuelles) et de planifier les travaux et les tâches qui s’y exécutent. L’application cliente ou le service que vous créez utilisera les API Batch pour communiquer avec le service Batch.

Vous pouvez efficacement traiter des charges de travail à grande échelle pour votre organisation, ou fournir un service frontal à vos clients afin qu’ils puissent exécuter des travaux et des tâches, à la demande ou selon un calendrier, sur un nœud, des centaines de nœuds, voire des milliers. Vous pouvez également utiliser Batch dans le cadre d’un plus grand flux de travail, géré par des outils comme [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [!TIP]
> Si vous souhaitez mieux comprendre les fonctionnalités offertes par l’API Batch, consultez la page [Vue d’ensemble des fonctionnalités d’Azure Batch](batch-api-basics.md).
> 
> 

### <a name="azure-accounts-youll-need"></a>Comptes Azure dont vous aurez besoin
Lorsque vous développez des solutions Batch, vous devrez utiliser les comptes suivants dans Microsoft Azure.

* **Compte et abonnement Azure** : si vous ne possédez pas encore d’abonnement Azure, vous pouvez activer les [avantages de votre abonnement MSDN][msdn_benefits] ou vous inscrire pour obtenir un [compte Azure gratuit][free_account]. Lorsque vous créez un compte, un abonnement par défaut est automatiquement créé.
* **Compte Batch** : lorsque vos applications interagissent avec le service Batch, le nom du compte, l’URL du compte et une clé d’accès sont utilisés comme informations d’identification. Toutes vos ressources Batch, telles que les pools, les nœuds de calcul, les travaux et les tâches, sont associées à un compte Batch. Vous pouvez [créer un compte Azure Batch](batch-account-create-portal.md) à l’aide du portail Azure.
* **Compte de stockage** : Batch inclut la prise en charge intégrée de l’utilisation des fichiers dans [Stockage Azure][azure_storage]. Presque tous les scénarios du service Batch font appel à Azure Storage pour la préparation des programmes exécutés par vos tâches et les données qu’ils traitent ainsi que pour le stockage des données de sortie générées par vos tâches. Pour créer un compte de stockage, consultez l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

### <a name="batch-development-apis"></a>API de développement Batch
Vos applications et services peuvent émettre des appels d’API REST directs, utiliser une ou plusieurs des bibliothèques clientes suivantes, ou une combinaison des deux, pour gérer les ressources de calcul et exécuter des charges de travail parallèles à grande échelle à l’aide du service Batch.

| API | Informations de référence sur l'API | Télécharger | Exemples de code |
| --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |[MSDN][batch_rest] |
| **Batch .NET** |[MSDN][api_net] |[NuGet ][api_net_nuget] |[GitHub][api_sample_net] |
| **Python Batch** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |
| **Batch Java** (preview) |[github.io][api_java] |[Maven][api_java_jar] |[GitHub][api_sample_java] |

### <a name="batch-resource-management"></a>Gestion des ressources Batch
Outre les API clientes, vous pouvez utiliser les éléments suivants pour gérer les ressources au sein de votre compte Batch.

* [Applets de commande PowerShell pour Batch][batch_ps] : les applets de commande Azure Batch contenues dans le module [Azure PowerShell](../powershell-install-configure.md) vous permettent de gérer les ressources du service Batch avec PowerShell.
* [CLI Azure](../xplat-cli-install.md): l’interface de ligne de commande Azure est un ensemble d’outils multiplateforme qui fournit des commandes d’environnement permettant d’interagir avec de nombreux services Azure, y compris le service Batch.
* Bibliothèque cliente [.NET Batch Management](batch-management-dotnet.md) : également disponible via [NuGet][api_net_mgmt_nuget], la bibliothèque cliente .NET Batch Management permet de gérer les comptes Batch, les quotas et les packages d’application par programme. Consultez les références pour la bibliothèque de gestion sur [MSDN][api_net_mgmt].

### <a name="batch-tools"></a>Outils du service Batch
Même si ces outils ne sont pas indispensables pour créer des solutions à l’aide du service Batch, ils peuvent s’avérer utiles lors de la création et le débogage des services et applications Batch.

* [Portail Azure][portal] : vous pouvez créer, surveiller et supprimer des pools, des travaux et des tâches dans les panneaux Batch du portail Azure. Vous pouvez consulter les informations d’état de ces éléments et d’autres ressources pendant que vous exécutez vos travaux, et même télécharger des fichiers des nœuds de calcul de vos pools (télécharger un élément `stderr.txt` d’une tâche mise en échec pendant la résolution des problèmes, par exemple). Vous pouvez également télécharger des fichiers du Bureau à distance (RDP), à utiliser pour vous connecter aux nœuds de calcul.
* [Azure Batch Explorer][batch_explorer] : Batch Explorer fournit une fonctionnalité de gestion des ressources Batch similaire à celle du portail Azure, mais dans une application cliente Windows Presentation Foundation (WPF) autonome. Il s’agit de l’un des exemples d’applications Batch .NET disponibles sur [GitHub][github_samples]. Vous pouvez le générer avec Visual Studio 2015 ou une version ultérieure et l’utiliser pour accéder aux ressources et de votre compte Batch et les gérer pendant que vous développez et déboguez vos solutions Batch. Consultez les détails sur les travaux, les pools et les tâches, téléchargez des fichiers des nœuds de calcul et connectez-vous à distance aux nœuds à l’aide des fichiers du Bureau à distance (RDP) téléchargeables avec Batch Explorer.
* [Explorateur de Stockage Microsoft Azure][storage_explorer] : bien qu’il ne s’agisse pas à proprement parler d’un outil Azure Batch, l’Explorateur de stockage est l’un de vos meilleurs alliés pendant le développement et le débogage de vos solutions Batch.

## <a name="scenario-scale-out-a-parallel-workload"></a>Scénario : Montée en charge d’une charge de travail parallèle
Une solution courante qui utilise les API Batch pour interagir avec le service Batch suppose une montée en charge de travaux intrinsèquement parallèles (par exemple, le rendu d’images pour les scènes 3D) sur un pool de nœuds de calcul. Ce pool de nœuds de calcul peut être, par exemple, une « ferme de rendu » qui fournit des dizaines, des centaines voire des milliers de cœurs à votre travail de rendu.

Le schéma suivant illustre un flux de travail Batch courant, dans lequel une application cliente ou un service hébergé utilise Batch pour exécuter une charge de travail parallèle.

![Flux de travail de la solution Batch][2]

Dans ce scénario courant, votre application ou service traite une charge de travail de calcul dans Azure Batch en effectuant les opérations suivantes :

1. Chargement des **fichiers d’entrée** et de **l’application** qui traitera ces fichiers dans votre compte de stockage Azure. Les fichiers d’entrée peuvent correspondre à des données que votre application va traiter (par exemple, des données de modélisation financière ou des fichiers vidéo à transcoder). Les fichiers d’application peuvent être n’importe quelle application pour traiter les données, telle qu’une application de rendu 3D ou un transcodeur multimédia.
2. Création d’un **pool** de nœuds de calcul dans votre compte Batch : il s’agit des machines virtuelles qui exécuteront les tâches. Vous devez spécifier des propriétés, telles que la [taille du nœud](../cloud-services/cloud-services-sizes-specs.md), le système d’exploitation et l’emplacement d’installation de l’application dans Stockage Azure lorsque les nœuds rejoignent le pool (c’est-à-dire l’application que vous avez chargée à l’étape 1). Vous pouvez également configurer la [mise à l’échelle automatique](batch-automatic-scaling.md)du pool (ajustement dynamique du nombre de nœuds de calcul dans le pool) en réponse à la charge de travail que génèrent vos tâches.
3. Création d’un **travail** Batch pour exécuter la charge de travail sur le pool de nœuds de calcul. Lorsque vous créez un travail, vous devez l’associer à un pool Batch.
4. Ajoutez des **tâches** au travail. Lorsque vous ajoutez des tâches à un travail, le service Batch planifie automatiquement l’exécution des tâches sur les nœuds de calcul du pool. Chaque tâche utilise l’application que vous avez téléchargée pour traiter les fichiers d’entrée.
   
   * 4a. Avant son exécution, la tâche peut charger les données (les fichiers d’entrée) qu’elle devra traiter dans le nœud de calcul auquel elle est affectée. Si l’application n’a pas déjà été installée sur le nœud (voir l’étape 2), vous pouvez la télécharger à cet emplacement. Lorsque les téléchargements sont terminés, les tâches s’exécutent sur les nœuds qui leur sont attribués.
5. Au cours de l’exécution des tâches, vous pouvez interroger le service Batch pour surveiller la progression du travail et des tâches associées. Votre application cliente ou votre service communiquent avec le service Batch via HTTPS ; si vous souhaitez surveiller des milliers de tâches exécutées sur des milliers de nœuds de calcul, veillez donc à [interroger efficacement le service Batch](batch-efficient-list-queries.md).
6. Une fois les tâches exécutées, elles peuvent chargent leurs données de sortie dans Azure Storage. Vous pouvez également extraire des fichiers directement à partir des nœuds de calcul.
7. Lorsque votre analyse détecte la fin de l’exécution des tâches de votre travail, votre application cliente ou votre service peut télécharger les données de sortie afin de les traiter ou de les évaluer.

N’oubliez pas qu’il ne s’agit ici que d’une des nombreuses façons d’utiliser Batch et que ce scénario décrit seulement quelques-unes des fonctionnalités disponibles. Par exemple, vous pouvez exécuter [plusieurs tâches en parallèle](batch-parallel-node-tasks.md) sur chaque nœud de calcul et utiliser [les tâches de préparation et d’exécution de travaux](batch-job-prep-release.md) pour préparer les nœuds à l’exécution de vos travaux avant de les nettoyer.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous disposez d’une vue d’ensemble du service Batch, le moment est venu d’approfondir cette première approche pour découvrir comment utiliser ce service dans le traitement de charges de travail parallèles nécessitant beaucoup de ressources système.

* Consultez la [Présentation des fonctionnalités du service Batch pour les développeurs](batch-api-basics.md). Elle contient des informations indispensables pour toute personne se préparant à l’utilisation de Batch. L’article contient des informations plus détaillées sur les ressources du service Batch telles que les pools, les nœuds, les travaux et les tâches, ainsi que sur les nombreuses fonctionnalités API que vous pouvez utiliser lors de la création de votre application Batch.
* [Prise en main de la bibliothèque Azure Batch pour .NET](batch-dotnet-get-started.md) pour apprendre à utiliser C# et la bibliothèque .NET Batch pour exécuter une charge de travail simple à l’aide d’un flux de travail Batch courant. Cet article servira de référence essentielle dans la découverte de l’utilisation du service Batch. Il existe également une [version Python](batch-python-tutorial.md) du didacticiel.
* Téléchargez les [exemples de code sur GitHub][github_samples] pour voir comment C# et Python peuvent interagir avec Batch pour planifier et traiter des exemples de charge de travail.
* Consultez le [parcours d’apprentissage de Batch][learning_path] pour avoir une idée des ressources dont vous allez disposer dans votre découverte du service Batch.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Nov16_HO2-->


