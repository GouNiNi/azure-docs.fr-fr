---
title: Analyse de sentiments Twitter en temps réel avec Stream Analytics | Microsoft Docs
description: Découvrez comment utiliser Stream Analytics pour l’analyse de sentiments Twitter en temps réel. Aide pas à pas allant de la génération d’événements à la gestion des données sur un tableau de bord en direct.
keywords: analyse de tendances twitter en temps réel, analyse de sentiments, analyse des réseaux sociaux, exemple d’analyse de tendances
services: stream-analytics
documentationcenter: ''
author: jeffstokes72
manager: jhubbard
editor: cgronlun

ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2016
ms.author: jeffstok

---
# <a name="social-media-analysis:-real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analyse des médias sociaux : analyse de sentiments Twitter en temps réel dans Azure Stream Analytics
Apprenez à créer une solution d’analyse de sentiments pour l’analyse des réseaux sociaux en intégrant des événements Twitter dans Azure Event Hubs. Vous allez écrire une requête Azure Stream Analytics pour analyser les données. Ensuite, vous allez soit stocker les résultats en vue d’une consultation ultérieure, soit utiliser un tableau de bord et [Power BI](https://powerbi.com/) pour fournir des informations en temps réel.

Les outils d’analyse de réseaux sociaux aident les organisations à comprendre les sujets populaires, les sujets significatifs et les avis apparaissant dans un grand nombre de billets sur les réseaux sociaux. L’analyse de sentiments, aussi appelée *exploration d’opinions*, utilise des outils d’analyse de réseaux sociaux pour déterminer les attitudes envers un produit, une idée, etc. L’analyse de tendances Twitter en temps réel constitue un excellent exemple, car le modèle d’abonnement hashtag vous permet de suivre des mots clés spécifiques et de développer l’analyse de sentiments sur le flux.

## <a name="scenario:-sentiment-analysis-in-real-time"></a>Scénario : analyse de sentiments en temps réel
Une entreprise qui dispose d’un site web de médias souhaite obtenir un avantage sur ses concurrents en présentant des contenus immédiatement pertinents pour ses lecteurs. Elle utilise l’analyse des réseaux sociaux sur des sujets pertinents pour ses lecteurs en effectuant une analyse de sentiments en temps réel des données de Twitter. Pour identifier les tendances en temps réel sur Twitter, l’entreprise doit analyser en temps réel le volume et les sentiments des tweets relatifs aux principaux sujets. Elle a donc essentiellement besoin d’un moteur d’analyse de sentiments basé sur le flux de ce réseau social.

## <a name="prerequisites"></a>Composants requis
* Compte Twitter et [jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)
* [TwitterClient.zip](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip) à partir du Centre de téléchargement Microsoft
* Facultatif : code source pour le client Twitter dans [GitHub](https://aka.ms/azure-stream-analytics-twitterclient)

## <a name="create-an-event-hub-input-and-a-consumer-group"></a>Création d’une entrée de hub d’événements et d’un groupe de consommateurs
L’exemple d’application génère des événements et les transmet vers une instance de hub d’événements (ou simplement « hub d’événement » pour faire plus court). Les hubs d’événements Service Bus constituent la méthode la plus efficace pour la réception d’événements dans Stream Analytics. Consultez la documentation relative aux hubs d’événements dans la [documentation de Service Bus](/documentation/services/service-bus/).

Procédez comme suit pour créer un hub d’événements.

1. Dans le portail Azure, cliquez sur **NOUVEAU** > **APP SERVICES** > **SERVICE BUS** > **HUB D’ÉVÉNEMENTS** > **CRÉATION RAPIDE**, puis entrez un nom, une région et un espace de noms nouveau ou existant.  
2. Nous vous recommandons de faire en sorte que chaque travail Stream Analytics lise les événements à partir d’un seul groupe de consommateurs de hubs d’événements. Nous vous expliquerons comment créer un groupe de consommateurs ultérieurement. Pour plus d’informations sur les groupes de consommateurs, consultez [Vue d’ensemble des hubs d’événements Azure](https://msdn.microsoft.com/library/azure/dn836025.aspx). Pour créer un groupe de consommateurs, accédez au hub d’événements nouvellement créé et cliquez sur l’onglet **GROUPES DE CONSOMMATEURS**, puis sur **CRÉER** en bas de la page, et entrez un nom pour votre groupe de consommateurs.
3. Pour accorder l’accès au hub d’événements, vous devez créer une stratégie d’accès partagé. Cliquez sur l’onglet **CONFIGURER** de votre hub d’événements.
4. Sous **STRATÉGIES D’ACCÈS PARTAGÉ**, créez une stratégie ayant les autorisations **GÉRER**.
   
   ![Stratégies d’accès partagé où vous pouvez créer une stratégie ayant les autorisations Gérer.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-ananlytics-shared-access-policies.png)
5. Cliquez sur **ENREGISTRER** en bas de la page.
6. Accédez au **TABLEAU DE BORD**, cliquez sur **INFORMATIONS DE CONNEXION** en bas de la page, puis copiez et enregistrez les informations de connexion. Utilisez l’icône de copie qui apparaît sous l’icône de recherche.

## <a name="configure-and-start-the-twitter-client-application"></a>Configurer et démarrer l’application client Twitter
Nous vous proposons une application client capable de se connecter aux données de Twitter par le biais des [API Streaming de Twitter](https://dev.twitter.com/streaming/overview) pour collecter les événements Tweet sur un ensemble de sujets paramétrable. L’outil open source [Sentiment140](http://help.sentiment140.com/) est utilisé pour affecter une valeur de sentiment à chaque tweet.

* 0 = négatif
* 2 = neutre
* 4 = positif

Les événements Tweet sont ensuite transmis au hub d’événements.  

Procédez comme suit pour configurer l’application :

1. [Téléchargez la solution TwitterClient](http://download.microsoft.com/download/1/7/4/1744EE47-63D0-4B9D-9ECF-E379D15F4586/TwitterClient.zip).
2. Ouvrez le fichier TwitterClient.exe.config et remplacez oauth_consumer_key, oauth_consumer_secret, oauth_token et oauth_token_secret par les jetons Twitter avec vos valeurs.  
   
   [Procédure de génération d’un jeton d’accès OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens)  
   
   Notez que vous devez créer une application vide pour générer un jeton.  
3. Remplacez les valeurs EventHubConnectionString et EventHubName dans le fichier TwitterClient.exe.config par la chaîne de connexion et le nom de votre hub d’événements. La chaîne de connexion copiée précédemment vous donne la chaîne de connexion et le nom de votre hub d’événements. Par conséquent, pensez à les séparer et à les placer dans le champ approprié. Prenons par exemple la chaîne de connexion suivante :
   
       Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey;EntityPath=yourhub
   
   Le fichier TwitterClient.exe.config doit contenir vos paramètres comme dans l’exemple ci-dessous :
   
       add key="EventHubConnectionString" value="Endpoint=sb://your.servicebus.windows.net/;SharedAccessKeyName=yourpolicy;SharedAccessKey=yoursharedaccesskey"
       add key="EventHubName" value="yourhub"
   
   Il est important de noter que le texte « EntityPath= » n’apparaît **pas** dans la valeur EventHubName.
4. *Facultatif :* définissez les mots clés à rechercher.  Par défaut, cette application recherche « Azure, Skype, XBox, Microsoft, Seattle ».  Si vous le souhaitez, vous pouvez modifier ces mots clés en changeant les valeurs de **twitter_keywords** dans le fichier TwitterClient.exe.config.
5. Exécutez TwitterClient.exe pour démarrer votre application. Vous voyez s’afficher les événements Tweet tandis que les valeurs **CreatedAt**, **Topic** et **SentimentScore** sont transmises à votre hub d’événements.
   
   ![Analyse de sentiments : valeurs SentimentScore transmises à un hub d’événements.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-sentiment-output-to-event-hub.png)

## <a name="create-a-stream-analytics-job"></a>Création d’un travail Stream Analytics
Maintenant que nous avons un flux d’événements Tweet diffusé en temps réel depuis Twitter, nous pouvons configurer un travail Stream Analytics pour analyser ces événements en temps réel.

### <a name="provision-a-stream-analytics-job"></a>Configuration d’un travail Stream Analytics
1. Dans le [portail Azure](https://manage.windowsazure.com/), cliquez sur **NOUVEAU** > **DATA SERVICES** > **STREAM ANALYTICS** > **CRÉATION RAPIDE**.
2. Spécifiez les valeurs suivantes, puis cliquez sur **CRÉER UN TRAVAIL STREAM ANALYTICS**:
   
   * **NOM DU TRAVAIL**: entrez un nom pour le travail.
   * **RÉGION**: sélectionnez la région où vous souhaitez exécuter le travail. Envisagez de placer le travail et le hub d’événements dans la même région pour être certain d’améliorer les performances et de ne pas payer pour un transfert de données entre différentes régions.
   * **COMPTE DE STOCKAGE** : choisissez le compte de stockage que vous souhaitez utiliser pour stocker les données de surveillance de tous les travaux Stream Analytics en cours d’exécution dans cette région. Vous pouvez choisir un compte de stockage existant ou en créer un.
3. Dans le volet gauche, cliquez sur **STREAM ANALYTICS** pour afficher une liste des travaux Stream Analytics.  
   ![Icône du service Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-service-icon.png)
   
   Le nouveau travail est affiché avec l’état **CRÉÉ**. Notez que le bouton **DÉMARRER** situé en bas de la page est désactivé. Avant de pouvoir démarrer le travail, vous devez configurer son entrée, sa sortie et sa requête.

### <a name="specify-job-input"></a>Spécification d'une entrée de travail
1. En haut de la page de votre travail Stream Analytics, cliquez sur **ENTRÉES**, puis sur **AJOUTER UNE ENTRÉE**. La boîte de dialogue qui s’ouvre vous guidera le long d’une procédure de configuration de votre entrée.
2. Sélectionnez **FLUX DE DONNÉES**, puis cliquez avec le bouton droit.
3. Sélectionnez **HUB D’ÉVÉNEMENTS**, puis cliquez avec le bouton droit.
4. Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :
   
   * **ALIAS D’ENTRÉE** : entrez un nom convivial pour cette entrée de travail, comme *TwitterStream*. Notez que vous utiliserez ce nom dans la requête par la suite.
     **HUB D’ÉVÉNEMENTS** : si le hub d’événements que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, sélectionnez l’espace de noms dans lequel est situé le hub d’événements.
     
       Si votre hub d’événements est situé dans un autre abonnement, cliquez sur **Utiliser le hub d’événements à partir d’un autre abonnement** et entrez manuellement **l’ESPACE DE NOMS SERVICE BUS**, le **NOM DU HUB D’ÉVÉNEMENTS**, le **NOM DE LA STRATÉGIE DU HUB D’ÉVÉNEMENTS**, la **CLÉ DE STRATÉGIE DU HUB D’ÉVÉNEMENTS** et le **NOMBRE DE PARTITIONS DU HUB D’ÉVÉNEMENTS**.
   * **NOM DU HUB D’ÉVÉNEMENTS** : sélectionnez le nom du hub d’événements.
   * **NOM DE LA STRATÉGIE DU HUB D’﻿﻿﻿ÉVÉNEMENTS** : sélectionnez la stratégie de hub d’événements créée précédemment dans ce didacticiel.
   * **GROUPE DE CONSOMMATEURS DU HUB D’ÉVÉNEMENTS** : entrez le nom du groupe de consommateurs créé précédemment dans ce didacticiel.
5. Cliquez avec le bouton droit.
6. Spécifiez les valeurs suivantes :
   
   * **FORMAT ﻿﻿﻿DU SÉRIALISEUR D’ÉVÉNEMENT**: JSON
   * **ENCODAGE**: UTF8
7. Cliquez sur la **coche** pour ajouter cette source et vérifier que Stream Analytics peut se connecter au hub d’événements.

### <a name="specify-job-query"></a>Spécification de la requête du travail
Stream Analytics prend en charge un modèle de requête simple et déclaratif pour la description des transformations. Pour plus d’informations sur ce langage, consultez la page [Références sur le langage des requêtes d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ce didacticiel aborde la création et le test de plusieurs requêtes sur des données Twitter.

#### <a name="sample-data-input"></a>Exemples d’entrée de données
Pour appliquer votre requête à des données de travail réelles, vous pouvez utiliser la fonctionnalité **EXEMPLES DE DONNÉES** pour extraire des événements à partir de votre flux de données et créer un fichier .json contenant les événements du test.

1. Sélectionnez l’entrée de votre hub d’événements, puis cliquez sur **EXEMPLES DE DONNÉES** en bas de la page.
2. Dans la boîte de dialogue qui s’affiche, entrez une **HEURE DE DÉBUT** pour le démarrage de la collecte des données et une **DURÉE** afin de déterminer la quantité de données supplémentaires à traiter.
3. Cliquez sur le bouton **DÉTAILS**, puis sur le lien **Cliquez ici** pour télécharger et enregistrer le fichier .json généré.

#### <a name="pass-through-query"></a>Requête directe
Pour commencer, nous allons effectuer une simple requête directe qui projette tous les champs d’un événement.

1. En haut de la page du travail Stream Analytics, cliquez sur **REQUÊTE**.
2. Dans l’éditeur de code, remplacez le modèle de requête initial par le suivant :
   
       SELECT * FROM TwitterStream
   
   Assurez-vous que le nom de la source d’entrée correspond à celui que vous avez spécifié précédemment.
3. Cliquez sur **Test** dans l’éditeur de requête.
4. Accédez à votre exemple de fichier .json.
5. Cliquez sur la **coche** et consultez les résultats affichés sous la définition de la requête.
   
   ![Résultats affichés sous la définition de requête](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sentiment-by-topic.png)

#### <a name="count-of-tweets-by-topic:-tumbling-window-with-aggregation"></a>Nombre de tweets par sujet : fenêtre bascule avec agrégation
Pour comparer le nombre de mentions entre les sujets, nous allons utiliser une [fenêtre bascule (TumblingWindow)](https://msdn.microsoft.com/library/azure/dn835055.aspx) pour obtenir le nombre de mentions par sujet toutes les cinq secondes.

1. Modifiez la requête dans l’éditeur de code comme ceci :
   
       SELECT System.Timestamp as Time, Topic, COUNT(*)
       FROM TwitterStream TIMESTAMP BY CreatedAt
       GROUP BY TUMBLINGWINDOW(s, 5), Topic
   
   Cette requête utilise le mot clé **TIMESTAMP BY** pour spécifier un champ d’horodatage dans la charge utile à utiliser dans le calcul temporel. Si ce champ n’est pas spécifié, l’opération de fenêtrage est réalisée en utilisant l’heure d’arrivée de chaque événement dans le hub d’événements.  Pour en savoir plus, consultez la section « Heure d’arrivée par rapport à l’heure de l’application » de la page [Référence du langage de requête d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).
   
   Cette requête permet également d’accéder à un horodatage pour la fin de chaque fenêtre à l’aide de la propriété **System.Timestamp**.
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.

#### <a name="identify-trending-topics:-sliding-window"></a>Identification des tendances : fenêtre glissante
Pour identifier les tendances, nous allons rechercher des sujets dépassant une valeur de seuil de mention dans un laps de temps donné. Pour les besoins de ce didacticiel, nous allons consulter les rubriques mentionnées plus de 20 fois pendant les 5 dernières secondes en utilisant une [fenêtre glissante (SlidingWindow)](https://msdn.microsoft.com/library/azure/dn835051.aspx).

1. Modifiez la requête dans l’éditeur de code comme ceci : SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions     FROM TwitterStream TIMESTAMP BY CreatedAt     GROUP BY SLIDINGWINDOW(s, 5), topic     HAVING COUNT(*) > 20
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.
   
   ![Sortie de la requête de fenêtre glissante](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-query-output.png)

#### <a name="count-of-mentions-and-sentiment:-tumbling-window-with-aggregation"></a>Nombre de mentions et sentiment : fenêtre bascule avec agrégation
La dernière requête que nous allons tester utilise une **fenêtre bascule (TumblingWindow)** pour obtenir le nombre de mentions et la moyenne, le minimum, le maximum et l’écart type de score de sentiment de chaque sujet toutes les cinq secondes.

1. Modifiez la requête dans l’éditeur de code comme ceci :
   
       SELECT System.Timestamp as Time, Topic, COUNT(*), AVG(SentimentScore), MIN(SentimentScore),
       Max(SentimentScore), STDEV(SentimentScore)
       FROM TwitterStream TIMESTAMP BY CreatedAt
       GROUP BY TUMBLINGWINDOW(s, 5), Topic
2. Cliquez sur **Réexécuter** dans l’éditeur de requête pour afficher les résultats de la requête.
3. Nous allons utiliser cette requête pour notre tableau de bord.  Cliquez sur **ENREGISTRER** en bas de la page.

## <a name="create-output-sink"></a>Création du récepteur de sortie
Maintenant que nous avons défini un flux d’événements, un hub d’événements d’entrée pour la réception des événements et une requête pour effectuer une transformation sur le flux, la dernière étape consiste à définir un récepteur de sortie pour le travail.  Nous allons écrire les événements tweet agrégés à partir de notre requête de travail dans un stockage d’objets blob Azure.  Selon les besoins spécifiques de votre application, vous pouvez également transmettre vos résultats à une base de données SQL Azure, un stockage de tables Azure ou un hub d’événements.

Si vous n’avez pas déjà de conteneur pour le stockage des objets blob, procédez comme suit pour en créer un :

1. Utilisez un compte de stockage existant ou créez-en un en cliquant sur **NOUVEAU** > **DATA SERVICES** > **STOCKAGE** > **CRÉATION RAPIDE**, puis suivez les instructions qui s’affichent.
2. Sélectionnez le compte de stockage, cliquez en haut de la page sur **CONTENEURS**, puis sur **AJOUTER**.
3. Entrez un **NOM** pour votre conteneur et définissez son **ACCÈS** sur **Objet blob public**.

## <a name="specify-job-output"></a>Spécification de la sortie du travail
1. En haut de la page de votre travail Stream Analytics, cliquez sur **SORTIE**, puis sur **AJOUTER UNE SORTIE**. La boîte de dialogue qui s’ouvre vous indique les étapes à suivre pour configurer votre sortie.
2. Sélectionnez **STOCKAGE D’OBJETS BLOB**, puis cliquez avec le bouton droit.
3. Saisissez ou sélectionnez les valeurs suivantes sur la troisième page :
   
   * **ALIAS DE SORTIE**: entrez un nom convivial pour cette sortie de travail.
   * **ABONNEMENT** : si le stockage d’objets blob que vous avez créé est situé dans le même abonnement que le travail Stream Analytics, cliquez sur **Utiliser le compte de stockage de l’abonnement actuel**. Si votre espace de stockage appartient à un autre abonnement, cliquez sur **Utiliser le stockage associé à un autre abonnement** et entrez manuellement les informations des champs **COMPTE DE STOCKAGE**, **CLÉ DU COMPTE DE STOCKAGE** et **CONTENEUR**.
   * **COMPTE DE STOCKAGE**: sélectionnez le nom du compte de stockage.
   * **CONTENEUR**: sélectionnez le nom du conteneur.
   * **PRÉFIXE DU NOM DE FICHIER** : entrez un préfixe de fichier à utiliser lors de l’écriture de la sortie de l’objet blob.
4. Cliquez avec le bouton droit.
5. Spécifiez les valeurs suivantes :
   * **FORMAT ﻿﻿﻿DU SÉRIALISEUR D’ÉVÉNEMENT**: JSON
   * **ENCODAGE**: UTF8
6. Cliquez sur la **coche** pour ajouter cette source et vérifier que Stream Analytics peut se connecter au compte de stockage.

## <a name="start-job"></a>Démarrage du travail
Maintenant que nous avons spécifié une entrée, une requête et une sortie pour le travail Stream Analytics, nous pouvons le démarrer.

1. Sur le **TABLEAU DE BORD** du travail, en bas de la page, cliquez sur **DÉMARRER**.
2. Dans la boîte de dialogue qui s’affiche, cliquez sur **HEURE DE DÉBUT DU TRAVAIL**, puis sur la **coche** en bas de la boîte de dialogue. L’état du travail passe à **Démarrage**, puis à **En cours d’exécution**.

## <a name="view-output-for-sentiment-analysis"></a>Afficher la sortie de l’analyse de sentiments
Une fois que votre travail en cours d’exécution traite le flux Twitter en temps réel, choisissez la façon dont vous souhaitez afficher la sortie de l’analyse de sentiments. Pour afficher la sortie de votre travail en temps réel, utilisez un outil tel que l’[explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) ou [Azure Explorer](http://www.cerebrata.com/products/azure-explorer/introduction). À ce stade, vous pouvez utiliser [Power BI](https://powerbi.com/) pour étendre les fonctionnalités de votre application afin d’ajouter un tableau de bord personnalisé comme celui de la capture d’écran suivante.

![Analyse des médias sociaux : sortie de l’analyse de sentiments Stream Analytics (exploration d’opinions) dans un tableau de bord Power BI.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-output-power-bi.png)

## <a name="get-support"></a>Obtenir de l'aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=Oct16_HO2-->


