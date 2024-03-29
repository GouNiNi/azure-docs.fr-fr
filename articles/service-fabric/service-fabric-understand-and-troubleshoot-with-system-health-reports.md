---
title: Utilisation des rapports d’intégrité du système pour la résolution des problèmes | Microsoft Docs
description: Décrit les rapports d’intégrité envoyés par les composants Azure Service Fabric et leur utilisation pour la résolution des problèmes de cluster ou d’application.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2016
ms.author: oanapl

---
# <a name="use-system-health-reports-to-troubleshoot"></a>Utiliser les rapports d’intégrité du système pour la résolution des problèmes
Les composants Azure Service Fabric signalent sans configuration l’intégrité de toutes les entités du cluster. Le [magasin d’intégrité](service-fabric-health-introduction.md#health-store) crée et supprime des entités en fonction des rapports du système. Il les organise au sein d’une hiérarchie qui tient compte des interactions entre les entités.

> [!NOTE]
> Pour mieux comprendre les concepts relatifs à l’intégrité, consultez la documentation relative au [modèle d’intégrité du Service Fabric](service-fabric-health-introduction.md).
> 
> 

Les rapports d’intégrité du système procurent une visibilité sur les fonctionnalités du cluster et des applications, et signalent les problèmes d’intégrité. Pour les applications et services, les rapports d’intégrité du système vérifient que les entités sont implémentées et qu’elles se comportent correctement du point de vue de Service Fabric. Le rapport ne fournit aucune information sur l’intégrité de la logique métier du service ni sur la détection des processus bloqués. Les services utilisateur peuvent enrichir les données d’intégrité avec des informations spécifiques à leur logique.

> [!NOTE]
> Les rapports de surveillance d’intégrité sont visibles uniquement *après* que les composants système ont créé une entité. Lorsqu’une entité est supprimée, le magasin d’intégrité élimine automatiquement l’ensemble des rapports d’intégrité qui lui sont associés. Cela vaut également quand une nouvelle instance de l’entité est créée (par exemple une nouvelle instance de réplica de service est créée). Tous les rapports associés à l’ancienne instance sont supprimés et éliminés du magasin.
> 
> 

Les rapports sur les composants système sont identifiés par la source, qui commence par le préfixe « **System.** » . Les pilotes de surveillance ne peuvent pas utiliser le même préfixe pour leurs sources, car les rapports dotés de paramètres non valides sont rejetés.
Examinons certains rapports du système et tâchons d’identifier les éléments déclencheurs et d’imaginer des moyens de corriger les problèmes potentiels qu’ils représentent.

> [!NOTE]
> Service Fabric continue d’ajouter des rapports sur les domaines d’intérêt qui améliorent la visibilité sur les événements associés au cluster et aux applications.
> 
> 

## <a name="cluster-system-health-reports"></a>Rapports d’intégrité du système sur le cluster
L’entité d’intégrité du cluster est créée automatiquement dans le magasin d’intégrité. Si tout fonctionne correctement, elle ne présente pas de rapport système.

### <a name="neighborhood-loss"></a>Perte de voisinage
**System.Federation** indique une erreur s’il détecte une perte de voisinage. Le rapport est issu de nœuds individuels ; l’ID de nœud est inclus dans le nom de propriété. Si un voisinage est perdu dans l’ensemble de l’anneau de Service Fabric, deux événements sont généralement signalés (les deux côtés de l’intervalle indiquent une erreur). Si plusieurs voisinages sont perdus, d’autres d’événements ont lieu.

Le rapport spécifie le délai d’expiration du bail globale comme durée de vie. Il est renvoyé lorsque la moitié de la durée de vie est atteinte, tant que la condition reste active. L’événement arrivé à expiration est automatiquement supprimé. Le comportement de suppression à expiration garantit le nettoyage approprié du rapport dans le magasin d’intégrité, même si le nœud de création de rapports est arrêté.

* **SourceId**: System.Federation.
* **Property** : commence par **Neighborhood** et inclut des informations sur le nœud.
* **Étapes suivantes**: recherchez ce qui a provoqué la perte de voisinage (par exemple, vérifiez la communication entre les nœuds du cluster).

## <a name="node-system-health-reports"></a>Rapports d’intégrité du système sur les nœuds
**System.FM**, qui représente le service Failover Manager, est l’autorité qui gère les informations sur les nœuds de cluster. Un rapport System.FM indiquant son état doit être alloué à chaque nœud. Les entités de nœud sont supprimées lorsque l’état du nœud est supprimé (consultez [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-up/down"></a>Nœud activé/désactivé
System.FM consigne la valeur OK lorsque le nœud rejoint l’anneau (il est opérationnel). Il indique une erreur lorsque le nœud quitte l’anneau (il est inactif, en raison d’une mise à niveau ou simplement d’une défaillance). La hiérarchie d’intégrité développée par le magasin d’intégrité agit sur les entités déployées en corrélation avec les rapports sur les nœuds de System.FM. Elle traite le nœud comme un parent virtuel de toutes les entités déployées. Les entités déployées sur ce nœud sont exposées via des requêtes si le nœud est indiqué comme actif par System/FM, avec la même instance comme instance associée aux entités. Lorsque System.FM fait état de l’inactivité ou du redémarrage du nœud (nouvelle instance), le magasin d’intégrité nettoie automatiquement les entités déployées qui peuvent exister uniquement sur le nœud inactif ou sur l’instance précédente du nœud.

* **SourceId**: System.FM
* **Property**: State
* **Étapes suivantes**: si le nœud est inactif en raison d’une mise à niveau, il doit redevenir actif une fois l’opération terminée. Dans ce cas, l’état d’intégrité doit repasser sur OK. Si le nœud ne redevient pas actif ou s’il échoue, le problème requiert un examen plus approfondi.

L’exemple suivant représente l’événement System.FM avec un état d’intégrité OK pour le nœud actif :

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Expiration du certificat
**System.FabricNode** indique un avertissement lorsque les certificats utilisés par le nœud sont sur le point d’arriver à expiration. Chaque nœud comporte trois certificats associés : **Certificate_cluster**, **Certificate_server** et **Certificate_default_client**. Lorsque la date d’expiration est à au moins deux semaines, l’état d’intégrité du rapport est OK. Si elle a lieu dans les deux semaines qui suivent, le type de rapport est un avertissement. La durée de vie de ces événements est infinie, et ils sont supprimés lorsqu’un nœud quitte un cluster.

* **SourceId**: System.FabricNode
* **Property** : commence par **Certificate** et contient des informations supplémentaires sur le type de certificat
* **Étapes suivantes**: mettez à jour les certificats sur le point d’arriver à expiration.

### <a name="load-capacity-violation"></a>Violation de capacité de charge
L’équilibrage de charge de Service Fabric indique un avertissement s’il détecte une violation de la capacité du nœud.

* **SourceId**: System.PLB
* **Property** : commence par **Capacity**
* **Étapes suivantes**: contrôlez les mesures fournies et examinez la capacité actuelle sur le nœud.

## <a name="application-system-health-reports"></a>Rapports d’intégrité du système sur les applications
**System.CM**, qui représente le service Cluster Manager, est l’autorité qui gère les informations sur une application.

### <a name="state"></a>State
System.CM consigne la valeur OK lorsque l’application a été créée ou mise à jour. Il informe le magasin d’intégrité lorsque l’application a été supprimée afin qu’elle puisse en être retirée.

* **SourceId**: System.CM
* **Property**: State
* **Étapes suivantes**: si l’application a été créée, elle doit inclure le rapport d’intégrité du Gestionnaire du cluster. Dans le cas contraire, vérifiez l’état de l’application en émettant une requête (par exemple, l’applet de commande PowerShell **Get-ServiceFabricApplication -ApplicationName *nom_application***).

L’exemple suivant représente l’événement d’état sur l’application **fabric:/WordCount** :

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Rapports d’intégrité du système sur les services
**System.FM**, qui représente le service Failover Manager, est l’autorité qui gère les informations sur les services.

### <a name="state"></a>State
System.FM consigne la valeur OK lorsque le service a été créé. Il supprime l’entité du magasin d’intégrité lorsque le service a été supprimé.

* **SourceId**: System.FM
* **Property**: State

L’exemple suivant représente l’événement d’état sur le service **fabric:/WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violation de réplicas non placés
**System.PLB** indique un avertissement s’il ne peut pas trouver d’emplacement pour un ou plusieurs réplicas de service. Lorsqu’il arrive à expiration, le rapport est supprimé.

* **SourceId**: System.FM
* **Property**: State
* **Étapes suivantes**: vérifiez les contraintes de service et l’état actuel de l’emplacement.

L’exemple suivant représente une violation pour un service configuré avec 7 réplicas cibles dans un cluster à 5 nœuds :

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Rapports d’intégrité du système sur les partitions
**System.FM**, qui représente le service Failover Manager, est l’autorité qui gère les informations sur les partitions de service.

### <a name="state"></a>State
System.FM consigne la valeur OK lorsque la partition créée est intègre. Il élimine l’entité du magasin d’intégrité lorsque la partition est supprimée.

Si la partition présente une valeur inférieure au nombre minimal de réplicas, une erreur est signalée. Si la partition présente une valeur non inférieure au nombre minimal de réplicas, mais inférieure au nombre cible de réplicas, un avertissement est signalé. Si la partition subit une perte de quorum, System.FM indique une erreur.

Les autres événements importants incluent un avertissement quand la reconfiguration et la génération prennent plus de temps que prévu. Les délais impartis pour la génération et la reconfiguration sont configurables en fonction des scénarios de service. Par exemple, si un service présente un état défini en téraoctet, par exemple une instance SQL Database, la génération prendra davantage de temps que celle d’un service affichant un état d’un volume moindre.

* **SourceId**: System.FM
* **Property**: State
* **Étapes suivantes**: si l’état d’intégrité n’est pas OK, il est possible que certains réplicas n’aient pas été créés, ouverts ou promus comme réplicas principaux ou secondaires de manière correcte. Dans de nombreux cas, la cause principale est un bogue de service dans l’implémentation du rôle d’ouverture ou de modification.

L’exemple suivant représente une partition saine :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

L’exemple suivant représente l’intégrité d’une partition qui présente un nombre de réplicas inférieur à la valeur cible. L’étape suivante consiste à obtenir la description de partition, qui représente le mode de configuration : **MinReplicaSetSize** est défini sur 2 et **TargetReplicaSetSize** est défini sur 7. Obtenez ensuite le nombre de nœuds dans le cluster : 5. Par conséquent, dans ce cas, deux réplicas ne peuvent pas être placés.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violation des contraintes de réplicas
**System.PLB** indique un avertissement s’il détecte une violation des contraintes de réplicas et qu’il ne peut pas placer les réplicas de la partition.

* **SourceId**: System.PLB
* **Property** : commence par **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Rapports d’intégrité du système sur les réplicas
**System.RA**, qui représente le composant Reconfiguration Agent, est l’autorité de l’état des réplicas.

### <a name="state"></a>State
**System.RA** consigne la valeur OK lorsque le réplica a été créé.

* **SourceId**: System.RA
* **Property**: State

L’exemple suivant représente un réplica sain :

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>État d’ouverture du réplica
La description de ce rapport d’intégrité contient l’heure de début (temps universel coordonné) de l’appel d’API.

**System.RA** indique un avertissement si l’ouverture du réplica dure plus longtemps que la période configurée (valeur par défaut : 30 minutes). Si l’API a une incidence sur la disponibilité du service, le rapport est émis plus rapidement (intervalle configurable avec une valeur de 30 secondes par défaut). La durée inclut le temps nécessaire à l’ouverture du duplicateur et du service. Lorsque l’ouverture se termine, la propriété affiche la valeur OK.

* **SourceId**: System.RA
* **Property**: **ReplicaOpenStatus**
* **Étapes suivantes**: si l’état d’intégrité ne présente pas la valeur OK, recherchez pourquoi l’ouverture du réplica prend plus de temps que prévu.

### <a name="slow-service-api-call"></a>Appel lent d’API de service
**System.RAP** et **System.Replicator** indiquent un avertissement si un appel de code de service utilisateur prend plus de temps que la durée configurée. L’avertissement est effacé à l’exécution de l’appel.

* **SourceId**: System.RAP ou System.Replicator
* **Property**: nom de l’API lente. La description fournit plus de détails sur le délai de mise en attente de l’API.
* **Étapes suivantes**: recherchez pourquoi l’appel prend plus de temps que prévu.

L’exemple suivant représente une partition affichant une perte de quorum et la procédure d’investigation exécutée pour identifier la raison. L’un des réplicas présente un état d’intégrité d’avertissement ; vous êtes ainsi renseigné sur sa condition. Le code montre que le service prend plus de temps que prévu ; il s’agit d’un événement signalé par System.RAP. Une fois cette information reçue, il convient maintenant d’examiner le code de service et d’effectuer les recherches qui s’imposent. Dans ce cas, l’implémentation **RunAsync** du service avec état génère une exception non prise en charge. Notez que les réplicas sont en cours de recyclage. Il est donc possible qu’aucun d’entre eux n’affiche l’état d’avertissement. Vous pouvez réessayer d’obtenir l’état d’intégrité et rechercher les éventuelles différences d’ID de réplica. Dans certains cas, les nouvelles tentatives peuvent vous donner des indices.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Lors du démarrage de l’application défaillante sous le débogueur, les fenêtres des événements de diagnostic affichent l’exception levée par RunAsync :

![Événements de diagnostic Visual Studio 2015 : Échec de RunAsync dans la structure : /HelloWorldStatefulApplication.][1]

Événements de diagnostic Visual Studio 2015 : Échec de RunAsync dans la **structure : /HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>File d’attente de réplication complète
**System.Replicator** indique un avertissement si la file d’attente de réplication est pleine. Sur le rôle principal, cela est généralement dû à la lenteur d’un ou de plusieurs réplicas secondaires à accuser réception des opérations. Sur le rôle secondaire, cela se produit habituellement lorsque le service prend trop de temps pour appliquer les opérations. L’avertissement est effacé une fois que la file d’attente n’est plus pleine.

* **SourceId**: System.Replicator
* **Property** : **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, en fonction du rôle de réplica

### <a name="slow-naming-operations"></a>Opérations de nommage lentes
**System.NamingService** signale l’intégrité sur son réplica principal quand une opération de nommage prend trop de temps. [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) et [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx) sont des exemples d’opérations de nommage. D’autres méthodes se trouvent sous FabricClient, par exemple dans les [méthodes de gestion de service](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) ou les [méthodes de gestion de propriété](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [!NOTE]
> Le service d’affectation de noms résout les noms des services dans un emplacement du cluster et permet aux utilisateurs de gérer les propriétés et les noms des services. Il s’agit d’un service persistant partitionné Service Fabric. L’une des partitions représente le propriétaire de l’autorité, qui contient des métadonnées sur tous les noms et les services Service Fabric. Les noms Service Fabric sont mappés à des partitions différentes, appelées partitions Propriétaire du nom. Ainsi, le service est extensible. En savoir plus sur le [service de nommage](service-fabric-architecture.md).
> 
> 

Quand une opération de nommage prend plus longtemps que prévu, elle est marquée avec un Avertissement sur le *réplica principal de la partition de service d’affectation de noms qui effectue l’opération*. Si l’opération se termine avec succès, l’avertissement est effacé. Si l’opération se termine avec une erreur, le rapport d’intégrité inclut des détails sur l’erreur.

* **SourceId**: System.NamingService
* **Property** : commence par le préfixe **Duration_** et identifie l’opération lente et le nom Service Fabric sur lequel l’opération est appliquée. Par exemple, si la création de service au nom fabric:/MyApp/MyService prend trop de temps, la propriété est Duration_AOCreateService.fabric:/MyApp/MyService. AO pointe vers le rôle de la partition de nommage pour ce nom et cette opération.
* **Étapes suivantes**: vérifier pourquoi l’opération de nommage échoue. Chaque opération peut avoir différentes causes principales. Par exemple, une opération de suppression de service peut se bloquer sur un nœud car l’hôte d’application se bloque constamment sur un nœud à cause d’un bogue utilisateur dans le code de service.

L’exemple suivant illustre une opération de création de service. L’opération a duré plus longtemps que la durée configurée. AO réessaie et envoie le travail à NO. NO a terminé la dernière opération avec Timeout. Dans ce cas, le même réplica est principal pour les rôles AO et NO.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Rapports d’intégrité du système sur les applications déployées
**System.Hosting** est l’autorité régnant sur les entités déployées.

### <a name="activation"></a>Activation
System.Hosting consigne la valeur OK lorsqu’une application a été activée sur le nœud. Dans le cas contraire, il indique une erreur.

* **SourceId**: System.Hosting
* **Property**: Activation, inclut la version de déploiement
* **Étapes suivantes**: si l’application est défectueuse, rechercher la raison de l’échec de l’activation.

L’exemple suivant représente une activation réussie :

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** indique une erreur en cas d’échec du téléchargement du package d’application.

* **SourceId**: System.Hosting
* **Property** : **Download:*RolloutVersion***
* **Étapes suivantes**: rechercher la raison de l’échec du téléchargement sur le nœud.

## <a name="deployedservicepackage-system-health-reports"></a>Rapports d’intégrité du système sur le package de service déployé
**System.Hosting** est l’autorité régnant sur les entités déployées.

### <a name="service-package-activation"></a>Activation du package de service
System.Hosting consigne la valeur OK si l’activation du package de service sur le nœud est réussie. Dans le cas contraire, il indique une erreur.

* **SourceId**: System.Hosting
* **Property**: Activation
* **Étapes suivantes**: examiner la raison de l’échec de l’activation.

### <a name="code-package-activation"></a>Activation du package de code
**System.Hosting** consigne la valeur OK pour chaque package de code en cas de réussite de l’activation. En cas d’échec de l’activation, il indique un avertissement conformément à la configuration. Si l’activation de **CodePackage** échoue, ou s’il se termine avec une erreur supérieure à la valeur **CodePackageHealthErrorThreshold** configurée, System.Hosting indique une erreur. Si un package de service contient plusieurs packages de code, un rapport d’activation est généré pour chacun d’entre eux.

* **SourceId**: System.Hosting
* **Property** : utilise le préfixe **CodePackageActivation** et contient le nom du package de code et le point d’entrée **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** (par exemple, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Inscription du type de service
**System.Hosting** consigne la valeur OK si le type de service a été inscrit correctement. Il indique une erreur si l’inscription n’a pas été effectuée à temps (conformément à la configuration de **ServiceTypeRegistrationTimeout**). Si l’inscription du type de service dans le nœud est annulée, cela est dû à la fermeture du runtime. Hosting indique un avertissement.

* **SourceId**: System.Hosting
* **Property** : utilise le préfixe **ServiceTypeRegistration** et contient le nom du type de service (par exemple, **ServiceTypeRegistration:FileStoreServiceType**)

L’exemple suivant représente un package de service déployé sain :

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Télécharger
**System.Hosting** indique une erreur en cas d’échec du téléchargement du package de service.

* **SourceId**: System.Hosting
* **Property** : **Download:*RolloutVersion***
* **Étapes suivantes**: rechercher la raison de l’échec du téléchargement sur le nœud.

### <a name="upgrade-validation"></a>Validation de mise à niveau
**System.Hosting** indique une erreur en cas d’échec de la validation lors la mise à niveau ou en cas d’échec de la mise à niveau sur le nœud.

* **SourceId**: System.Hosting
* **Property** : utilise le préfixe **FabricUpgradeValidation** et contient la version de mise à niveau
* **Description**: désigne l’erreur rencontrée

## <a name="next-steps"></a>Étapes suivantes
[Affichage rapports d’intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Comment signaler et contrôler l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Surveiller et diagnostiquer les services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)

<!--HONumber=Oct16_HO2-->


