---
title: Comment installer sans assistance le connecteur du proxy d’application Azure AD | Microsoft Docs
description: Explique comment effectuer une installation silencieuse du connecteur du Proxy d’application Azure AD pour offrir un accès à distance sécurisé à vos applications locales.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban

---
# Comment installer silencieusement le connecteur du Proxy d'application Azure AD
Vous souhaitez pouvoir envoyer un script d’installation vers plusieurs serveurs Windows ou vers des serveurs Windows sans interface utilisateur. Cette rubrique explique comment créer un script Windows PowerShell qui active l’installation sans assistance pour installer et inscrire votre connecteur de Proxy d’application Azure AD.

## Activation de l’accès
Le Proxy d’application fonctionne avec un service Windows Server léger appelé connecteur, à l’intérieur de votre réseau. Pour que le connecteur du Proxy d'application puisse fonctionner, il doit être inscrit auprès de votre annuaire Azure AD à l'aide d'un identifiant d’administrateur global et d’un mot de passe. Généralement, ces informations sont saisies pendant l'installation du connecteur dans une boîte de dialogue contextuelle. Sinon, vous pouvez utiliser Windows PowerShell pour créer un objet d’informations d’identification afin d’entrer vos informations d’inscription, ou vous pouvez créer votre propre jeton et l’utiliser pour communiquer vos informations d’inscription.

## Étape 1 : Installation du connecteur sans inscription
Pour installer les MSI du connecteur sans inscrire le connecteur, procédez comme suit :

1. Ouvrez une invite de commandes.
2. Exécutez la commande suivante dans laquelle /q signifie une installation silencieuse ; l’installation ne vous demande pas d’accepter le Contrat de Licence Utilisateur Final.
   
        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Étape 2 : Sélection du connecteur avec Azure Active Directory
Vous pouvez effectuer cette étape à l’aide d’une des méthodes suivantes :

* Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
* Inscription du connecteur à l’aide d’un jeton créé hors connexion

### Inscription du connecteur à l’aide d’un objet d’informations d’identification Windows PowerShell
1. Créez l’objet d’informations d’identification de Windows PowerShell en exécutant la commande suivante, où les valeurs « <username> » et « <password> » doivent être remplacées par le nom d’utilisateur et le mot de passe pour accéder à votre annuaire :
   
        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
2. Accédez à **C:\\Program Files\\Microsoft AAD App Proxy Connector** et exécutez le script, où $cred représente l’objet d’informations d’identification PowerShell que vous avez créé :
   
        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred

### Inscription du connecteur à l’aide d’un jeton créé hors connexion
1. Créez un jeton hors connexion à l’aide de la classe AuthenticationContext, en utilisant les valeurs de l’extrait de code :

        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





1. Une fois que vous disposez du jeton, créez une chaîne SecureString en utilisant le jeton : <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
2. Exécutez la commande Windows PowerShell suivante, où SecureToken est le nom du jeton que vous avez créé précédemment et tenantID le GUID de votre locataire : <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`

## Voir aussi
* [Activation du proxy d’application Azure AD](active-directory-application-proxy-enable.md)
* [Publier des applications avec votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
* [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
* [Résoudre les problèmes rencontrés avec le proxy d’application](active-directory-application-proxy-troubleshoot.md)

Pour les dernières nouvelles et mises à jour, visitez [Application Proxy blog](http://blogs.technet.com/b/applicationproxyblog/) (blog sur les proxys d’application)

<!---HONumber=AcomDC_0622_2016-->