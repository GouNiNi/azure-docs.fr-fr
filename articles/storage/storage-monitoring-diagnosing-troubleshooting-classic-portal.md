---
title: Analyse, diagnostic et résolution des problèmes rencontrés sur Microsoft Azure Storage | Microsoft Docs
description: Utilisation de fonctionnalités telles que l’analyse du stockage, la journalisation côté client et d’autres outils tiers pour identifier, diagnostiquer et résoudre les problèmes liés à Azure Storage.
services: storage
documentationcenter: ''
author: jasonnewyork
manager: tadb
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: jahogg

---
# <a name="monitor,-diagnose,-and-troubleshoot-microsoft-azure-storage"></a>Surveiller, diagnostiquer et résoudre les problèmes liés à Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Vue d'ensemble
Le diagnostic et la résolution des problèmes dans une application distribuée hébergée dans un environnement cloud peuvent s'avérer plus complexes que dans des environnements traditionnels. Les applications peuvent être déployées dans une infrastructure PaaS ou IaaS, localement, sur un appareil mobile ou selon une formule combinée. Le trafic réseau de votre application traverse généralement des réseaux publics et privés, et votre application peut utiliser diverses technologies de stockage telles que les tables, les objets blob, les files d'attente ou les fichiers de Microsoft Azure Storage, en plus d'autres magasins de données tels que les bases de données relationnelles et de documents.

Pour gérer avec succès de telles applications, vous devez les analyser de façon proactive et savoir comment diagnostiquer et résoudre n'importe quel problème associé à leur fonctionnement et leurs technologies associées. En tant qu'utilisateur des services Azure Storage, vous devez surveiller en permanence les services de stockage utilisés par votre application afin de détecter tout comportement imprévu (par ex., des temps de réponse plus lents que d'habitude), et utiliser la journalisation afin de collecter davantage de données détaillées et analyser chaque problème en profondeur. Les informations de diagnostic obtenues via l'analyse et la journalisation vous aideront à déterminer la cause première du problème rencontré par votre application. Vous pouvez alors résoudre le problème et déterminer la procédure appropriée pour y remédier. Azure Storage est l'un des principaux services de Azure et un élément essentiel de la plupart des solutions que les clients déploient dans l'infrastructure Azure. Azure Storage inclut des fonctionnalités qui permettent de simplifier l'analyse, le diagnostic et la résolution des problèmes de stockage rencontrés par vos applications sur le cloud.

> [!NOTE]
> Pour l’instant, les fonctionnalités de mesure et de journalisation ne sont pas activées pour les comptes de stockage avec un type de réplication Stockage redondant dans une zone (ZRS). 
> 
> 

Pour obtenir un guide pratique de bout en bout pour la résolution des problèmes dans les applications Azure Storage, consultez [Résolution des problèmes de bout en bout avec les métriques et la journalisation Azure, AzCopy et Message Analyzer](storage-e2e-troubleshooting.md).

* [Introduction]
  * [Organisation de ce guide]
* [Analyse de votre service de stockage]
  * [Analyse de l’état d’intégrité du service]
  * [Analyse de la capacité]
  * [Analyse de la disponibilité]
  * [Analyse des performances]
* [Diagnostic des problèmes de stockage]
  * [Problèmes d’état d’intégrité du service]
  * [Problèmes de performances]
  * [Erreurs de diagnostic]
  * [Problèmes d’émulateur de stockage]
  * [Outils de journalisation du stockage]
  * [Utilisation des outils de journalisation réseau]
* [Suivi de bout en bout]
  * [Corrélation des données de journalisation]
  * [ID de la demande client]
  * [ID de la demande serveur]
  * [Horodatages]
* [Instructions pour la résolution des problèmes]
  * [Les métriques indiquent une valeur AverageE2ELatency élevée et une valeur AverageServerLatency faible]
  * [Les métriques indiquent une valeur AverageE2ELatency faible et une valeur AverageServerLatency faible, mais le client constate une latence élevée]
  * [Les métriques indiquent une valeur AverageServerLatency élevée]
  * [Vous constatez des retards inattendus dans la livraison des messages d’une file d’attente]
  * [Les métriques indiquent une augmentation de la valeur PercentThrottlingError]
  * [Les métriques indiquent une augmentation de la valeur PercentTimeoutError]
  * [Les métriques indiquent une augmentation de la valeur PercentNetworkError]
  * [Le client reçoit des messages HTTP 403 (Forbidden)]
  * [Le client reçoit des messages HTTP 404 (Not found)]
  * [Le client reçoit des messages HTTP 409 (Conflict)]
  * [Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d’analyse incluent des opérations avec un statut de transaction ClientOtherErrors]
  * [Les métriques de capacité indiquent une augmentation inattendue de l’utilisation de la capacité de stockage]
  * [Vous constatez des redémarrages inattendus des machines virtuelles associées à un grand nombre de disques durs virtuels]
  * [Votre problème provient de l’utilisation de l’émulateur de stockage pour le développement ou les tests]
  * [Vous rencontrez des problèmes pendant l’installation du Kit de développement logiciel (SDK) Azure pour .NET]
  * [Vous rencontrez un autre problème avec un service de stockage]
* [Annexes]
  * [Annexe 1 : utilisation de Fiddler pour capturer le trafic HTTP et HTTPS]
  * [Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]
  * [Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]
  * [Annexe 4 : utilisation d’Excel pour afficher les métriques et les données de journalisation]
  * [Annexe 5 : analyse avec Application Insights pour Visual Studio Team Services]

## <a name="<a-name="introduction"></a>introduction"></a><a name="introduction"></a>Introduction
Ce guide vous explique comment utiliser des fonctionnalités telles que Azure Storage Analytics, la journalisation côté client dans la bibliothèque cliente de Azure Storage, et d’autres outils tiers permettant d’identifier, diagnostiquer et résoudre les problèmes liés à Azure Storage.

![][1]

*Figure 1 Analyse, diagnostic et résolution des problèmes*

Ce guide est destiné principalement aux développeurs de services en ligne qui utilisent les services Azure Storage et aux professionnels de l’informatique responsables de la gestion de tels services en ligne. Ce guide a pour objectifs de :

* Vous aider à maintenir l'état d'intégrité et les performances de vos comptes Azure Storage.
* Mettre à votre disposition les processus et outils nécessaires pour vous aider à déterminer si un problème rencontré dans une application est lié à Azure Storage.
* Mettre à votre disposition des mesures concrètes pour la résolution des problèmes liés à Azure Storage.

### <a name="<a-name="how-this-guide-is-organized"></a>how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Organisation de ce guide
La section «[Analyse de votre service de stockage]» explique comment analyser l'état d'intégrité et les performances de vos services Azure Storage à l'aide des métriques Azure Storage Analytics (métriques de stockage).

La section «[Diagnostic des problèmes de stockage]» explique comment diagnostiquer les problèmes à l'aide de la journalisation Azure Storage Analytics (journalisation du stockage). Elle explique également comment activer la journalisation côté client à l'aide des fonctionnalités dans une des bibliothèques clientes, telles que la bibliothèque cliente de stockage pour .NET ou le Kit de développement logiciel (SDK) pour Java.

La section «[Suivi de bout en bout]» explique comment mettre en corrélation les informations contenues dans divers fichiers journaux et les données métriques.

La section «[Instructions pour la résolution des problèmes]» fournit des instructions pour la résolution de certains des problèmes de stockage communs.

La section «[Annexes]» inclut des informations concernant l'utilisation d'autres outils tels que Wireshark et Netmon pour l'analyse des données des paquets réseau, Fiddler pour l'analyse des messages HTTP/HTTPS, et l'analyseur de message Microsoft pour la mise en corrélation des données de journalisation.

## <a name="<a-name="monitoring-your-storage-service"></a>monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Analyse de votre service de stockage
Si vous connaissez les outils d’analyse de performances Windows, vous pouvez considérer les métriques de stockage comme l’équivalent, dans Azure Storage, des compteurs de l’Analyseur de performances Windows. Les métriques de stockage incluent un vaste éventail de métriques (appelées compteurs dans la terminologie de l’Analyseur de performances Windows) telles que la disponibilité du service, le nombre total de demandes du service ou le pourcentage de demandes réussies du service (pour obtenir une liste complète des métriques disponibles, consultez la section <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schéma de table de métriques Storage Analytics</a> sur MSDN). Vous pouvez spécifier si vous désirez que le service de stockage collecte et agrège les métriques toutes les heures ou toutes les minutes. Pour plus d’informations sur la façon d’activer les métriques et d’analyser vos comptes de stockage, consultez la section <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">Activation des métriques de stockage</a> sur MSDN.

Vous pouvez sélectionner les métriques horaires à afficher dans le portail Azure et configurer les règles de notification par messagerie des administrateurs quand une métrique horaire dépasse un seuil spécifique (pour plus d’informations, consultez la page <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">Procédure : réception de notifications d’alerte et gestion de règles d’alerte dans Azure</a>). Le service de stockage collecte les métriques du mieux qu’il peut, mais peut ne pas enregistrer toutes les opérations de stockage.

La figure 2 ci-dessous illustre la page Monitor du portail Azure Classic, où vous pouvez afficher des métriques telles que la disponibilité, le nombre total de demandes et les valeurs de latence moyennes pour un compte de stockage. Une règle de notification a également été configurée afin d'alerter l'administrateur lorsque la disponibilité chute en dessous d'un certain niveau. Lorsque ces données sont affichées, l'un des possibles éléments d'enquête est la présence d'un pourcentage inférieur à 100 % dans le service de table (pour plus d'informations, voir la section «[Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d'analyse incluent des opérations avec un statut de transaction ClientOtherErrors]»).

![][2]

*Figure 2 Affichage des métriques de stockage dans le portail Azure Classic*

Vous devez surveiller en permanence vos applications Azure afin de vous assurer qu’elles sont intègres et fonctionnent comme prévu en :

* Établissant certaines métriques de base pour l'application, qui vous permettront de comparer les données actuelles et d'identifier toute modification significative dans le comportement du stockage Azure et votre application. Les valeurs de vos métriques de base seront, dans de nombreux cas, spécifiques à l'application et doivent être établies lors des tests de performances de votre application.
* Enregistrant les métriques par minute et en les utilisant pour analyser activement les erreurs inattendues et anomalies telles que les pics au niveau du nombre d'erreurs ou du taux de demandes.
* Enregistrant les métriques horaires et en les utilisant pour analyser les valeurs moyennes telles que le nombre d'erreurs et le taux de demandes moyens.
* Enquêtant sur les problèmes potentiels à l'aide des outils de diagnostic abordés plus bas dans la section «[Diagnostic des problèmes de stockage]».

Les graphiques de la Figure3 ci-dessous illustrent comment la moyenne établie pour les métriques horaires peut cacher certains pics d'activité. Les métriques horaires s'affichent pour indiquer un taux de demandes stable ; les métriques par minute révèlent les fluctuations réelles.

![][3]

La suite de cette section décrit quelles métriques vous devriez analyser et pourquoi.

### <a name="<a-name="monitoring-service-health"></a>monitoring-service-health"></a><a name="monitoring-service-health"></a>Analyse de l’état d’intégrité du service
Vous pouvez utiliser le [portail Azure Classic](https://manage.windowsazure.com) pour afficher l’état du service de stockage (et des autres services Azure) dans toutes les régions Azure de par le monde. Vous pouvez ainsi savoir immédiatement si un problème sur lequel vous n’avez pas de contrôle affecte la région dans laquelle vous utilisez l’application.

Le portail Azure Classic peut également envoyer des notifications des incidents qui affectent les divers services Azure.
Remarque : Ces informations étaient auparavant accessibles avec les données d’historique, sur le tableau de bord du service Azure à l’adresse <a href="http://status.azure.com" target="_blank">http://status.azure.com</a>.

Le portail Azure recueille les informations d’état à l’intérieur des centres de données Azure (analyse de l’intérieur vers l’extérieur), mais vous pouvez également adopter une approche de l’extérieur vers l’intérieur et générer des transactions synthétiques qui accèdent périodiquement à votre application web Azure à partir de plusieurs emplacements. Les services offerts par <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">Keynote</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a> et Application Insights pour Visual Studio Team Services sont des exemples de ce type d’approche. Pour plus d’informations sur Application Insights pour Visual Studio Team Services, consultez «[Annexe 5 : analyse avec Application Insights pour Visual Studio Team Services]».

### <a name="<a-name="monitoring-capacity"></a>monitoring-capacity"></a><a name="monitoring-capacity"></a>Analyse de la capacité
Les métriques de stockage enregistrent uniquement les métriques de capacité pour le service d’objet blob, car les objets blob constituent généralement la majeure partie des données stockées (lors de l'écriture, il n'est pas possible d'utiliser les métriques de stockage pour analyser la capacité de vos tables et files d'attente). Ces données sont accessibles dans la table **$MetricsCapacityBlob** si vous avez activé l'analyse pour le service d'objet blob. Les métriques de stockage enregistrent ces données une fois par jour, et vous pouvez utiliser la valeur de la **RowKey** pour déterminer si la ligne contient une entité associée à des données utilisateur (valeur **data**) ou des données d’analyse (valeur **analytics**). Chaque entité stockée contient des informations sur la quantité de stockage utilisée (**Capacity** mesurée en octets) et le nombre actuel de conteneurs (**ContainerCount**) et d’objets blob (**ObjectCount**) utilisés dans le compte de stockage. Pour plus d’informations sur les métriques de capacité stockées dans la table **$MetricsCapacityBlob**, consultez <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schéma de table de métriques Storage Analytics</a> sur MSDN.

> [!NOTE]
> Ces valeurs doivent être analysées en guise de préavertissement lorsque vous approchez des limites de capacité de votre compte de stockage. Dans le portail Azure, dans la page **Monitor** de votre compte de stockage, vous pouvez ajouter des règles d’alerte pour être averti lorsque l’utilisation agrégée du stockage dépasse ou chute en dessous des seuils que vous spécifiez.
> 
> 

Pour plus d’informations sur l’estimation de la taille des divers objets de stockage tels que les objets blob, consultez le billet de blog <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity</a>.

### <a name="<a-name="monitoring-availability"></a>monitoring-availability"></a><a name="monitoring-availability"></a>Analyse de la disponibilité
Vous devez analyser la disponibilité des services de stockage dans votre compte de stockage en examinant la colonne **Availability** de vos tables de métriques horaires ou par minute — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La colonne **Availability** contient une valeur de pourcentage qui indique la disponibilité du service ou de l’opération API représentée par la ligne (la **RowKey** s’affiche si la ligne contient des métriques pour l’ensemble du service ou pour une opération API spécifique).

Toute valeur inférieure à 100 % indique que certaines demandes de stockage échouent. Vous pouvez connaître la raison de l'échec en examinant les autres colonnes dans les données métriques qui indiquent les nombres de demandes avec différents types d'erreur, tels que **ServerTimeoutError**. Il est normal que la valeur de la colonne **Availability** descende provisoirement en dessous de 100% pour des raisons telles que des délais d'expiration de serveur temporaires, lorsque le service déplace des partitions afin de mieux équilibrer la charge de la demande ; la logique de nouvelle tentative dans votre application client doit gérer ces conditions intermittentes. La page <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> énumère les types de transactions que les métriques de stockage incluent dans leur calcul de la valeur **Availability** .

Dans le portail Azure, dans la page **Monitor** de votre compte de stockage, vous pouvez ajouter des règles d’alerte pour être averti lorsque la valeur de **Availability** pour un service chute en dessous d’un seuil que vous spécifiez.

La section «[Instructions pour la résolution des problèmes]» de ce guide décrit certains des problèmes de service de stockage liés à la disponibilité.

### <a name="<a-name="monitoring-performance"></a>monitoring-performance"></a><a name="monitoring-performance"></a>Analyse des performances
Pour analyser les performances de vos services de stockage, vous pouvez utiliser les métriques suivantes des tables de métriques horaires ou par minute.

* Les valeurs dans **AverageE2ELatency** et **AverageServerLatency** indiquent le temps moyen nécessaire au service de stockage ou à l’opération API pour traiter les demandes. **AverageE2ELatency** est une mesure de la latence de bout en bout, qui inclut le temps nécessaire pour lire la demande et envoyer la réponse, en plus du temps nécessaire pour traiter la demande (elle inclut donc la latence réseau lorsque la demande parvient au service de stockage) ; **AverageServerLatency** est une mesure du seul temps de traitement et n’inclut donc aucune latence réseau associée à la communication avec le client. Voir la section «[Les métriques indiquent une valeur AverageE2ELatency élevée et une valeur AverageServerLatency faible]» plus bas dans ce guide pour examiner les raisons d'une éventuelle différence importante entre ces deux valeurs.
* Les valeurs dans les colonnes **TotalIngress** et **TotalEgress** indiquent le volume total des données (en octets) entrant dans et sortant de votre service de stockage, ou via un type d’opération API spécifique.
* Les valeurs dans la colonne **TotalRequests** indiquent le nombre total de demandes que le service de stockage de l'opération API reçoit. **TotalRequests** est le nombre total de demandes que le service de stockage reçoit.

Toute modification inattendue de ces valeurs est généralement l’indicateur d’un problème qui doit faire l’objet d’une enquête.

Dans le portail Azure, dans la page **Monitor** de votre compte de stockage, vous pouvez ajouter des règles d’alerte pour être averti lorsque des métriques de performances pour ce service dépassent ou chutent en dessous d’un seuil que vous spécifiez.

La section «[Instructions pour la résolution des problèmes]» de ce guide décrit certains des problèmes de service de stockage liés aux performances.

## <a name="<a-name="diagnosing-storage-issues"></a>diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnostic des problèmes de stockage
Il existe différentes façons de savoir si votre application a rencontré un problème, dont :

* Défaillance majeure qui provoque un blocage de l'application ou son arrêt.
* Changements significatifs des valeurs de base dans les métriques que vous analysez, tel qu'indiqué dans la section précédente «[Analyse de votre service de stockage]».
* Rapports des utilisateurs de votre application indiquant qu'une certaine opération ne s'est pas effectuée comme prévu ou qu'une fonctionnalité est défectueuse.
* Erreurs générées au sein de votre application et affichées dans les fichiers journaux ou via d'autres méthodes de notification.

Les problèmes associés aux services de stockage Azure se répartissent généralement en quatre catégories principales :

* Votre application connaît un problème de performances signalé par les utilisateurs ou révélé par des changements dans les métriques de performances.
* Il existe un problème au niveau de l'infrastructure Azure Storage dans une ou plusieurs régions.
* Votre application connaît un problème de performances signalé par les utilisateurs ou révélé par une augmentation dans les métriques de nombre d'erreurs que vous analysez.
* Pendant le développement et les tests, il se peut que vous utilisiez l'émulateur de stockage local et rencontriez des problèmes spécifiques à son utilisation.

Les sections suivantes expliquent les étapes à suivre pour le diagnostic et la résolution des problèmes dans chacune de ces quatre catégories. La section «[Instructions pour la résolution des problèmes]» plus bas dans ce guide aborde plus en détail certains des problèmes que vous pouvez être amené à rencontrer.

### <a name="<a-name="service-health-issues"></a>service-health-issues"></a><a name="service-health-issues"></a>Problèmes d’état d’intégrité du service
Les problèmes d’état du service sont généralement des problèmes sur lesquels vous n’avez pas de contrôle. Le portail Azure Classic fournit des informations sur n’importe quel problème en cours avec les services Azure, y compris les services de stockage. Si vous avez opté pour un stockage géo-redondant avec accès en lecture lors de la création de votre compte de stockage, lorsque vos données ne sont plus accessibles depuis l'emplacement principal, votre application peut passer provisoirement à une copie en lecture seule sur l'emplacement secondaire. Pour ce faire, votre application doit être capable de passer indifféremment des emplacements de stockage principaux aux emplacements de stockage secondaires, et de fonctionner dans un mode de fonctionnalités réduites, avec des données en lecture seule. Les bibliothèques clientes Azure Storage vous permettent de définir une stratégie de nouvelle tentative afin de passer à une lecture depuis le stockage secondaire lorsque la lecture depuis le stockage principal échoue. Votre application doit également être capable de reconnaître que les données de l’emplacement secondaire sont cohérentes. Pour plus d’informations, consultez le billet de blog <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Azure Storage Redundancy Options and Read Access Geo Redundant Storage</a>.

### <a name="<a-name="performance-issues"></a>performance-issues"></a><a name="performance-issues"></a>Problèmes de performances
Les performances d’une application peuvent être subjectives, en particulier du point de vue de l’utilisateur. C'est pourquoi il est important de disposer de métriques de base afin de vous aider à identifier les problèmes de performances éventuels. De nombreux facteurs peuvent affecter les performances d'un service de stockage Azure du point de vue de l'application cliente. Ces facteurs peuvent affecter le service de stockage, l'infrastructure cliente ou l'infrastructure réseau ; il est donc important d'établir une stratégie d'identification de la cause initiale du problème de performances.

Après avoir identifié l'emplacement probable de la cause du problème de performances à partir des métriques, vous pouvez utiliser les fichiers journaux afin de disposer d'informations détaillées pour un diagnostic et une résolution en profondeur du problème.

La section «[Instructions pour la résolution des problèmes]» plus bas dans ce guide aborde plus en détail certains des problèmes de performances que vous pouvez être amené à rencontrer.

### <a name="<a-name="diagnosing-errors"></a>diagnosing-errors"></a><a name="diagnosing-errors"></a>Erreurs de diagnostic
Les utilisateurs de votre application peuvent vous signaler des erreurs identifiées par l'application cliente. Les métriques de stockage enregistrent également les décomptes des différents types d’erreurs de vos services de stockage, tels que **NetworkError**, **ClientTimeoutError** ou **AuthorizationError**. Les métriques de stockage enregistrent uniquement les décomptes des différents types d'erreurs, mais vous pouvez obtenir des informations plus détaillées concernant les demandes individuelles en examinant les journaux côté serveur, côté client et réseau. Le code d'état HTTP renvoyé par le service de stockage peut généralement servir d'indication pour expliquer l'échec de la demande.

> [!NOTE]
> N’oubliez pas que vous devriez voir des erreurs intermittentes : les erreurs dues à des problèmes réseau temporaires ou les erreurs d’application par exemple.
> 
> 

Les ressources suivantes sur MSDN sont utiles pour comprendre les codes d’état et d’erreur liés au stockage :

* <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codes d’erreur API REST courants</a>
* <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Codes d’erreur de service BLOB</a>
* <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Codes d’erreur de service de File d’attente</a>
* <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Codes d’erreur de service de Table</a>

### <a name="<a-name="storage-emulator-issues"></a>storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problèmes liés à l’émulateur de stockage
Le Kit de développement logiciel (SDK) Azure inclut un émulateur de stockage que vous pouvez exécuter sur une station de travail de développement. Cet émulateur simule la plupart des comportements des services de stockage Azure et est utile lors du développement et des tests, vous permettant d’exécuter les applications qui utilisent les services de stockage Azure sans avoir besoin d’un abonnement et d’un compte de stockage Azure.

La section «[Instructions pour la résolution des problèmes]» de ce guide décrit certains des problèmes liés à l'utilisation de l'émulateur de stockage.

### <a name="<a-name="storage-logging-tools"></a>storage-logging-tools"></a><a name="storage-logging-tools"></a>Outils de journalisation du stockage
La journalisation du stockage permet de journaliser côté serveur les demandes de stockage dans votre compte de stockage Azure. Pour plus d’informations concernant l’activation de la journalisation côté serveur et l’accès aux données de journalisation, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">Utilisation de la journalisation côté serveur</a> sur MSDN.

La bibliothèque cliente de stockage pour .NET vous permet de collecter les données de journalisation côté client, liées aux opérations de stockage réalisées par votre application. Pour plus d’informations concernant l’activation de la journalisation côté client et l’accès aux données de journalisation, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">Journalisation côté client avec la bibliothèque cliente de stockage</a> sur MSDN.

> [!NOTE]
> Dans certains cas (par ex., erreurs d’autorisation SAS), il peut arriver qu’un utilisateur signale une erreur pour laquelle vous ne trouvez aucune donnée de demande dans les journaux de stockage côté serveur. Vous pouvez utiliser les fonctionnalités de journalisation de la bibliothèque cliente de stockage pour savoir si la cause du problème se situe au niveau client ou utiliser les outils d'analyse de réseau pour examiner le réseau.
> 
> 

### <a name="<a-name="using-network-logging-tools"></a>using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Utilisation des outils de journalisation réseau
Vous pouvez capturer le trafic entre le client et le serveur afin d'obtenir des informations détaillées concernant les données échangées entre le client et le serveur, et concernant les conditions réseau sous-jacentes. Parmi les outils de journalisation réseau utiles, on retrouve :

* Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) est un proxy de débogage web gratuit qui vous permet d’examiner les en-têtes et données de charge utile des messages de requête et de réponse HTTP et HTTPS. Pour plus d’informations, consultez «[Annexe 1 : utilisation de Fiddler pour capturer le trafic HTTP et HTTPS]».
* Le Moniteur réseau Microsoft (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/download/details.aspx?id=4865</a>) et Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) sont des analyseurs de protocole réseau gratuits qui vous permettent d’afficher des informations détaillées concernant les paquets pour de nombreux protocoles réseau. Pour plus d’informations sur Wireshark, consultez «[Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]».
* L'analyseur de message Microsoft est un outil de Microsoft qui remplace Netmon et qui, en plus de capturer les données des paquets réseau, vous aide à afficher et analyser les données de journalisation capturées à partir d'autres outils. Pour plus d’informations, consultez «[Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]».
* Si vous souhaitez effectuer un test de connectivité de base pour vérifier que votre ordinateur client peut se connecter au service de stockage Azure via le réseau, vous ne pouvez pas le faire en utilisant l'outil **ping** standard sur le client. Mais l'outil **tcping** vous permet de vérifier la connectivité. Vous pouvez télécharger **Tcping** à l’adresse <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a>.

Dans de nombreux cas, les données de journalisation issues de la journalisation du stockage et de la bibliothèque cliente de stockage seront suffisantes pour diagnostiquer un problème, mais dans certains scénarios, il se peut que vous ayez besoin de plus d’informations que celles fournies par ces outils de journalisation réseau. Par exemple, utiliser Fiddler pour afficher les messages HTTP et HTTPS vous permet d'afficher les données d'en-tête et de charge utile envoyées aux et par les services de stockage, ce qui vous permet de vérifier comment une application cliente effectue les nouvelles tentatives d'opérations de stockage. Les analyseurs de protocole tels que Wireshark fonctionnent au niveau des paquets et vous permettent d'afficher les données TCP afin de résoudre les problèmes de perte de paquets et de connectivité. L’analyseur de message est capable de fonctionner au niveau des couches HTTP et TCP.

## <a name="<a-name="end-to-end-tracing"></a>end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Suivi de bout en bout
Le suivi de bout en bout basé sur plusieurs fichiers journaux est une technique utile pour l’identification des problèmes potentiels. Vous pouvez utiliser les informations de date/heure de vos données métriques pour savoir où commencer à chercher des informations plus détaillées dans vos fichiers journaux afin de résoudre le problème.

### <a name="<a-name="correlating-log-data"></a>correlating-log-data"></a><a name="correlating-log-data"></a>Corrélation des données de journalisation
Lors de l'affichage des journaux à partir des applications clientes, des suivis réseau et du stockage côté serveur, leur journalisation est essentielle afin de pouvoir mettre en corrélation les demandes à travers différents fichiers journaux. Les fichiers journaux incluent un certain nombre de champs différents, utiles en tant qu'identificateurs de corrélation. L'ID de la demande client est le champ le plus utile pour mettre en corrélation les entrées dans les différents journaux. Mais il peut parfois être utile d'utiliser l'ID de la demande serveur ou les horodatages. Les sections suivantes expliquent plus en détail ces options.

### <a name="<a-name="client-request-id"></a>client-request-id"></a><a name="client-request-id"></a>ID de la demande client
La bibliothèque cliente de stockage génère automatiquement un ID de demande client unique pour chaque demande.

* Dans le journal côté client créé par la bibliothèque cliente de stockage, l'ID de la demande client s'affiche dans le champ **Client Request ID** de chaque entrée de journal associée à la demande.
* Dans un suivi réseau comme celui capturé par Fiddler, l'ID de la demande client s'affiche dans les messages de demande en tant que valeur d'en-tête HTTP **x-ms-client-request-id** .
* Dans le journal de journalisation du stockage côté serveur, l’ID de la demande client s’affiche dans la colonne Client request ID.

> [!NOTE]
> Plusieurs demandes peuvent partager le même ID de demande client, car le client peut affecter cette valeur (même si la bibliothèque cliente de stockage affecte une nouvelle valeur automatiquement). Quand le client effectue de nouvelles tentatives, ces dernières partagent le même ID de demande client. Quand un lot est envoyé par le client, le lot a un seul ID de demande client.
> 
> 

### <a name="<a-name="server-request-id"></a>server-request-id"></a><a name="server-request-id"></a>ID de la demande serveur
Le service de stockage génère automatiquement les ID de demande serveur.

* Dans le journal de journalisation du stockage côté serveur, l'ID de la demande serveur s'affiche dans la colonne **Request ID header** .
* Dans un suivi réseau comme celui capturé par Fiddler, l'ID de la demande serveur s'affiche dans les messages de réponse en tant que valeur d'en-tête HTTP **x-ms-request-id** .
* Dans le journal côté client créé par la bibliothèque cliente de stockage, l'ID de la demande serveur s'affiche dans la colonne **Operation Text** pour l'entrée de journal qui affiche les détails de la réponse du serveur.

> [!NOTE]
> Le service de stockage affecte toujours un ID de demande serveur unique à chaque demande qu’il reçoit ; par conséquent, chaque nouvelle tentative du client et chaque opération incluse dans un lot a un ID de demande serveur unique.
> 
> 

Si la bibliothèque cliente de stockage génère une **StorageException** dans le client, la propriété **RequestInformation** contient un objet **RequestResult** qui inclut une propriété **ServiceRequestID**. Vous pouvez également accéder à un objet **RequestResult** à partir d’une instance **OperationContext**.

L’exemple de code ci-dessous montre comment définir une valeur **ClientRequestId** personnalisée en associant un objet **OperationContext** à la demande du service de stockage. Il montre également comment récupérer la valeur **ServerRequestId** à partir du message de réponse.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="<a-name="timestamps"></a>timestamps"></a><a name="timestamps"></a>Horodatages
Vous pouvez également utiliser les horodatages pour trouver des entrées de journal associées, mais sans oublier les éventuelles variations d'horloges entre le client et le serveur. Votre recherche des entrées côté serveur correspondantes doit s'appliquer dans une plage de plus ou moins 15minutes par rapport à l'horodatage sur le client. N’oubliez pas que les métadonnées des objets blob contenant des métriques indiquent la plage de temps pour les métriques stockées dans l’objet blob ; ce qui est utile lorsque vous avez plusieurs objets blob de métriques pour la même minute ou heure.

## <a name="<a-name="troubleshooting-guidance"></a>troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Instructions pour la résolution des problèmes
Cette section est destinée à vous aider à diagnostiquer et résoudre certains des problèmes communs que votre application est susceptible de rencontrer lors de l’utilisation des services de stockage Azure. La liste ci-dessous permet d’identifier les informations pertinentes pour un problème spécifique.

**Arbre de décision pour la résolution des problèmes**

- - -
Votre problème concerne-t-il les performances d'un des services de stockage ?

* [Les métriques indiquent une valeur AverageE2ELatency élevée et une valeur AverageServerLatency faible]
* [Les métriques indiquent une valeur AverageE2ELatency faible et une valeur AverageServerLatency faible, mais le client constate une latence élevée]
* [Les métriques indiquent une valeur AverageServerLatency élevée]
* [Vous constatez des retards inattendus dans la livraison des messages d’une file d’attente]

- - -
Votre problème concerne-t-il la disponibilité d’un des services de stockage ?

* [Les métriques indiquent une augmentation de la valeur PercentThrottlingError]
* [Les métriques indiquent une augmentation de la valeur PercentTimeoutError]
* [Les métriques indiquent une augmentation de la valeur PercentNetworkError]

- - -
Votre application client reçoit-elle une réponse HTTP 4XX (telle que 404) d’un service de stockage ?

* [Le client reçoit des messages HTTP 403 (Forbidden)]
* [Le client reçoit des messages HTTP 404 (Not found)]
* [Le client reçoit des messages HTTP 409 (Conflict)]

- - -
[Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d’analyse incluent des opérations avec un statut de transaction ClientOtherErrors]

- - -
[Les métriques de capacité indiquent une augmentation inattendue de l’utilisation de la capacité de stockage]

- - -
[Vous constatez des redémarrages inattendus des machines virtuelles associées à un grand nombre de disques durs virtuels]

- - -
[Votre problème provient de l’utilisation de l’émulateur de stockage pour le développement ou les tests]

- - -
[Vous rencontrez des problèmes pendant l’installation du Kit de développement logiciel (SDK) Azure pour .NET]

- - -
[Vous rencontrez un autre problème avec un service de stockage]

- - -
### <a name="<a-name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a>metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Les métriques indiquent une valeur AverageE2ELatency élevée et une valeur AverageServerLatency faible
L’illustration de l’outil d’analyse du portail donne un exemple où la valeur **AverageE2ELatency** est nettement supérieure à la valeur **AverageServerLatency**.

![][4]

Notez que le service de stockage calcule uniquement la métrique **AverageE2ELatency** pour les demandes réussies et, contrairement à la valeur **AverageServerLatency**, inclut le temps nécessaire au client pour envoyer les données et recevoir l’accusé de réception du service de stockage. Par conséquent, une différence entre les valeurs **AverageE2ELatency** et **AverageServerLatency** peut être due à une réponse lente de l’application client ou aux conditions sur le réseau.

> [!NOTE]
> Vous pouvez également afficher les valeurs **E2ELatency** et **ServerLatency** pour des opérations de stockage individuelles dans les données de journalisation du stockage.
> 
> 

#### <a name="investigating-client-performance-issues"></a>Enquête sur les problèmes de performances client
Les raisons possibles à une réponse lente du client incluent un nombre limité de connexions ou threads disponibles. Il se peut que le problème puisse être résolu en modifiant le code client afin de le rendre plus efficace (par exemple, en utilisant des appels asynchrones vers le service de stockage), ou en utilisant une machine virtuelle plus puissante (avec davantage de cœurs et de mémoire).

Pour les services de Table et de File d’attente, l’algorithme Nagle peut également provoquer de hautes valeurs **AverageE2ELatency** par rapport à **AverageServerLatency** : pour plus d’informations, consultez le billet <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">Nagle’s Algorithm is Not Friendly towards Small Requests</a> (L’algorithme Nagle n’est pas convivial pour les petites demandes) sur le blog de l’équipe de Microsoft Azure Storage. Vous pouvez désactiver l’algorithme Nagle dans le code en utilisant la classe **ServicePointManager** dans l’espace de noms **System.Net**. Cette opération doit être effectuée avant de réaliser des appels vers les services de table et de file d’attente dans votre application, car elle n’affecte pas les connexions déjà ouvertes. L’exemple suivant provient de la méthode **Application_Start** dans un rôle de travail.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Vous devez consulter les journaux côté client pour savoir combien de demandes votre application cliente soumet, et vérifier la présence dans votre client de goulots d’étranglement au niveau des performances générales de .NET, telles que l’UC, .NET garbage collection, l’utilisation du réseau ou la mémoire (la première étape pour la résolution des problèmes des applications clientes .NET consiste à consulter la section <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">Débogage, suivi et profilage</a> sur MSDN).

#### <a name="investigating-network-latency-issues"></a>Enquête sur les problèmes de latence du réseau
Une latence de bout en bout élevée, causée par le réseau, est généralement associée à des conditions provisoires. Vous pouvez enquêter sur les problèmes de réseau provisoires et permanents (par ex., les paquets perdus) à l'aide d'outils tels que Wireshark ou l'analyseur de message Microsoft.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, consultez «[Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]».

Pour plus d’informations sur l’utilisation de l’analyseur de message Microsoft pour résoudre les problèmes de réseau, consultez la section «[Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]».

### <a name="<a-name="metrics-show-low-averagee2elatency-and-low-averageserverlatency"></a>metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Les métriques indiquent une valeur AverageE2ELatency faible et une valeur AverageServerLatency faible, mais le client constate une latence élevée
Dans ce scénario, la cause la plus probable est un retard des demandes de stockage à atteindre le service de stockage. Vous devez enquêter sur la raison pour laquelle les demandes envoyées par le client ne parviennent pas au service d'objet blob.

Les raisons possibles à un retard de l'envoi des demandes par le client incluent un nombre limité de connexions ou threads disponibles. Vous devez également vérifier si le client effectue plusieurs nouvelles tentatives et, si c'est le cas, enquêter sur la raison. Vous pouvez réaliser cette opération par programme en consultant l’objet **OperationContext** associé à la demande et en récupérant la valeur **ServerRequestId**. Pour plus d'informations, voir l'exemple de code de la section «[ID de la demande serveur]».

En l’absence de problèmes au niveau du client, vous pouvez enquêter sur la présence de problèmes potentiels au niveau du réseau, tels que la perte de paquets. Vous pouvez utiliser des outils tels que Wireshark ou l’analyseur de message Microsoft pour enquêter sur les problèmes de réseau.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, consultez «[Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]».

Pour plus d’informations sur l’utilisation de l’analyseur de message Microsoft pour résoudre les problèmes de réseau, consultez la section «[Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]».

### <a name="<a-name="metrics-show-high-averageserverlatency"></a>metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Les métriques indiquent une valeur AverageServerLatency élevée
En présence d'une valeur **AverageServerLatency** élevée pour les demandes de téléchargement d'objet blob, vous devez utiliser les journaux de journalisation du stockage pour savoir si des demandes répétées ont été envoyées pour le même objet blob (ou ensemble d'objets blob). Pour les demandes de chargement d’objet blob, vous devez enquêter sur la taille de blocs utilisés par le client (par exemple, des blocs de taille inférieure à 64 K peuvent entraîner des charges plus lourdes, sauf si les lectures se font également par blocs de taille inférieure à 64 K) et savoir si plusieurs clients chargent des blocs sur le même objet blob en parallèle. Vous devez également vérifier dans les métriques par minute la présence de pics au niveau du nombre de demandes, provoquant un dépassement des valeurs cibles d’extensibilité par seconde : consultez également la section «[Les métriques indiquent une augmentation de la valeur PercentTimeoutError]».

Si vous constatez une valeur **AverageServerLatency** élevée pour les demandes de téléchargement d'objet blob lorsque des demandes répétées sont envoyées pour le même objet blob ou ensemble d'objets blob, vous devez envisager la mise en cache de ces objets blob à l'aide du cache Azure ou du réseau de distribution de contenu Azure. Pour les demandes de chargement, vous devez améliorer le débit en augmentant la taille des blocs. Pour les requêtes vers les tables, il est également possible d'implémenter une mise en cache côté client sur les clients qui effectuent les mêmes opérations de requête et lorsque les données ne changent pas fréquemment.

Des valeurs **AverageServerLatency** élevées peuvent également indiquer la présence de tables mal conçues ou de requêtes donnant lieu à des opérations d'analyse ou qui suivent l'anti-modèle d'ajout/ajout de préfixe. Voir la section «[Les métriques indiquent une augmentation de la valeur PercentThrottlingError]» pour plus d'informations.

> [!NOTE]
> Pour obtenir une liste de contrôle exhaustive des performances, consultez [Liste de contrôle des performances et de l’extensibilité de Microsoft Azure Storage](storage-performance-checklist.md).
> 
> 

### <a name="<a-name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Vous constatez des retards inattendus dans la livraison des messages d’une file d’attente
Si vous constatez un retard entre le moment où une application ajoute un message à une file d'attente et le moment où ce dernier peut être lu à partir de la file d'attente, procédez comme suit pour diagnostiquer le problème :

* Vérifiez que l'application ajoute avec succès les messages à la file d'attente. Vérifiez que l'application n'effectue pas plusieurs tentatives de la méthode **AddMessage** avant d'effectuer l'opération avec succès. Les journaux de la bibliothèque cliente de stockage affichent toutes les tentatives répétées d'opérations de stockage.
* Vérifiez l'absence de variations d'horloges entre le rôle de travail qui ajoute le message à la file d'attente et le rôle de travail qui lit le message à partir de la file d'attente, donnant l'impression d'un retard de traitement.
* Vérifiez si le rôle de travail qui lit les messages à partir de la file d'attente échoue. Si un client de file d’attente appelle la méthode **GetMessage**, mais ne parvient pas à répondre avec un accusé de réception, le message demeurera invisible dans la file d’attente jusqu’à ce que la période **invisibilityTimeout** ait expiré. Ce n'est qu'à ce moment que le message pourra à nouveau être traité.
* Vérifiez si la longueur de la file d'attente augmente avec le temps. Cela peut arriver si vous ne disposez pas d'assez de travailleurs pour traiter les messages que les autres travailleurs placent dans la file d'attente. Vous devez également consulter les métriques indiquant si les demandes de suppression échouent et le décompte de résorption de file d’attente des messages, pouvant indiquer l'échec de tentatives répétées de supprimer le message.
* Vérifiez dans les journaux de journalisation du stockage la présence d’opérations de file d’attente présentant des valeurs **E2ELatency** et **ServerLatency** supérieures à celles prévues, pendant une période plus longue que prévu.

### <a name="<a-name="metrics-show-an-increase-in-percentthrottlingerror"></a>metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Les métriques indiquent une augmentation de la valeur PercentThrottlingError
Les erreurs de limitation se produisent lorsque vous dépassez les valeurs cibles d’évolutivité d’un service de stockage. Le service de stockage effectue cette opération afin de s’assurer qu’aucun client ne peut utiliser le service au détriment des autres utilisateurs. Pour plus d’informations sur les cibles d’évolutivité des comptes de stockage et les cibles de performances des partitions dans les comptes de stockage, voir <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Objectifs de performance et évolutivité d'Azure Storage</a>.

Si la métrique **PercentThrottlingError** indique une augmentation du pourcentage de demandes qui échouent avec une erreur de limitation, vous devez enquêter sur un des deux scénarios suivants :

* [Augmentation provisoire de la valeur PercentThrottlingError]
* [Augmentation permanente de l’erreur PercentThrottlingError]

Une augmentation de la valeur **PercentThrottlingError** se produit souvent en même temps qu'une augmentation du nombre de demandes de stockage, ou lors des tests initiaux de la charge de votre application. Elle peut également se manifester dans le client sous forme de messages d’état HTTP « 503 Server Busy » ou « 500 Operation Timeout » à partir des opérations de stockage.

#### <a name="<a-name="transient-increase-in-percentthrottlingerror"></a>transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Augmentation provisoire de la valeur PercentThrottlingError
Si vous constatez des pics de la valeur **PercentThrottlingError** qui coïncident avec des périodes de forte activité de l’application, implémentez une stratégie d’interruption des nouvelles tentatives exponentielle (non linéaire) dans votre client. Cela réduira la charge immédiate sur la partition et aidera votre application à aplanir les pics de trafic. Pour plus d’informations sur la façon d’implémenter des stratégies de nouvelle tentative à l’aide de la bibliothèque cliente de stockage, voir <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Espace de noms Microsoft.WindowsAzure.Storage.RetryPolicies</a> sur MSDN.

> [!NOTE]
> Vous pouvez également constater des pics de la valeur **PercentThrottlingError** qui ne coïncident pas avec des périodes de forte activité de l’application. La cause la plus probable est le déplacement de partitions, par le service de stockage, pour améliorer l’équilibrage de la charge.
> 
> 

#### <a name="<a-name="permanent-increase-in-percentthrottlingerror"></a>permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Augmentation permanente de l’erreur PercentThrottlingError
Si vous constatez une valeur constamment élevée pour **PercentThrottlingError** à la suite d'une augmentation permanente de vos volumes de transaction, ou lorsque vous effectuez vos tests de charge initiaux sur votre application, vous devez évaluer comment votre application utilise les partitions de stockage et si elle approche des cibles d'évolutivité pour un compte de stockage. Par exemple, si vous constatez des erreurs de limitation dans une file d'attente (considérée comme une partition unique), vous devez envisager l'utilisation de files d'attente supplémentaires afin de distribuer les transactions à travers plusieurs partitions. Si vous constatez des erreurs de limitation sur une table, vous devez envisager l’utilisation d’un schéma de partitionnement différent afin de distribuer vos transactions à travers plusieurs partitions en utilisant une plage de valeurs de clé de partition plus large. Une cause fréquente à ce problème est l’anti-modèle d’ajout/ajout de préfixe où vous sélectionnez la date en tant que clé de partition, pour ensuite écrire toutes les données d’un jour spécifique sur une partition : en cas de charge, cela peut entraîner un goulot d’étranglement d’écriture. Vous devez envisager une conception de partitionnement différente ou évaluer s'il ne vaudrait pas mieux utiliser un stockage d'objets blob. Vous devez également vérifier si la limitation se produit suite à des pics de votre trafic et rechercher des moyens d'assouplir votre modèle de demandes.

Si vous distribuez vos transactions à travers plusieurs partitions, vous devez tenir compte des limites d'évolutivité définies pour le compte de stockage. Par exemple, si vous utilisez dix files d'attente, chacune avec une capacité de traitement maximum de 2000 messages de 1Ko par seconde, votre limite globale sera de 20 000 messages par seconde pour le compte de stockage. Si vous devez traiter plus de 20 000 entités par seconde, vous devez envisager l’utilisation de plusieurs comptes de stockage. Vous devez également garder à l’esprit que la taille de vos demandes et entités a un impact sur le moment où le service de stockage limite vos clients : si vous recevez des demandes et entités plus larges, vous pourrez être limité plus tôt.

Une requête mal conçue peut également vous amener à atteindre les limites d'évolutivité pour les partitions de table. Par exemple, une requête avec un filtre qui ne sélectionne qu'un pour cent des entités dans une partition, mais recherche toutes les entités dans une partition devra accéder à chaque entité. Chaque lecture d’entité sera ajoutée au décompte total de transactions dans cette partition, vous amenant à atteindre rapidement les cibles d’évolutivité.

> [!NOTE]
> Vos tests de performances doivent mettre à jour toutes les requêtes mal conçues dans votre application.
> 
> 

### <a name="<a-name="metrics-show-an-increase-in-percenttimeouterror"></a>metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Les métriques indiquent une augmentation de la valeur PercentTimeoutError
Vos métriques indiquent une augmentation de la valeur **PercentTimeoutError** pour un de vos services de stockage. En même temps, le client reçoit un grand nombre de messages d'état HTTP « 500 Operation Timeout » à partir des opérations de stockage.

> [!NOTE]
> Il se peut qu’apparaissent des erreurs de délai d’expiration provisoires lorsque le service de stockage équilibre les demandes en déplaçant une partition vers un nouveau serveur.
> 
> 

La métrique **PercentTimeoutError** est un agrégat des métriques suivantes : **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** et **SASServerTimeoutError**.

Les délais d'expiration du serveur sont provoqués par une erreur sur le serveur. Les délais d’expiration clients se produisent lorsqu’une opération sur le serveur a dépassé le délai d’expiration spécifié par le client ; par exemple, un client qui utilise la bibliothèque cliente de stockage peut définir un délai d’expiration pour une opération en utilisant la propriété **ServerTimeout** de la classe **QueueRequestOptions**.

Les délais d'expiration du serveur indiquent un problème au niveau du service de stockage, qui exige une enquête plus approfondie. Vous pouvez utiliser les métriques pour savoir si vous atteignez les limites d'évolutivité pour le service et identifier les pics de trafic susceptibles d'être la cause de ce problème. Si le problème est intermittent, il peut être dû à une activité d'équilibrage de charge dans le service. Si le problème persiste et n'est pas provoqué par le fait que votre application a atteint les limites d'évolutivité du service, vous devez signaler le problème au support. Pour les délais d’expiration clients, vous devez décider si le délai d’expiration est défini sur une valeur appropriée dans le client et soit modifier la valeur de délai d’expiration dans le client, soit rechercher un moyen d’améliorer les performances des opérations dans le service de stockage, par exemple, en optimisant vos requêtes de table ou en réduisant la taille de vos messages.

### <a name="<a-name="metrics-show-an-increase-in-percentnetworkerror"></a>metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Les métriques indiquent une augmentation de la valeur PercentNetworkError
Vos métriques indiquent une augmentation de la valeur **PercentNetworkError** pour un de vos services de stockage. La métrique **PercentNetworkError** est un agrégat des métriques suivantes : **NetworkError**, **AnonymousNetworkError** et **SASNetworkError**. Cela se produit lorsque le service de stockage détecte une erreur de réseau associée à une demande de stockage du client.

La cause la plus fréquente de cette erreur est une déconnexion du client avant l'expiration d'un délai dans le service de stockage. Vous devez contrôler le code dans votre client afin de comprendre pourquoi et quand le client s'est déconnecté du service de stockage. Vous pouvez également utiliser Wireshark, l'analyseur de message Microsoft ou Tcping pour enquêter sur les problèmes de connectivité réseau à partir du client. Ces outils sont décrits dans la section [Annexes].

### <a name="<a-name="the-client-is-receiving-403-messages"></a>the-client-is-receiving-http-403-(forbidden)-messages"></a><a name="the-client-is-receiving-403-messages"></a>Le client reçoit des messages HTTP 403 (Forbidden)
Si votre application client génère des erreurs HTTP403 (Forbidden), l'une des causes probables est l'utilisation par le client d'une signature d'accès partagé (SAS) arrivée à expiration lors de l'envoi d'une demande de stockage (d'autres causes possibles incluent les variations d'horloges, les clés non valides et les en-têtes vides). Si une clé SAS arrivée à expiration est la cause, aucune entrée ne s'affiche dans les données de journalisation du stockage côté serveur. Le tableau suivant inclut un exemple de journal côté client généré par la bibliothèque cliente de stockage, qui illustre ce type de problème :

| Source | Commentaires | Commentaires | ID de la demande client | Operation Text |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |Démarrage de l'opération avec l'emplacement Primary par mode d'emplacement PrimaryOnly. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |Démarrage d’une demande synchrone vers https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr=c&amp;si=mypolicy&amp;sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |Attente de la réponse. |
| Microsoft.WindowsAzure.Storage |Avertissement |2 |85d077ab-… |Exception levée pendant l’attente de la réponse : Le serveur distant a renvoyé une erreur : (403) Forbidden. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |Réponse reçue. Code d'état = 403, ID de la demande = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.WindowsAzure.Storage |Avertissement |2 |85d077ab-… |Exception levée pendant l’opération : Le serveur distant a renvoyé une erreur : (403) Forbidden. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |Vérification si l’opération doit être tentée à nouveau. Nombre de nouvelles tentatives = 0, Code d’état HTTP = 403, Exception = Le serveur distant a renvoyé une erreur : (403) Forbidden. |
| Microsoft.WindowsAzure.Storage |Information |3 |85d077ab-… |L'emplacement suivant a été défini sur Primary, sur base du mode d'emplacement. |
| Microsoft.WindowsAzure.Storage |Error |1 |85d077ab-… |La stratégie de nouvelle tentative n’a pas autorisé de nouvelle tentative. Échec avec Le serveur distant a renvoyé une erreur : (403) Forbidden. |

Dans ce scénario, vous devez rechercher pourquoi le jeton SAS expire avant que le client n'envoie le jeton au serveur :

* Généralement, vous ne devez pas définir d'heure de début lorsque vous créez une SAS à utiliser immédiatement par un client. S'il existe de faibles variations d'horloges entre l'hôte qui génère la SAS sur base de l'heure actuelle et le service de stockage, il est possible que le service de stockage reçoive une SAS qui n'est pas encore valide.
* Vous ne devez pas définir une durée d'expiration très courte pour une SAS. À nouveau, de petites variations d'horloges entre l'hôte qui génère la SAS et le service de stockage peuvent donner l'impression que la SAS a expiré plus tôt que prévu.
* Le paramètre de version dans la clé SAS (par exemple **sv=2012-02-12**) correspond-il à la version de la bibliothèque cliente de stockage que vous utilisez ? Vous devez toujours utiliser la dernière version de la bibliothèque cliente de stockage. Pour plus d’informations sur la gestion des versions du jeton SAS, consultez [Nouveautés de Microsoft Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx).
* * Si vous régénérez vos clés d’accès de stockage (cliquez sur **Gérer les clés d’accès** dans n’importe quelle page de votre compte de stockage sur le portail Azure Classic), cela peut invalider tous les jetons SAS existants. Un problème peut survenir si vous générez des jetons SAS avec une durée d'expiration longue pour les applications clientes dans le cache.

Si vous utilisez la bibliothèque cliente de stockage pour générer des jetons SAS, il est facile de créer un jeton valide. Toutefois, si vous utilisez l’API REST Stockage et que vous créez des jetons SAS manuellement, vous devez lire attentivement la rubrique <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Délégation de l’accès avec une signature d’accès partagé</a> sur MSDN.

### <a name="<a-name="the-client-is-receiving-404-messages"></a>the-client-is-receiving-http-404-(not-found)-messages"></a><a name="the-client-is-receiving-404-messages"></a>Le client reçoit des messages HTTP 404 (Non trouvé)
Si l’application client reçoit un message HTTP 404 (Non trouvé) du serveur, cela signifie que l’objet que le client tentait d’utiliser (tel qu’une entité, une table, un objet blob, un conteneur ou une file d’attente) n’existe pas dans le service de stockage. Il existe un certain nombre de raisons possibles à ce problème, dont :

* [Le client ou un autre processus a supprimé l’objet]
* [Problème d’autorisation de signature d’accès partagé (SAS)]
* [Le code JavaScript du côté client n’est pas autorisé à accéder à l’objet]
* [Défaillance réseau]

#### <a name="<a-name="client-previously-deleted-the-object"></a>the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Le client ou un autre processus a supprimé l’objet
Dans les scénarios où le client tente de lire, mettre à jour ou supprimer des données dans un service de stockage, il est habituellement facile d'identifier dans les journaux côté serveur une précédente opération qui a supprimé l'objet en question du service de stockage. Très souvent, les données de journalisation indiquent qu'un autre utilisateur ou processus a supprimé l'objet. Dans le journal de journalisation du stockage côté serveur, les colonnes operation-type et requested-object-key s'affichent lorsqu'un client a supprimé un objet.

Dans le scénario où un client tente d'insérer un objet, la raison pour laquelle cette opération génère une réponse HTTP404 (Not found) peut ne pas être immédiatement identifiable, car le client est en train de créer un objet. Toutefois, si le client crée un objet blob, il doit être possible de trouver son conteneur ; si le client crée le message, il doit être possible de trouver sa file d'attente, et si le client ajoute une ligne, il doit être possible de trouver sa table.

Vous pouvez utiliser le journal côté client de la bibliothèque cliente de stockage pour obtenir des informations plus détaillées concernant l'instant où le client envoie des demandes spécifiques au service de stockage.

Le journal côté client suivant, généré par la bibliothèque cliente de stockage, illustre le problème d'un client incapable de trouver le conteneur de l'objet blob qu'il est en train de créer. Ce journal inclut les détails des opérations de stockage suivantes :

| ID de la demande | Opération |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** pour supprimer le conteneur de l'objet blob. Notez que cette opération inclut une demande **HEAD** pour vérifier l’existence du conteneur. |
| e2d06d78… |**CreateIfNotExists** pour créer le conteneur de l'objet blob. Notez que cette opération inclut une demande **HEAD** qui vérifie l’existence du conteneur. La demande **HEAD** renvoie un message 404, mais continue. |
| de8b1c3c-... |**UploadFromStream** pour créer l'objet blob. La demande **PUT** échoue avec un message 404. |

Entrées du journal :

| ID de la demande | Operation Text |
| --- | --- |
| 07b26a5d-... |Démarrage d'une demande synchrone vers https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Attente de la réponse. |
| 07b26a5d-... |Réponse reçue. Code d’état = 200, ID de la demande = eeead849-...Content-MD5 = , ETag =    &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Les en-têtes de réponse ont été traités avec succès ; passage à la suite de l'opération. |
| 07b26a5d-... |Téléchargement du corps de la réponse. |
| 07b26a5d-... |Opération exécutée avec succès. |
| 07b26a5d-... |Démarrage d'une demande synchrone vers https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |Attente de la réponse. |
| 07b26a5d-... |Réponse reçue. Code d'état = 202, ID de la demande = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Les en-têtes de réponse ont été traités avec succès ; passage à la suite de l'opération. |
| 07b26a5d-... |Téléchargement du corps de la réponse. |
| 07b26a5d-... |Opération exécutée avec succès. |
| e2d06d78-... |Démarrage d'une demande synchrone vers https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Attente de la réponse. |
| de8b1c3c-... |Démarrage d'une demande synchrone vers https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Préparation de l'écriture des données de la demande. |
| e2d06d78-... |Exception levée pendant l’attente de la réponse : Le serveur distant a renvoyé une erreur : (404) Not Found. |
| e2d06d78-... |Réponse reçue. Code d'état = 404, ID de la demande = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Les en-têtes de réponse ont été traités avec succès ; passage à la suite de l'opération. |
| e2d06d78-... |Téléchargement du corps de la réponse. |
| e2d06d78-... |Opération exécutée avec succès. |
| e2d06d78-... |Démarrage d'une demande synchrone vers https://domemaildist.blob.core.windows.net/azuremmblobcontainer. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |Attente de la réponse. |
| de8b1c3c-... |Écriture des données de la demande. |
| de8b1c3c-... |Attente de la réponse. |
| e2d06d78-... |Exception levée pendant l’attente de la réponse : Le serveur distant a renvoyé une erreur : (409) Conflict. |
| e2d06d78-... |Réponse reçue. Code d’état = 409, ID de la demande = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Erreur de téléchargement du corps de la réponse. |
| de8b1c3c-... |Exception levée pendant l’attente de la réponse : Le serveur distant a renvoyé une erreur : (404) Not Found. |
| de8b1c3c-... |Réponse reçue. Code d’état = 404, ID de la demande = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Exception levée pendant l’opération : Le serveur distant a renvoyé une erreur : (404) Not Found. |
| de8b1c3c-... |La stratégie de nouvelle tentative n’a pas autorisé de nouvelle tentative. Échec avec Le serveur distant a renvoyé une erreur : (404) Not Found. |
| e2d06d78-... |La stratégie de nouvelle tentative n’a pas autorisé de nouvelle tentative. Échec avec Le serveur distant a renvoyé une erreur : (409) Conflict. |

Dans cet exemple, le journal indique que le client entrelace les demandes de la méthode **CreateIfNotExists** (ID de demande e2d06d78…) avec les demandes de la méthode **UploadFromStream** (de8b1c3c-...) ; cela est dû au fait que l’application cliente appelle ces méthodes de façon asynchrone. Vous devez modifier le code asynchrone dans le client de façon à ce qu'il crée le conteneur avant de tenter de charger des données dans un objet blob de ce conteneur. Idéalement, vous devriez créer tous vos conteneurs à l’avance.

#### <a name="<a-name="sas-authorization-issue"></a>a-shared-access-signature-(sas)-authorization-issue"></a><a name="SAS-authorization-issue"></a>Problème d’autorisation de signature d’accès partagé (SAP)
Si l’application cliente tente d’utiliser une clé SAS qui n’inclut pas les autorisations requises pour l’opération, le service de stockage renvoie un message HTTP 404 (Non trouvé) au client. Vous verrez également apparaître une valeur **SASAuthorizationError** non nulle dans les métriques.

Le tableau suivant donne un exemple de message de journal côté serveur à partir du fichier journal de journalisation du stockage :

<table>
  <tr>
    <td>Heure de début de la demande</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Type d'opération</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>État de la demande</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Code d'état HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Type d'authentification</td>
    <td>Sas</td>
  </tr>
  <tr>
    <td>Type de service</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>URL de la demande</td>
    <td>
    https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp;sr=c&amp;amp;si=mypolicy&amp;amp;sig=XXXXX&amp;amp;api-version=2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>Request ID header</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID de la demande client</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Vous devez rechercher pourquoi l’application cliente tente d’effectuer une opération qu’elle n’est pas autorisée à effectuer.

#### <a name="<a-name="javascript-code-does-not-have-permission"></a>client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Le code JavaScript du côté client n’est pas autorisé à accéder à l’objet
Si vous utilisez un client JavaScript et que le service de stockage renvoie des messages HTTP 404, vous devez vérifier la présence des erreurs JavaScript suivantes dans le navigateur :

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [!NOTE]
> Vous pouvez utiliser les Outils de développement F12 dans Internet Explorer pour procéder au suivi des messages échangés entre le navigateur et le service de stockage lors de la résolution des problèmes JavaScript côté client.
> 
> 

Ces erreurs sont dues au fait que le navigateur implémente la restriction de sécurité <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">same-origin policy</a>, qui empêche une page web d’appeler une API dans un domaine différent de celui dont la page provient.

Pour contourner le problème JavaScript, vous devez configurer le service Partage des ressources cross-origin (CORS) pour le service de stockage auquel le client accède. Pour plus d’informations, voir <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">Prise en charge du service Partage des ressources cross-origine (CORS) pour les services Azure Storage</a> sur MSDN.

L'exemple de code suivant montre comment configurer votre service d'objet blob afin de permettre l'exécution de JavaScript dans le domaine Contoso pour accéder à un objet blob dans votre service de stockage d'objets blob :

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="<a-name="network-failure"></a>network-failure"></a><a name="network-failure"></a>Défaillance réseau
Dans certaines circonstances, la perte de paquets réseau peut amener le service de stockage à renvoyer des messages HTTP 404 au client. Par exemple, lorsque l'application cliente supprime une entité du service de table, le client génère une exception de stockage avec un message d'état « HTTP 404 (Not Found) » du service de table. Lorsque vous recherchez la table dans le service de stockage de table, vous constatez que le service a supprimé l'entité comme prévu.

Les détails de l’exception dans le client incluent l’ID de demande (7e84f12d...) affecté par le service de Table pour la demande : vous pouvez utiliser ces informations pour rechercher les détails de la demande dans les journaux de stockage côté serveur en effectuant une recherche dans la colonne **request-id-header** du fichier journal. Vous pouvez également utiliser les métriques pour savoir quand ce type d’erreurs se produit, puis effectuer une recherche dans les fichiers journaux sur base de l’heure à laquelle les métriques ont enregistré cette erreur. L’entrée du journal indique que la suppression a échoué avec un message d’état « HTTP (404) Client Other Error ». La même entrée du journal inclut l’ID de la demande généré par le client dans la colonne **client-request-id** (813ea74f…).

Le journal côté serveur inclut également une autre entrée avec la même valeur **client-request-id** (813ea74f…) pour une opération de suppression réussie de la même entité, et provenant du même client. Cette opération de suppression réussie s'est produite peu avant l'échec de la demande de suppression.

La cause la plus probable de ce scénario est que le client a envoyé une demande de suppression de l'entité au service de table, qui a réussi, mais n'a pas reçu d'accusé de réception du serveur (peut-être à cause d'un problème de réseau provisoire). Le client a ensuite tenté automatiquement d’effectuer à nouveau l’opération (en utilisant le même **client-request-id**). Cette tentative a échoué, car l’entité avait déjà été supprimée.

Si ce problème se produit fréquemment, vous devez rechercher pourquoi le client ne reçoit pas les accusés de réception du service de table. Si le problème est intermittent, vous devez capturer l’erreur « HTTP (404) Not Found » et la journaliser dans le client, mais permettre au client de continuer.

### <a name="<a-name="the-client-is-receiving-409-messages"></a>the-client-is-receiving-http-409-(conflict)-messages"></a><a name="the-client-is-receiving-409-messages"></a>Le client reçoit des messages HTTP 409 (Conflict)
Le tableau suivant inclut un extrait du journal côté serveur pour deux opérations client : **DeleteIfExists** suivie de **CreateIfNotExists** avec le même nom de conteneur d’objet blob. Notez que chaque opération cliente génère l’envoi de deux demandes au serveur : d’abord une demande **GetContainerProperties** afin de vérifier l’existence du conteneur, puis la demande **DeleteContainer** ou **CreateContainer**.

| Timestamp | Opération | Résultat | Nom du conteneur | ID de la demande client |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

Le code de l’application cliente supprime puis recrée immédiatement un conteneur d’objets blob du même nom : la méthode **CreateIfNotExists** (ID de demande client bc881924-...) échoue avec l’erreur HTTP 409 (Conflit). Lorsqu’un client supprime des conteneurs d’objet blob, des tables ou des files d’attente, le nom devient à nouveau disponible après une courte période.

Chaque fois qu'elle crée des conteneurs, l'application cliente utilise des noms de conteneur uniques si le modèle de suppression/recréation est commun.

### <a name="<a-name="metrics-show-low-percent-success"></a>metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d’analyse incluent des opérations avec un statut de transaction ClientOtherErrors
La métrique **PercentSuccess** capture le pourcentage d'opérations réussies sur base de leur code d'état HTTP. Les opérations avec des codes d’état 2XX sont considérées comme réussies ; celles avec des codes d’état dans les plages 3XX, 4XX et 5XX sont considérées comme non réussies et réduisent la valeur de la métrique **PercentSucess** . Dans les fichiers journaux de stockage côté serveur, ces opérations sont enregistrées avec un statut de transaction **ClientOtherErrors**.

Il est important de noter que ces opérations ont été réalisées avec succès et n'affectent donc pas d'autres métriques telles que la disponibilité. Voici quelques exemples d'opérations qui s'exécutent avec succès, mais qui génèrent des codes d'état HTTP d'échec :

* **ResourceNotFound** (Not Found 404), par exemple, à partir d'une demande GET vers un objet blob qui n'existe pas.
* **ResouceAlreadyExists** (Conflict 409), par exemple, à partir d’une opération **CreateIfNotExist** où la ressource existe déjà.
* **ConditionNotMet** (Not Modified 304), par exemple, à partir d’une opération conditionnelle, comme lorsqu’un client envoie une valeur **ETag** et un en-tête HTTP **If-None-Match** pour demander une image uniquement si elle a été mise à jour depuis la dernière opération.

Vous trouverez une liste des codes d’erreur API REST communs renvoyés par les services de stockage à la page <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Codes d’erreur API REST communs</a>.

### <a name="<a-name="capacity-metrics-show-an-unexpected-increase"></a>capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Les métriques de capacité indiquent une augmentation inattendue de l’utilisation de la capacité de stockage
Si vous constatez des changements soudains, inattendus dans l'utilisation de la capacité de votre compte de stockage, vous pouvez enquêter sur les raisons d'abord en consultant vos métriques de disponibilité ; par exemple, une augmentation du nombre de demandes de suppression qui échouent peut provoquer une augmentation du volume de stockage d'objets blob que vous utilisez, car les opérations de nettoyage spécifiques à l'application supposées libérer de l'espace peuvent ne pas fonctionner comme prévu (par exemple, suite à l'expiration des jetons SAS utilisés pour libérer de l'espace).

### <a name="<a-name="you-are-experiencing-unexpected-reboots"></a>you-are-experiencing-unexpected-reboots-of-azure-virtual-machines-that-have-a-large-number-of-attached-vhds"></a><a name="you-are-experiencing-unexpected-reboots"></a>Vous constatez des redémarrages inattendus des machines virtuelles Azure associées à un grand nombre de disques durs virtuels
Si une machine virtuelle Azure est associée à un grand nombre de disques durs virtuels dans le même compte de stockage, vous risquez de dépasser les cibles d’évolutivité pour un compte de stockage individuel, ce qui provoquera une panne de la machine virtuelle. Vous devez consulter les métriques par minute pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) et vérifier la présence de pics dépassant les cibles d’évolutivité pour un compte de stockage. La section «[Les métriques indiquent une augmentation de la valeur PercentThrottlingError]» vous aidera à déterminer si une limitation a été appliquée à votre compte de stockage.

En général, chaque opération d’entrée ou de sortie individuelle sur un disque dur virtuel à partir d’une machine virtuelle traduit les opérations **Get Page** ou **Put Page** sur l’objet blob de la page sous-jacente. Vous pouvez donc utiliser les opérations d’E/S par seconde (IOPS) estimées pour votre environnement pour déterminer le nombre de disques durs virtuels que vous pouvez avoir dans un compte de stockage unique, sur base du comportement spécifique de votre application. Nous déconseillons d’avoir plus de 40 disques dans un compte de stockage unique. Pour obtenir des détails sur les cibles d’extensibilité actuelles des comptes de stockage, en particulier le taux de demandes total et la bande passante totale pour le type de compte de stockage que vous utilisez, consultez <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Objectifs de performance et évolutivité d’Azure Storage</a>.
Si vous dépassez les cibles d’évolutivité pour votre compte de stockage, vous devez placer vos disques durs virtuels dans plusieurs comptes de stockage différents afin de réduire l’activité dans chaque compte individuel.

### <a name="<a-name="your-issue-arises-from-using-the-storage-emulator"></a>your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Votre problème provient de l’utilisation de l’émulateur de stockage pour le développement ou les tests
On utilise généralement un émulateur de stockage lors du développement et des tests afin d'éviter l'utilisation d'un compte de stockage Azure. Voici les problèmes communs que vous êtes susceptible de rencontrer lors de l’utilisation de l’émulateur de stockage :

* [La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]
* [Erreur « The value for one of the HTTP headers is not in the correct format » (Le format de la valeur d’un des en-têtes HTTP est incorrect) lors de l’utilisation de l’émulateur de stockage]
* [L’exécution de l’émulateur de stockage exige des privilèges d’administration]

#### <a name="<a-name="feature-x-is-not-working"></a>feature-"x"-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage
L'émulateur de stockage ne prend pas en charge toutes les fonctionnalités des services de stockage Azure (par ex., le service de fichiers). Pour plus d’informations, consultez <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">Différences entre l’émulateur de stockage et les services Azure Storage</a> sur MSDN.

Pour accéder à ces fonctions non prises en charge par l’émulateur de stockage, vous devez utiliser le service de stockage Azure dans le cloud.

#### <a name="<a-name="error-http-header-not-correct-format"></a>error-"the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Erreur « The value for one of the HTTP headers is not in the correct format » (Le format de la valeur d’un des en-têtes HTTP est incorrect) lors de l’utilisation de l’émulateur de stockage
Vous êtes en train de tester votre application, qui utilise la bibliothèque cliente de stockage, avec l'émulateur de stockage local, et les appels de méthode tels que **CreateIfNotExists** échouent avec un message d'erreur « The value for one of the HTTP headers is not in the correct format » (Le format de la valeur d'un des en-têtes HTTP est incorrect). Cela indique que la version de l'émulateur de stockage que vous utilisez ne prend pas en charge la version de la bibliothèque cliente de stockage que vous utilisez. La bibliothèque cliente de stockage ajoute l'en-tête **x-ms-version** à toutes les demandes qu'elle effectue. Si l'émulateur de stockage ne reconnaît pas la valeur dans l'en-tête **x-ms-version** , il refuse la demande.

Vous pouvez utiliser les journaux de la bibliothèque cliente de stockage pour afficher la valeur de l' **en-tête x-ms-version** envoyée. Vous pouvez également afficher la valeur de l' **en-tête x-ms-version** si vous utilisez Fiddler pour procéder au suivi des demandes à partir de votre application cliente.

Ce scénario se produit généralement lorsque vous installez et utilisez la dernière version de la bibliothèque cliente de stockage sans mettre à jour l’émulateur de stockage. Vous devez soit installer la dernière version de l’émulateur de stockage, soit utiliser le stockage cloud au lieu de l’émulateur pour le développement et les tests.

#### <a name="<a-name="storage-emulator-requires-administrative-privileges"></a>running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>L’exécution de l’émulateur de stockage exige des privilèges d’administration
Vous êtes invité à entrer vos informations d'identification d'administrateur lorsque vous exécutez l'émulateur de stockage. Cela ne se produit que lors de la toute première initialisation de l'émulateur de stockage. Une fois qu'il a été initialisé, vous n'avez plus besoin de privilèges d'administration pour l'exécuter à nouveau.

Pour plus d’informations, consultez <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">Initialisation de l’émulateur de stockage à l’aide de l’outil en ligne de commande</a> sur MSDN (vous pouvez également initialiser l’émulateur de stockage dans Visual Studio, qui nécessite aussi des privilèges d’administration).

### <a name="<a-name="you-are-encountering-problems-installing-the-windows-azure-sdk"></a>you-are-encountering-problems-installing-the-azure-sdk-for-.net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Vous rencontrez des problèmes pendant l’installation du Kit de développement logiciel (SDK) Azure pour .NET
Lorsque vous tentez d'installer le Kit de développement logiciel (SDK), vous ne parvenez pas à installer l'émulateur de stockage sur votre machine locale. Le journal d'installation contient un des messages suivants :

* CAQuietExec:  Error: Unable to access SQL instance (CAQuietExec :  Erreur : Impossible d’accéder à l’instance SQL)
* CAQuietExec:  Error: Unable to create database (CAQuietExec :  Erreur : Impossible de créer la base de données)

Cela est dû à un problème au niveau de l'installation LocalDB existante. Par défaut, l'émulateur de stockage utilise LocalDB pour conserver les données lorsqu'il simule les services de stockage Azure. Vous pouvez réinitialiser votre instance LocalDB en exécutant les commandes suivantes dans une fenêtre d'invite de commandes avant de tenter d'installer le Kit de développement logiciel (SDK).

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

La commande **delete** supprime tous les anciens fichiers de base de données des installations précédentes de l'émulateur de stockage.

### <a name="<a-name="you-have-a-different-issue-with-a-storage-service"></a>you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Vous rencontrez un autre problème avec un service de stockage
Si les sections de résolution des problèmes précédentes n’incluent pas le problème que vous rencontrez avec un service de stockage, vous devez adopter l’approche suivante pour diagnostiquer et résoudre votre problème.

* Consultez vos métriques afin d'identifier tout changement par rapport au comportement de base normal. Sur base des métriques, vous pouvez déterminer si le problème est provisoire ou permanent, ainsi que les opérations de stockage affectées par le problème.
* Vous pouvez utiliser les informations des métriques afin de faciliter vos recherches dans vos données de journalisation côté serveur et obtenir des informations plus détaillées concernant les erreurs rencontrées. Ces informations peuvent vous aider à analyser et résoudre le problème.
* Si les informations des journaux côté serveur ne suffisent pas pour résoudre le problème, vous pouvez utiliser les journaux côté client de la bibliothèque cliente de stockage pour enquêter sur le comportement de votre application cliente, ainsi que des outils tels que Fiddler, Wireshark et l'analyseur de message Microsoft pour enquêter sur votre réseau.

Pour plus d’informations sur l’utilisation de Fiddler, consultez «[Annexe 1 : utilisation de Fiddler pour capturer le trafic HTTP et HTTPS]».

Pour plus d’informations sur l’utilisation de Wireshark, consultez «[Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]».

Pour plus d’informations sur l’utilisation de l’analyseur de message Microsoft, consultez «[Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]».

## <a name="<a-name="appendices"></a>appendices"></a><a name="appendices"></a>Annexes
Les annexes décrivent plusieurs outils qui peuvent s’avérer utiles lors du diagnostic et de la résolution des problèmes liés à Azure Storage (et aux autres services). Ces outils ne font pas partie de Azure Storage, et certains d'entre eux sont des produits tiers. En tant que tels, les outils décrits dans les annexes ne sont couverts par aucun contrat de support technique que vous pourriez avoir établi avec Microsoft Azure ou Azure Storage ; vous devez donc procéder à votre propre évaluation et examiner les options de licence et de support technique disponibles proposées par les fournisseurs de ces outils.

### <a name="<a-name="appendix-1"></a>appendix-1:-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Annexe 1 : utilisation de Fiddler pour capturer le trafic HTTP et HTTPS
Fiddler est un outil utile pour l’analyse du trafic HTTP et HTTPS entre votre application cliente et le service de stockage Azure que vous utilisez. Vous pouvez télécharger Fiddler à l’adresse <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>.

> [!NOTE]
> Fiddler est capable de décoder le trafic HTTPS ; vous devez lire avec attention la documentation de Fiddler pour vous familiariser avec ce processus et comprendre les implications en matière de sécurité.
> 
> 

Cette annexe explique brièvement comment configurer Fiddler pour capturer le trafic entre la machine locale sur laquelle vous avez installé Fiddler et les services de stockage Azure.

Après avoir lancé Fiddler, il commence à capturer le trafic HTTP et HTTPS de votre ordinateur local. Voici quelques commandes utiles pour contrôler Fiddler :

* Arrêt et démarrage de la capture du trafic. Dans le menu principal, accédez à **File**, puis cliquez sur **Capture Traffic** pour activer et désactiver la capture.
* Enregistrement des données de trafic capturées. Dans le menu principal, accédez à **File**, cliquez sur **Save**, puis sur **All Sessions** : cela vous permet d’enregistrer le trafic dans un fichier d’archive de la session. Vous pouvez charger à nouveau ultérieurement un fichier Session Archive à des fins d'analyse, ou l'envoyer, si nécessaire, au support Microsoft.

Pour limiter le volume de trafic capturé par Fiddler, vous pouvez utiliser des filtres que vous configurez dans l'onglet **Filters** . La capture d'écran suivante illustre un filtre qui capture uniquement le trafic envoyé au point de terminaison de stockage **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="<a-name="appendix-2"></a>appendix-2:-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau
Wireshark est un analyseur de protocole réseau qui vous permet d’afficher des informations détaillées concernant les paquets pour de nombreux protocoles réseau. Vous pouvez télécharger Wireshark à l’adresse <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>.

La procédure suivante explique comment capturer des informations détaillées concernant les paquets pour le trafic à partir de la machine locale où vous avez installé Wireshark, vers le service de table de votre compte de stockage Azure.

1. Lancez Wireshark sur votre ordinateur local.
2. Dans la section **Start** , sélectionnez l'interface réseau locale ou des interfaces connectées à Internet.
3. Cliquez sur **Capture Options**.
4. Ajoutez un filtre à la zone de texte **Capture Filter** . Par exemple, **host contosoemaildist.table.core.windows.net** configurera Wireshark pour capturer uniquement les paquets envoyés au ou par le point de terminaison de stockage du service de table dans le compte de stockage **contosoemaildist**. Pour obtenir la liste complète des filtres de capture, accédez au site <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>.
   
   ![][6]
5. Cliquez sur **Start**. Wireshark commence à capturer tous les paquets envoyés au ou par le point de terminaison de stockage du service de table lors de l'utilisation de votre application cliente sur votre ordinateur local.
6. Lorsque vous avez terminé, dans le menu principal, cliquez sur **Capture**, puis sur **Stop**.
7. Pour enregistrer les données capturées dans un fichier de capture Wireshark, dans le menu principal, cliquez sur **File**, puis sur **Save**.

WireShark met en évidence toutes les erreurs détectées dans la fenêtre **packetlist** . Vous pouvez également utiliser la fenêtre **Expert Info** (cliquez sur **Analyze**, puis sur **Expert Info**) pour afficher un récapitulatif des erreurs et avertissements.

![][7]

Vous pouvez également choisir d'afficher les données TCP telles que la couche d'application les voit en cliquant avec le bouton droit de la souris sur les données TCP et en sélectionnant **Follow TCP Stream**. Cette option est particulièrement utile si vous avez capturé votre image mémoire sans filtre de capture. Pour plus d’informations, voir <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">ici</a>.

![][8]

> [!NOTE]
> Pour plus d’informations sur l’utilisation de Wireshark, consultez le <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Guide d’utilisation de Wireshark</a>.
> 
> 

### <a name="<a-name="appendix-3"></a>appendix-3:-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau
Vous pouvez utiliser l’analyseur de message Microsoft pour capturer le trafic HTTP et HTTPS de la même façon qu’avec Fiddler, et le trafic réseau de la même façon qu’avec Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configuration d'une nouvelle session de suivi Web à l'aide de l'analyseur de message Microsoft
Pour configurer une nouvelle session de suivi web pour le trafic HTTP et HTTPS à l’aide de l’analyseur de message Microsoft, exécutez l’application Analyseur de message Microsoft et, dans le menu **File**, cliquez sur **Capture/Trace**. Dans la liste des scénarios de suivi disponibles, sélectionnez **Web Proxy**. Ensuite, dans le panneau **Trace Scenario Configuration**, dans la zone de texte **HostnameFilter**, ajoutez les noms de vos points de terminaison de stockage (ces noms figurent dans le portail Azure Classic). Par exemple, si le nom de votre compte de stockage Azure est **contosodata**, vous devez ajouter ce qui suit dans la zone de texte **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [!NOTE]
> Un caractère espace sépare les noms d’hôte.
> 
> 

Lorsque vous êtes prêt à commencer la collecte des données de suivi, cliquez sur le bouton **Start With** .

Pour plus d’informations concernant le suivi **Web Proxy** de l’analyseur de message Microsoft, consultez la section <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">Fournisseur PEF-WebProxy</a> sur TechNet.

Le suivi **Web Proxy** intégré de l'analyseur de message Microsoft est basé sur Fiddler ; il est capable de capturer le trafic HTTPS côté client et d'afficher les messages HTTPS non chiffrés. Le suivi **Web Proxy** fonctionne via la configuration d'un proxy local pour tout le trafic HTTP et HTTPS, lui permettant d'accéder aux messages non chiffrés.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostic des problèmes de réseau à l'aide de l'analyseur de message Microsoft
En plus d’utiliser le suivi **Web Proxy** de l’analyseur de message Microsoft pour capturer le détail du trafic HTTP/HTTPS entre l’application cliente et le service de stockage, vous pouvez utiliser le suivi **Local Link Layer** intégré pour capturer les informations relatives aux paquets réseau. Cela vous permet de capturer des données similaires à celles que vous capturez avec Wireshark et de diagnostiquer les problèmes de réseau tels que les paquets perdus.

La capture d’écran suivante illustre un exemple de suivi **Local Link Layer** avec des messages **d’information** dans la colonne **DiagnosisTypes**. Cliquer sur une icône dans la colonne **DiagnosisTypes** affiche les détails du message. Dans cet exemple, le serveur a retransmis le message #305, car il n'a reçu aucun accusé de réception du client :

![][9]

Lorsque vous créez la session de suivi dans l'analyseur de message Microsoft, vous pouvez spécifier des filtres afin de limiter le nombre de parasites dans le suivi. Sur la page **Capture / Trace** où vous définissez le suivi, cliquez sur le lien **Configure** à côté de **Microsoft-Windows-NDIS-PacketCapture**. La capture d'écran suivante illustre une configuration qui filtre le trafic TCP pour les adresses IP de trois services de stockage :

![][10]

Pour plus d’informations concernant le suivi Local Link Layer de l’analyseur de message Microsoft, consultez <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">Fournisseur PEF-NDIS-PacketCapture</a> sur TechNet.

### <a name="<a-name="appendix-4"></a>appendix-4:-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Annexe 4 : utilisation d’Excel pour afficher les métriques et les données de journalisation
De nombreux outils vous permettent de télécharger les données métriques de stockage à partir du stockage de table Azure dans un format délimité, permettant leur chargement aisé dans Excel afin de les consulter ou les analyser. Les données de journalisation du stockage d’objets blob Azure sont déjà dans un format délimité qui peut être chargé dans Excel. Toutefois, vous devez ajouter des en-têtes de colonnes appropriés sur la base des informations des sections <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">Format de journal de Storage Analytics</a> et <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schéma de table de métriques Storage Analytics</a>.

Pour importer vos données de journalisation du stockage dans Excel, après les avoir téléchargées à partir stockage d’objets blob :

* Dans le menu **Data**, cliquez sur **From Text**.
* Accédez au fichier journal que vous souhaitez afficher et cliquez sur **Import**.
* À l’étape 1 du **Text Import Wizard**, sélectionnez **Delimited**.

À l’étape 1 du **Text Import Wizard**, sélectionnez **Semicolon** comme unique délimiteur et guillemet double comme **Text qualifier**. Ensuite, cliquez sur **Finish** et sélectionnez l'emplacement des données dans votre classeur.

### <a name="<a-name="appendix-5"></a>appendix-5:-monitoring-with-application-insights-for-visual-studio-team-services"></a><a name="appendix-5"></a>Annexe 5 : analyse avec Application Insights pour Visual Studio Team Services
Vous pouvez également utiliser la fonctionnalité Application Insights pour Visual Studio Team Services dans le cadre de votre analyse de performances et de disponibilité. Cet outil permet de :

* Vous assurer que votre service Web est disponible et réactif. Que votre application soit destinée à un site Web ou un périphérique qui utilise un service Web, l'outil peut tester votre URL toutes les quelques minutes depuis divers emplacements de par le monde et vous signaler tout problème éventuel.
* Rapidement diagnostiquer tous les problèmes ou exceptions de performances rencontrés par votre service Web. Découvrez si l'UC ou d'autres ressources sont en difficulté, obtenez les traces de la pile à partir des exceptions et effectuez des recherches aisées dans les suivis de journalisation. Si les performances de l'application chutent en deçà des limites acceptables, nous pouvons vous envoyer un e-mail. Vous pouvez analyser les services Web .NET et Java.

Au moment de la rédaction du présent document, Application Insights était à l’état de version préliminaire. Pour plus d’informations, consultez <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">Application Insights pour Visual Studio Team Services sur MSDN</a>.

<!--Anchors-->
[Introduction]: #introduction
[Organisation de ce guide]: #how-this-guide-is-organized

[Analyse de votre service de stockage]: #monitoring-your-storage-service
[Analyse de l’état d’intégrité du service]: #monitoring-service-health
[Analyse de la capacité]: #monitoring-capacity
[Analyse de la disponibilité]: #monitoring-availability
[Analyse des performances]: #monitoring-performance

[Diagnostic des problèmes de stockage]: #diagnosing-storage-issues
[Problèmes d’état d’intégrité du service]: #service-health-issues
[Problèmes de performances]: #performance-issues
[Erreurs de diagnostic]: #diagnosing-errors
[Problèmes d’émulateur de stockage]: #storage-emulator-issues
[Outils de journalisation du stockage]: #storage-logging-tools
[Utilisation des outils de journalisation réseau]: #using-network-logging-tools

[Suivi de bout en bout]: #end-to-end-tracing
[Corrélation des données de journalisation]: #correlating-log-data
[ID de la demande client]: #client-request-id
[ID de la demande serveur]: #server-request-id
[Horodatages]: #timestamps

[Instructions pour la résolution des problèmes]: #troubleshooting-guidance
[Les métriques indiquent une valeur AverageE2ELatency élevée et une valeur AverageServerLatency faible]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Les métriques indiquent une valeur AverageE2ELatency faible et une valeur AverageServerLatency faible, mais le client constate une latence élevée]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Les métriques indiquent une valeur AverageServerLatency élevée]: #metrics-show-high-AverageServerLatency
[Vous constatez des retards inattendus dans la livraison des messages d’une file d’attente]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Les métriques indiquent une augmentation de la valeur PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Augmentation provisoire de la valeur PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Augmentation permanente de l’erreur PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Les métriques indiquent une augmentation de la valeur PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Les métriques indiquent une augmentation de la valeur PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Le client reçoit des messages HTTP 403 (Forbidden)]: #the-client-is-receiving-403-messages
[Le client reçoit des messages HTTP 404 (Not found)]: #the-client-is-receiving-404-messages
[Le client ou un autre processus a supprimé l’objet]: #client-previously-deleted-the-object
[Problème d’autorisation de signature d’accès partagé (SAS)]: #SAS-authorization-issue
[Le code JavaScript du côté client n’est pas autorisé à accéder à l’objet]: #JavaScript-code-does-not-have-permission
[Défaillance réseau]: #network-failure
[Le client reçoit des messages HTTP 409 (Conflict)]: #the-client-is-receiving-409-messages

[Les métriques indiquent une valeur PercentSuccess faible ou les entrées du journal d’analyse incluent des opérations avec un statut de transaction ClientOtherErrors]: #metrics-show-low-percent-success
[Les métriques de capacité indiquent une augmentation inattendue de l’utilisation de la capacité de stockage]: #capacity-metrics-show-an-unexpected-increase
[Vous constatez des redémarrages inattendus des machines virtuelles associées à un grand nombre de disques durs virtuels]: #you-are-experiencing-unexpected-reboots
[Votre problème provient de l’utilisation de l’émulateur de stockage pour le développement ou les tests]: #your-issue-arises-from-using-the-storage-emulator
[La fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]: #feature-X-is-not-working
[Erreur « The value for one of the HTTP headers is not in the correct format » (Le format de la valeur d’un des en-têtes HTTP est incorrect) lors de l’utilisation de l’émulateur de stockage]: #error-HTTP-header-not-correct-format
[L’exécution de l’émulateur de stockage exige des privilèges d’administration]: #storage-emulator-requires-administrative-privileges
[Vous rencontrez des problèmes pendant l’installation du Kit de développement logiciel (SDK) Azure pour .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Vous rencontrez un autre problème avec un service de stockage]: #you-have-a-different-issue-with-a-storage-service

[Annexes]: #appendices
[Annexe 1 : utilisation de Fiddler pour capturer le trafic HTTP et HTTPS]: #appendix-1
[Annexe 2 : utilisation de Wireshark pour capturer le trafic réseau]: #appendix-2
[Annexe 3 : utilisation de l’analyseur de message Microsoft pour capturer le trafic réseau]: #appendix-3
[Annexe 4 : utilisation d’Excel pour afficher les métriques et les données de journalisation]: #appendix-4
[Annexe 5 : analyse avec Application Insights pour Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting-classic-portal/mma-screenshot-2.png



<!--HONumber=Oct16_HO2-->


