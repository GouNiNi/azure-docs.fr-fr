---
title: API Application Insights pour les événements et les mesures personnalisés | Microsoft Docs
description: Insérez quelques lignes de code dans votre application de périphérique ou de bureau, votre page web ou votre service pour suivre l'utilisation et diagnostiquer les problèmes.
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 09/11/2016
ms.author: awills

---
# API Application Insights pour les événements et les mesures personnalisés
*Application Insights est à l'état de version préliminaire.*

Insérez quelques lignes de code dans votre application pour découvrir ce qu’en font les utilisateurs ou pour faciliter le diagnostic des problèmes. Vous pouvez envoyer la télémétrie depuis des applications de périphérique et de bureau, des clients web et des serveurs web. L’API de télémétrie [Visual Studio Application Insights](app-insights-overview.md) vous permet d’envoyer des événements et mesures personnalisés, et vos propres versions de la télémétrie standard. Cette API est la même que celle utilisée par les collecteurs de données standard d’Application Insights.

## API summary
L’API est uniforme sur toutes les plateformes, à l’exception de quelques petites variations.

| Méthode | Utilisé pour |
| --- | --- |
| [`TrackPageView`](#page-views) |Pages, écrans, panneaux ou formes |
| [`TrackEvent`](#track-event) |Actions de l’utilisateur et autres événements. Utilisé pour suivre le comportement de l’utilisateur ou pour analyser les performances. |
| [`TrackMetric`](#track-metric) |Mesures de performances telles que la longueur des files d’attente non liées à des événements spécifiques |
| [`TrackException`](#track-exception) |Exceptions de journal pour des diagnostics. Effectuez un suivi lorsqu’ils se produisent par rapport à d’autres événements et examinez les arborescences des appels de procédure. |
| [`TrackRequest`](#track-request) |Notez la fréquence et la durée des requêtes du serveur pour l’analyse des performances. |
| [`TrackTrace`](#track-trace) |Messages du journal de diagnostic Vous pouvez également capturer des journaux tiers. |
| [`TrackDependency`](#track-dependency) |Consignez la durée et la fréquence des appels vers les composants externes dont dépend votre application. |

Vous pouvez [associer des propriétés et des mesures](#properties) à la plupart de ces appels de télémétrie.

## <a name="prep"></a>Avant de commencer
Si vous ne l'avez pas encore fait :

* Ajoutez le Kit de développement logiciel (SDK) Application Insights à votre projet :
  
  * [Projet ASP.NET][greenbrown]
  * [Projet Java][java]
  * [JavaScript dans chaque page web][client]
* Ajoutez au code de votre périphérique ou de votre serveur web :
  
    *C# :* `using Microsoft.ApplicationInsights;`
  
    *VB :* `Imports Microsoft.ApplicationInsights`
  
    *Java :* `import com.microsoft.applicationinsights.TelemetryClient;`

## Construction d’un TelemetryClient
Construisez une instance de TelemetryClient (sauf en JavaScript dans les pages web) :

*C# :*

    private TelemetryClient telemetry = new TelemetryClient();

*VB :*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient est thread-safe.

Nous vous recommandons d’utiliser une instance de `TelemetryClient` pour chaque module de votre application. Par exemple, vous pouvez avoir une instance de `TelemetryClient` dans votre service web pour signaler les requêtes http entrantes et un autre instance dans une classe d’intergiciels pour signaler les événements de logique métier. Vous pouvez définir des propriétés telles que `TelemetryClient.Context.User.Id` pour assurer le suivi des utilisateurs et des sessions ou `TelemetryClient.Context.Device.Id` pour identifier l’ordinateur. Cette information est associée à tous les événements envoyés par l'instance.

## Suivi des événements
Dans Application Insights, un *événement personnalisé* est un point de données que vous pouvez afficher à la fois dans [Metrics Explorer][metrics] en tant que nombre agrégé et dans [Recherche de diagnostic][diagnostic] en tant qu’occurrences individuelles. (Il n’est pas lié à des « événements » de type MVC ou autres.)

Insérez des appels TrackEvent dans votre code pour compter la fréquence à laquelle les utilisateurs choisissent une fonctionnalité particulière, la fréquence à laquelle ils atteignent des objectifs particuliers ou à laquelle ils commettent éventuellement des types d’erreurs particuliers.

Par exemple, dans une application de jeu, envoyez un événement chaque fois qu'un utilisateur gagne le jeu :

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*VB*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### Affichage de vos événements dans le portail Azure
Pour voir un nombre de vos événements, ouvrez un panneau [Metrics Explorer](app-insights-metrics-explorer.md), ajoutez un nouveau graphique, puis sélectionnez les événements.

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Pour comparer le nombre d'événements différents, définissez le type de graphique sur Grille et groupez par nom d'événement :

![](./media/app-insights-api-custom-events-metrics/07-grid.png)

Dans la grille, cliquez sur un nom d'événement pour voir les occurrences individuelles de cet événement.

![Extrayez les événements](./media/app-insights-api-custom-events-metrics/03-instances.png)

Cliquez sur n'importe quelle occurrence pour afficher plus d’informations.

Pour vous concentrer sur des événements spécifiques dans la recherche ou Metrics Explorer, définissez le filtre du panneau sur les noms d'événements qui vous intéressent :

![Ouvrez Filtres, développez Nom de l'événement et sélectionnez une ou plusieurs valeurs](./media/app-insights-api-custom-events-metrics/06-filter.png)

## Suivi des mesures
Utilisez TrackMetric pour envoyer des mesures qui ne sont pas associées à des événements particuliers. Par exemple, vous pouvez analyser la longueur d’une file d'attente à des intervalles réguliers.

Les mesures sont affichées sous forme de graphiques statistiques dans Metrics Explorer, mais contrairement aux événements, vous ne pouvez pas rechercher des occurrences individuelles dans Recherche de diagnostic.

Les valeurs de mesures doivent être > = 0 pour s'afficher correctement.

*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

En fait, vous pouvez procéder ainsi dans un thread d'arrière-plan :

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Pour afficher les résultats, ouvrez Metrics Explorer et ajoutez un nouveau graphique. Définissez-le pour afficher votre mesure.

![Ajouter un nouveau graphique ou sélectionnez un graphique et sélectionnez votre métrique sous Personnalisé](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Il existe certaines [limites au nombre de mesures](#limits) que vous pouvez utiliser.

## Affichages de page
Dans un périphérique ou une application de page web, la télémétrie d'affichage de page est envoyée par défaut lorsque chaque écran ou page est chargé. Mais vous pouvez modifier cela pour suivre les affichages de page à différents moments. Par exemple, dans une application qui affiche les onglets ou les panneaux, vous pouvez effectuer le suivi d'une « page » chaque fois que l'utilisateur ouvre un nouveau panneau.

![Filtre d'utilisation dans le panneau Vue d'ensemble](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Les données d’utilisateur et de session sont envoyées en tant que propriétés avec les affichages de page, de façon à ce que les graphiques d’utilisateur et de session soient actifs s’il existe une télémétrie de l’affichage de page.

#### Affichages de pages personnalisées
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Si vous avez plusieurs onglets dans différentes pages HTML, vous pouvez aussi spécifier l'URL :

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### Affichages de la page de durée
Par défaut, les heures déclarées « Temps de chargement de l’affichage de la page » sont mesurées à partir du moment où le navigateur envoie la demande, jusqu’à ce que l’événement de chargement de la page du navigateur soit appelé.

Au lieu de cela, vous pouvez :

* Définir une durée explicite dans l’appel [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview).
  * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Utiliser les appels de minutage d’affichage de la page `startTrackPage` et `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Le nom que vous utilisez comme premier paramètre associe les appels de démarrage et d’arrêt. Le nom de la page actuelle est utilisé par défaut.

Les durées de chargement de la page résultantes affichées dans Metric Explorer sont dérivées de l’intervalle entre les appels de démarrage et d’arrêt. C’est à vous de décider quel intervalle de temps vous voulez mesurer.

## Suivi des requêtes
Utilisé par le Kit de développement logiciel (SDK) du serveur pour consigner les requêtes HTTP.

Vous pouvez également l'appeler vous-même si vous souhaitez simuler des requêtes dans le cas où le module du service web n’est pas en cours d'exécution.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetry.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## Contexte de l’opération
Les éléments de télémétrie peuvent être associés en leur joignant un ID d’opération commun. Le module de suivi de requête standard effectue cette opération pour les exceptions et les autres événements envoyés lors du traitement d’une requête HTTP. Dans [Recherche](app-insights-diagnostic-search.md) et [Analytique](app-insights-analytics.md), vous pouvez utiliser l’ID pour trouver facilement tous les événements associés à la requête.

Pour définir l’ID, le plus simple consiste à définir un contexte d’opération à l’aide de ce modèle :

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item - for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.

En plus de définir un contexte d’opération, `StartOperation` crée un élément de télémétrie du type que vous spécifiez, et l’envoie lorsque vous supprimez l’opération ou appelez explicitement `StopOperation`. Si vous utilisez `RequestTelemetry` comme type de télémétrie, alors sa durée est définie sur l’intervalle compris entre le début et la fin.

Les contextes de l’opération ne peuvent pas être imbriqués. S’il existe déjà un contexte d’opération, son ID est associé à tous les éléments de contenu, y compris l’élément créé avec StartOperation.

Dans Recherche, le contexte d’opération est utilisé pour créer la liste d’éléments connexes :

![Éléments connexes](./media/app-insights-api-custom-events-metrics/21.png)

## Suivi des exceptions
Envoyez des exceptions à Application Insights : pour [les compter][metrics] comme indication de la fréquence d'un problème, et pour [examiner des occurrences individuelles][diagnostic]. Les rapports incluent des arborescences des appels de procédure.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

Les Kits de développement logiciel (SDK) interceptent de nombreuses exceptions automatiquement, ce qui vous évite ainsi d’avoir toujours à appeler TrackException explicitement.

* ASP.NET : [écriture d'un code pour intercepter les exceptions](app-insights-asp-net-exceptions.md)
* J2EE : [les exceptions sont interceptées automatiquement](app-insights-java-get-started.md#exceptions-and-request-failures)
* JavaScript : détection automatique. Si vous souhaitez désactiver la collecte automatique, ajoutez une ligne dans l'extrait de code que vous insérez dans vos pages web :
  
    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

## Suivi de traces
Utilisez ceci pour diagnostiquer des problèmes en envoyant une « piste de navigation » à Application Insights. Vous pouvez envoyer des blocs de données de diagnostic et les inspecter dans [Recherche de diagnostic][diagnostic].

[Les adaptateurs de journaux][trace] utilisent cette API pour envoyer des journaux tiers au portail.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Vous pouvez effectuer une recherche dans le contenu du message, mais (contrairement aux valeurs de propriété), vous ne pouvez pas les filtrer.

La limite de taille sur `message` est plus importante que la limite des propriétés. l’un des avantages de TrackTrace est que vous pouvez insérer des données relativement longues dans le message. Par exemple, vous pourriez y encoder des données POST.

Par ailleurs, vous pouvez ajouter un niveau de gravité à votre message. Comme pour les autres données de télémétrie, vous pouvez également ajouter des valeurs de propriété que vous pouvez utiliser pour filtrer ou rechercher différents jeux de traces. Par exemple :

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Cela vous permettrait, dans [Recherche][diagnostic], de filtrer facilement tous les messages d’un niveau de gravité particulier portant sur une base de données particulière.

## Suivi des dépendances
Utilisez cet appel pour suivre les temps de réponse et les taux de réussite des appels vers un bloc de code externe. Les résultats s'affichent dans les graphiques de dépendance sur le portail.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

N’oubliez pas que les Kits de développement logiciel (SDK) de serveur incluent un [module de dépendance](app-insights-dependencies.md) qui détecte certains appels de dépendance et en effectue le suivi automatiquement. C'est notamment le cas des bases de données et des API REST. Vous devez installer un agent sur votre serveur pour que le module fonctionne. Vous utiliserez cet appel si vous souhaitez effectuer le suivi des appels qui ne sont pas interceptés par le système de suivi automatisé, ou si vous ne souhaitez pas installer l'agent.

Pour désactiver le module de suivi des dépendances standard, modifiez le fichier [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) et supprimez la référence à `DependencyCollector.DependencyTrackingTelemetryModule`.

## Vidage des données
Normalement, le Kit de développement logiciel (SDK) envoie des données à des moments choisis pour minimiser l'impact sur l'utilisateur. Toutefois, dans certains cas vous pouvez vider la mémoire tampon - par exemple, si vous utilisez le Kit de développement logiciel (SDK) dans une application qui s'arrête.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Notez que la fonction est asynchrone pour les canaux en mémoire, mais synchrone si vous choisissez d’utiliser le [canal persistant](app-insights-api-filtering-sampling.md#persistence-channel).

## Utilisateurs authentifiés
Dans une application Web, les utilisateurs sont identifiés par des cookies par défaut. Un utilisateur peut être compté plusieurs fois s’il accède à votre application à partir d’un autre ordinateur ou navigateur, ou s’il supprime des cookies.

Mais si les utilisateurs se connectent à votre application, vous pouvez obtenir un nombre plus précis en définissant l’ID de l’utilisateur authentifié dans le code du navigateur :

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

Dans une application MVC Web ASP.NET, par exemple :

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\", "\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Il n’est pas nécessaire d’utiliser le nom de connexion réel de l’utilisateur. Il doit uniquement s’agir d’un ID unique pour cet utilisateur. Il ne doit pas inclure d'espaces ni l'un des caractères suivants : `,;=|`.

L’ID d’utilisateur est également défini dans un cookie de session et envoyé au serveur. Si le Kit de développement logiciel (SDK) de serveur est installé, l’ID d’utilisateur authentifié est envoyé dans le cadre des propriétés de contexte de télémétrie client et serveur, afin que vous puissiez filtrer et rechercher sur celui-ci.

Si votre application regroupe les utilisateurs par comptes, vous pouvez également fournir un identificateur pour ce compte (avec les mêmes restrictions de caractères).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

Dans [Metrics Explorer](app-insights-metrics-explorer.md), vous pouvez créer un graphique qui compte les **Utilisateurs authentifiés** et les **Comptes d'utilisateurs**.

Vous pouvez également [rechercher][diagnostic] les points de données client avec des comptes et des noms d'utilisateurs spécifiques.

## <a name="properties"></a>Filtrez, recherchez et segmentez vos données avec des propriétés
Vous pouvez associer des propriétés et des mesures à vos événements (et également à des mesures, des affichages de page, des exceptions et d'autres données de télémétrie).

Les **propriétés** sont des valeurs de chaîne que vous pouvez utiliser pour filtrer votre télémétrie dans les rapports d'utilisation. Par exemple, si votre application fournit plusieurs jeux, vous pouvez attacher le nom du jeu à chaque événement pour vous permettre de savoir quels sont les jeux les plus populaires.

Il existe une limite d'environ 1 Ko sur la longueur de chaîne. (Si vous souhaitez envoyer d’importants blocs de données, utilisez le paramètre de message de [TrackTrace](#track-trace).)

Les **mesures** sont des valeurs numériques qui peuvent être représentées sous forme graphique. Par exemple, observez s'il existe une augmentation progressive des scores atteints par vos joueurs. Les graphiques peuvent être segmentés par les propriétés envoyées avec l'événement pour vous permettre d’obtenir des graphiques distincts ou empilés pour différents jeux.

Les valeurs de mesures doivent être > = 0 pour s'afficher correctement.

Il existe certaines [limites au nombre de propriétés, de valeurs de propriété et de mesures](#limits) que vous pouvez utiliser.

*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Veillez à ne pas journaliser des informations personnelles dans les propriétés.
> 
> 

**Si vous avez utilisé des mesures**, ouvrez Metrics Explorer et sélectionnez la mesure à partir du groupe personnalisé :

![Ouvrez Metrics Explorer, sélectionnez le graphique puis sélectionnez la mesure](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Si votre mesure n'apparaît pas, ou si l'en-tête personnalisé n'y figure pas, fermez le panneau de sélection et réessayez ultérieurement. L’agrégation des mesures via le pipeline peut parfois prendre une heure.*

**Si vous avez utilisé des propriétés et des mesures**, segmentez la mesure par la propriété :

![Définissez le groupe, puis sélectionnez la propriété sous Grouper par](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

Dans **Recherche de diagnostic**, vous pouvez afficher les propriétés et les mesures des occurrences individuelles d’un événement.

![Sélectionnez une instance, puis sélectionnez « ... »](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Utilisez le champ de recherche pour voir les occurrences de l'événement présentant une valeur de propriété particulière.

![Tapez un terme dans Rechercher](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[En savoir plus sur les expressions de recherche][diagnostic].

#### Autre façon de définir des propriétés et des mesures
Si cela est plus pratique, vous pouvez collecter les paramètres d'un événement dans un objet séparé :

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Ne réutilisez pas la même instance d’élément de télémétrie (`event` dans cet exemple) pour appeler Track*() plusieurs fois. Cela peut provoquer un envoi de données de télémétrie configurées de façon incorrecte.
> 
> 

## <a name="timed"></a> Événements de durée
Vous avez parfois besoin d’obtenir une représentation graphique de la durée nécessaire à la réalisation d’une action. Par exemple, vous souhaitez savoir de combien de temps les utilisateurs ont besoin pour évaluer leurs choix dans un jeu. Il s'agit d'un exemple intéressant de l'utilisation du paramètre de mesure.

*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propriétés par défaut pour la télémétrie personnalisée
Si vous souhaitez définir des valeurs de propriété par défaut pour certains des événements personnalisés que vous écrivez, vous pouvez les définir dans un TelemetryClient. Ils sont associés à chaque élément de télémétrie envoyée à partir de ce client.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");



Les appels de télémétrie individuels peuvent remplacer les valeurs par défaut dans leurs dictionnaires de propriété.

**Pour les clients Web JavaScript**, [utilisez des initialiseurs de télémétrie JavaScript](#js-initializer).

**Pour ajouter des propriétés à toutes les données de télémétrie**, notamment les données des modules de collecte standard, [implémentez `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## Échantillonnage, filtrage et traitement de la télémétrie
Vous pouvez écrire du code pour traiter votre télémétrie avant de l’envoyer à partir du Kit de développement logiciel (SDK). Le traitement inclut les données envoyées par les modules de télémétrie standard, telles que la collection de requêtes HTTP et la collection de dépendances.

* [Ajoutez des propriétés](app-insights-api-filtering-sampling.md#add-properties) aux données de télémétrie en implémentant `ITelemetryInitializer`, par exemple pour ajouter des numéros de version ou des valeurs calculées à partir d’autres propriétés.
* Le [filtrage](app-insights-api-filtering-sampling.md#filtering) permet de modifier ou d’ignorer les données de télémétrie avant leur envoi du Kit de développement logiciel (SDK) en implémentant `ITelemetryProcesor`. Vous contrôlez ce qui est envoyé ou rejeté, mais vous devez tenir compte de l’impact sur vos critères. Suivant la façon dont vous ignorez les éléments, vous risquez de ne plus pouvoir naviguer entre des éléments connexes.
* [L’échantillonnage](app-insights-api-filtering-sampling.md#sampling) est une solution intégrée pour réduire le volume des données envoyées à partir de votre application vers le portail. Il n’affecte pas les mesures affichées, ni votre capacité à diagnostiquer les problèmes en naviguant entre des éléments connexes, tels que les exceptions, les requêtes et les affichages de page.

[En savoir plus](app-insights-api-filtering-sampling.md)

## Désactivation de la télémétrie
Pour **arrêter et démarrer dynamiquement** la collecte et la transmission de la télémétrie :

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Pour **désactiver les collecteurs standard sélectionnés** (par exemple, les compteurs de performances, les requêtes HTTP ou les dépendances), supprimez ou commentez les lignes correspondantes dans [ApplicationInsights.config][config]. Par exemple, vous pouvez faire cela si vous souhaitez envoyer vos propres données TrackRequest.

## <a name="debug"></a>Mode Développeur :
Pendant le débogage, il est utile d'avoir votre télémétrie envoyée par le pipeline afin que vous puissiez voir immédiatement les résultats. Vous obtenez également des messages supplémentaires qui vous permettent de suivre tout problème relatif à la télémétrie. Désactivez-les lors de la production, car ils peuvent ralentir votre application.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Définir la clé d'instrumentation pour la télémétrie personnalisée sélectionnée
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a>Clé d'instrumentation dynamique
Pour éviter de mélanger la télémétrie fournie par les environnements de développement, de test et de production, vous pouvez [créer des ressources Application Insights distinctes][create] et modifier leurs clés en fonction de l’environnement.

Au lieu de récupérer la clé d'instrumentation à partir du fichier de configuration, vous pouvez la définir dans votre code. Définissez la clé dans une méthode d'initialisation, par exemple global.aspx.cs dans un service ASP.NET :

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



Dans les pages web, vous pouvez la définir depuis l'état du serveur web au lieu de la coder littéralement dans le script. Par exemple, dans une page web générée dans une application ASP.NET :

*JavaScript dans Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## TelemetryContext
TelemetryClient a une propriété de contexte contenant un certain nombre de valeurs qui sont envoyées avec toutes les données de télémétrie. Elles sont normalement définies par les modules de télémétrie standard, mais vous pouvez également les définir vous-même. Par exemple :

    telemetry.Context.Operation.Name = "MyOperationName";

Si vous définissez une de ces valeurs vous-même, supprimez la ligne appropriée dans [ApplicationInsights.config][config], de sorte que vos valeurs et les valeurs standard ne se mélangent pas.

* **Composant** : identifie l'application et sa version
* **Périphérique** : données du périphérique sur lequel l'application est en cours d'exécution (dans les applications web, il s’agit du serveur ou du périphérique client à partir duquel la télémétrie est envoyée)
* **Clé d’instrumentation** : identifie la ressource d'Application Insights dans Azure où apparaît la télémétrie. Elle est généralement récupérée dans ApplicationInsights.config
* **Emplacement** : identifie l'emplacement géographique du périphérique.
* **Opération** : dans les applications web, il s’agit de la requête HTTP actuelle. Dans d'autres types d'application, vous pouvez définir celle-ci sur les événements regroupés.
  * **ID** : une valeur générée qui met en relation différents événements de manière à ce que vous trouviez les « Éléments associés » lorsque vous inspectez un événement dans la Recherche de diagnostic.
  * **Nom** : un identificateur, généralement l'URL de la requête HTTP.
  * **SyntheticSource** : si elle est non nulle ou vide, cette chaîne indique que la source de la requête a été identifiée en tant que robot ou test web. Par défaut, celle-ci sera exclue des calculs dans Metrics Explorer.
* **Propriétés** : ce sont les propriétés qui sont envoyées avec toutes les données de télémétrie. Elles peuvent être remplacées dans les appels Track* individuels.
* **Session** : identifie la session de l’utilisateur. L'ID est définie sur une valeur générée qui est modifiée lorsque l'utilisateur n'a pas été actif pendant un certain temps.
* **Utilisateur** : informations utilisateur.

## Limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

*Comment puis-je éviter d'atteindre la limite de débit de données ?*

* Utilisez l’[échantillonnage](app-insights-sampling.md).

*Combien de temps les données sont-elles conservées ?*

* Consultez [Rétention de données et confidentialité][data].

## Documents de référence
* [Référence ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Référence Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Référence JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Kit de développement logiciel Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Kit de développement logiciel (SDK) iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## Code du Kit de développement logiciel (SDK)
* [Kit de développement logiciel (SDK) principal ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Packages Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Kit SDK Java](https://github.com/Microsoft/ApplicationInsights-Java)
* [Kit de développement logiciel (SDK) JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Toutes les plateformes](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Questions
* *Quelles exceptions peuvent être lancées par les appels Track\_() ?*
  
    Aucune. Vous n’avez pas besoin de les inclure dans des clauses try-catch. Si le Kit de développement logiciel (SDK) rencontre des problèmes, il enregistrera des messages que vous pourrez consulter dans la sortie de la console de débogage et, si les messages aboutissent, dans la recherche de diagnostic.
* *Existe-t-il une API REST pour obtenir des données à partir du portail ?*
  
    Oui, elle sera bientôt disponible. En attendant, utilisez [l’exportation continue](app-insights-export-telemetry.md).

## <a name="next"></a>Étapes suivantes
[Recherche d'événements et de journaux][diagnostic]

[Exemples et procédures pas à pas](app-insights-code-samples.md)

[Résolution des problèmes][qna]

<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md



<!---HONumber=AcomDC_0914_2016-->