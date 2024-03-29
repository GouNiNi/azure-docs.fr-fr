---
title: Créer et utiliser une signature d’accès partagé avec Blob Storage | Microsoft Docs
description: Ce didacticiel vous montre comment créer des signatures d’accès partagé en vue d’une utilisation avec Blob Storage et comment les utiliser dans vos applications clientes.
services: storage
documentationcenter: ''
author: tamram
manager: carmonm
editor: tysonn

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2016
ms.author: tamram

---
# <a name="shared-access-signatures,-part-2:-create-and-use-a-sas-with-blob-storage"></a>Signatures d’accès partagé, partie 2 : créer et utiliser une signature d’accès partagé avec Blob Storage
## <a name="overview"></a>Vue d'ensemble
[partie 1](storage-dotnet-shared-access-signature-part-1.md) de ce didacticiel traitait des signatures d'accès partagé et indiquait les meilleures pratiques les concernant. La partie 2 indique comment générer et utiliser les signatures d’accès partagé avec Blob Storage. Les exemples ont été écrits en C# et utilisent la bibliothèque du client Azure Storage pour .NET. Les scénarios traités incluent les points suivants de l'utilisation des signatures d'accès partagé :

* Génération d'une signature d'accès partagé sur un conteneur
* Génération d'une signature d'accès partagé sur un objet blob
* Création d'une stratégie d'accès stockée pour gérer les signatures sur les ressources d'un conteneur
* Test des signatures d'accès partagé à partir d'une application cliente

## <a name="about-this-tutorial"></a>À propos de ce didacticiel
Ce didacticiel traite spécifiquement de la création des signatures d'accès partagé pour les conteneurs et les objets blob en créant deux applications console. La première application console génère des signatures d'accès partagé sur un conteneur et un objet blob. Cette application connaît les clés de compte de stockage. La seconde application console, qui se comporte comme une application cliente, accède aux ressources du conteneur et de l'objet blob en utilisant les signatures d'accès partagé créées avec la première application. Cette application utilise les signatures d’accès partagé uniquement pour authentifier son accès aux ressources du conteneur et de l’objet blob. Elle ne connaît pas les clés de compte.

## <a name="part-1:-create-a-console-application-to-generate-shared-access-signatures"></a>Partie 1 : créer une application console pour générer des signatures d’accès partagé
Commencez par vérifier que la bibliothèque cliente Azure Storage pour .NET est installée. Vous pouvez installer le [package NuGet](http://nuget.org/packages/WindowsAzure.Storage/ "package NuGet") qui contient les assemblys les plus à jour pour la bibliothèque cliente. Cette méthode est recommandée pour garantir que les correctifs les plus récents sont installés. Vous pouvez également télécharger la bibliothèque cliente avec la version la plus récente du [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/).

Dans Visual Studio, créez une application console Windows et nommez-la **GenerateSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll** en utilisant l’une des méthodes suivantes :

* Si vous souhaitez installer le package NuGet, commencez par installer le [Client NuGet](https://docs.nuget.org/consume/installing-nuget). Dans Visual Studio, sélectionnez **Project | Manage NuGet Packages**, effectuez une recherche en ligne sur **Azure Storage** et suivez les instructions d’installation.
* Vous pouvez également rechercher les assemblys dans votre installation du Kit de développement logiciel (SDK) Azure et y ajouter des références.

Au début du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;    
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Modifiez le fichier app.config pour qu'il contienne un paramètre de configuration avec une chaîne de connexion qui pointe vers votre compte de stockage. Votre fichier app.config ressemble à ceci :

    <configuration>
      <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
      </startup>
      <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
      </appSettings>
    </configuration>

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>Génération d'un URI de signature d'accès partagé pour un conteneur
Pour commencer, ajoutons une méthode pour générer une signature d'accès partagé sur un nouveau conteneur. Dans ce cas, la signature n'est pas associée à une stratégie d'accès stockée et véhicule sur l'URI les informations concernant son heure d'expiration et les autorisations qu'elle accorde.

Ajoutons du code à la méthode **Main()** pour authentifier l'accès à votre compte de stockage et créer un conteneur :

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Insert calls to the methods created below here...

        //Require user input before closing the console window.
        Console.ReadLine();
    }

Ajoutez ensuite une nouvelle méthode qui génère la signature d'accès partagé pour le conteneur et renvoie l'URI de la signature :

    static string GetContainerSasUri(CloudBlobContainer container)
    {
        //Set the expiry time and permissions for the container.
        //In this case no start time is specified, so the shared access signature becomes valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

        //Generate the shared access signature on the container, setting the constraints directly on the signature.
        string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler **GetContainerSasUri()** et écrire l’URI de la signature dans la fenêtre de la console :

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouveau conteneur. L’URI ressemble à cela :       

    https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D

Une fois que l’exécution du code est lancée, la signature d’accès partagé que vous avez créée sur le conteneur est valide pendant vingt-quatre heures. La signature accorde une autorisation client pour créer une liste d'objets blob dans le conteneur et écrire un nouvel objet blob dans le conteneur.

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>Génération d'un URI de signature d'accès partagé pour un objet blob
Nous allons ensuite écrire un code similaire pour créer un objet blob dans le conteneur et générer une signature d'accès partagé pour lui. Cette signature d'accès partagé n'est pas associée à une stratégie d'accès stockée et inclut l'heure de début, l'heure d'expiration et les informations d'autorisation sur l'URI.

Ajoutez une nouvelle méthode qui crée un objet blob et y écrit du texte, puis génère une signature d'accès partagé et renvoie l'URI de la signature :

    static string GetBlobSasUri(CloudBlobContainer container)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Set the expiry time and permissions for the blob.
        //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
        //The shared access signature will be valid immediately.
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
        sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
        sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
        sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

        //Generate the shared access signature on the blob, setting the constraints directly on the signature.
        string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler **GetBlobSasUri()** et écrire l’URI de la signature d’accès partagé dans la fenêtre de la console :    

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();


Compilez et exécutez pour renvoyer l'URI de la signature d'accès partagé pour le nouvel objet blob. L’URI ressemble à cela :

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>Création d’une stratégie d’accès stockée sur le conteneur
Créons une stratégie d'accès stockée sur le conteneur pour définir les contraintes des signatures d'accès partagé qui y sont associées.

Dans les exemples précédents, nous avons spécifié l'heure de début (implicitement ou explicitement), l'heure d'expiration et les autorisations sur l'URI de la signature d'accès partagé lui-même. Dans les exemples suivants, nous allons spécifier cela sur la stratégie d'accès stockée, pas sur la signature. Cela nous permettra de modifier ces contraintes sans devoir émettre de nouveau la signature d'accès partagé.

Il est possible de définir un certain nombre de contraintes sur la signature partagée et le reste sur la stratégie d’accès stockée. Toutefois, l'heure de début, l'heure d'expiration et les autorisations doivent être définies sur l'un ou l'autre emplacement. Ainsi, vous ne pouvez pas spécifier des autorisations à la fois sur la signature d'accès partagé et sur la stratégie d'accès stockée.

Notez que lorsque vous ajoutez une stratégie d’accès à un conteneur, vous devez obtenir les autorisations existantes du conteneur, ajouter la nouvelle stratégie d’accès et ensuite définir les autorisations du conteneur.

Ajoutez une nouvelle méthode qui crée une stratégie d’accès stockée sur un conteneur et renvoie le nom de la stratégie :

    static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
        string policyName)
    {
        //Create a new shared access policy and define its constraints.
        SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
        };

        //Get the container's existing permissions.
        BlobContainerPermissions permissions = container.GetPermissions();

        //Add the new policy to the container's permissions, and set the container's permissions.
        permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
        container.SetPermissions(permissions);
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour supprimer les stratégies d’accès existantes avant d’appeler la méthode **CreateSharedAccessPolicy()** :    

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

Notez que lorsque vous désactivez les stratégies d’accès sur un conteneur, vous devez d’abord obtenir les autorisations existantes du conteneur, puis les désactiver, et enfin définir les autorisations à nouveau.

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>Génération d'un URI de signature d'accès partagé sur le conteneur qui utilise une stratégie d'accès
Créons maintenant une autre signature d'accès partagé sur le conteneur que nous avons créé précédemment, mais en associant la signature à la stratégie d'accès que nous avons créée dans l'exemple précédent.

Ajoutez une nouvelle méthode pour générer une autre signature d'accès partagé sur le conteneur :

    static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Generate the shared access signature on the container. In this case, all of the constraints for the
        //shared access signature are specified on the stored access policy.
        string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return container.Uri + sasContainerToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler la méthode **GetContainerSasUriWithPolicy** :

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>Génération d'un URI de signature d'accès partagé sur l'objet blob qui utilise une stratégie d'accès
Pour terminer, ajoutons une méthode similaire pour créer un autre objet blob et générer une signature d'accès partagé associée à une stratégie d'accès.

Ajoutez une nouvelle méthode pour créer un objet blob et générer une signature d'accès partagé :

    static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
    {
        //Get a reference to a blob within the container.
        CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

        //Upload text to the blob. If the blob does not yet exist, it will be created.
        //If the blob does exist, its existing content will be overwritten.
        string blobContent = "This blob will be accessible to clients via a shared access signature. " +
        "A stored access policy defines the constraints for the signature.";
        MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        ms.Position = 0;
        using (ms)
        {
            blob.UploadFromStream(ms);
        }

        //Generate the shared access signature on the blob.
        string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        //Return the URI string for the container, including the SAS token.
        return blob.Uri + sasBlobToken;
    }

Ajoutez les lignes suivantes à la fin de la méthode **Main()** avant d’appeler **Console.ReadLine()** pour appeler la méthode **GetBlobSasUriWithPolicy** :    

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

La méthode **Main()** doit maintenant ressembler à ceci. Lancez-la pour écrire les URI de signature d'accès partagé sur la fenêtre de la console, puis copiez et collez-les dans un fichier texte pour les utiliser dans la deuxième partie de ce didacticiel.

    static void Main(string[] args)
    {
        //Parse the connection string and return a reference to the storage account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

        //Create the blob client object.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        //Get a reference to a container to use for the sample code, and create it if it does not exist.
        CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
        container.CreateIfNotExists();

        //Generate a SAS URI for the container, without a stored access policy.
        Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, without a stored access policy.
        Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
        Console.WriteLine();

        //Clear any existing access policies on container.
        BlobContainerPermissions perms = container.GetPermissions();
        perms.SharedAccessPolicies.Clear();
        container.SetPermissions(perms);

        //Create a new access policy on the container, which may be optionally used to provide constraints for
        //shared access signatures on the container and the blob.
        string sharedAccessPolicyName = "tutorialpolicy";
        CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

        //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
        Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
        Console.WriteLine();

        Console.ReadLine();
    }

Lorsque vous exécutez l'application console GenerateSharedAccessSignatures, la sortie est semblable à ce qui suit dans la fenêtre de la console. Il s'agit des signatures d'accès partagé que vous allez utiliser dans la partie 2 du didacticiel.

![sas-console-output-1][sas-console-output-1]

## <a name="part-2:-create-a-console-application-to-test-the-shared-access-signatures"></a>Partie 2 : créer une application console pour tester les signatures d’accès partagé
Pour tester les signatures d’accès partagé créées dans les exemples précédents, nous allons créer une deuxième application console qui utilise les signatures afin d’exécuter les opérations sur le conteneur et un objet blob.

> [!NOTE]
> Si plus de 24 heures se sont écoulées depuis la fin de la première partie de ce didacticiel, les signatures que vous avez générées ne sont plus valides. Dans ce cas, exécutez le code dans la première application console pour générer de nouvelles signatures d’accès partagé pour la seconde partie du didacticiel.
> 
> 

Dans Visual Studio, créez une application console Windows et nommez-la **ConsumeSharedAccessSignatures**. Ajoutez des références à **Microsoft.WindowsAzure.Configuration.dll** et **Microsoft.WindowsAzure.Storage.dll**, comme précédemment.

Au début du fichier Program.cs, ajoutez les instructions **using** suivantes :

    using System.IO;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;

Dans le corps de la méthode **Main()** , ajoutez les constantes suivantes et mettez leurs valeurs à jour dans les signatures d'accès partagé générées dans la partie 1 du didacticiel.

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
    }

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>Ajout d'une méthode pour tester les opérations sur le conteneur avec une signature d'accès partagé
Nous allons ensuite ajouter une méthode qui teste des opérations représentatives sur le conteneur avec une signature d'accès partagé sur le conteneur. Notez que la signature d'accès partagé permet de renvoyer une référence au conteneur pour authentifier l'accès au conteneur sur la base de la signature uniquement.

Ajoutez la méthode suivante au fichier Program.cs :

    static void UseContainerSAS(string sas)
    {
        //Try performing container operations with the SAS provided.

        //Return a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

        //Create a list to store blob URIs returned by a listing operation on the container.
        List<ICloudBlob> blobList = new List<ICloudBlob>();

        //Write operation: write a new blob to the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
            string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //List operation: List the blobs in the container.
        try
        {
            foreach (ICloudBlob blob in container.ListBlobs())
            {
                blobList.Add(blob);
            }
            Console.WriteLine("List operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("List operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Get a reference to one of the blobs in the container and read it.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            MemoryStream msRead = new MemoryStream();
            msRead.Position = 0;
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                Console.WriteLine(msRead.Length);
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        Console.WriteLine();

        //Delete operation: Delete a blob in the container.
        try
        {
            CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettez à jour la méthode **Main()** pour appeler **UseContainerSAS()** avec les deux signatures d’accès partagé que vous avez créées dans le conteneur :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        Console.ReadLine();
    }


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>Ajout d'une méthode pour tester les opérations sur l'objet blob avec une signature d'accès partagé
Pour terminer, nous allons ajouter une méthode qui teste des opérations représentatives sur l'objet blob avec une signature d'accès partagé sur l'objet blob. Ici, nous utilisons le constructeur **CloudBlockBlob(String)**, en passant la signature d'accès partagé, pour renvoyer une référence vers l'objet blob. Aucune autre authentification n'est nécessaire, car l'opération est basée uniquement sur la signature.

Ajoutez la méthode suivante au fichier Program.cs :

    static void UseBlobSAS(string sas)
    {
        //Try performing blob operations using the SAS provided.

        //Return a reference to the blob using the SAS URI.
        CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

        //Write operation: Write a new blob to the container.
        try
        {
            string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
            MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
            msWrite.Position = 0;
            using (msWrite)
            {
                blob.UploadFromStream(msWrite);
            }
            Console.WriteLine("Write operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Write operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Read operation: Read the contents of the blob.
        try
        {
            MemoryStream msRead = new MemoryStream();
            using (msRead)
            {
                blob.DownloadToStream(msRead);
                msRead.Position = 0;
                using (StreamReader reader = new StreamReader(msRead, true))
                {
                    string line;
                    while ((line = reader.ReadLine()) != null)
                    {
                        Console.WriteLine(line);
                    }
                }
            }
            Console.WriteLine("Read operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Read operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }

        //Delete operation: Delete the blob.
        try
        {
            blob.Delete();
            Console.WriteLine("Delete operation succeeded for SAS " + sas);
            Console.WriteLine();
        }
        catch (StorageException e)
        {
            Console.WriteLine("Delete operation failed for SAS " + sas);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }        
    }


Mettez à jour la méthode **Main()** pour appeler **UseBlobSAS()** avec les deux signatures d’accès partagé que vous avez créées sur l’objet blob :

    static void Main(string[] args)
    {
        string containerSAS = "<your container SAS>";
        string blobSAS = "<your blob SAS>";
        string containerSASWithAccessPolicy = "<your container SAS with access policy>";
        string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

        //Call the test methods with the shared access signatures created on the container, with and without the access policy.
        UseContainerSAS(containerSAS);
        UseContainerSAS(containerSASWithAccessPolicy);

        //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
        UseBlobSAS(blobSAS);
        UseBlobSAS(blobSASWithAccessPolicy);

        Console.ReadLine();
    }

Exécutez l'application console et observez la sortie pour connaître les opérations autorisées en fonction des signatures. La sortie dans la fenêtre de la console ressemble à ceci :

![sas-console-output-2][sas-console-output-2]

## <a name="next-steps"></a>Étapes suivantes
[Signatures d'accès partagé, partie 1 : présentation du modèle SAP](storage-dotnet-shared-access-signature-part-1.md)

[Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md)

[Délégation de l’accès avec une signature d’accès partagé (API REST)](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[Présentation des signatures d’accès partagé de table et de file d’attente](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG



<!--HONumber=Oct16_HO2-->


