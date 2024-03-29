---
title: Utilisation des rubriques Service Bus (Ruby | Microsoft Docs
description: Découvrez comment utiliser les rubriques et abonnements Service Bus dans Azure. Les exemples de code sont écrits pour les applications Ruby.
services: service-bus
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-topics/subscriptions"></a>Utilisation des rubriques/abonnements Service Bus
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Cet article décrit l’utilisation des rubriques et des abonnements Service Bus depuis les applications Ruby. Les scénarios couverts incluent la **création de rubriques et d’abonnements, la création de filtres d’abonnement, l’envoi de messages** à une rubrique, **la réception de messages en provenance d’un abonnement** et enfin **la suppression de rubriques et d’abonnements**. Pour plus d’informations sur les rubriques et les abonnements, consultez la section [Étapes suivantes](#next-steps).

## <a name="service-bus-topics-and-subscriptions"></a>Rubriques et abonnements Service Bus
Les rubriques et les abonnements Service Bus prennent en charge un modèle de communication de messagerie *de publication et d'abonnement* . Lors de l’utilisation de rubriques et d’abonnements, les composants d’une application distribuée ne communiquent pas directement entre eux ; ils échangent plutôt des messages via une rubrique, qui fait office d’intermédiaire.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

Contrairement aux files d’attente Service Bus, où chaque message est traité par un seul consommateur, les rubriques et les abonnements fournissent une forme de communication **un-à-plusieurs**, à l’aide d’un modèle de publication et d’abonnement. Il est possible d’inscrire plusieurs abonnements à une rubrique. Lorsqu’un message est envoyé à une rubrique, il est alors mis à disposition de chaque abonnement pour être traité indépendamment.

Un abonnement à une rubrique ressemble à une file d’attente virtuelle qui reçoit des copies des messages envoyés à la rubrique. Vous pouvez éventuellement inscrire des règles de filtre pour une rubrique par abonnement, ce qui vous permet de filtrer ou de restreindre les messages d’une rubrique reçus en fonction des abonnements à une rubrique.

Les rubriques et les abonnements Service Bus vous permettent de mettre votre infrastructure à l’échelle pour traiter de nombreux messages parmi un grand nombre d’utilisateurs et d’applications.

## <a name="create-a-namespace"></a>Créer un espace de noms
Pour utiliser les files d’attente Service Bus dans Azure, vous devez d’abord créer un espace de noms. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application. Vous devez créer l’espace de noms via l’interface de ligne de commande car le [portail Azure][portail Azure] ne crée pas l’espace de noms avec une connexion ACS.

Pour créer un espace de noms :

1. Ouvrez une fenêtre de console Azure PowerShell.
2. Tapez la commande suivante pour créer un espace de noms. Fournissez votre propre valeur d'espace de noms et spécifiez la même région que votre application.
   
    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```
   
    ![Créer l'espace de noms](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>Obtention d'informations d'identification de gestion par défaut pour l'espace de noms
Afin d’effectuer des opérations de gestion, comme la création d’une file d’attente, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées.

L'applet de commande PowerShell que vous avez exécutée pour créer l'espace de noms Service Bus affiche la clé qui vous permet de gérer l'espace de noms. Copiez la valeur **DefaultKey**. Vous utiliserez cette valeur dans votre code, plus loin dans ce didacticiel.

![Copier la clé](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [!NOTE]
> Pour obtenir cette clé, vous pouvez également ouvrir une session sur le [portail Azure][portail Azure] et accéder aux informations de connexion de votre espace de noms.
> 
> 

## <a name="create-a-ruby-application"></a>Création d'une application Ruby
Pour obtenir des instructions, consultez le guide [Création d’une application Ruby sur Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configuration de votre application pour l'utilisation de Service Bus
Pour utiliser Service Bus, téléchargez et utilisez le package Azure Ruby, qui inclut un ensemble de bibliothèques permettant de communiquer avec les services de stockage REST.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilisation de RubyGems pour obtenir le package
1. Ouvrez une interface de ligne de commande, telle que **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Tapez « gem install azure » dans la fenêtre de commande pour installer gem et les dépendances.

### <a name="import-the-package"></a>Importation du package
À l'aide de votre éditeur de texte, ajoutez la commande suivante au début du fichier Ruby où vous comptez utiliser le stockage :

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configuration d’une connexion Service Bus
Le module Azure lit les variables d’environnement **AZURE\_SERVICEBUS\_NAMESPACE** et **AZURE\_SERVICEBUS\_ACCESS\_KEY** pour obtenir les informations nécessaires à la connexion à votre espace de noms. Si ces variables d’environnement ne sont pas définies, vous devez spécifier les informations d’espace de noms avant d’utiliser **Azure::ServiceBusService** grâce au code suivant :

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Attribuez à l'espace de noms la valeur que vous avez créée plutôt que l'URL entière. Par exemple, utilisez **« votreespacedenomsexemple »** et non « votreespacedenomsexemple.servicebus.windows.net ».

## <a name="create-a-topic"></a>Création d'une rubrique
L’objet **Azure::ServiceBusService** permet d’utiliser des rubriques. Le code suivant crée un objet **Azure::ServiceBusService**. Pour créer une rubrique, utilisez la méthode **create\_topic()**. L’exemple suivant crée une rubrique ou imprime les erreurs s’il en existe.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Vous pouvez également transmettre un objet **Azure::ServiceBus::Topic** avec des options complémentaires, ce qui vous permet de remplacer les paramètres de rubrique par défaut comme la durée de vie de message ou la taille de file maximale. L'exemple suivant montre comment définir la taille maximale de la file d'attente sur 5 Go et la durée de vie de message sur une minute :

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Création d’abonnements
Les abonnements de rubrique sont également créés à l’aide de l’objet **Azure::ServiceBusService**. Les abonnements sont nommés et peuvent être assortis d'un filtre facultatif qui limite l'ensemble des messages transmis à la file d'attente virtuelle de l'abonnement.

Les abonnements sont persistants et continuent à exister jusqu’à leur suppression ou celle de la rubrique à laquelle ils sont associés. Si votre application contient une logique pour la création d’un abonnement, elle doit d’abord vérifier si l’abonnement existe déjà en utilisant la méthode getSubscription.

### <a name="create-a-subscription-with-the-default-(matchall)-filter"></a>Création d’un abonnement avec le filtre par défaut (MatchAll)
Le filtre **MatchAll** est le filtre utilisé par défaut si aucun filtre n’est spécifié lors de la création d’un abonnement. Lorsque le filtre **MatchAll** est utilisé, tous les messages publiés dans la rubrique sont placés dans la file d’attente virtuelle de l’abonnement. Dans l’exemple suivant, l’abonnement « all-messages » qui est créé utilise le filtre par défaut **MatchAll**.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Création d’abonnements avec des filtres
Vous pouvez également définir des filtres pour spécifier quels sont les messages, parmi ceux envoyés à une rubrique, qui doivent apparaître dans un abonnement spécifique.

Parmi les types de filtres pris en charge par les abonnements, **Azure::ServiceBus::SqlFilter** est le plus flexible ; il implémente un sous-ensemble de SQL92. Les filtres SQL opèrent au niveau des propriétés des messages publiés dans la rubrique. Pour plus de détails sur les expressions utilisables avec un filtre SQL, examinez la syntaxe [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Vous pouvez ajouter des filtres à un abonnement en utilisant la méthode **create\_rule()** de l’objet **Azure::ServiceBusService**. Cette méthode vous permet d’ajouter de nouveaux filtres à un abonnement existant.

Étant donné que le filtre par défaut est appliqué automatiquement à tous les nouveaux abonnements, vous devez d’abord supprimer le filtre par défaut ou le filtre **MatchAll** remplacera tous les autres filtres spécifiés. Vous pouvez supprimer la règle par défaut en utilisant la méthode **delete\_rule()** sur l’objet **Azure::ServiceBusService**.

Dans l’exemple suivant, l’abonnement « high-messages » est créé avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété personnalisée **message\_number** a une valeur supérieure à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

De la même manière, l’exemple suivant crée l’abonnement « low-messages » avec un filtre **Azure::ServiceBus::SqlFilter** qui sélectionne uniquement les messages dont la propriété **message_number** a une valeur inférieure ou égale à 3 :

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

À présent, lorsqu’un message est envoyé à « test-topic », il est toujours remis aux destinataires abonnés à l’abonnement de rubrique « all-messages » et est remis de manière sélective aux destinataires abonnés aux abonnements de rubrique « high-messages » et « low-messages » (en fonction du contenu du message).

## <a name="send-messages-to-a-topic"></a>Envoi de messages à une rubrique
Pour envoyer un message à une rubrique Service Bus, votre application doit utiliser la méthode **send\_topic\_message()** sur l’objet **Azure::ServiceBusService**. Les messages envoyés aux rubriques Service Bus sont des instances des objets **Azure::ServiceBus::BrokeredMessage**. Les objets **Azure::ServiceBus::BrokeredMessage** possèdent un ensemble de propriétés standard (telles que **label** et **time\_to\_live**), un dictionnaire servant à conserver les propriétés personnalisées propres à une application, ainsi qu’un corps de données de chaîne. Une application peut définir le corps du message en transmettant une valeur de chaîne à la méthode **send\_topic\_message()** pour remplir toutes les propriétés standard requises avec les valeurs par défaut.

L’exemple suivant montre comment envoyer cinq messages de test à la rubrique « test-topic ». Notez que la valeur de la propriété personnalisée **message_number** de chaque message varie au niveau de l’itération de la boucle (détermine l’abonnement qui le reçoit) :

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Les rubriques Service Bus prennent en charge une taille de message maximale de 256 Ko dans le [niveau Standard](service-bus-premium-messaging.md) et de 1 Mo dans le [niveau Premium](service-bus-premium-messaging.md). L’en-tête, qui comprend les propriétés d’application standard et personnalisées, peut avoir une taille maximale de 64 Ko. Si une rubrique n'est pas limitée par le nombre de messages qu'elle peut contenir, elle l'est en revanche par la taille totale des messages qu'elle contient. Cette taille de rubrique est définie au moment de la création. La limite maximale est de 5 Go.

## <a name="receive-messages-from-a-subscription"></a>Réception des messages d’un abonnement
La méthode **receive\_subscription\_message()** de l’objet **Azure::ServiceBusService** permet de recevoir les messages d’un abonnement. Par défaut, les messages sont lus et verrouillés sans être supprimés de l’abonnement. Il est possible de lire et de supprimer le message de l’abonnement en définissant l’option **peek\_lock** sur **false**.

Avec le comportement par défaut, la lecture et la suppression sont une opération en deux étapes, ce qui permet également de prendre en charge des applications ne pouvant pas fonctionner avec des messages manquants. Lorsque Service Bus reçoit une demande, il recherche le prochain message à consommer, le verrouille pour empêcher d'autres consommateurs de le recevoir, puis le renvoie à l'application. Dès lors que l’application a terminé le traitement du message (ou qu’elle l’a stocké de manière fiable pour un traitement ultérieur), elle accomplit la deuxième étape du processus de réception en appelant la méthode **delete\_subscription\_message()** et en fournissant le message à supprimer sous la forme d’un paramètre. La méthode **delete\_subscription\_message()** marque le message comme étant consommé et le supprime de l’abonnement.

Si le paramètre **:peek\_lock** est défini sur **false**, la lecture et la suppression des messages suivent un modèle plus simple qui fonctionne mieux pour les scénarios dans lesquels une application peut ne pas traiter un message en cas d’échec. Pour mieux comprendre, imaginez un scénario dans lequel le consommateur émet la demande de réception et subit un incident avant de la traiter. Comme Service Bus a marqué le message comme étant consommé, lorsque l’application redémarre et recommence à consommer des messages, elle manque le message consommé avant l’incident.

L’exemple suivant montre comment des messages peuvent être reçus et traités à l’aide de **receive\_subscription\_message()**. Dans l’exemple, un message est d’abord reçu, puis supprimé de l’abonnement « low-messages » par le biais de **:peek\_lock** défini sur **false**. Un autre message de « high-messages » est ensuite reçu, puis supprimé via **delete\_subscription\_message()** :

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Gestion des blocages d'application et des messages illisibles
Service Bus intègre des fonctionnalités destinées à faciliter la récupération à la suite d’erreurs survenues dans votre application ou de difficultés à traiter un message. Si une application réceptrice ne parvient pas à traiter le message pour une raison quelconque, elle appelle la méthode **unlock\_subscription\_message()** pour l’objet **Azure::ServiceBusService**. Cela amène Service Bus à déverrouiller le message dans l’abonnement et à le rendre à nouveau disponible en réception, pour la même application consommatrice ou pour une autre.

De même, il faut savoir qu’un message verrouillé dans un abonnement est assorti d’un délai d’expiration et que si l’application ne parvient pas à traiter le message dans le temps imparti (par exemple, si l’application subit un incident), Service Bus déverrouille le message automatiquement et le rend à nouveau disponible en réception.

Si l’application subit un incident après le traitement du message, mais avant l’émission de la méthode **delete\_subscription\_message()**, le message est à nouveau remis à l’application lorsqu’elle redémarre. Dans ce type de traitement, souvent appelé **Au moins une fois**, chaque message est traité au moins une fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Toutefois, dans certaines circonstances, un même message peut être remis une nouvelle fois. Cette logique est souvent obtenue grâce à la propriété **message\_id** du message, qui reste constante pendant les tentatives de remise.

## <a name="delete-topics-and-subscriptions"></a>Suppression de rubriques et d'abonnements
Les rubriques et les abonnements sont persistants et doivent être supprimés de façon explicite par le biais du [portail Azure][portail Azure] ou par programme. L’exemple suivant montre comme supprimer la rubrique intitulée « test-topic ».

```
azure_service_bus_service.delete_topic("test-topic")
```

La suppression d’une rubrique a également pour effet de supprimer les abonnements inscrits dans la rubrique. Les abonnements peuvent aussi être supprimés de manière indépendante. Le code suivant montre comment supprimer l’abonnement « high-messages » de la rubrique « test-topic » :

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les principes de base des rubriques Service Bus, consultez ces liens pour en savoir plus.

* Consultez [Files d’attente, rubriques et abonnements](service-bus-queues-topics-subscriptions.md).
* Référence d’API pour [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
* Accédez au référentiel du [Kit de développement logiciel (SDK) Azure pour Ruby](https://github.com/Azure/azure-sdk-for-ruby) sur GitHub.

[portail Azure]: https://portal.azure.com



<!--HONumber=Oct16_HO2-->


