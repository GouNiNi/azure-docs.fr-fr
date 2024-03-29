---
title: Débogage de travaux U-SQL | Microsoft Docs
description: 'Apprenez à déboguer un vertex U-SQL ayant échoué à l’aide de Visual Studio. '
services: data-lake-analytics
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Déboguer du code C# dans U-SQL pour les tâches Data Lake Analytics
Apprenez à déboguer des travaux U-SQL ayant échoué en raison de bogues dans le code utilisateur à l’aide des outils Azure Data Lake Visual Studio.

L’outil Visual Studio vous permet de télécharger le code compilé et les données du vertex nécessaires à partir du cluster pour tracer et déboguer des travaux ayant échoué.

Les systèmes de Big Data fournissent généralement un modèle d’extensibilité via des langages tels que Java, C#, Python, etc. Plusieurs de ces systèmes fournissent des informations de débogage limitées, ce qui rend les erreurs difficiles à déboguer dans le code personnalisé. Les derniers outils de Visual Studio sont fournis avec une fonctionnalité appelée « Échec de débogage du vertex ». À l’aide de cette fonctionnalité, vous pouvez télécharger les données d’exécution à partir d’Azure sur une station de travail locale afin de pouvoir déboguer le code C# personnalisé posant problème en utilisant le même runtime et les mêmes données d’entrée à partir du cloud. Une fois les problèmes résolus, vous pouvez réexécuter le code révisé dans Azure à partir des outils.

Pour une présentation vidéo de cette fonctionnalité, consultez la page [Déboguer votre code personnalisé dans Azure Data Lake Analytics](https://mix.office.com/watch/1bt17ibztohcb).

> [!NOTE]
> Visual Studio peut se bloquer si vous n’avez pas les deux mises à niveau Windows suivantes : [Microsoft Visual C++ 2015 Redistributable Update 2](https://www.microsoft.com/download/details.aspx?id=51682), [Universal C Runtime pour Windows](https://www.microsoft.com/download/details.aspx?id=50410&wa=wsignin1.0).
> 
> 

## Composants requis
* Avoir parcouru l’article [Prise en main](data-lake-analytics-data-lake-tools-get-started.md).

## Création et configuration de projets de débogage
Lorsque vous ouvrez un travail ayant échoué dans l’outil Data Lake Visual Studio, vous obtenez une alerte. Les informations détaillées sur l’erreur s’affichent dans l’onglet Erreur et dans la barre d’alerte jaune en haut de la fenêtre.

![Télécharger le vertex Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

**Pour télécharger le vertex et créer une solution de débogage**

1. Dans Visual Studio, ouvrez un travail U-SQL ayant échoué.
2. Cliquez sur **Télécharger** pour télécharger toutes les ressources et les flux d’entrée requis. Cliquez sur **Réessayer** si le téléchargement échoue.
3. Cliquez sur **Ouvrir** une fois le téléchargement terminé pour créer un projet de débogage local. Une nouvelle solution Visual Studio appelée **VertexDebug** avec un projet vide appelé **LocalVertexHost** est créée.

Si les opérateurs définis par l’utilisateur sont utilisés dans le code U-SQL en arrière-plan (Script.usql.cs), vous devez créer un projet en C# de bibliothèque de classes avec le code des opérateurs définis par l’utilisateur et inclure le projet dans la solution VertexDebug.

Si vous avez enregistré des assemblys .dll dans votre base de données Data Lake Analytics, vous devez ajouter le code source des assemblys à la solution VertexDebug.

Si vous avez créé une bibliothèque de classes C# distincte pour votre code U-SQL et les assemblys .dll inscrits dans votre base de données Data Lake Analytics, vous devez ajouter le projet C# source des assemblys à la solution VertexDebug.

Dans certains cas rares, vous utilisez les opérateurs définis par l’utilisateur dans le code U-SQL suivant le fichier (Script.usql.cs) dans la solution d’origine. Si vous souhaitez que cela fonctionne, vous devez créer une bibliothèque C# contenant le code source et modifier le nom de l’assembly sur celui enregistré dans le cluster. Vous pouvez obtenir le nom de l’assembly enregistré dans le cluster en vérifiant que le script est en cours d’exécution dans le cluster. Vous pouvez le faire en ouvrant le travail U-SQL et en cliquant sur « script » dans le volet de travail.

**Pour configurer la solution**

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet C# que vous venez de créer, puis cliquez sur **Propriétés**.
2. Définissez le chemin de sortie sur le chemin du répertoire de travail du projet LocalVertexHost. Vous pouvez obtenir le chemin du répertoire de travail du projet LocalVertexHost par le biais des propriétés de LocalVertexHost.
3. Générez votre projet C# afin de placer le fichier .pdb dans le répertoire de travail du projet LocalVertexHost, ou vous pouvez copier manuellement le fichier .pdb dans ce dossier.
4. Dans **Paramètres d’exception**, vérifiez les exceptions Common Language Runtime :

![Configuration de Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

## Débogage du travail
Une fois que vous avez créé une solution de débogage en téléchargeant le vertex et une fois l’environnement configuré, vous pouvez commencer à déboguer votre code U-SQL.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **LocalVertexHost** que vous venez de créer, pointez sur **Déboguer**, puis cliquez sur **Démarrer une nouvelle instance**. Le projet LocalVertexHost doit être défini comme projet de démarrage. Le message suivant peut s’afficher pour la première fois. Vous pouvez l’ignorer. L’affichage de l’écran de débogage peut prendre jusqu'à une minute.
   
   ![Avertissement Visual Studio pour le débogage U-SQL Azure Data Lake Analytics](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)
2. Utilisez votre expérience de débogage basée sur Visual Studio (watch, variables, etc.) pour résoudre le problème.
3. Une fois que vous avez identifié un problème, corrigez le code, puis régénérez le projet C# avant de le tester à nouveau et ce, jusqu’à ce que tous les problèmes soient résolus. Une fois le débogage effectué correctement, la fenêtre de sortie affiche le message suivant
   
     The Program ‘LocalVertexHost.exe’ has exited with code 0 (0x0).

## Renvoyer le travail
Une fois que vous avez terminé de déboguer votre code U-SQL, vous pouvez renvoyer le travail ayant échoué.

1. Enregistrez les nouveaux assemblys .dll dans votre base de données ADLA.
   
   1. À partir de l’Explorateur de serveurs/Explorateur cloud dans l’outil Data Lake Visual Studio, développez le nœud **Bases de données**
   2. Cliquez avec le bouton droit sur Assemblys pour enregistrer les assemblys.
   3. Enregistrez vos nouveaux assemblys .dll dans la base de données ADLA.
2. Ou copiez votre code C# dans le code script.usql.cs--C# en arrière-plan du fichier.
3. Renvoyez le travail.

## Étapes suivantes
* [Didacticiel : Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
* [Didacticiel : Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Développer des opérateurs définis par l’utilisateur U-SQL pour des travaux Azure Data Lake Analytics](data-lake-analytics-u-sql-develop-user-defined-operators.md)

<!---HONumber=AcomDC_0914_2016-->