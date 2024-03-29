---
title: Authentification et autorisation dans Azure App Service | Microsoft Docs
description: Référence et présentation conceptuelles de la fonctionnalité d’authentification/autorisation pour Azure App Service.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''

ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender

---
# Authentification et autorisation dans Azure App Service
## Qu’est-ce que l’authentification/autorisation App Service ?
L’authentification/autorisation App Service est une fonctionnalité qui permet à votre application de connecter les utilisateurs. Vous n’êtes ainsi donc pas obligé de modifier le code sur le serveur principal. Elle propose un moyen simple de protéger votre application et fonctionne avec des données par utilisateur.

App Service utilise l’identité fédérée, dans laquelle un fournisseur d’identité tiers stocke les comptes et authentifie les utilisateurs. L’application utilise les informations sur cette identité pour qu’elle n’ait pas à stocker ces informations elle-même. App Service prend en charge cinq fournisseurs d’identité prêts à l’emploi : Azure Active Directory, Facebook, Google, Compte Microsoft et Twitter. Votre application peut exploiter plusieurs de ces fournisseurs d’identité, afin de proposer à vos utilisateurs plusieurs options de connexion. Vous pouvez également étendre cette prise en charge intégrée en ajoutant un autre fournisseur d’identité ou [votre propre solution d’identité personnalisée][custom-auth].

Si vous voulez commencer tout de suite, consultez l’un des didacticiels suivants :

* [Ajout de l’authentification à votre application iOS][iOS] \(ou [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] ou encore [Cordova])
* [Authentification utilisateur pour API Apps dans Azure App Service][apia-user]
* [Prise en main d’Azure App Services, 2e partie][web-getstarted]

## Fonctionnement de l’authentification dans App Service
Pour s’authentifier à l’aide d’un des fournisseurs d’identité, vous devez d’abord configurer le fournisseur d’identité pour qu’il reconnaisse votre application. Le fournisseur d’identité vous fournit alors des ID et des secrets, que vous devez fournir à votre tour à App Service. Cela termine la relation d’approbation pour qu’App Service puisse valider des assertions d’utilisateur du fournisseur d’identité, telles que les jetons d’authentification.

Pour connecter un utilisateur en utilisant l’un de ces fournisseurs, vous devez rediriger cet utilisateur vers un point de terminaison de connexion associé à ce fournisseur. Si les clients utilisent un navigateur web, vous pouvez faire en sorte qu’App Service redirige automatiquement l’ensemble des utilisateurs non authentifiés vers le point de terminaison de connexion. Dans le cas contraire, vous devez rediriger vos clients vers `{your App Service base URL}/.auth/login/<provider>`, où `<provider>` correspond à l’une des valeurs suivantes : aad, facebook, google, microsoft ou twitter. Les scénarios portant sur les appareils mobiles et les API sont expliqués plus loin dans cet article.

Les utilisateurs qui interagissent avec votre application via un navigateur web se verront affecter un cookie, qui leur permettra de rester authentifiés tant qu’ils seront connectés à votre application. Pour les autres types de clients, comme les clients mobiles, un jeton web JSON (JWT) est émis pour le client, et doit être présenté dans l’en-tête `X-ZUMO-AUTH`. Les kits de développement logiciel (SDK) du client Mobile Apps gèrent cette étape pour vous. Il est également possible d’ajouter directement un jeton d’accès ou d’identité Azure Active Directory dans l’en-tête `Authorization`, en tant que [jeton du porteur](https://tools.ietf.org/html/rfc6750).

App Service valide n’importe quel cookie ou un jeton émis par votre application pour authentifier des utilisateurs. Pour restreindre l’accès à votre application, consultez la section [Autorisation](#authorization), plus loin dans cet article.

### Authentification d’appareils mobiles avec un Kit de développement logiciel (SDK) de fournisseur
Une fois que tout est configuré sur le système back-end, vous pouvez modifier les clients mobiles pour la connexion à App Service. Il existe deux approches :

* Exploitez un Kit de développement logiciel (SDK) publié par un fournisseur d’identité donné pour établir l’identité, puis accédez à App Service.
* Utilisez une seule ligne de code pour que le Kit de développement logiciel (SDK) client Mobile Apps puisse connecter des utilisateurs.

> [!TIP]
> La plupart des applications doivent utiliser un Kit de développement logiciel (SDK) de fournisseur pour obtenir une expérience de connexion plus cohérente et tirer parti de la prise en charge de l’actualisation et d’autres avantages propres au fournisseur.
> 
> 

Lorsque vous utilisez un Kit de développement logiciel (SDK), l’expérience de connexion s’intègre parfaitement avec le système d’exploitation sur lequel l’application s’exécute. Elle permet également d’obtenir un jeton de fournisseur et certaines informations utilisateur sur le client, ce qui facilite beaucoup la consommation d’API graphiques et la personnalisation de l’expérience utilisateur. Dans les blogs et sur les forums, cette opération est de temps en temps appelée « flux client » ou « flux dirigé vers le client » car le code sur le client connecte les utilisateurs et a accès à un jeton de fournisseur.

Une fois qu’un jeton de fournisseur est obtenu, il doit être envoyé à App Service à des fins de validation. Lorsque App Service a validé le jeton, App Service crée un jeton App Service, qui est renvoyé au client. Le Kit de développement logiciel (SDK) du client Mobile Apps présente des méthodes d’assistance pour gérer cet échange et lier automatiquement le jeton à toutes les demandes adressées au back-end de l’application. Le développeur peut également conserver une référence au jeton de fournisseur s’il le souhaite.

### Authentification d’appareils mobiles sans Kit de développement logiciel (SDK) de fournisseur
Si vous ne souhaitez pas configurer un Kit de développement logiciel (SDK) de fournisseur, vous pouvez autoriser la fonctionnalité Mobile Apps d’Azure App Service à effectuer la connexion à votre place. Le SDK client Mobile Apps ouvre un affichage web du fournisseur de votre choix et connecte l’utilisateur. Dans les blogs et sur les forums, cette opération est de temps en temps appelée « flux serveur » ou « flux dirigé vers le serveur », car le serveur gère le processus de connexion des utilisateurs et le Kit de développement logiciel (SDK) client ne reçoit jamais le jeton du fournisseur.

Le code nécessaire pour démarrer ce flux est inclus dans le didacticiel sur l’authentification pour chaque plateforme. À la fin du flux, le Kit de développement logiciel (SDK) client dispose d’un jeton App Service et le jeton est automatiquement joint à toutes les requêtes adressées au back-end de l’application.

### Authentification de service à service
Bien que vous puissiez fournir aux utilisateurs l’accès à votre application, vous pouvez également faire confiance à une autre application pour appeler votre propre API. Par exemple, une application web pourrait appeler une API dans une autre application web. Dans ce scénario, vous utilisez des informations d’identification correspondant à un compte de service au lieu des informations d’identification de l’utilisateur pour obtenir un jeton. Un compte de service est également appelé *principal de service* dans Azure Active Directory. L’authentification pratiquée avec ce type de compte est également appelée « scénario de service à service ».

> [!IMPORTANT]
> Comme les applications mobiles s’exécutent sur des appareils clients, celles-ci ne sont *pas* considérées comme des applications approuvées et ne doivent pas utiliser un flux de principal de service. Au lieu de cela, elles doivent utiliser un flux utilisateur décrit précédemment.
> 
> 

Pour les scénarios de service à service, App Service peut protéger votre application à l’aide d’Azure Active Directory. Il suffit que l’application appelante fournisse un jeton d’autorisation du principal de service Azure Active Directory, en fournissant l’ID du client et son secret via Azure Active Directory. Vous trouverez un exemple de ce scénario utilisant des applications API ASP.NET dans le didacticiel [Authentification de principal du service pour API Apps dans Azure App Service][apia-service].

Si vous souhaitez utiliser l’authentification App Service pour gérer un scénario de service à service, vous pouvez utiliser les certificats clients ou l’authentification de base. Pour plus d’informations sur les certificats clients dans Azure, consultez [Configuration de l’authentification mutuelle TLS pour une application Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Pour en savoir plus sur l’authentification de base dans ASP.NET, voir [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters) (Filtres d’authentification dans ASP.NET Web API 2).

L’authentification du compte de service à partir d’une application logique App Service vers une application API est un cas particulier, qui est expliqué en détail dans la section [Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques](../app-service-logic/app-service-logic-custom-hosted-api.md).

## <a name="authorization"></a>Fonctionnement de l’autorisation dans App Service
Vous bénéficiez d’un contrôle absolu sur les requêtes pouvant accéder à votre application. Vous pouvez configurer les fonctionnalités d’authentification/d’autorisation d’App Service en leur affectant l’un des comportements suivants :

* Autoriser uniquement les requêtes authentifiées à accéder à votre application.
  
    Si un navigateur reçoit une requête anonyme, App Service la redirige vers la page de connexion du fournisseur d’identité de votre choix pour que les utilisateurs puissent se connecter. Si la requête provient d’un appareil mobile, une réponse HTTP du type *401 non autorisé* est renvoyée.
  
    Grâce à cette option, vous n’avez pas besoin d’écrire du code d’authentification dans votre application. S’il vous faut une autorisation plus fine, vous pouvez tirer parti des informations sur l’utilisateur disponibles pour votre code.
* Autoriser toutes les requêtes à atteindre votre application, mais valider les requêtes authentifiées et transmettre les informations d’authentification dans les en-têtes HTTP.
  
    Cette option permet de confier les décisions en matière d’autorisation à votre code d’application. Elle permet un traitement plus souple des requêtes anonymes, mais vous devez écrire du code à cette fin.
* Autoriser toutes les requêtes à atteindre votre application, sans recours aux informations d’authentification dans les requêtes.
  
    Dans ce cas, la fonctionnalité d’authentification/d’autorisation est désactivée. Les tâches d’authentification et d’autorisation sont entièrement à la charge du code de votre application.

Les comportements ci-dessus sont contrôlés par le biais de l’option **Action à exécuter quand une demande n’est pas authentifiée** du portail Azure. Si vous choisissez **Log in with *provider name* (Se connecter avec nom du fournisseur)**, toutes les demandes doivent être authentifiées. L’option **Autoriser la demande (aucune action)** confie les décisions en matière d’autorisation à votre code, mais fournit malgré tout des informations d’authentification. Si vous souhaitez que votre code gère toutes ces opérations, vous pouvez désactiver la fonctionnalité d’autorisation/d’authentification.

## Utilisation des identités des utilisateurs dans votre application
App Service transmet certaines informations sur les utilisateurs à votre application, au moyen d’en-têtes spéciaux. Ces en-têtes ne sont pas autorisés dans les requêtes externes ; ils ne seront présents que si la fonctionnalité d’autorisation/d’authentification d’App Service les définit. Voici quelques exemples d’en-têtes :

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

Tout code, quels que soient le langage ou l’infrastructure utilisés, peut trouver les informations qu’il recherche dans ces en-têtes. Dans le cas d’applications ASP.NET 4.6, le paramètre **ClaimsPrincipal** est automatiquement défini sur les valeurs appropriées.

Votre application peut également obtenir des informations supplémentaires sur l’utilisateur en émettant la commande HTTP GET sur le point de terminaison `/.auth/me` de votre application. Un jeton valide inclus dans la requête renvoie une charge utile JSON présentant des informations sur le fournisseur utilisé, le jeton du fournisseur sous-jacent et d’autres données sur l’utilisateur. Les Kits de développement logiciel (SDK) serveur de Mobile Apps offrent des méthodes d’assistance permettant de manipuler ces données. Pour plus d’informations, consultez [Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), et [Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## Documentation et ressources supplémentaires
### Fournisseurs d’identité
Les didacticiels suivants montrent comment configurer App Service pour exploiter différents fournisseurs d’authentification :

* [Comment configurer votre application pour utiliser une connexion Azure Active Directory][AAD]
* [Comment configurer votre application pour utiliser une connexion Facebook][Facebook]
* [Comment configurer votre application pour utiliser une connexion Google][Google]
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft][MSA]
* [Comment configurer votre application pour utiliser une connexion Twitter][Twitter]

Si vous voulez utiliser un système d’identité différent de ceux qui sont fournis ici, vous pouvez également exploiter la [prise en charge de l’authentification personnalisée en version préliminaire dans le Kit de développement logiciel (SDK) serveur .NET de Mobile Apps][custom-auth], qui peut être utilisée dans les applications API, mobiles ou web.

### Applications web
Les didacticiels suivants montrent comment ajouter l’authentification à une application web :

* [Prise en main d’Azure App Services, 2e partie][web-getstarted]

### Applications mobiles
Les didacticiels suivants expliquent comment ajouter une fonctionnalité d’authentification à vos clients mobiles en utilisant le flux dirigé vers le serveur :

* [Ajout de l’authentification à votre application iOS][iOS]
* [Ajout de l’authentification à votre application Android][Android]
* [Ajout de l’authentification à votre application Windows][Windows]
* [Ajout de l’authentification à votre application Xamarin.iOS][Xamarin.iOS]
* [Ajout de l’authentification à votre application Xamarin.Android][Xamarin.Android]
* [Ajout de l’authentification à votre application Xamarin.Forms][Xamarin.Forms]
* [Ajout de l’authentification à votre application Cordova][Cordova]

Utilisez les ressources suivantes si vous souhaitez exploiter le flux dirigé vers le client pour Azure Active Directory :

* [Bibliothèque d’authentification Active Directory pour iOS][ADAL-iOS]
* [Bibliothèque d’authentification Active Directory pour Android][ADAL-Android]
* [Bibliothèque d’authentification Active Directory pour Windows et Xamarin][ADAL-dotnet]

Utilisez les ressources suivantes si vous souhaitez exploiter le flux dirigé vers le client pour Facebook :

* [Kit de développement logiciel (SDK) Facebook pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Utilisez les ressources suivantes si vous souhaitez exploiter le flux dirigé vers le client pour Twitter :

* [Twitter Fabric pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Utilisez les ressources suivantes si vous souhaitez exploiter le flux dirigé vers le client pour Google :

* [Kit de développement logiciel (SDK) Google Sign-In pour iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### Applications API
Les didacticiels suivants expliquent comment protéger vos applications API :

* [Authentification utilisateur pour API Apps dans Azure App Service][apia-user]
* [Authentification de principal du service pour API Apps dans Azure App Service][apia-service]

[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

<!---HONumber=AcomDC_0831_2016-->