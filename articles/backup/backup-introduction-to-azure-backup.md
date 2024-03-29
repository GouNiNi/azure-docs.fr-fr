---
title: "Qu’est-ce qu’Azure Backup ? | Microsoft Docs"
description: "Grâce à Azure Backup et à Recovery Services, vous pouvez sauvegarder et restaurer des données et des applications à partir de serveurs Windows, d’ordinateurs clients Windows, de serveurs System Center DPM ou de machines virtuelles Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: tysonn
keywords: "sauvegarde et restauration ; services de restauration ; solutions de sauvegarde"
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2016
ms.author: jimpark; trinadhk
translationtype: Human Translation
ms.sourcegitcommit: cf3930f209e84ee9b14b56566ca19d31382946aa
ms.openlocfilehash: cefb405b4f30ca5fe20f6acfaee5ebba2690990b


---
# <a name="what-is-azure-backup"></a>Qu’est-ce qu’Azure Backup ?
Azure Backup est le service Azure qui vous permet de sauvegarder (ou de protéger) et de restaurer vos données dans le cloud Microsoft. Azure Backup remplace votre solution de sauvegarde locale ou hors site par une solution basée dans le cloud à la fois fiable, sécurisée et économique. Azure Backup propose plusieurs composants que vous pouvez télécharger et déployer sur l’ordinateur ou sur le serveur approprié, ou dans le cloud. Vous déployez un composant (ou un agent) en fonction de ce que vous souhaitez protéger. Vous pouvez utiliser tous les composants Azure Backup (que vous protégiez des données en local ou dans le cloud) pour sauvegarder des données dans un coffre de sauvegarde au sein d’Azure. Pour plus d’informations sur le composant à utiliser pour protéger des données, des applications ou des charges de travail spécifiques, consultez le [tableau des composants Azure Backup](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (plus loin dans cet article).

[Regarder une vidéo de présentation d’Azure Backup](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>Pourquoi utiliser Azure Backup ?
Les solutions de sauvegarde traditionnelles ont évolué et considèrent désormais le cloud comme un point de terminaison ou une destination de stockage statique similaire aux disques ou bandes. Bien que simple, cette approche est limitée et ne tire pas pleinement parti d’une plateforme de cloud sous-jacente, ce qui se traduit par une solution coûteuse et inefficace. Certaines solutions sont coûteuses, car elles vous obligent à payer un type de stockage inapproprié ou une capacité de stockage inutile. D’autres encore sont inefficaces, car elles ne fournissent pas le type ou la quantité de stockage dont vous avez besoin, ou les tâches administratives prennent beaucoup trop de temps. Par opposition, Azure Backup offre les principaux avantages suivants :

**Gestion automatique du stockage** : les environnements hybrides impliquent souvent un stockage hétérogène (une partie en local et une autre dans le cloud). Avec Azure Backup, l’utilisation d’appareils de stockage locaux ne génère aucun coût. Azure Backup alloue et gère automatiquement le stockage de sauvegarde sur la base d’un modèle de paiement à l’utilisation. Avec le paiement à l’utilisation, vous payez uniquement le stockage que vous utilisez. Pour plus d’informations, consultez [l’article sur les tarifs Azure](https://azure.microsoft.com/pricing/details/backup).

**Mise à l’échelle illimitée** : Azure Backup utilise la puissance et l’échelle illimitée du cloud Azure pour garantir la haute disponibilité, sans supplément de maintenance ou de surveillance. Vous pouvez configurer des alertes pour fournir des informations sur les événements, mais vous n’avez pas à vous soucier de la haute disponibilité de vos données dans le cloud.

**Diverses options de stockage** : la réplication du stockage est l’un des facteurs de haute disponibilité. La sauvegarde Azure propose deux types de réplication : le [stockage localement redondant](../storage/storage-redundancy.md#locally-redundant-storage) et le [stockage géorépliqué](../storage/storage-redundancy.md#geo-redundant-storage). Choisissez l’option de stockage de sauvegarde en fonction de vos besoins :

* Le stockage localement redondant (LRS) réplique vos données trois fois (il crée trois copies de vos données) dans un centre de données associé au sein de la même région. Le stockage LRS est une option économique qui convient parfaitement aux clients désireux de maîtriser leur budget, car il protège les données contre les défaillances matérielles locales.
* Le stockage par géoréplication (GRS) réplique vos données vers une région secondaire à des centaines de kilomètres de l’emplacement primaire des données sources. Le stockage GRS est plus onéreux que le stockage LRS, mais il fournit un niveau supérieur de durabilité des données, même en cas de panne régionale.

**Transfert de données illimité** : Azure Backup ne limite pas la quantité de données entrantes ou sortantes transférées. Par ailleurs, les données transférées ne sont pas facturées par Azure Backup. Toutefois, si vous utilisez le service Azure Import/Export pour importer de grandes quantités de données, les données entrantes ont un coût. Pour plus d’informations sur ce coût, consultez [Flux de travail de la sauvegarde hors connexion dans Azure Backup](backup-azure-backup-import-export.md). Les données sortantes sont les données transférées depuis un coffre de sauvegarde pendant une opération de restauration.

**Chiffrement des données** : le chiffrement des données garantit une transmission et un stockage sécurisés de vos données dans le cloud public. La phrase secrète de chiffrement est stockée en local et n’est jamais transmise ou stockée dans Azure. Si vous avez besoin de restaurer des données, vous êtes le seul à disposer de la phrase secrète de chiffrement ou de la clé.

**Sauvegarde cohérente avec les applications** : pour sauvegarder un serveur de fichiers, une machine virtuelle ou une base de données SQL, vous avez besoin de savoir qu’un point de récupération contient toutes les données requises pour restaurer la copie de sauvegarde. Azure Backup fournit des sauvegardes cohérentes avec les applications, qui garantissent qu’aucun correctif supplémentaire n’est requis pour restaurer les données. La restauration de données cohérentes avec les applications réduit le délai de restauration, ce qui permet de rétablir rapidement le fonctionnement normal.

**Conservation à long terme** : sauvegardez vos données dans Azure pendant 99 ans. Au lieu de basculer les copies de sauvegarde sur disque vers la sauvegarde sur bande, puis de déplacer cette dernière vers un emplacement hors site pour le stockage à long terme, vous pouvez utiliser Azure pour la rétention à court terme et à long terme.

## <a name="which-azure-backup-components-should-i-use"></a>Quels composants Azure Backup dois-je utiliser ?
Si vous ne savez pas quel composant Azure Backup utiliser pour vos besoins, consultez le tableau suivant qui explique ce que vous pouvez protéger avec chaque composant. Le portail Azure fournit un Assistant intégré pour vous aider à choisir le composant à télécharger et à déployer. L’Assistant, qui fait partie de la création du coffre Recovery Services, vous permet de sélectionner un objectif de sauvegarde et de choisir les données ou les applications à protéger en quelques étapes.

| Composant | Avantages | limites | Qu’est-ce qui est protégé ? | Où sont stockées les sauvegardes ? |
| --- | --- | --- | --- | --- |
| Agent Azure Backup (MARS) |<li>Sauvegarde des fichiers et des dossiers sur un système d’exploitation Windows physique ou virtuel (les machines virtuelles peuvent être locales ou dans Azure)<li>Aucun serveur de sauvegarde distinct n’est requis. |<li>Sauvegarde 3 fois par jour <li>Ne tient pas compte des applications ; restauration au niveau du fichier, du dossier et du volume seulement, <li>  Linux non pris en charge. |<li>Fichiers, <li>Dossiers |Archivage de sauvegarde Azure |
| System Center DPM |<li>Instantanés tenant compte des applications (VSS)<li>Flexibilité totale concernant le moment d’exécution de la sauvegarde<li>Granularité de récupération (tout)<li>Peut utiliser le coffre Azure Backup<li>Prise en charge de Linux (s’il est hébergé sur Hyper-V) <li>Protection des machines virtuelles VMware avec DPM 2012 R2 |Ne prend pas en charge la sauvegarde de la charge de travail Oracle |<li>Fichiers, <li>Dossiers,<li> Volumes, <li>Machines virtuelles,<li> Applications,<li> Charges de travail |<li>Coffre Azure Backup,<li> Disque connecté localement,<li>  Bande (locale uniquement) |
| Azure Backup Server |<li>Instantanés tenant compte des applications (VSS)<li>Flexibilité totale concernant le moment d’exécution de la sauvegarde<li>Granularité de récupération (tout)<li>Peut utiliser le coffre Azure Backup<li>Prise en charge de Linux (s’il est hébergé sur Hyper-V)<li>Ne nécessite pas de licence System Center |<li>Absence de prise en charge hétérogène (sauvegarde de machine virtuelle VMware, sauvegarde de la charge de travail Oracle)<li>Requiert toujours un abonnement Azure en direct<li>Aucune prise en charge de la sauvegarde sur bande |<li>Fichiers, <li>Dossiers,<li> Volumes, <li>Machines virtuelles,<li> Applications,<li> Charges de travail |<li>Coffre Azure Backup,<li> Disque connecté localement |
| Sauvegarde des machines virtuelles IaaS Azure |<li>Sauvegardes natives pour Windows/Linux<li>Aucune installation spécifique d’agent n’est requise<li>Sauvegarde au niveau structure sans nécessiter d’infrastructure de sauvegarde |<li>Sauvegarde des machines virtuelles une fois par jour <li>Restauration des machines virtuelles uniquement au niveau du disque<li>Impossible d’effectuer une sauvegarde en local |<li>Machines virtuelles, <li>Tous les disques (à l’aide de PowerShell) |<p>Archivage de sauvegarde Azure</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>Quels sont les scénarios de déploiement de chaque composant ?
| Composant | Déploiement possible dans Azure ? | Déploiement possible localement ? | Stockage cible pris en charge |
| --- | --- | --- | --- |
| Agent Azure Backup (MARS) |<p>**Oui**</p> <p>L’agent Azure Backup peut être déployé sur n’importe quelle machine virtuelle Windows Server exécutée dans Azure.</p> |<p>**Oui**</p> <p>L’agent Backup peut être déployé sur n’importe quelle machine virtuelle ou physique Windows Server.</p> |<p>Archivage de sauvegarde Azure</p> |
| System Center DPM |<p>**Oui**</p><p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide de System Center DPM](backup-azure-dpm-introduction.md).</p> |<p>**Oui**</p> <p>Apprenez-en davantage sur [la protection des charges de travail et des machines virtuelles dans votre centre de données](https://technet.microsoft.com/en-us/system-center-docs/dpm/data-protection-manager).</p> |<p>Disque connecté localement,</p> <p>Coffre Azure Backup,</p> <p>Bande (locale uniquement)</p> |
| Azure Backup Server |<p>**Oui**</p><p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>**Oui**</p> <p>Apprenez-en davantage sur [la protection des charges de travail dans Azure à l’aide d’Azure Backup Server](backup-azure-microsoft-azure-backup.md).</p> |<p>Disque connecté localement,</p> <p>Archivage de sauvegarde Azure</p> |
| Sauvegarde des machines virtuelles IaaS Azure |<p>**Oui**</p><p>Partie de la structure Azure</p><p>Spécialisé dans la [sauvegarde des machines virtuelles Azure IaaS (infrastructure en tant que service)](backup-azure-vms-introduction.md).</p> |<p>**Non**</p> <p>Utilisez System Center DPM pour sauvegarder des machines virtuelles dans votre centre de données.</p> |<p>Archivage de sauvegarde Azure</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>Quelles applications et charges de travail est-il possible de sauvegarder ?
Le tableau suivant fournit une matrice des données et des charges de travail pouvant être protégées à l’aide d’Azure Backup. La colonne Solution Azure Backup contient des liens vers la documentation de déploiement de cette solution. Chaque composant Azure Backup peut être déployé dans un environnement de modèle de déploiement Classic ou Resource Manager.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

| Données ou charge de travail | Environnement source | Solution Azure Backup |
| --- | --- | --- |
| Fichiers et dossiers |Windows Server |<p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Fichiers et dossiers |Ordinateur Windows |<p>[Agent Azure Backup](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machine virtuelle Hyper-V (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ l’agent Azure Backup),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (inclut l’agent Azure Backup)</p> |
| Machines virtuelles IaaS Azure (Windows) |exécution dans Azure |[Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |
| Machines virtuelles IaaS Azure (Linux) |exécution dans Azure |[Azure Backup (extension de machine virtuelle)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Prise en charge de Linux
Le tableau suivant montre les composants Azure Backup prenant en charge Linux.  

| Composant | Prise en charge Linux (approuvée par Azure) |
| --- | --- |
| Agent Azure Backup (MARS) |Aucun (agent uniquement sur Windows) |
| System Center DPM |Sauvegarde cohérente avec les fichiers sur Hyper-V seulement<br/> (non disponible pour la machine virtuelle Azure) |
| Azure Backup Server |Sauvegarde cohérente avec les fichiers sur Hyper-V seulement<br/> (non disponible pour la machine virtuelle Azure) |
| Sauvegarde des machines virtuelles IaaS Azure |Oui |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Utilisation des machines virtuelles Premium Storage avec Azure Backup
Azure Backup protège les machines virtuelles Premium Storage. Stockage Premium Azure est un stockage SSD conçu pour supporter des charges de travail nécessitant de nombreuses E/S. Stockage Premium est intéressant pour les charges de travail des machines virtuelles. Pour plus d’informations sur Stockage Premium, consultez l’article [Stockage Premium : stockage hautes performances pour les charges de travail des machines virtuelles Azure](../storage/storage-premium-storage.md).

### <a name="back-up-premium-storage-vms"></a>Sauvegarder des machines virtuelles Premium Storage
Lors de la sauvegarde de machines virtuelles Premium Storage, le service Backup crée un emplacement temporaire intermédiaire dans le compte Premium Storage. L'emplacement intermédiaire, nommé « AzureBackup- », est égal à la taille totale des données des disques Premium attachés à la machine virtuelle.

> [!NOTE]
> Évitez de modifier l'emplacement intermédiaire.
>
>

Une fois la sauvegarde terminée, l'emplacement intermédiaire est supprimé. Le prix du stockage utilisé pour l’emplacement intermédiaire est conforme à l’ensemble de la [tarification de Premium Storage](../storage/storage-premium-storage.md#pricing-and-billing).

### <a name="restore-premium-storage-vms"></a>Restaurer des machines virtuelles Premium Storage
Les machines virtuelles Stockage Premium peuvent être restaurées dans Stockage Premium ou dans le stockage standard. La restauration d'un point de récupération de machines virtuelles Premium Storage dans Premium Storage est le processus de restauration classique. Toutefois, il peut être rentable de restaurer un point de récupération de machines virtuelles Premium Storage dans le stockage standard. Ce type de restauration peut être utilisé si vous avez besoin d'un sous-ensemble de fichiers de la machine virtuelle.

## <a name="what-are-the-features-of-each-backup-component"></a>Quelles sont les fonctionnalités de chaque composant Azure Backup ?
Les sections suivantes comportent des tableaux qui résument la disponibilité ou la prise en charge de diverses fonctionnalités dans chaque composant Azure Backup. Pour un support ou des détails supplémentaires, consultez les informations après chaque tableau.

### <a name="storage"></a>Storage
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Archivage de sauvegarde Azure |![Oui][green] |![Oui][green] |![Oui][green] |![Oui][green] |
| Stockage sur disque | |![Oui][green] |![Oui][green] | |
| Stockage sur bande | |![Oui][green] | | |
| Compression <br/>(dans le coffre de sauvegarde) |![Oui][green] |![Oui][green] |![Oui][green] | |
| Sauvegarde incrémentielle |![Oui][green] |![Oui][green] |![Oui][green] |![Oui][green] |
| Déduplication de disque | |![Partiellement][yellow] |![Partiellement][yellow] | |

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

Le coffre Azure Backup est la cible de stockage par défaut sur tous les composants. System Center DPM et le serveur de sauvegarde Azure offrent également la possibilité de disposer d’une copie du disque local. Toutefois, seul System Center DPM permet d’écrire des données sur un périphérique de stockage sur bande.

#### <a name="compression"></a>Compression
En outre, les sauvegardes sont compressées afin de réduire la quantité d’espace de stockage requise. L’extension de machine virtuelle est le seul composant qui n’effectue aucune compression. L’extension de machine virtuelle copie toutes les données de sauvegarde de votre compte de stockage vers le coffre de sauvegarde dans la même région. Aucune compression n’est effectuée lors du transfert des données. Le transfert des données sans compression augmente légèrement le stockage utilisé. Toutefois, le stockage des données sans compression réduit les délais de restauration au cas où vous auriez besoin d’utiliser ce point de récupération.

#### <a name="incremental-backup"></a>Sauvegarde incrémentielle
Chaque composant prend en charge la sauvegarde incrémentielle quel que soit le stockage cible (disque, bande, coffre de sauvegarde). La sauvegarde incrémentielle garantit un stockage efficace des sauvegardes, en transférant uniquement les modifications apportées depuis la dernière sauvegarde.

#### <a name="disk-deduplication"></a>Déduplication de disque
Vous pouvez tirer parti de la déduplication lorsque vous déployez System Center DPM ou le serveur de sauvegarde Azure [sur une machine virtuelle Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). Windows Server effectue la déduplication (au niveau de l’hôte) sur les disques durs virtuels attachés en tant que stockage de sauvegarde à la machine virtuelle.

> [!NOTE]
> La déduplication n’est pas disponible dans Azure pour aucun des composants Azure Backup. Lorsque System Center DPM et Azure Backup Server sont déployés dans Azure, les disques de stockage attachés à la machine virtuelle ne peuvent pas être dédupliqués.
>
>

### <a name="security"></a>Sécurité
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Sécurité du réseau<br/> (vers Azure) |![Oui][green] |![Oui][green] |![Oui][green] |![Partiellement][yellow] |
| Sécurité des données<br/> (dans Azure) |![Oui][green] |![Oui][green] |![Oui][green] |![Partiellement][yellow] |

![clé de table](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Sécurité du réseau
L’ensemble du trafic de sauvegarde entre vos serveurs et le coffre de sauvegarde est chiffré à l’aide du protocole AES (Advanced Encryption Standard) 256. Les données de sauvegarde sont envoyées via une connexion HTTPS sécurisée. Les données de sauvegarde sont également stockées dans le coffre Azure Backup sous une forme chiffrée. Vous êtes le seul, en tant que client Azure, à connaître la phrase secrète pour déverrouiller ces données. Microsoft ne peut déchiffrer les données de sauvegarde à aucun moment.

> [!WARNING]
> Une fois le coffre de sauvegarde établi, vous êtes le seul à avoir accès à la clé de chiffrement. Microsoft ne conserve jamais de copie de votre clé de chiffrement et n’a pas accès à la clé. Si la clé est égarée, Microsoft ne peut pas récupérer les données de sauvegarde.
>
>

#### <a name="data-security"></a>Sécurité des données
La sauvegarde des machines virtuelles Azure exige la configuration du chiffrement *dans* la machine virtuelle. Utilisez BitLocker sur les machines virtuelles Windows et **dm-crypt** sur les machines virtuelles Linux. Azure Backup ne chiffre pas automatiquement les données de sauvegarde en provenance de ce chemin d’accès.

### <a name="network"></a>Réseau
| Fonctionnalité | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Compression réseau <br/>(vers le **serveur de sauvegarde**) | |![Oui][green] |![Oui][green] | |
| Compression réseau <br/>(vers le **coffre de sauvegarde**) |![Oui][green] |![Oui][green] |![Oui][green] | |
| Protocole réseau <br/>(vers le **serveur de sauvegarde**) | |TCP |TCP | |
| Protocole réseau <br/>(vers le **coffre de sauvegarde**) |HTTPS |HTTPS |HTTPS |HTTPS |

![clé de table](./media/backup-introduction-to-azure-backup/table-key-2.png)

L’extension de machine virtuelle lit directement les données à partir du compte de stockage Azure via le réseau de stockage. Il n’est donc pas nécessaire de compresser ce trafic.

Si vous sauvegardez vos données sur un System Center DPM ou sur un serveur de sauvegarde Azure, compressez les données transférées du serveur principal vers le serveur de sauvegarde. Le fait de compresser les données avant de les sauvegarder dans DPM ou dans le serveur de sauvegarde Azure permet d’économiser de la bande passante.

#### <a name="network-throttling"></a>Limitation du réseau
L’agent Azure Backup assure une limitation du réseau qui permet de contrôler l’utilisation de la bande passante réseau pendant le transfert de données. Cette limitation peut s’avérer utile si vous avez besoin de sauvegarder des données pendant les heures de travail, mais ne souhaitez pas que le processus de sauvegarde interfère avec le reste du trafic internet. La limitation du transfert de données s’applique aux activités de sauvegarde et de restauration.

### <a name="backup-and-retention"></a>Sauvegarde et rétention
|  | Agent Azure Backup | System Center DPM | Azure Backup Server | Sauvegarde des machines virtuelles IaaS Azure |
| --- | --- | --- | --- | --- |
| Fréquence de sauvegarde<br/> (vers le coffre de sauvegarde) |Trois sauvegardes par jour |Deux sauvegardes par jour |Deux sauvegardes par jour |Une sauvegarde par jour |
| Fréquence de sauvegarde<br/> (vers le disque) |Non applicable |<li>Toutes les 15 minutes pour SQL Server <li>Toutes les heures pour les autres charges de travail |<li>Toutes les 15 minutes pour SQL Server <li>Toutes les heures pour les autres charges de travail</p> |Non applicable |
| Options de rétention |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |Quotidienne, hebdomadaire, mensuelle, annuelle |
| Période de rétention |Jusqu’à 99 ans |Jusqu’à 99 ans |Jusqu’à 99 ans |Jusqu’à 99 ans |
| Points de récupération dans le coffre Azure Backup |Illimité |Illimité |Illimité |Illimité |
| Points de récupération sur le disque local |Non applicable |<li>64 pour les serveurs de fichiers,<li>448 pour les serveurs d’applications |<li>64 pour les serveurs de fichiers,<li>448 pour les serveurs d’applications |Non applicable |
| Points de récupération sur bande |Non applicable |Illimité |Non applicable |Non applicable |

## <a name="what-is-the-vault-credential-file"></a>Qu’est-ce que le fichier d’informations d’identification de coffre ?
Le fichier d’informations d’identification de coffre est un certificat qui est généré par le portail pour chaque coffre de sauvegarde. Le portail télécharge ensuite la clé publique pour le Service de contrôle d’accès (ACS). La clé privée vous est fournie lors du téléchargement des informations d’identification. Utilisez-la pour enregistrer les ordinateurs que vous protégez. La clé privée vous permet d’authentifier les serveurs ou les ordinateurs pour envoyer des données de sauvegarde vers un coffre de sauvegarde particulier.

Vous utilisez uniquement les informations d’identification du coffre pour enregistrer les serveurs ou les ordinateurs. Toutefois, soyez attentif avec les informations d’identification du coffre. En cas de perte ou de récupération par d’autres personnes, les informations d’identification du coffre peuvent être utilisées pour enregistrer d’autres ordinateurs dans le même coffre. Comme les données de sauvegarde sont chiffrées à l’aide d’une phrase secrète à laquelle vous êtes le seul à avoir accès, les données de sauvegarde existantes ne peuvent pas être compromises. Les informations d’identification du coffre expirent au bout de 48 heures. Même si vous pouvez télécharger les informations d’identification du coffre de sauvegarde aussi souvent que vous le souhaitez, seules les dernières informations d’identification peuvent être utilisées pour l’enregistrement.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>Quelle est la différence entre Azure Backup et Azure Site Recovery ?
Azure Backup et Azure Site Recovery sont liés dans la mesure où les deux services sauvegardent les données et peuvent les restaurer. Toutefois, leurs principaux atouts sont différents.

Azure Backup protège les données en local et dans le cloud. Azure Site Recovery coordonne la réplication, le basculement et la restauration automatique des machines virtuelles et des serveurs physiques. Les deux services sont importants, car votre solution de récupération d’urgence doit protéger vos données et les rendre récupérables (Backup) *et* assurer la disponibilité de vos charges de travail (Site Recovery) en cas de panne.

Les concepts qui suivent vont vous aider à prendre des décisions importantes en matière de sauvegarde et de récupération.

| Concept | Détails | Sauvegarde | Récupération d’urgence |
| --- | --- | --- | --- |
| Objectif de point de récupération (RPO) |Quantité de perte de données acceptable si la récupération doit être exécutée. |Les solutions de sauvegarde offrent des RPO extrêmement variables. Les sauvegardes de machines virtuelles ont généralement un RPO d’un jour, contre seulement 15 minutes pour les sauvegardes de base de données. |Les solutions de récupération d’urgence ont un RPO faible. La copie de récupération d’urgence peut devoir être prête en seulement quelques secondes ou quelques minutes. |
| Objectif de délai de récupération (RTO) |Quantité de temps nécessaire pour effectuer une récupération ou une restauration complète. |Un RPO plus long est généralement synonyme pour la solution de sauvegarde d’une bien plus grande quantité de données à traiter, ce qui rallonge d’autant le RTO. Par exemple, il peut falloir plusieurs jours pour restaurer des données à partir de bandes, selon le temps nécessaire au transport de la bande depuis un site externe. |Les solutions de récupération d’urgence ont un RTO plus faible car elles sont davantage synchronisées avec la source et ont moins de modifications à traiter. |
| Rétention |Durée pendant laquelle les données doivent être stockées |Pour les scénarios qui exigent une reprise des opérations (altération des données, suppression accidentelle de fichiers, défaillances du système d’exploitation), les données de sauvegarde sont généralement conservées pendant 30 jours au maximum.<br>Du point de vue de la conformité, il se peut que vous deviez stocker les données pendant des mois, voire des années. Dans ce cas, les données de sauvegarde sont parfaitement adaptées aux besoins d’archivage. |Une récupération d’urgence porte uniquement sur les données de récupération opérationnelle, soit en général quelques heures, sans dépasser une journée. Puisque les solutions de récupération d’urgence sont conçues pour capturer les données à un niveau extrêmement précis, l’utilisation des données de récupération d’urgence n’est pas recommandée dans le cadre d’une rétention à long terme. |

## <a name="next-steps"></a>Étapes suivantes
Utilisez l’une des didacticiels suivants pour obtenir des instructions complètes détaillées pour protéger les données sur Windows Server, ou protéger une machine virtuelle (VM) dans Azure :

* [Sauvegarde des fichiers et dossiers](backup-try-azure-backup-in-10-mins.md)
* [Sauvegarde des machines virtuelles Azure](backup-azure-vms-first-look-arm.md)

Pour plus d’informations sur la protection des autres charges de travail, consultez l’un des articles suivants :

* [Sauvegarder Windows Server](backup-configure-vault.md)
* [Sauvegarder les charges de travail des applications](backup-azure-microsoft-azure-backup.md)
* [Sauvegarde des machines virtuelles IaaS Azure](backup-azure-vms-prepare.md)

[vert]: ./media/backup-introduction-to-azure-backup/green.png
[jaune]: ./media/backup-introduction-to-azure-backup/yellow.png
[rouge]: ./media/backup-introduction-to-azure-backup/red.png



<!--HONumber=Nov16_HO3-->


