---
title: Déployer un service web Machine Learning | Microsoft Docs
description: Comment convertir une expérience de formation en expérience prédictive, la préparer en vue de son déploiement, puis la déployer en tant que service web Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: garye

---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Déployer un service web Azure Machine Learning
Microsoft Azure Machine Learning vous permet de générer, tester et déployer des solutions d’analyse prédictive.

D'un point de vue très général, cela s'effectue en trois étapes :

* **[Créez une expérience d'apprentissage]** : Azure Machine Learning Studio est un environnement de développement visuel collaboratif qui vous permet d'effectuer l'apprentissage et de tester un modèle d'analyse prédictive à l'aide de données d'apprentissage que vous fournissez.
* **[Convertissez-la en expérience prédictive]** : lorsque votre modèle a été formé avec des données existantes et que vous êtes prêt à l’utiliser pour la notation de nouvelles données, vous préparez et simplifiez votre expérience prédictive.
* **Déployez-la en tant que service web** : vous pouvez déployer votre expérience prédictive en tant que [nouveau service web] ou [service web classique] Azure. Les utilisateurs peuvent envoyer des données à votre modèle et recevoir des prédictions de votre modèle.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Créez une expérience d'apprentissage
Pour effectuer l'apprentissage d'un modèle d'analyse prédictive, utilisez Azure Machine Learning Studio pour créer une expérience d'apprentissage dans laquelle vous incluez plusieurs modules pour charger des données d'apprentissage, préparer les données en fonction de vos besoins, appliquer des algorithmes d'apprentissage automatique et évaluer les résultats. Vous pouvez effectuer une itération sur une expérience et essayer des algorithmes d'apprentissage automatique différents pour comparer et évaluer les résultats.

Le processus de création et gestion d’expériences d’apprentissage est traité de manière plus approfondie dans d’autres sections. Pour plus d’informations, voir les articles suivants :

* [créez une expérience simple dans Azure Machine Learning Studio](machine-learning-create-experiment.md)
* [développez une solution prédictive avec Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)
* [importez vos données d'apprentissage dans Azure Machine Learning Studio](machine-learning-data-science-import-data.md)
* [gérez des itérations d'expériences dans Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Convertir l'expérience de formation en expérience prédictive
Après avoir formé votre modèle, vous êtes prêt à convertir votre expérience d’apprentissage en expérience prédictive pour noter des nouvelles données.

En la convertissant en expérience prédictive, vous préparez votre modèle formé à un déploiement en tant que service web de notation. Les utilisateurs du service web peuvent envoyer des données d’entrée à votre modèle, qui leur renvoie les résultats de sa prédiction. Lorsque vous convertissez l’expérience en expérience prédictive, tenez compte du mode d’utilisation de votre modèle par les autres utilisateurs.

Pour convertir votre expérience de formation en expérience prédictive, cliquez sur **Exécuter** en bas du canevas de l’expérience, cliquez sur **Configurer le service web**, puis sélectionnez **Service web prédictif**.

![Convertir une expérience de notation](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Pour plus d’informations sur la façon d’effectuer cette conversion, voir [Conversion d’une expérience de formation Machine Learning en expérience prédictive](machine-learning-convert-training-experiment-to-scoring-experiment.md).

Les étapes suivantes décrivent le déploiement d’une expérience prédictive en tant que nouveau service web. Vous pouvez également déployer l’expérience en tant que service web classique.

## <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Déployer l’expérience prédictive en tant que nouveau service web
À présent que l’expérience prédictive a été correctement préparée, vous pouvez la déployer en tant que service web Azure. Le service web permet aux utilisateurs d’envoyer des données à votre modèle qui leur retourne ses prédictions.

Pour déployer votre expérience prédictive, cliquez sur **Exécuter** en bas de la zone de dessin de l’expérience. Une fois l’expérience terminée, cliquez sur **Déployer le service web**, puis sélectionnez **Déployer le service web [Nouveau]**.  La page de déploiement du portail de service web Machine Learning s’ouvre. 

### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Page d’expérience de déploiement du portail du service web Machine Learning
Dans la page de l’expérience de déploiement, entrez le nom du service web.
Sélectionnez un plan de tarification. Si vous disposez d’un plan de tarification existant, vous pouvez le sélectionner. Sinon, vous devez créer un nouveau plan de tarification pour le service. 

1. Dans la liste déroulante **Plan tarifaire**, sélectionnez un plan existant ou choisissez l’option **Select new plan** (Sélectionner un nouveau plan).
2. Dans **Plan Name**, (Nom du plan), tapez un nom qui identifiera le plan sur votre facture.
3. Sélectionnez un des **niveaux de plans mensuels**. Les niveaux de plan s’appliquent par défaut aux plans de votre région et votre service web est déployé dans cette région.

Cliquez sur **Déployer** pour ouvrir la page **Démarrage rapide** de votre service web.

La page de démarrage rapide du service web vous donne accès et vous fournit des conseils sur les tâches les plus courantes que vous allez effectuer après la création d’un service web. À partir de là, vous pouvez facilement accéder à la fois à la page Test et à la page Utiliser.

<!-- ![Deploy the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-web-service"></a>Tester votre service Web
Pour tester votre nouveau service web, cliquez sur **Tester le service web** dans la section des tâches courantes. Dans la page de test, vous pouvez tester votre service web en tant que Service de requête-réponse (RRS) ou Service d’exécution par lots (BES). 

La page de test RRS affiche les entrées, les sorties et tous les paramètres globaux que vous avez définis pour l’expérience. Pour tester le service web, vous pouvez entrer manuellement les valeurs appropriées pour les entrées ou fournir un fichier au format CSV contenant les valeurs de test. 

Pour tester le service en tant que Service de requête-réponse (RRS), en mode d’affichage liste, entrez les valeurs appropriées pour les entrées et cliquez sur **Test Request-Response**(Tester la requête-réponse). Vos résultats de prédiction s’affichent dans la colonne de sortie à gauche de la page.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Pour tester votre service d’exécution de lots (BES), cliquez sur **Lot**. Sur la page de test BES, cliquez sur Parcourir en dessous de votre entrée et sélectionnez un fichier CSV contenant les exemples de valeurs appropriés. Si vous n’avez pas de fichier CSV et que vous avez créé votre expérience prédictive à l’aide de Machine Learning Studio, vous pouvez télécharger le jeu de données à utiliser pour votre expérience prédictive.

Pour télécharger le jeu de données, ouvrez Machine Learning Studio. Ouvrez votre expérience prédictive et cliquez avec le bouton droit sur l’entrée correspondant à votre expérience. Dans le menu contextuel, sélectionnez **Jeu de données**, puis **Télécharger**. 

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-7-mls-download.png)

Cliquez sur **Test**. L’état de votre travail de traitement par lots s’affiche à droite sous **Test Batch Jobs**(Travaux de test de traitement par lots).

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the Web service](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)-->

Dans la page **CONFIGURATION**, vous pouvez modifier la description et le titre, mettre à jour la clé du compte de stockage et activer les exemples de données pour votre service web.

![Configurer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Après avoir déployé le service web, vous pouvez :

* **y accéder** via l’API du service web ;
* **le gérer** via le portail de services web Azure Machine Learning ou le portail Azure Classic ; 
* **le mettre à jour** si vous modifiez votre modèle.

### <a name="access-the-web-service"></a>Accéder au service web
Après avoir déployé votre service web à partir de Machine Learning Studio, vous pouvez envoyer des données au service et recevoir des réponses par programme.

La page **Utiliser** fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Elle contient notamment la clé API pour activer l’accès autorisé au service. 

Pour plus d’informations sur l’accès à un service web Machine Learning, voir [Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-Web-services.md).

### <a name="manage-your-new-web-service"></a>Gérer votre nouveau service web
Vous pouvez gérer vos services web classiques via le portail des services web Machine Learning. Dans la [page principale du portail](https://services.azureml-test.net/), cliquez sur **Services web**. Dans la page Services web, vous pouvez supprimer ou copier un service. Pour surveiller un service spécifique, cliquez sur le service, puis sur **Tableau de bord**. Pour surveiller les travaux de traitement par lots associés au service web, cliquez sur **Batch Request Log**(Journal de demande de traitement par lots).

## <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Déployer l’expérience prédictive sous la forme d’un service web classique
À présent que l’expérience prédictive est suffisamment préparée, vous pouvez la publier en tant que service web Azure. Le service web permet aux utilisateurs d’envoyer des données à votre modèle qui leur retourne ses prédictions.

Pour déployer votre expérience prédictive, cliquez sur **Exécuter** en bas du canevas de l’expérience, puis cliquez sur **Déployer le service web**. Le service web est configuré et vous êtes placé dans le tableau de bord du service web.

![Déployer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Pour tester le service web, cliquez sur le lien **Test** dans le tableau de bord du service web. Une boîte de dialogue s'affiche pour vous demander les données d'entrée du service. Il s'agit des colonnes attendues par l'expérience de notation. Entrez un jeu de données, puis cliquez sur **OK**. Les résultats générés par le service web sont affichés au bas du tableau de bord.

Vous pouvez cliquer sur le lien d’aperçu **Test** pour tester votre service dans le portail des services web Azure Machine Learning, comme indiqué précédemment dans la section relative au nouveau service web.

![Tester le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Dans la page **CONFIGURATION**, vous pouvez modifier le nom d’affichage du service et lui donner une description. Le nom et la description s’affichent dans le [portail Azure Classic](http://manage.windowsazure.com/) où vous gérez vos services web.

Vous pouvez fournir une description de vos données d’entrée, de vos données de sortie et des paramètres de service web en saisissant une chaîne pour chaque colonne sous **SCHÉMA D’ENTRÉE**, **SCHÉMA DE SORTIE** et **PARAMÈTRE DU SERVICE WEB**. Ces descriptions sont utilisées dans la documentation relative à l’exemple de code fournie pour le service web.

Vous pouvez activer la journalisation pour diagnostiquer toute défaillance que vous observez lors de l’accès à votre service web. Pour plus d’informations, voir [Activation de la journalisation pour les services web Machine Learning](machine-learning-web-services-logging.md).

![Configurer le service web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

Vous pouvez également configurer les points de terminaison pour le service web dans le portail des services web Azure Machine Learning en procédant de façon similaire à la procédure décrite précédemment dans la section relative au nouveau service web. Les options sont différentes. Vous pouvez ajouter ou modifier la description du service, activer la journalisation et activer des exemples de données à des fins de test.

### <a name="access-the-web-service"></a>Accéder au service web
Après avoir déployé votre service web à partir de Machine Learning Studio, vous pouvez envoyer des données au service et recevoir des réponses par programme.

Le tableau de bord fournit toutes les informations dont vous avez besoin pour accéder à votre service web. Par exemple, la clé API est fournie afin de permettre un accès autorisé au service et des pages d'aide API sont fournies pour vous aider à commencer à écrire votre code.

Pour plus d’informations sur l’accès à un service web Machine Learning, voir [Utilisation d’un service web Azure Machine Learning déployé à partir d’une expérience Machine Learning](machine-learning-consume-Web-services.md).

### <a name="manage-the-web-service"></a>Gérer le service web
Vous pouvez effectuer diverses actions pour analyser un service web. Vous pouvez le mettre à jour et le supprimer. Vous pouvez également ajouter des points de terminaison à un service web classique en plus du point de terminaison par défaut créé lors de son déploiement.

Pour plus d’informations, voir [Gérer un espace de travail Azure Machine Learning](machine-learning-manage-workspace.md) et [Gérer un service web à l’aide du portail des services web Azure Machine Learning](machine-learning-manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Web service endpoints using the REST API, see **Azure machine learning Web service endpoints**.
-->

## <a name="update-the-web-service"></a>Mettre à jour le service web
Vous pouvez apporter des modifications à votre service web, par exemple mettre à jour le modèle avec des données d’apprentissage supplémentaires, et le déployer à nouveau, en remplaçant le service web d’origine.

Pour mettre à jour le service web, ouvrez l’expérience prédictive d’origine utilisée pour déployer le service web et effectuez une copie modifiable en cliquant sur **ENREGISTRER SOUS**. Effectuez vos modifications, puis cliquez sur **Déployer le service web**. 

Étant donné que vous avez déjà déployé cette expérience, une invite vous demande si vous souhaitez remplacer (service web classique) ou mettre à jour (nouveau service web) le service existant. Si vous cliquez sur **OUI** ou **Mettre à jour**, le service web existant est arrêté et la nouvelle expérience prédictive est déployée à la place.

> [!NOTE]
> Si vous avez apporté des modifications de configuration dans le service web d’origine, par exemple, si vous saisissez un nouveau nom d’affichage ou une description, vous devrez saisir ces valeurs à nouveau.
> 
> 

Pour mettre à jour votre service web, vous pouvez reformer le modèle à l’aide d’un programme. Pour plus d'informations, consultez la page [Reformation des modèles Machine Learning par programme](machine-learning-retrain-models-programmatically.md).

<!-- internal links -->
[Créez une expérience d'apprentissage]: #create-a-training-experiment
[Convertissez-la en une expérience prédictive]: #convert-the-training-experiment-to-a-predictive-experiment.
[nouveau]: #deploy-the-predictive-experiment-as-a-new-Web-service
[classique]: #deploy-the-predictive-experiment-as-a-new-Web-service
[Accéder]: #access-the-Web-service
[Gérer]: #manage-the-Web-service-in-the-azure-management-portal
[Mettre à jour]: #update-the-Web-service



<!--HONumber=Oct16_HO2-->


