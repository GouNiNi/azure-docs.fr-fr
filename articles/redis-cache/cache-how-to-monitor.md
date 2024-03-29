---
title: Surveillance du Cache Redis Azure | Microsoft Docs
description: Découvrez comment surveiller l’état et les performances de vos instances de cache Redis Azure
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: ''

ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: sdanie

---
# Surveillance du cache Redis Azure
Le cache Redis Azure offre plusieurs possibilités de surveillance de vos instances de cache. Vous pouvez afficher les mesures, épingler des graphiques de mesure au Tableau d’accueil, personnaliser la plage de date et d’heure des graphiques de surveillance, ajouter et supprimer des mesures dans les graphiques et définir des alertes lorsque certaines conditions sont remplies. Ces outils vous permettent de surveiller l’intégrité de vos instances Cache Redis Azure et vous aident à gérer vos applications de mise en cache.

Lorsque les diagnostics du cache sont activés, les mesures des instances de cache Redis Azure sont collectées toutes les 30 secondes environ et stockées afin de pouvoir être affichées dans les graphiques de mesures et évaluées par les règles d’alerte.

Les mesures de cache sont collectées à l’aide de la commande Redis [INFO](http://redis.io/commands/info). Pour plus d’informations sur les différentes valeurs INFO utilisées pour chaque mesure de cache, consultez la section [Mesures disponibles et intervalles de création des rapports](#available-metrics-and-reporting-intervals).

Pour voir les mesures de cache, [accédez](cache-configure.md#configure-redis-cache-settings) à votre instance de cache dans le [portail Azure](https://portal.azure.com). Les mesures des instances de cache Redis Azure sont accessibles dans le panneau **Mesures Redis**.

![Mesures Redis][redis-cache-redis-metrics-blade]

> [!IMPORTANT]
> Si le message suivant s’affiche dans le panneau **Mesures Redis**, suivez les étapes de la section [Activer les diagnostics du cache](#enable-cache-diagnostics) pour activer les diagnostics du cache.
> 
> `Monitoring may not be enabled. Click here to turn on Diagnostics.`
> 
> 

Le panneau **Mesures Redis** comporte des graphiques de **surveillance** qui affichent les mesures du cache. Chaque graphique peut être personnalisé en ajoutant ou en supprimant des mesures et en modifiant l’intervalle de création des rapports. Pour afficher et configurer les opérations et les alertes, le panneau **Cache Redis** comporte une section **Opérations** qui affiche les **événements** et les **règles d’alerte** du cache.

## Activer les diagnostics du cache
Cache Redis Azure vous offre la possibilité de stocker les données de diagnostic dans un compte de stockage. Vous pouvez ainsi utiliser les outils que vous voulez pour accéder directement à ces données et les traiter. Pour que données du diagnostic de cache soient collectées, stockées et affichées dans le portail Azure, un compte de stockage doit être configuré. Les caches de la même région et du même abonnement partagent le même compte de stockage de diagnostics, et lorsque la configuration est modifiée, elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région.

Pour activer et configurer les diagnostics du cache, accédez au panneau **Cache Redis** de votre instance de cache. Si le diagnostic n’est pas encore activé, un message s’affiche à la place du graphique de diagnostic.

![Activer les diagnostics du cache][redis-cache-enable-diagnostics]

Cliquez sur le message pour afficher le panneau **Mesure**, puis cliquez sur **Paramètres de diagnostic** pour activer et configurer les paramètres de diagnostic pour l’instance de service de cache.

![Paramètres de diagnostic][redis-cache-diagnostic-settings]

![Configuration de la collecte des diagnostics][redis-cache-configure-diagnostics]

Cliquez sur le bouton **Activer** pour activer les diagnostics du cache et afficher la configuration des diagnostics.

Cliquez sur la flèche à droite de **Compte de stockage** pour sélectionner le compte de stockage qui doit contenir les données de diagnostic. Pour de meilleures performances, sélectionnez un compte de stockage qui se trouve dans la même région que votre cache.

Une fois les paramètres de diagnostic configurés, cliquez sur **Enregistrer** pour enregistrer la configuration. Il peut s’écouler quelques instants avant que les modifications prennent effet.

> [!IMPORTANT]
> Les caches de la même région et du même abonnement partagent les mêmes paramètres de stockage de diagnostics, et lorsque la configuration est modifiée (diagnostics activés/désactivés ou changement du compte de stockage), elle s’applique à tous les caches de l’abonnement qui se trouvent dans cette région.
> 
> 

Pour voir les mesures stockées, examinez les tables de votre compte de stockage dont le nom commence par `WADMetrics`. Pour plus d’informations sur l’accès aux mesures stockées en dehors du portail Azure, consultez l’exemple [Access Redis cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).

> [!NOTE]
> Seules les mesures stockées dans le compte de stockage sélectionné sont affichées dans le portail Azure. Si vous changez de compte de stockage, les données du compte de stockage configuré précédemment restent disponibles en téléchargement, mais elles ne sont pas affichées dans le portail Azure.
> 
> 

## Mesures disponibles et intervalles de création des rapports
Les mesures de cache font l’objet de rapports à différents intervalles, **Dernière heure**, **Aujourd’hui**, **Semaine dernière** et **Personnalisé**. Le panneau **Mesure** de chaque mesure affiche les valeurs moyennes, minimales et maximales de chaque mesure du graphique et certaines mesures affichent le total pour l’intervalle de création des rapports.

Chaque mesure inclut deux versions. La première version mesure les performances de la totalité du cache, tandis que pour les caches qui utilisent le [clustering](cache-how-to-premium-clustering.md), une seconde version de la mesure comportant `(Shard 0-9)` dans son nom évalue les performances d’une seule partition d’un cache. Par exemple, si un cache comporte 4 partitions, `Cache Hits` est la quantité totale d’accès pour le cache entier, tandis que `Cache Hits (Shard 3)` est simplement le nombre d’accès à cette partition du cache.

> [!NOTE]
> Même lorsque le cache est inactif sans applications clientes actives connectées, vous pouvez constater une certaine activité du cache, par exemple les opérations en cours d’exécution, l’utilisation de la mémoire et les clients connectés. Cette activité est normale dans le cadre du fonctionnement d’une instance de cache Redis Azure.
> 
> 

| Mesure | Description |
| --- | --- |
| Présences dans le cache |Nombre de recherches clés réussies au cours de l’intervalle de création des rapports. Cette valeur correspond à la commande Redis [INFO](http://redis.io/commands/info) `keyspace_hits`. |
| Absences dans le cache |Nombre de recherches clés non réussies au cours de l’intervalle de création des rapports. Cette valeur correspond à la commande Redis INFO `keyspace_misses`. Les absences dans le cache ne signifient pas nécessairement qu’il y a un problème dans le cache. Par exemple, en cas d’utilisation du mode de programmation de type cache-aside, une application recherche d’abord l’élément dans le cache. Si cet élément ne s’y trouve pas (Absence dans le cache), il est récupéré dans la base de données et ajouté au cache pour la prochaine fois. Les absences dans le cache sont un comportement normal pour le mode de programmation de type cache-aside. Si le nombre d’absences dans le cache est plus élevé que prévu, examinez la logique d’application qui remplit le cache et y lit les informations. Si des éléments sont supprimés du cache en raison d’une trop grande sollicitation de la mémoire, des absences dans le cache peuvent se produire, mais `Used Memory` ou `Evicted Keys` sont de meilleures mesures pour surveiller la pression sur la mémoire. |
| Clients connectés |Nombre de connexions client au cache au cours de l’intervalle de création des rapports spécifié. Cette valeur correspond à la commande Redis INFO `connected_clients`. Une fois la [limite de connexions](cache-configure.md#default-redis-server-configuration) atteinte, les tentatives de connexion ultérieures au cache échouent. Notez que même s’il n’y a aucune application cliente active, il peut rester quelques instances de clients connectés en raison de connexions et processus internes. |
| Clés exclues |Nombre d’éléments supprimés du cache au cours de l’intervalle de création des rapports, en raison de la limite `maxmemory`. Cette valeur correspond à la commande Redis INFO `evicted_keys`. |
| Clés expirées |Nombre d’éléments expirés dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur correspond à la commande Redis INFO `expired_keys`. |
| Gets |Nombre d’opérations get dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` et `cmdstat_getrange`. Elle est équivalente à la somme du nombre de présences et d’absences au cours de l’intervalle de création du rapport. |
| Charge du serveur Redis |Pourcentage de cycles dans lesquels le serveur Redis est occupé par le traitement et n’est pas inactif, en attente de messages. Si ce compteur atteint 100, c’est que le serveur Redis a atteint un plafond de performances et que le processeur ne peut pas fonctionner plus rapidement. Si vous voyez une forte charge de serveur Redis, des exceptions d’expiration seront levées dans le client. Dans ce cas, vous devez envisager d’effectuer une mise à l’échelle ou bien de partitionner vos données sur plusieurs caches. |
| Sets |Nombre d’opérations set dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` et `cmdstat_setnx`. |
| Total des opérations |Nombre total de commandes traitées par le serveur de cache au cours de l’intervalle spécifié. Cette valeur correspond à la commande Redis INFO `total_commands_processed`. Notez que lorsque le cache Redis Azure est uniquement utilisé pour publication et téléchargement, il n’y a aucune mesure pour `Cache Hits`, `Cache Misses`, `Gets` ou `Sets`, mais il y aura des mesures de `Total Operations` qui reflètent l’utilisation du cache pour ces opérations. |
| Mémoire utilisée |Quantité de mémoire cache, exprimée en Mo, utilisée pour les paires clé/valeur dans le cache au cours de l’intervalle de création des rapports. Cette valeur correspond à la commande Redis INFO `used_memory`. Elle n’inclut pas les métadonnées ou la fragmentation. |
| Taille de la mémoire résidente utilisée |Quantité de mémoire cache utilisée (exprimée en Mo) au cours de l’intervalle de création des rapports, fragmentation et métadonnées comprises. Cette valeur correspond à la commande Redis INFO `used_memory_rss`. |
| UC |Utilisation du processeur du serveur de cache Redis Azure sous forme de pourcentage au cours de l’intervalle de création des rapports spécifiée. Cette valeur correspond au compteur de performances `\Processor(_Total)\% Processor Time` du système d’exploitation. |
| Lecture du cache |Quantité de données lues dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. **Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous souhaitez configurer des alertes pour des limites de bande passante réseau côté serveur, vous pouvez les créer à l’aide de ce compteur `Cache Read`. Consultez [cette table](cache-faq.md#cache-performance) pour connaître les limites de bande passante observées pour des caches de différentes tailles et différents niveaux de tarification.** |
| Cache d’écriture |Quantité de données écrites dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. Cette valeur correspond à la bande passante réseau des données envoyées au cache depuis le client. |

## Affichage des mesures et personnalisation des graphiques
Vous pouvez afficher une vue d’ensemble des mesures pour votre cache dans le panneau **Mesures Redis**. Pour accéder au panneau **Mesures Redis**, choisissez **Tous les paramètres** > **Mesures Redis**.

![Mesures Redis][redis-cache-redis-metrics]

Le panneau **Mesures Redis** contient les graphiques suivants.

| Graphique des mesures Redis | Mesures affichées |
| --- | --- |
| Lecture du cache et Cache d’écriture |Lecture du cache |
| Cache d’écriture | |
| Clients connectés |Clients connectés |
| Présences et absences |Présences dans le cache |
| Absences dans le cache | |
| Total des commandes |Total des opérations |
| Gets et Sets |Gets |
| Sets | |
| Utilisation du processeur |UC |
| Utilisation de la mémoire |Mémoire utilisée |
| Taille de la mémoire résidente utilisée | |
| Charge du serveur Redis |Charge du serveur |
| Nombre de clés |Nombre total de clés |
| Clés exclues | |
| Clés expirées | |

Pour une vue plus détaillée des mesures sur un graphique en particulier et pour personnaliser le graphique, cliquez sur le graphique souhaité dans le panneau **Mesures Redis** pour afficher le panneau **Mesure** de ce graphique.

![Volet Metric][redis-cache-metric-blade]

Les alertes définies sur la base des mesures affichées par un graphique sont répertoriées en bas du panneau **Mesure** de ce graphique.

Pour ajouter ou supprimer des mesures ou pour modifier l’intervalle de création des rapports, choisissez **Modifier le graphique**.

Pour ajouter ou supprimer des mesures dans le graphique, cliquez sur la case à cocher en regard du nom de la mesure. Pour modifier l’intervalle de création des rapports, cliquez sur l’intervalle souhaité. Pour modifier le **type de graphique**, cliquez sur le style souhaité. Une fois les modifications effectuées, cliquez sur **Enregistrer**.

![Modifier le graphique][redis-cache-edit-chart]

Une fois que vous avez cliqué sur **Enregistrer**, vos modifications sont conservées jusqu’à la fermeture du panneau **Mesure**. La mesure et l'intervalle de temps d'origine s'affichent de nouveau lorsque vous rouvrez le panneau. Pour plus d’informations sur la personnalisation des graphiques, consultez la page [Surveillance des mesures de service](../azure-portal/insights-how-to-customize-monitoring.md).

Pour voir les mesures d’une période spécifique sur le graphique, pointez la souris sur une des barres ou un des points du graphique qui correspond à l’heure souhaitée, et les mesures de cet intervalle sont affichées.

![Afficher les détails du graphique][redis-cache-view-chart-details]

## Comment surveiller un cache premium avec le clustering
Les caches Premium pour lesquels le [clustering](cache-how-to-premium-clustering.md) est activé peuvent comporter jusqu'à 10 partitions. Chaque partition possède ses propres mesures, et ces mesures sont agrégées afin de fournir des mesures pour le cache dans son ensemble. Chaque mesure inclut deux versions. La première version mesure les performances de la totalité du cache et la seconde version de la mesure comportant `(Shard 0-9)` dans son nom évalue les performances d’une seule partition d’un cache. Par exemple, si un cache comporte 3 partitions, `Cache Hits` est la quantité totale d’accès pour le cache entier, tandis que `Cache Hits (Shard 2)` est simplement le nombre d’accès à cette partition du cache.

Chaque graphique de surveillance affiche les mesures de niveau supérieur du cache ainsi que les mesures de chaque partition du cache.

![Surveiller][redis-cache-premium-monitor]

Passer la souris sur les points de données affiche les détails de ce point dans le temps.

![Surveiller][redis-cache-premium-point-summary]

Les valeurs plus élevées sont généralement les valeurs d'agrégation pour le cache tandis que les valeurs inférieures représentent les mesures individuelles pour la partition. Notez que dans cet exemple, il existe trois partitions et les correspondances dans le cache sont réparties uniformément entre les partitions.

![Surveiller][redis-cache-premium-point-shard]

Pour plus de détails, cliquez sur le graphique pour afficher une vue développée dans le panneau **Mesure**.

![Surveiller][redis-cache-premium-chart-detail]

Par défaut, chaque graphique inclut le compteur de performances du cache de niveau supérieur ainsi que les compteurs de performance des partitions individuelles. Vous pouvez personnaliser ces informations dans le panneau **Modifier le graphique**.

![Surveiller][redis-cache-premium-edit]

Pour plus d'informations sur les compteurs de performances disponibles, consultez [Mesures disponibles et intervalles de création des rapports](#available-metrics-and-reporting-intervals).

## Opérations et alertes
La section **Opérations** du panneau **Cache Redis** comporte les sections **Événements** et **Règles d’alerte**.

![Opérations][redis-cache-operations-events]

Pour voir la liste des opérations de cache récentes, cliquez sur le graphique **Événements** pour afficher le panneau **Événements**. La récupération et la régénération des clés d’accès et l’activation et la résolution des règles d’alerte sont des exemples d’opérations. Pour plus d’informations sur chaque événement, cliquez sur l’événement dans le panneau **Événements**.

Pour plus d’informations sur les événements, consultez la page [Affichage des événements et des journaux d’audit](../azure-portal/insights-debugging-with-events.md).

La section **Règles d’alerte** affiche le nombre d’alertes pour l’instance de cache. Une règle d’alerte permet de surveiller votre instance de cache et de recevoir un courrier électronique chaque fois qu’une valeur atteint le seuil défini dans la règle.

Les règles d’alerte sont évaluées environ toutes les cinq minutes. Lorsqu’une règle d’alerte est activée, les notifications configurées sont envoyées. Les activations de règle d’alerte et les notifications ne sont pas traitées instantanément. Il peut y avoir un délai de plusieurs minutes avant que la règle d’alerte ne soit activée et les notifications envoyées.

Les règles d’alerte peuvent être consultées et définies dans le panneau **Mesure** de chaque graphique de surveillance, ou bien dans le panneau **Règles d’alerte**.

Les règles d’alerte possèdent les propriétés suivantes :

| Propriété de règle d’alerte | Description |
| --- | --- |
| Ressource |Ressource évaluée par la règle d’alerte. Lorsque vous créez une règle d’alerte à partir d’un cache Redis, le cache est la ressource. |
| Name |Nom qui identifie de façon unique la règle d’alerte dans l’instance de cache actuelle. |
| Description |Description facultative de la règle d’alerte. |
| Mesure |Mesure à surveiller par la règle d’alerte. Pour voir la liste des mesures de cache, consultez la section Mesures disponibles et intervalles de création des rapports. |
| Condition |Opérateur de condition de la règle d’alerte. Les choix possibles sont : supérieur à, supérieur ou égal à, inférieur à, inférieur ou égal à |
| Seuil |Valeur utilisée pour comparer les mesures à l’aide de l’opérateur spécifié par la propriété de condition. En fonction de la mesure, cette valeur peut être en octets par seconde, en octets, en pourcentage ou bien en nombre. |
| Période |Spécifie la période pendant laquelle la valeur moyenne de la mesure est utilisée pour la comparaison de la règle d’alerte. Par exemple, si la période correspond à la dernière heure, la valeur moyenne de mesure de l’intervalle d’une heure est utilisée pour la comparaison. Si vous souhaitez être averti lorsque le seuil est atteint en raison d’un pic d’activité, définissez un délai plus court. Pour être averti en cas d’activité soutenue au-dessus du seuil, utilisez une période plus longue. |
| Messages aux administrateurs et coadministrateurs du service |Si cette option est activée, un courrier électronique est envoyé à l’administrateur et au coadministrateur du service lorsque l’alerte se déclenche. |
| Message supplémentaire pour l’administrateur |Adresse facultative pour qu’un autre administrateur soit averti lorsque l’alerte est déclenchée. |

Une seule notification est envoyée par activation de la règle d’alerte. Une fois le seuil de la règle dépassé et qu’une notification est envoyée, la règle n’est plus évaluée jusqu’à ce que la mesure soit de nouveau inférieure au seuil. Si par la suite la mesure dépasse le seuil, l’alerte est réactivée et une notification est envoyée.

Pour voir toutes les règles d’alerte de votre instance de cache, cliquez sur la partie **Règles d’alerte** du panneau **Cache Redis**. Pour afficher uniquement les règles d’alerte qui utilisent une mesure spécifique, accédez au panneau **Mesure** du graphique qui contient cette mesure.

![Règles d'alerte][redis-cache-alert-rules]

Pour ajouter une règle d’alerte, cliquez sur **Ajouter une alerte** à partir du panneau **Mesure** ou du panneau **Règles d’alerte**.

Entrez les critères de règle souhaités dans le panneau **Ajouter une alerte** et cliquez sur **OK**.

![Ajouter une règle d’alerte][redis-cache-add-alert]

> [!NOTE]
> Lorsque vous créez une règle d’alerte en cliquant sur **Ajouter une alerte** dans le panneau **Mesure**, seules les mesures affichées sur le graphique de ce panneau apparaissent dans la liste déroulante **Mesure**. Lorsque vous créez une règle d’alerte en cliquant sur **Ajouter une alerte** dans le panneau **Règles d’alerte**, toutes les mesures apparaissent dans la liste déroulante **Mesure**.
> 
> 

Une fois que la règle d’alerte est enregistrée, elle s’affiche dans le panneau **Règles d’alerte**, ainsi que dans le panneau **Mesure** de tous les graphiques qui affichent la mesure utilisée dans la règle d’alerte. Pour modifier une règle d’alerte, cliquez sur le nom de la règle d’alerte pour afficher le panneau **Modifier la règle**. Dans le panneau **Modifier la règle**, vous pouvez modifier les propriétés de la règle, supprimer ou désactiver la règle d’alerte ou réactiver la règle si elle a été désactivée.

> [!NOTE]
> Toutes les modifications apportées aux propriétés de la règle peuvent prendre quelques instants avant d’apparaître dans le panneau **Règles d’alerte** ou dans le panneau **Mesure**.
> 
> 

Lorsqu’une règle d’alerte est activée, un courrier électronique est envoyé selon la configuration de la règle d’alerte et une icône d’alerte s’affiche dans la partie **Règles d’alerte** du panneau **Cache Redis**.

Les règles d’alerte sont considérées comme résolues lorsque la condition d’alerte n’est plus vraie. Une fois la condition de règle d’alerte résolue, l’icône d’alerte est remplacée par une coche. Pour plus d’informations sur l’activation et la résolution des alertes, cliquez sur **Événements** dans le panneau **Cache Redis** pour afficher les événements dans le panneau **Événements**.

Pour plus d’informations sur les alertes dans Azure, consultez la page [Réception de notifications d’alerte](../azure-portal/insights-receive-alert-notifications.md).

## Mesures dans le panneau Cache Redis
Le panneau **Cache Redis** affiche les catégories de mesures suivantes.

* [Graphiques de surveillance](#monitoring-charts)
* [Graphiques d’utilisation](#usage-charts)

### Graphiques de surveillance
La section **Surveillance** comporte les graphiques **Présences et absences**, **Gets et Sets**, **Connexions** et **Total des commandes**.

![Graphiques de surveillance][redis-cache-monitoring-part]

Les graphiques **Surveillance** affichent les mesures suivantes.

| Graphique de surveillance | Mesures de cache |
| --- | --- |
| Présences et absences |Présences dans le cache |
| Absences dans le cache | |
| Gets et Sets |Gets |
| Sets | |
| Connexions |Clients connectés |
| Total des commandes |Total des opérations |

Pour plus d’informations sur les mesures affichées et la personnalisation des graphiques de cette section, consultez la section [Affichage des mesures et personnalisation des graphiques](#how-to-view-metrics-and-customize-charts).

### Graphiques d’utilisation
La section **Utilisation** contient les graphiques **Charge du serveur Redis**, **Utilisation de la mémoire**, **Bande passante réseau** et **Utilisation du processeur** et affiche également le **niveau tarifaire** de l’instance de cache.

![Graphiques d’utilisation][redis-cache-usage-part]

**Niveau tarifaire** affiche le niveau tarifaire du cache et peut être utilisé pour la [mise à l’échelle](cache-how-to-scale.md) du cache à un autre niveau tarifaire.

Les graphiques d’**utilisation** affichent les mesures suivantes.

| Graphique d’utilisation | Mesures de cache |
| --- | --- |
| Charge du serveur Redis |Charge du serveur |
| Utilisation de la mémoire |Mémoire utilisée |
| Bande passante réseau |Cache d’écriture |
| Utilisation du processeur |UC |

Pour plus d’informations sur les mesures affichées et la personnalisation des graphiques de cette section, consultez la section [Affichage des mesures et personnalisation des graphiques](#how-to-view-metrics-and-customize-charts).

<!-- IMAGES -->

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

[redis-cache-redis-metrics]: ./media/cache-how-to-monitor/redis-cache-redis-metrics.png

[redis-cache-redis-metrics-blade]: ./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png

<!---HONumber=AcomDC_0831_2016-->