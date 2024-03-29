---
title: Prise en main de l'authentification pour Mobile Apps dans Xamarin iOS
description: Découvrez comment utiliser Mobile Apps pour authentifier les utilisateurs de votre application Xamarin iOS via divers fournisseurs d'identité, notamment AAD, Google, Facebook, Twitter et Microsoft.
services: app-service\mobile
documentationcenter: xamarin
author: mattchenderson
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/28/2016
ms.author: mahender

---
# Ajout de l'authentification à votre application Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

Cette rubrique montre comment authentifier les utilisateurs d'une application App Service Mobile App à partir de votre application cliente. Dans ce didacticiel, vous allez ajouter l’authentification au projet de démarrage rapide Xamarin.iOS à l’aide d’un fournisseur d’identité pris en charge par App Service. Une fois l’utilisateur authentifié et autorisé par votre application Mobile App, la valeur de l’ID utilisateur s’affiche ; vous pouvez alors accéder aux données de table limitées.

Vous devez commencer par suivre le didacticiel [Création d’une application Xamarin.iOS]. Si vous n’utilisez pas le projet de serveur du démarrage rapide téléchargé, vous devez ajouter le package d’extension d’authentification à votre projet. Pour plus d'informations sur les packages d'extension de serveur, consultez [Fonctionnement avec le Kit de développement logiciel (SDK) du serveur principal .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Inscription de votre application pour l'authentification et configuration d'App Services
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## Restriction des autorisations pour les utilisateurs authentifiés
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. Dans Visual Studio ou Xamarin Studio, exécutez le projet client sur un appareil ou un émulateur. Vérifiez qu'une exception non gérée avec un code d'état 401 (Non autorisé) est générée après le démarrage de l'application. L’échec est consigné dans la console du débogueur. Ainsi, dans Visual Studio, vous devez voir l’échec dans la fenêtre de sortie.

&nbsp;&nbsp;Cet échec non autorisé se produit, car l’application tente d’accéder à votre backend Mobile App en tant qu’utilisateur non authentifié. La table *TodoItem* nécessite désormais l’authentification.

Ensuite, vous mettrez à jour l’application cliente pour demander des ressources au backend Mobile App avec un utilisateur authentifié.

## Ajout de l'authentification à l'application
Dans cette section, vous allez modifier l'application de façon à afficher un écran de connexion avant d'afficher des données. Quand l'application démarre, elle ne se connecte pas à votre service App Service et n'affiche pas de données. Après le premier geste d'actualisation de l'utilisateur, l'écran de connexion s'affiche. Une fois la connexion réussie, la liste des tâches s'affiche.

1. Dans le projet client, ouvrez le fichier **QSTodoService.cs** et ajoutez l’instruction suivante et `MobileServiceUser` avec l’accesseur à la classe QSTodoService :
   
    ```
        using UIKit;
    ```
   
        // Logged in user
        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }
2. Ajoutez une nouvelle méthode nommée **Authenticate** à **QSTodoService** avec la définition suivante :

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    >[AZURE.NOTE] Si vous utilisez un autre fournisseur d’identité que Facebook, remplacez la valeur passée à la méthode **LoginAsync** ci-dessus par l’une des valeurs suivantes : _MicrosoftAccount_, _Twitter_, _Google_ ou _WindowsAzureActiveDirectory_.

1. Ouvrez **QSTodoListViewController.cs**. Modifiez la définition de méthode de **ViewDidLoad** pour supprimer l’appel à **RefreshAsync()** vers la fin :
   
        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();
   
            todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();
   
           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }
   
            // Comment out the call to RefreshAsync
            // await RefreshAsync ();
        }
2. Modifiez la méthode **RefreshAsync** pour vous authentifier si la propriété **User** a la valeur null. Ajoutez le code suivant en haut de la définition de méthode :
   
        // start of RefreshAsync method
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }
        }
        // rest of RefreshAsync method
3. Dans Visual Studio ou Xamarin Studio connecté à votre hôte de build Xamarin sur votre Mac, exécutez le projet client ciblant un périphérique ou un émulateur. Vérifiez que l'application n'affiche aucune donnée.
   
    Effectuez le geste d'actualisation en affichant la liste des éléments, ce qui fait apparaître l'écran de connexion. Une fois que vous avez entré des informations d'identification valides, l'application affiche la liste des tâches et vous pouvez mettre à jour les données.

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Création d’une application Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_0706_2016-->