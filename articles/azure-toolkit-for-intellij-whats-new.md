---
title: Nouveautés du kit de ressources Azure pour IntelliJ | Microsoft Docs
description: En savoir plus sur les dernières fonctionnalités du kit de ressources Azure pour IntelliJ.
services: ''
documentationcenter: java
author: rmcmurray
manager: erikre
editor: ''

ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm;asirveda;martinsawicki

---
# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Nouveautés du Kit de ressources Azure pour IntelliJ
## <a name="azure-toolkit-for-intellij-releases"></a>Versions du kit de ressources Azure pour IntelliJ
Cet article contient des informations sur les différentes versions et les dernières mises à jour du kit de ressources Azure pour IntelliJ.

> [!NOTE]
> Il existe également une boîte à outils Azure pour l’environnement de développement Eclipse. Pour plus d’informations, consultez [Kit de ressources Azure pour Eclipse].
> 
> 

### <a name="august-26-2016"></a>26 août 2016
La version d’août 2016 du kit de ressources Azure pour IntelliJ inclut les améliorations suivantes :

* **Distributions personnalisées du JDK**. Le kit de ressources Azure pour IntelliJ prend désormais en charge la spécification et le déploiement d’une version arbitraire du JDK dans votre conteneur Azure WebApp :
  * Outre les JDK fournis par Azure, vous pouvez également choisir parmi une large sélection de versions Zulu OpenJDK mises à disposition sur Azure par Azul Systems.
  * Vous pouvez également spécifier votre propre distribution JDK si vous chargez un fichier ZIP sur votre compte de stockage.
* **Améliorations apportées à l’affichage de l’Explorateur Azure**:
  * Prise en charge de la gestion des Machines Virtuelles suivant le nouveau modèle Azure Resource Manager : vous pouvez lister, créer et supprimer des machines virtuelles Resource Manager sans quitter l’IDE.
  * Prise en charge de la gestion d’objets blob des comptes de stockage à l’aide d’Azure Resource Manager, qui complète les fonctionnalités existantes de gestion des comptes de stockage « Classic ».
* **Microsoft JDBC Driver 6.0 pour SQL Server**. Cette mise à jour inclut le dernier pilote JDBC de Microsoft SQL Server (version 6.0), qui est maintenant inclus en tant que bibliothèque facile à ajouter à vos projets Java, en remplacement de l’ancienne version.

### <a name="june-29-2016"></a>29 juin 2016
La version de juin 2016 du kit de ressources Azure pour IntelliJ inclut les améliorations suivantes :

* **Spécification Java 8**. Le kit de ressources Azure pour IntelliJ requiert désormais Java 8. Cette exigence ne s’applique qu’au kit de ressources, vos applications peuvent continuer à utiliser toutes les versions de Java prises en charge par Azure.
* **Prise en charge des JDK Java les plus récents**. Les dernières versions des JDK Java sont désormais prises en charge par le kit de ressources Azure pour IntelliJ.
* **Prise en charge du Kit de développement logiciel (SDK) Azure v2.9.1**. La dernière version du Kit de développement logiciel (SDK) Azure est désormais le minimum requis pour le Kit de ressources Azure pour IntelliJ.
* **Exemples intégrés**. Le kit de ressources Azure pour IntelliJ comporte désormais plusieurs exemples d’applications qui permettent aux développeurs de se lancer.
* **Intégration d’outils HDInsight**. Les outils HDInsight d’Azure sont désormais fournis avec le kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Plug-in Outils HDInsight pour IntelliJ].
* **Débogage à distance des applications web Java**. Le kit de ressources Azure pour IntelliJ prend désormais en charge le débogage à distance des applications web sur Azure App Service.

### <a name="april-12-2016"></a>12 avril 2016
La version d’avril 2016 du kit de ressources Azure pour IntelliJ inclut les améliorations suivantes :

* **Prise en charge du Kit de développement logiciel (SDK) Azure v2.9.0**. La dernière version du Kit de développement logiciel (SDK) Azure est désormais le minimum requis pour le Kit de ressources Azure pour IntelliJ.
* **Diverses améliorations de la convivialité, de la réactivité et des performances liées à la prise en charge d’Azure Web App**. Plusieurs optimisations des performances dans la façon dont le Kit de ressources communique avec Azure donnent une interface utilisateur plus réactive.
* **Possibilité de supprimer un conteneur d’application web existant dans Azure à partir d’IntelliJ**. Le Kit de ressources Azure pour IntelliJ vous permet désormais de supprimer un conteneur web Azure existant sans quitter IntelliJ.

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur les boîtes à outils Azure pour les environnements de développement Java, consultez les liens suivants :

* [Kit de ressources Azure pour Eclipse]
  * [Installation du kit de ressources Azure pour Eclipse]
  * [Créer une application web « Hello World » pour Azure dans Eclipse]
  * [Nouveautés du kit de ressources Azure pour Eclipse]
* [Kit de ressources Azure pour IntelliJ]
  * [Installation du kit de ressources Azure pour IntelliJ]
  * [Créer une application web « Hello World » pour Azure dans IntelliJ]
  * *Nouveautés du kit de ressources Azure pour IntelliJ (cet article)*

Pour plus d’informations sur l’utilisation d’Azure avec Java, consultez le [Centre de développement Java pour Azure].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij.md
[Créer une application web « Hello World » pour Azure dans Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Créer une application web « Hello World » pour Azure dans IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installation du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installation du kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Nouveautés du kit de ressources Azure pour Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Nouveautés du Kit de ressources Azure pour IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centre de développement Java pour Azure]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in Outils HDInsight pour IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md



<!--HONumber=Oct16_HO2-->


