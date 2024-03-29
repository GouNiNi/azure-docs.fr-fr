---
title: "Vue d’ensemble d’Azure Media Services et scénarios courants | Microsoft Docs"
description: Cette rubrique offre une vue d&quot;ensemble d&quot;Azure Media Services
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/12/2016
ms.author: juliako;anilmur
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2a4395385ab0e6e71e5139883a544add0a081cf2


---
# <a name="azure-media-services-overview-and-common-scenarios"></a>Vue d’ensemble d’Azure Media Services et scénarios courants
Microsoft Azure Media Services est une plateforme extensible basée sur le cloud qui permet aux développeurs de créer des applications évolutives de gestion et de diffusion de médias. Media Services est basé sur les API REST qui permettent de télécharger, stocker, encoder et empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

Vous pouvez créer des flux de travail de bout en bout en utilisant uniquement Media Services. Vous pouvez aussi choisir d'utiliser des composants tiers pour certaines parties de votre flux de travail (par exemple, en encodant avec un encodeur tiers). Le contenu est ensuite téléchargé, protégé, empaqueté et remis à l'aide de Media Services.

Vous pouvez choisir de diffuser votre contenu en direct ou de distribuer du contenu à la demande. Cette rubrique décrit les scénarios courants pour distribuer vos contenus [en direct](media-services-overview.md#live_scenarios) ou [à la demande](media-services-overview.md#vod_scenarios). La rubrique contient également des liens vers d’autres rubriques pertinentes.

## <a name="sdks-and-tools"></a>Kits de développement logiciel (SDK) et outils
Pour créer des solutions Media Services, vous pouvez utiliser les composants suivants :

* [API REST Media Services](https://msdn.microsoft.com/library/azure/hh973617.aspx)
* Un des Kits de développement logiciel (SDK) de client disponibles :
* [Kit de développement logiciel (SDK) Azure Media Services pour .NET](https://github.com/Azure/azure-sdk-for-media-services),
* [Kit de développement logiciel (SDK) Azure pour Java](https://github.com/Azure/azure-sdk-for-java),
* [Kit de développement logiciel (SDK) Azure pour PHP](https://github.com/Azure/azure-sdk-for-php),
* [Azure Media Services pour Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (il s’agit d’une version non Microsoft du Kit de développement logiciel (SDK) Node.js. Il est géré par une communauté et ne fournit pas une couverture à 100 % des API AMS).
* Outils existants :
* [portail Azure](https://portal.azure.com/)
* [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) est une application Winforms/C# pour Windows)

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
Vous pouvez afficher les parcours d’apprentissage d’AMS ici :

* [Workflow en flux continu AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [Workflow de streaming à la demande AMS](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Composants requis
Pour commencer à utiliser Azure Media Services, vous devez disposer des éléments suivants :

1. Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com).
2. Un compte Azure Media Services. Utilisez le portail Azure, .NET ou l’API REST pour créer un compte Azure Media Services. Pour plus d’informations, consultez [Créer un compte](media-services-portal-create-account.md).
3. (Facultatif) Un environnement de développement configuré. Choisissez .NET ou API REST comme environnement de développement. Pour plus d’informations, consultez [Configuration d'environnement](media-services-dotnet-how-to-use.md).

En outre, découvrez comment [vous connecter](media-services-dotnet-connect-programmatically.md)par programmation.

1. (Recommandé) Allocation d’une ou de plusieurs unités d’échelle. Il est recommandé d’allouer une ou plusieurs unités d’échelle pour les applications dans un environnement de production.   Pour plus d’informations, consultez [Gestion des points de terminaison de diffusion en continu](media-services-portal-manage-streaming-endpoints.md).

## <a name="concepts-and-overview"></a>Concepts et vue d’ensemble
Pour les concepts Azure Media Services, consultez [Concepts](media-services-concepts.md).

Pour découvrir une série de procédures qui présente tous les principaux composants d’Azure Media Services, consultez les [didacticiels pas à pas Azure Media Services](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series). Cette série constitue une excellente présentation des concepts, et utilise l’outil AMSE pour effectuer les tâches AMS. Notez que l’outil AMSE est un outil Windows. Cet outil prend en charge la plupart des tâches que vous pouvez obtenir par programmation avec le [Kit de développement logiciel (SDK) AMS pour .NET](https://github.com/Azure/azure-sdk-for-media-services), le [Kit de développement logiciel (SDK) Azure pour Java](https://github.com/Azure/azure-sdk-for-java) ou le [Kit de développement logiciel (SDK) Azure pour PHP](https://github.com/Azure/azure-sdk-for-php).

## <a name="a-idvodscenariosadelivering-media-ondemand-with-azure-media-services-common-scenarios-and-tasks"></a><a id="vod_scenarios"></a>Diffusion multimédia à la demande avec Azure Media Services : tâches et scénarions courants
Cette section décrit les scénarios courants et fournit des liens vers des rubriques pertinentes. Le diagramme suivant présente les principaux composants de la plateforme Media Services impliqués dans la distribution de contenu à la demande. 

![Flux de travail VOD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

### <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-nonencrypted"></a>Protéger le contenu stocké et assurer une diffusion multimédia en continu en clair (sans chiffrement)
1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
   
    Il est recommandé d’appliquer une option de chiffrement de stockage à votre ressource afin de protéger votre contenu lors du téléchargement et lorsqu’il est au repos, lors du stockage.
2. Encoder en un ensemble de fichiers MP4 à débit adaptatif. 
   
    Il est recommandé d’appliquer une option de chiffrement de stockage à la ressource de sortie afin de protéger votre contenu lorsqu’il est au repos.
3. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique). 
   
    Si votre ressource est stockée sous forme chiffrée, vous **devez** configurer une stratégie de remise de ressources. 
4. Publiez la ressource en créant un localisateur à la demande.
   
    Assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu.
5. Diffusez le contenu publié.

### <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Protéger le contenu stocké et diffuser du contenu multimédia chiffré dynamiquement en continu
Pour pouvoir utiliser le chiffrement dynamique, vous devez obtenir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu chiffré.

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource. Appliquez l’option de chiffrement de stockage à la ressource.
2. Encoder en un ensemble de fichiers MP4 à débit adaptatif. Appliquez l’option de chiffrement de stockage à la ressource de sortie.
3. Créez la clé de contenu de chiffrement pour la ressource que vous souhaitez chiffrer dynamiquement pendant la lecture.
4. Configurez la stratégie d’autorisation de clé de contenu.
5. Configurez la stratégie de remise de ressources (utilisée par l’empaquetage dynamique et le chiffrement dynamique).
6. Publiez la ressource en créant un localisateur à la demande.
7. Diffusez le contenu publié. 

### <a name="use-media-analytics-to-derive-actionable-insights-from-your-videos"></a>Utiliser Media Analytics pour extraire des connaissances exploitables de vos vidéos
Media Analytics est une collection de composants visuels et vocaux qui aident les organisations et les entreprises à extraire des connaissances exploitables de leurs fichiers vidéo. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Media Analytics](media-services-analytics-overview.md).

1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
2. Utilisez un des services Media Analytics pour traiter vos vidéos :
   
   * **Indexeur** – [Traiter les vidéos avec l’Indexeur multimédia Azure 2](media-services-process-content-with-indexer2.md)
   * **Hyperlapse** – [Fichiers multimédias Hyperlapse avec Azure Media Hyperlapse](media-services-hyperlapse-content.md)
   * **Détection de mouvement** – [Détection de mouvement pour Azure Media Analytics](media-services-motion-detection.md).
   * **Détection des visages et émotions du visage** – [Détection des visages et des émotions pour Azure Media Analytics](media-services-face-and-emotion-detection.md).
   * **Synthèse vidéo** – [Utilisez les vidéos miniatures Azure Media pour créer une synthèse vidéo](media-services-video-summarization.md)
3. Les processeurs multimédias Media Analytics créent des fichiers MP4 ou JSON. Si un processeur multimédia a produit un fichier MP4, vous pouvez télécharger ce dernier progressivement. Si un processeur multimédia a produit un fichier JSON, vous pouvez télécharger ce dernier à partir d’Azure Blob Storage. 

### <a name="deliver-progressive-download"></a>Remettre le téléchargement progressif
1. Téléchargez un fichier mezzanine de haute qualité dans une ressource.
2. Encoder en un fichier MP4 unique.
3. Publiez la ressource en créant un localisateur à la demande ou de signature d’accès partagé (SAS, Shared Access Signature).
   
    Si vous utilisez un localisateur à la demande, assurez-vous d’avoir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de télécharger progressivement le contenu.
   
    Si vous utilisez un localisateur SAS, le contenu est téléchargé depuis le stockage d’objets blob Azure. Dans ce cas, il n’est pas nécessaire de disposer d’unités réservées de diffusion en continu.
4. Téléchargez le contenu de manière progressive.

## <a name="a-idlivescenariosadelivering-live-streaming-events-with-azure-media-services"></a><a id="live_scenarios"></a>Diffusion d’événements en direct en continu avec Azure Media Services
Lorsque vous utilisez la vidéo en flux continu, les composants suivants sont généralement impliqués :

* Une caméra utilisée pour diffuser un événement.
* Un encodeur vidéo dynamique qui convertit les signaux de la caméra en flux de données qui sont envoyés vers un service de vidéo en flux continu.

Éventuellement, plusieurs encodeurs dynamiques synchronisés. Pour certains événements en direct critiques qui exigent une disponibilité et une qualité d’expérience très élevées, il est recommandé d’utiliser des encodeurs redondants en mode actif-actif avec synchronisation date/heure pour obtenir un basculement transparent sans perte de données.

* Service de vidéo en flux continu qui vous permet d’effectuer les opérations suivantes :
* Recevoir du contenu en direct à l’aide de différents protocoles de diffusion de vidéo en flux continu (par exemple RTMP ou Smooth Streaming),
* Encoder votre flux en flux à débit adaptatif (facultatif)
* Afficher un aperçu de votre flux en direct
* Enregistrer et stocker le contenu ingéré pour le diffuser ultérieurement (vidéo à la demande)
* Fournir le contenu via des protocoles de diffusion communs (par exemple, MPEG DASH, Smooth, TLS, HDS) directement à vos clients ou à un réseau de distribution de contenu (CDN) pour une distribution supplémentaire

**Microsoft Azure Media Services** (AMS) offre la possibilité de recevoir, d’encoder, d’afficher, de stocker et de distribuer votre contenu vidéo en flux continu.

Quand vous distribuez votre contenu aux clients, votre objectif est de distribuer une vidéo de haute qualité à divers appareils dans des conditions de réseau différentes. Pour prendre en charge les conditions de qualité et de réseau, utilisez des encodeurs dynamiques pour encoder votre flux dans un flux vidéo à débit binaire multiple (débit binaire adaptatif).  Pour prendre en charge la diffusion en continu sur différents appareils, utilisez l’ [empaquetage dynamique](media-services-dynamic-packaging-overview.md) Media Services pour empaqueter de manière dynamique votre flux dans différents protocoles. Media Services prend en charge la distribution des technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Dans Azure Media Sercices, les **canaux**, les **programmes** et le **point de terminaison de diffusion en continu** gèrent toutes les fonctionnalités vidéo en flux continu, notamment la réception, le formatage, le DVR, la sécurité, l’extensibilité et la redondance.

Un **canal** représente un pipeline de traitement du contenu vidéo en flux continu. Un canal peut recevoir des flux d’entrée live de l’une des manières suivantes :

* Un encodeur en direct local envoie au canal un paquet **RTMP** ou **Smooth Streaming** (MP4 fragmenté) à débit binaire multiple, configuré pour un envoi **direct**. L’envoi **direct** correspond aux flux reçus qui transitent par les **canaux** sans traitement supplémentaire. Vous pouvez utiliser les encodeurs dynamiques suivants qui produisent un flux Smooth Streaming à débit binaire multiple : Elemental, Envivio, Cisco.  Les encodeurs dynamiques suivants produisent un flux au format RTMP : Adobe Flash Live, Telestream Wirecast et transcodeurs Tricaster.  Un encodeur live peut également envoyer un flux à débit binaire unique vers un canal qui n’est pas activé pour le Live Encoding, mais ce n’est pas recommandé. Lorsqu’il y est invité, Media Services fournit le flux aux clients.

> [!NOTE]
> La méthode pass-through est le moyen le plus économique de diffuser des vidéos en continu si plusieurs événements vous concernent sur une longue période, et si vous avez déjà investi dans des encodeurs locaux. Consultez les détails de la [tarification](/pricing/details/media-services/) .
> 
> 

* Un encodeur dynamique envoie un flux à vitesse de transmission unique vers le canal activé pour effectuer un encodage en direct avec Media Services dans l’un des formats suivants : RTP (MPEG-TS), RTMP ou Smooth Streaming (MP4 fragmenté). Le canal procède ensuite à l’encodage en temps réel du flux à débit binaire unique entrant en flux vidéo à débit binaire multiple (adaptatif). Lorsqu’il y est invité, Media Services fournit le flux aux clients.

### <a name="working-with-channels-that-receive-multibitrate-live-stream-from-onpremises-encoders-passthrough"></a>Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux (méthode directe)
Le diagramme suivant présente les principaux composants de la plateforme AMS impliqués dans ce flux de travail de **méthode directe** .

![Flux de travail live][live-overview2]

Pour plus d’informations, consultez [Utilisation des canaux recevant un flux dynamique à débit binaire multiple provenant d’encodeurs locaux](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilisation de canaux activés pour effectuer un encodage en temps réel avec Azure Media Services
Le schéma suivant illustre les principales parties de la plateforme AMS impliquées dans le flux de travail de vidéo en flux continu où un canal est activé pour effectuer un encodage live avec Media Services.

![Flux de travail live][live-overview1]

Pour plus d’informations, consultez [Utilisation de canaux activés pour effectuer un encodage en direct avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Utilisation de contenu
Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs. La rubrique suivante fournit des liens vers les Kits de développement logiciel (SDK) et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes pour utiliser la diffusion en continu de médias à partir de Media Services.

[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)

## <a name="enabling-azure-cdn"></a>Activation du CDN Azure
Media Services prend en charge l’intégration avec le CDN d’Azure. Pour plus d’informations sur l’activation du CDN Azure, voir [Gestion des points de terminaison de diffusion en continu dans un compte Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Mise à l’échelle d’un compte Media Services
Vous pouvez mettre à l’échelle **Media Services** en spécifiant le nombre d’**unités réservées de diffusion en continu** et d’**unités réservées d’encodage** que vous voulez attribuer à votre compte.

Vous pouvez aussi mettre à l’échelle votre compte Media Services en lui ajoutant des comptes de stockage. Chaque compte de stockage est limité à 500 To. Pour développer votre capacité stockage au-delà des limites par défaut, vous pouvez choisir de rattacher plusieurs comptes de stockage à un même compte Media Services.

[Cette](media-services-portal-scale-streaming-endpoints.md) rubrique offre des liens vers des rubriques connexes.

## <a name="support"></a>Support
[support Azure](https://azure.microsoft.com/support/options/) propose des options de support pour Azure, y compris Media Services.

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="service-level-agreement-sla"></a>Contrat de Niveau de Service (SLA)
* Pour Media Services, nous garantissons une disponibilité de 99,9 % des transactions d'API REST.
* Pour la diffusion en continu, nous traiterons avec succès les demandes de service avec une garantie de disponibilité de 99,9 % pour le contenu multimédia existant quand au moins une unité de diffusion en continu est achetée.
* Pour les Canaux en Direct, nous garantissons que les canaux en cours d’exécution auront une connectivité externe au moins 99,9 % du temps.
* Pour la Protection de Contenu, nous garantissons que nous serons en mesure de traiter les demandes de clé au moins 99,9 % du temps.
* Pour l’Indexeur, nous serons en mesure d’assurer les demandes de tâche d’indexation traitées avec une unité réservée d’encodage 99,9 % du temps.

Pour plus d’informations, consultez le [contrat SLA Microsoft Azure](https://azure.microsoft.com/support/legal/sla/).

<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
[vod-overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
[live-overview1]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png
[live-overview2]: ./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png




<!--HONumber=Nov16_HO2-->


