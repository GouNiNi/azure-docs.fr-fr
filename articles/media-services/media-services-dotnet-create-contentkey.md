---
title: Création de ContentKeys avec .NET
description: Apprenez à créer des clés de contenu qui fournissent un accès sécurisé aux ressources.
services: media-services
documentationcenter: ''
author: Juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako

---
# <a name="create-contentkeys-with-.net"></a>Création de ContentKeys avec .NET
> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services vous permet de créer et de fournir des ressources chiffrées. **ContentKey** fournit un accès sécurisé à vos **éléments multimédias**. 

Quand vous créez un élément multimédia (par exemple, avant de [charger des fichiers](media-services-dotnet-upload-files.md)), vous pouvez spécifier les options de chiffrement suivantes : **StorageEncrypted**, **CommonEncryptionProtected** ou **EnvelopeEncryptionProtected**. 

Quand vous fournissez des éléments multimédias à vos clients, vous pouvez [configurer les éléments multimédias devant être chiffrés dynamiquement](media-services-dotnet-configure-asset-delivery-policy.md) avec un des deux chiffrements suivants : **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Les ressources chiffrées doivent être associées à des **ContentKey**. Cet article décrit comment créer une clé de contenu.

> [!NOTE]
> Quand vous créez un élément multimédia **StorageEncrypted** à l’aide du SDK Media Services .NET, **ContentKey** est automatiquement créé et lié à l’élément multimédia.
> 
> 

## <a name="contentkeytype"></a>ContentKeyType
Une des valeurs que vous devez définir lors de la création d’une clé de contenu est le type de clé de contenu. Choisissez une des valeurs suivantes. 

    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for configuration encryption.
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a name="<a-id="envelope_contentkey"></a>create-envelope-type-contentkey"></a><a id="envelope_contentkey"></a>Créer une ContentKey de type enveloppe
L’extrait de code suivant crée une clé de contenu du type de chiffrement d’enveloppe. Il associe ensuite la clé à l’élément multimédia spécifié.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

appel

    IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a name="<a-id="common_contentkey"></a>create-common-type-contentkey"></a><a id="common_contentkey"></a>Créer une ContentKey de type commun
L’extrait de code suivant crée une clé de contenu du type de chiffrement commun. Il associe ensuite la clé à l’élément multimédia spécifié.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
appel

    IContentKey key = CreateCommonTypeContentKey(encryptedsset); 


## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO2-->


