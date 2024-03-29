---
title: Modèles d’applications logiques | Microsoft Docs
description: Découvrez comment utiliser les modèles d’application logique prédéfinis pour vous aider à démarrer
author: kevinlam1
manager: dwrede
editor: ''
services: app-service\logic
documentationcenter: ''

ms.service: app-service-logic
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: klam

---
# Modèles d’application logique
## Qu’est-ce qu’un modèle d’application logique ?
Un modèle d’application logique est une application logique prédéfinie que vous pouvez utiliser pour commencer rapidement à créer votre propre flux de travail.

Ces modèles constituent un bon moyen de découvrir les divers modèles qui peuvent être générés à l’aide d’applications logiques. Vous pouvez utiliser ces modèles tels quels ou les adapter à votre situation.

## Vue d’ensemble des modèles disponibles
Il existe de nombreux modèles disponibles actuellement publiés dans la plateforme d’application logique. Certaines catégories d’exemples, ainsi que le type de connecteurs utilisés dans ces dernières sont répertoriés ci-dessous.

### Modèles de cloud d’entreprise
Il s’agit de modèles qui s’intègrent à Dynamics CRM, Salesforce, Box, Azure Blob et d’autres connecteurs pour les besoins de votre cloud d’entreprise. L’organisation de vos prospects et l’enregistrement des données de vos fichiers d’entreprise figurent parmi les possibilités offertes par ces modèles.

### Modèles de pack d’intégration d’entreprise
Configurations des pipelines VETER (valider, extraire, transformer, enrichir, router), réception d’un document EDI X12 via AS2 et sa transformation au format XML, ainsi que le traitement de messages X12 et AS2.

### Modèles de modèles de protocole
Ces modèles se composent d’applications logiques contenant des modèles de protocole comme une demande-réponse via HTTP, ainsi que des intégrations sur FTP et SFTP. Utilisez-les tels qu’ils existent, ou comme base pour la création de modèles de protocole plus complexes.

### Modèles de productivité personnelle
Parmi les modèles permettant d’améliorer la productivité personnelle, figurent des modèles qui définissent des rappels quotidiens, qui transforment des éléments de travail importants en listes de tâches et des tâches fastidieuses en une étape unique d’approbation de l’utilisateur.

### Modèles Cloud clients
Il s’agit de modèles simples qui s’intègrent aux services des médias sociaux, tels que Twitter, Slak et aux messageries, et capables d’en renforcer les initiatives marketing. Parmi ceux-ci figurent également les modèles, tels que la copie dans le cloud qui permettent d’augmenter la productivité en passant moins de temps sur les tâches traditionnellement répétitives.

## Création d’une application logique à l’aide d’un modèle
Pour apprendre à utiliser un modèle d’application logique, accédez au concepteur d’applications logiques. Si vous accédez au concepteur en ouvrant une application logique existante, celle-ci se charge automatiquement dans votre vue du concepteur. Toutefois, si vous créez une nouvelle application logique, l’écran ci-dessous s’affiche. ![](../../includes/media/app-service-logic-templates/template7.png)

À partir de cet écran, vous pouvez choisir de commencer avec une application logique vide ou un modèle prédéfini. Si vous sélectionnez un des modèles, des informations supplémentaires vous sont fournies. Dans cet exemple, nous utilisons le modèle *Lorsqu’un nouveau fichier est créé dans Dropbox, copiez-le dans le modèle OneDrive*. ![](../../includes/media/app-service-logic-templates/template2.png)

Si vous choisissez d’utiliser le modèle, sélectionnez le bouton *utiliser ce modèle*. Vous devez vous connecter à vos comptes basés sur les connecteurs que le modèle utilise. Si vous avez déjà établi une connexion avec ces connecteurs, vous pouvez également choisir de continuer comme indiqué ci-dessous. ![](../../includes/media/app-service-logic-templates/template3.png)

Une fois la connexion établie et après avoir sélectionné *continuer*, l’application logique s’ouvre en mode concepteur. ![](../../includes/media/app-service-logic-templates/template4.png)

Dans l’exemple ci-dessus, comme c’est le cas avec de nombreux modèles, certains des champs de propriété obligatoire peuvent être remplis dans les connecteurs ; toutefois, certains peuvent encore nécessiter une valeur avant de pouvoir déployer correctement l’application logique. Si vous tentez de procéder à un déploiement sans saisir certains des champs manquants, vous en serez averti par un message d’erreur.

Si vous souhaitez revenir à la visionneuse de modèles, sélectionnez le bouton *Modèles* dans la barre de navigation supérieure. Si vous revenez dans la visionneuse de modèles, toute progression non enregistrée sera perdue. Avant de revenir à la visionneuse de modèles, un message d’avertissement vous en informant s’affiche. ![](../../includes/media/app-service-logic-templates/template5.png)

## Déploiement d’une application logique créée à partir d’un modèle
Une fois que vous avez chargé votre modèle et effectué les modifications souhaitées, sélectionnez le bouton d’enregistrement situé dans le coin supérieur gauche. Cela enregistre et publie votre application logique. ![](../../includes/media/app-service-logic-templates/template6.png)

Si vous souhaitez plus d’informations sur l’ajout d’étapes supplémentaires à un modèle d’application logique existant, ou l’apport de modifications générales, consultez [Créer une application logique](app-service-logic-create-a-logic-app.md) pour en savoir plus.

<!---HONumber=AcomDC_0831_2016-->