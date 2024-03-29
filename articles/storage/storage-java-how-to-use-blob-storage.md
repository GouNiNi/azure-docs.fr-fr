---
title: Utilisation du stockage d’objets blob Azure à partir de Java | Microsoft Docs
description: Stockez des données non structurées dans le cloud avec Azure Blob Storage (stockage d’objets).
services: storage
documentationcenter: java
author: rmcmurray
manager: wpickett
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/11/2016
ms.author: jwillis;robmcm

---
# Utilisation du stockage d'objets blob à partir de Java
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## Vue d'ensemble
Le stockage d’objets blob Azure est un service qui stocke des données non structurées dans le cloud en tant qu’objets/blobs. Ce service peut stocker tout type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d’installation d’application. Le stockage d’objets blob est également appelé Blob Storage.

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob Microsoft Azure. Les exemples sont écrits en Java et utilisent le [Kit de développement logiciel (SDK) Azure Storage pour Java][Kit de développement logiciel (SDK) Azure Storage pour Java]. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#Next-Steps).

> [!NOTE]
> un Kit de développement logiciel (SDK) est disponible pour les développeurs qui utilisent Azure Storage sur des appareils Android. Pour plus d'informations, consultez la page [Kit de développement logiciel (SDK) Azure Storage pour Android][Kit de développement logiciel (SDK) Azure Storage pour Android].
> 
> 

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’une application Java
Dans cet article, vous allez utiliser des fonctionnalités de stockage qui peuvent être exécutées dans une application Java en local ou dans le code d’un rôle Web ou d’un rôle de travail dans Azure.

Pour ce faire, vous devez installer le Kit de développement Java (JDK) et créer un compte Azure Storage dans votre abonnement Azure. Vous devez ensuite vérifier que votre système de développement répond à la configuration minimale requise et aux dépendances répertoriées dans le référentiel [Kit de développement logiciel (SDK) Azure Storage pour Java][Kit de développement logiciel (SDK) Azure Storage pour Java] sur GitHub. Si tel est le cas, vous pouvez suivre les instructions relatives au téléchargement et à l'installation des bibliothèques Azure Storage pour Java sur votre système à partir du référentiel. Une fois ces tâches effectuées, vous pouvez créer une application Java utilisant les exemples de cet article.

## Configurer votre application pour accéder au stockage d’objets blob
Ajoutez les instructions d’importation suivantes au début du fichier Java dans lequel vous voulez utiliser des API de stockage Azure pour accéder aux objets blob.

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## Configurer une chaîne de connexion au stockage Azure
Un client de stockage Azure utilise une chaîne de connexion au stockage pour stocker des points de terminaison et des informations d’identification permettant d’accéder aux services de gestion des données. Lors de l’exécution d’une application cliente, vous devez spécifier la chaîne de connexion au stockage au format suivant, en indiquant le nom de votre compte de stockage et sa clé d’accès primaire, correspondant aux valeurs *AccountName* et *AccountKey*, sur le [portail Azure](https://portal.azure.com). Cet exemple montre comment déclarer un champ statique pour qu’il contienne la chaîne de connexion.

    // Define the connection-string with your values
    public static final String storageConnectionString =
        "DefaultEndpointsProtocol=http;" +
        "AccountName=your_storage_account;" +
        "AccountKey=your_storage_account_key";

Dans une application exécutée au sein d'un rôle dans Microsoft Azure, cette chaîne peut être stockée dans le fichier de configuration de service *ServiceConfiguration.cscfg* et elle est accessible en appelant la méthode **RoleEnvironment.getConfigurationSettings**. L’exemple suivant récupère la chaîne de connexion auprès d’un élément **Setting** nommé *StorageConnectionString* dans le fichier de configuration du service.

    // Retrieve storage account from connection-string.
    String storageConnectionString =
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Les exemples ci-dessous partent du principe que vous avez utilisé l’une de ces deux méthodes pour obtenir la chaîne de connexion de stockage.

## Créer un conteneur
Un objet **CloudBlobClient** vous permet d’obtenir les objets de référence des conteneurs et objets blob. Le code suivant crée un objet **CloudBlobClient**.

> [!NOTE]
> D’autres méthodes permettent de créer des objets **CloudStorageAccount**. Pour plus d’informations, consultez la section **CloudStorageAccount** dans la page [Référence du Kit de développement logiciel (SDK) du client Azure Storage].
> 
> 

[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Utilisez l'objet **CloudBlobClient** pour obtenir une référence pointant vers le conteneur à utiliser. Si le conteneur n'existe pas, vous pouvez le créer en utilisant la méthode **createIfNotExists** ; sinon, le conteneur existant est renvoyé. Par défaut, le nouveau conteneur est privé. Vous devez donc indiquer votre clé d’accès au stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Get a reference to a container.
       // The container name must be lower case
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Create the container if it does not exist.
        container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Optionnel : configuration d’un conteneur pour un accès public
Par défaut, les autorisations d’un conteneur sont configurées pour un accès privé. Cependant, vous pouvez aisément les configurer pour permettre un accès public en lecture seule pour tous les internautes :

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## Charger un objet blob dans un conteneur
Pour télécharger un fichier vers un objet blob, obtenez une référence de conteneur et utilisez-la pour obtenir une référence d'objet blob. Dès lors que vous disposez d'une référence d'objet blob, vous pouvez télécharger un flux vers cet objet. Si l'objet blob n'existe pas, cette opération entraîne sa création. S'il existe, il est remplacé. Cet exemple de code illustre ce point, en supposant que le conteneur existe.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

        // Create or overwrite the "myimage.jpg" blob with contents from a local file.
        CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
        File source = new File(filePath);
        blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Création d’une liste d’objets blob dans un conteneur
Pour créer une liste d'objets blob dans un conteneur, commencez par obtenir une référence pointant vers un conteneur comme pour le téléchargement d'un objet blob. Vous pouvez utiliser la méthode **listBlobs** du conteneur avec une boucle **for**. Le code suivant génère l'URI de chaque objet blob d'un conteneur sur la console.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

        // Loop over blobs within the container and output the URI to each of them.
        for (ListBlobItem blobItem : container.listBlobs()) {
           System.out.println(blobItem.getUri());
       }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Notez que les noms que vous attribuez aux objets blob peuvent inclure des informations de chemin d’accès. Vous obtenez alors une structure de répertoires virtuels que vous pouvez organiser et parcourir de la même manière qu’un système de fichiers traditionnel. Notez que la structure de répertoires est uniquement virtuelle : les seules ressources disponibles dans le stockage d’objets blob sont des conteneurs et des objets blob. Toutefois, la bibliothèque cliente fournit un objet **CloudBlobDirectory** pour faire référence à un répertoire virtuel et simplifier le processus d’utilisation des objets blob organisés de cette façon.

Par exemple, vous pouvez avoir un conteneur nommé « photos », dans lequel vous pouvez télécharger des objets blob nommés « rootphoto1 », « 2010/photo1 », « 2010/photo2 » et « 2011/photo1 ». Vous créez ainsi virtuellement les répertoires « 2010 » et « 2011 » dans le conteneur « photos ». Lorsque vous appelez la méthode **listBlobs** pour le conteneur « photos », la collection renvoyée contient les objets **CloudBlobDirectory** et **CloudBlob** qui représentent les répertoires et objets blob contenus au niveau supérieur. Dans ce cas, les répertoires « 2010 » et « 2011 » et la photo « rootphoto1 » sont renvoyés. Vous pouvez utiliser l'opérateur **instanceof** pour différencier ces objets.

Vous pouvez également transmettre les paramètres à la méthode **listBlobs** avec le paramètre **useFlatBlobListing** défini sur true. Cela permet de renvoyer chaque objet blob, indépendamment du répertoire. Pour plus d’informations, consultez la section **CloudBlobContainer.listBlobs** dans la page [Référence du Kit de développement logiciel (SDK) du client Azure Storage].

## Téléchargement d’un objet blob
Pour télécharger des objets blob, procédez comme pour le chargement d'un objet blob afin d'obtenir une référence d'objet blob. Dans l'exemple de chargement, vous avez appelé la méthode upload sur l'objet blob. Dans l'exemple suivant, appelez la méthode download pour transférer les contenus d'objets blob vers un objet de flux tel que **FileOutputStream** pouvant être utilisé pour rendre l'objet blob persistant dans un fichier local.

    try
    {
        // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Loop through each blob item in the container.
       for (ListBlobItem blobItem : container.listBlobs()) {
           // If the item is a blob, not a virtual directory.
           if (blobItem instanceof CloudBlob) {
               // Download the item and save it to a file with the same name.
                CloudBlob blob = (CloudBlob) blobItem;
                blob.download(new FileOutputStream("C:\\mydownloads\" + blob.getName()));
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Supprimer un objet blob
Pour supprimer un objet blob, obtenez une référence d'objet blob et appelez la méthode **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Retrieve reference to a blob named "myimage.jpg".
       CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

       // Delete the blob.
       blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Suppression d’un conteneur d’objets blob
Enfin, pour supprimer un conteneur d’objets blob, obtenez sa référence, puis appelez la méthode **deleteIfExists**.

    try
    {
       // Retrieve storage account from connection-string.
       CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

       // Create the blob client.
       CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

       // Retrieve reference to a previously created container.
       CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

       // Delete the blob container.
       container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Étapes suivantes
Maintenant que vous connaissez les bases du stockage d’objets blob, consultez les liens suivants pour apprendre à exécuter des tâches de stockage plus complexes.

* [Kit de développement logiciel (SDK) Azure Storage pour Java][Kit de développement logiciel (SDK) Azure Storage pour Java]
* [Référence du Kit de développement logiciel (SDK) du client Azure Storage][Référence du Kit de développement logiciel (SDK) du client Azure Storage]
* [API REST d’Azure Storage][API REST d’Azure Storage]
* [Blog de l'équipe Azure Storage][Blog de l'équipe Azure Storage]

Pour plus d’informations, consultez également le [Centre pour développeurs Java](/develop/java/).

[Azure SDK for Java]: http://go.microsoft.com/fwlink/?LinkID=525671
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Java]: https://github.com/azure/azure-storage-java
[Kit de développement logiciel (SDK) Azure Storage pour Android]: https://github.com/azure/azure-storage-android
[Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[Référence du Kit de développement logiciel (SDK) du client Azure Storage]: http://dl.windowsazure.com/storage/javadoc/
[API REST d’Azure Storage]: https://msdn.microsoft.com/library/azure/dd179355.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/

<!---HONumber=AcomDC_0928_2016-->