---
title: 'Didacticiel : Intégration d’Azure Active Directory à RunMyProcess | Microsoft Docs'
description: Apprenez à utiliser RunMyProcess avec Azure Active Directory pour activer l’authentification unique, l’approvisionnement automatique et bien plus encore.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-runmyprocess"></a>Didacticiel : Intégration d’Azure Active Directory à RunMyProcess
L’objectif de ce didacticiel est de montrer comment intégrer Azure et RunMyProcess.  
Le scénario décrit dans ce didacticiel part du principe que vous disposez des éléments suivants :

* Un abonnement Azure valide
* Un locataire RunMyProcess

À l’issue de ce didacticiel, les utilisateurs d’Azure AD que vous avez affectés à RunMyProcess pourront s’authentifier de manière unique dans l’application sur votre site d’entreprise RunMyProcess (connexion initiée par le fournisseur du service) ou en s’aidant de la [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel se compose des blocs de construction suivants :

1. Activation de l’intégration d’application pour RunMyProcess
2. Configuration de l'authentification unique
3. Configuration de l'approvisionnement des utilisateurs
4. Affectation d’utilisateurs

![Scénario](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "Scenario")

## <a name="enabling-the-application-integration-for-runmyprocess"></a>Activation de l’intégration d’application pour RunMyProcess
Cette section décrit l’activation de l’intégration d’application pour RunMyProcess.

### <a name="to-enable-the-application-integration-for-runmyprocess,-perform-the-following-steps:"></a>Pour activer l’application de l’intégration pour RunMyProcess, procédez comme suit :
1. Dans le volet de navigation gauche du portail Azure Classic, cliquez sur **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
   ![Applications](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "Applications")
4. Cliquez sur **Ajouter** en bas de la page.
   
   ![Ajouter une application](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "Add application")
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
   ![Ajouter une application à partir de la galerie](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "Add an application from gallerry")
6. Dans la **zone de recherche**, entrez **RunMyProcess**.
   
   ![Galerie d’applications](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "Application Gallery")
7. Dans le volet de résultats, sélectionnez **RunMyProcess**, puis cliquez sur **Terminer** pour ajouter l’application.
   
   ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
   
   ## <a name="configuring-single-sign-on"></a>Configuration de l'authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier sur RunMyProcess avec leur compte Azure AD en utilisant la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un fichier de certificat codé en base 64.  
Si cette procédure ne vous est pas familière, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o).

### <a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Pour configurer l’authentification unique, procédez comme suit :
1. Dans le portail Azure Classic, dans la page d’intégration d’application **RunMyProcess**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Configure Single Sign-On")
2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à RunMyProcess**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Configure Single Sign-On")
3. Dans la page **Configurer l’URL de l’application**, dans la zone de texte **URL de connexion de RunMyProcess**, tapez votre URL selon le modèle suivant *http://entreprise.projectplace.com*, puis cliquez sur **Suivant**.
   
   ![Configurer l’URL de l’application](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "Configure App URL")
4. Dans la page **Configurer l’authentification unique sur RunMyProcess**, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Configure Single Sign-On")
5. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise RunMyProcess en tant qu’administrateur.
6. Accédez à **Account \> Configuration**.
   
   ![Compte](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "Account")
7. Cliquez sur l’onglet **Authentication method** .
8. Dans la section **Authentication method** , procédez comme suit :
   
   ![Authentification unique](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")
   
   1. Pour **Method**, sélectionnez **SSO with Samlv2**.
   2. Dans la page **Configurer l’authentification unique sur RunMyProcess** du portail Azure Classic, copiez la valeur **URL SSO SAML** et collez-la dans la zone de texte **SSO redirect**.
   3. Dans la page **Configurer l’authentification unique sur RunMyProcess** du portail Azure Classic, copiez la valeur **URL du service d’authentification unique** et collez-la dans la zone de texte **Logout redirect**.
   4. Dans la zone de texte **Name id Format**, entrez **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**.
   5. Créez un fichier **codé en base 64** à partir du certificat téléchargé.  
      
      > [!TIP]
      > Pour plus d’informations, consultez [Comment convertir un certificat binaire en fichier texte](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Ouvrez votre certificat codé en base 64 dans le Bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificate** .
   7. Cliquez sur **Enregistrer**.
9. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer l’authentification unique**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>Configuration de l'approvisionnement des utilisateurs

Pour permettre aux utilisateurs Azure AD de se connecter à RunMyProcess, vous devez les approvisionner dans RunMyProcess.  
Dans le cas de RunMyProcess, l’approvisionnement est une tâche manuelle.

### <a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>Pour approvisionner un compte d’utilisateur, procédez comme suit :
1. Connectez-vous au site d’entreprise **RunMyProcess** en tant qu’administrateur.
2. Accédez à **Account \> Users**, puis cliquez sur **New User**.
   
   ![New User](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "New User")
3. Dans la section **User Settings** , procédez comme suit :
   
   ![Profil](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "Profile")
   
   1. Tapez le nom et l’adresse électronique d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans les zones de texte correspondantes, à savoir, **Name** et **E-mail**.
   2. Sélectionnez des options pour **IDE language**, **Language** et **Profile**.
   3. Sélectionnez **Send account creation e-mail to me**.
   4. Cliquez sur **Enregistrer**.

> [!NOTE]
> Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur, fourni par RunMyProcess, pour approvisionner des comptes utilisateur Azure Active Directory.
> 
> 

## <a name="assigning-users"></a>Affectation d’utilisateurs
Pour tester votre configuration, vous devez autoriser les utilisateurs d’Azure AD concernés à accéder à votre application.

### <a name="to-assign-users-to-runmyprocess,-perform-the-following-steps:"></a>Pour affecter des utilisateurs à RunMyProcess, procédez comme suit :
1. Dans le portail Azure Classic, créez un compte de test.
2. Dans la page d’intégration d’application **RunMyProcess**, cliquez sur **Affecter des utilisateurs**.
   
   ![Affecter des utilisateurs](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "Assign Users")
3. Sélectionnez votre utilisateur de test, cliquez sur **Affecter**, puis sur **Oui** pour confirmer votre affectation.
   
   ![Oui](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "Yes")

Si vous souhaitez tester vos paramètres d’authentification unique, ouvrez le volet d’accès. Pour plus d'informations sur le panneau d'accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

<!--HONumber=Oct16_HO2-->


