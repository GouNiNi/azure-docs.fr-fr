---
title: Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP
description: Découvrez comment télécharger et installer le Kit de développement logiciel (SDK) Azure pour PHP.
documentationcenter: php
services: app-service\web
author: allclark
manager: douge
editor: ''

ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 06/01/2016
ms.author: allclark;yaqiyang

---
# Téléchargement du Kit de développement logiciel (SDK) Azure pour PHP
## Vue d'ensemble
Le Kit de développement logiciel (SDK) Azure pour PHP inclut des composants qui vous permettent de développer, de déployer et de gérer des applications PHP pour Azure. Il inclut plus précisément les éléments suivants :

* **Bibliothèques clientes PHP pour Azure**. L'interface de ces bibliothèques de classes permet d'accéder aux fonctionnalités Azure, telles que les services de gestion des données et les services cloud.  
* **Interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)**. Cet ensemble de commandes permet de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. L’interface de ligne de commande Azure fonctionne sur toutes les plateformes, dont Mac, Linux et Windows.
* **Azure PowerShell (Windows uniquement)**. Cet ensemble de cmdlets PowerShell permet de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines.
* **Émulateurs Azure (Windows uniquement)**. Les émulateurs de stockage et de calcul sont des émulateurs locaux des services cloud et de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure fonctionnent uniquement sur Windows.

Les sections ci-dessous présentent les procédures de téléchargement et d'installation des composants décrits plus haut.

Les instructions de cette rubrique partent du principe que [PHP][install-php] est installé.

> [!NOTE]
> Vous devez disposer de PHP 5.5 ou d’une version supérieure pour utiliser les bibliothèques clientes PHP pour Azure.
> 
> 

## Bibliothèques clientes PHP pour Azure
Les bibliothèques clientes PHP pour Azure fournissent une interface permettant d'accéder aux fonctionnalités Azure, telles que les services cloud et de gestion des données à partir d'un système d'exploitation. Ces bibliothèques peuvent être installées via le compositeur.

Pour plus d'informations sur l'utilisation des bibliothèques clientes PHP pour Azure, consultez les pages [Utilisation du service BLOB][blob-service], [Utilisation du service de Table][table-service] et [Utilisation du service de File d'attente][queue-service].

### Installation via Composer
1. [Installez Git][install-git].

    > [AZURE.NOTE] Sous Windows, vous devez aussi ajouter l’exécutable Git à votre variable d’environnement PATH.

1. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez-y le code suivant :
   
        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }
2. Téléchargez **[composer.phar][composer-phar]** à la racine du projet.
3. Ouvrez une invite de commandes et exécutez cette commande à la racine du projet :
   
        php composer.phar install

## Azure PowerShell et émulateurs Azure
Azure PowerShell est un ensemble de cmdlets PowerShell permettant de déployer et de gérer les services Azure, tels que Cloud Services et Virtual Machines. Les émulateurs de stockage Azure sont des émulateurs des services cloud et de gestion des données qui vous permettent de tester une application localement. Ces composants sont pris en charge uniquement par Windows.

Pour installer Azure PowerShell et les émulateurs Azure, il est recommandé d'utiliser [Microsoft Web Platform Installer][download-wpi]. Notez que vous pouvez également installer d'autres composants de développement, tels que PHP, SQL Server, les pilotes Microsoft pour SQL Server pour PHP et WebMatrix.

Pour plus d'informations sur l'utilisation d'Azure PowerShell, consultez la page [Utilisation d'Azure PowerShell][powershell-tools].

## Interface de ligne de commande Azure
L’interface de ligne de commande Azure est un ensemble de commandes permettant de déployer et de gérer des services Azure, tels que Sites Web Azure et Azure Virtual Machines. Pour plus d'informations sur l'installation de l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](xplat-cli-install.md).

## Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=AcomDC_0601_2016-->