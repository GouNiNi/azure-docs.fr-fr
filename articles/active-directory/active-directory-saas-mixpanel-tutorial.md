---
title: 'Didacticiel : Intégration d’Azure AD à Mixpanel | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mixpanel.
services: active-directory
documentationcenter: ''
author: jeevansd
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: jeedes

---
# <a name="tutorial:-azure-active-directory-integration-with-mixpanel"></a>Didacticiel : Intégration d’Azure Active Directory à Mixpanel
L’objectif de ce didacticiel est de vous montrer comment intégrer Mixpanel dans Azure AD (Azure Active Directory).

L’intégration de Mixpanel dans Azure AD vous offre les avantages suivants :

* Dans Azure AD, vous pouvez contrôler qui a accès à Mixpanel
* Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Mixpanel (via l’authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration d’Azure AD avec Mixpanel, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement Mixpanel pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Mixpanel à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mixpanel-from-the-gallery"></a>Ajout de Mixpanel à partir de la galerie
Pour configurer l’intégration de Mixpanel à Azure AD, vous devez ajouter Mixpanel à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Mixpanel à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1]
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2]
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3]
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4]
6. Dans la zone de recherche, entrez **Mixpanel**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_01.png)
7. Dans le volet des résultats, sélectionnez **Mixpanel**, puis cliquez sur **Terminé** pour ajouter l’application.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec Mixpanel avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Mixpanel équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Mixpanel associé.

Pour cela, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Mixpanel.

Pour configurer et tester l’authentification unique Azure AD avec Mixpanel, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Mixpanel](#creating-a-mixpanel-test-user)** pour avoir un équivalent de Britta Simon dans Mixpanel lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application Mixpanel.

**Pour configurer l’authentification unique Azure AD avec Mixpanel, procédez comme suit :**

1. Dans le portail Azure Classic, sur la page d’intégration de l’application **Mixpanel**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Mixpanel ?**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_03.png) 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_04.png) 

    a. Dans la zone de texte **URL d’authentification**, entrez l’URL utilisée par vos utilisateurs pour se connecter à votre application Mixpanel, au format suivant : **« https://mixpanel.com/login/ »**.

    > [AZURE.NOTE] Inscrivez-vous à l’adresse [https://mixpanel.com/register/](https://mixpanel.com/register/) pour configurer vos informations d’identification et contactez [l’équipe du support technique Mixpanel](mailto:support@Mixpanel.com) pour activer les paramètres d’authentification unique de votre client. Vous pouvez également obtenir la valeur de l’URL de connexion si nécessaire à partir de votre équipe de support Mixpanel.

    b. Cliquez sur **Suivant**.



1. Sur la page **Configurer l’authentification unique sur Mixpanel** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_05.png) 
   
    a. Cliquez sur **Télécharger le certificat**, puis enregistrez le fichier sur votre ordinateur. 
   
    b. Cliquez sur **Next**.
2. Dans une autre fenêtre de navigateur, connectez-vous à votre application Mixpanel en tant qu’administrateur.
3. Dans le coin gauche en bas de la page, cliquez sur l’icône représentant un petit **engrenage** . 
   
    ![Authentification unique Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) 
4. Cliquez sur l’onglet **Sécurité d’accès**, puis cliquez sur **Modifier les paramètres**.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) 
5. Sur la page **Modifier votre certificat**, cliquez sur **Choisir un fichier** pour charger votre certificat téléchargé, puis cliquez sur **Suivant**.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) 
6. Dans le portail Azure Classic, sur la page **Configurer l’authentification unique sur Mixpanel**, copiez la valeur de **l’URL du service d’authentification unique**, puis collez-la dans la zone de texte URL d’authentification dans la page **Modifier votre URL d’authentification** et cliquez sur **Suivant**.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) 
7. Cliquez sur **Done**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][10]
9. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Authentification unique Azure AD][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.
Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_09.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Next**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit :
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_06.png) 
   
   a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
   b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
   c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
   d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.
   
   e. Cliquez sur **Next**.
7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **Créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-a-mixpanel-test-user"></a>Création d’un utilisateur de test Mixpanel
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Mixpanel. 

1. Connectez-vous à votre site d’entreprise Mixpanel en tant qu’administrateur.
2. Dans le coin gauche en bas de la page, cliquez sur le bouton représentant un petit engrenage pour ouvrir la fenêtre **Paramètres** .
3. Cliquez sur l’onglet **Équipe** .
4. Dans la zone de texte **membre de l’équipe** , entrez l’adresse de messagerie de Britta dans le portail Azure.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) 
5. Cliquez sur **Inviter**. 

L’utilisateur recevra un courrier électronique pour configurer son profil.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à Mixpanel.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Mixpanel, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, cliquez dans la vue de répertoire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **Mixpanel**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_50.png) 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Mixpanel dans le volet d’accès, vous êtes connecté automatiquement à votre application Mixpanel.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_205.png



<!--HONumber=Oct16_HO2-->


