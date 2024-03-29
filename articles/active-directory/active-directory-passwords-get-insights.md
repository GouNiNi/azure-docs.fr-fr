---
title: Obtention d’informations grâce aux rapports sur la gestion des mots de passe | Microsoft Docs
description: Cet article explique comment utiliser les rapports pour obtenir des informations sur les opérations de gestion des mots de passe dans votre organisation.
services: active-directory
documentationcenter: ''
author: asteen
manager: femila
editor: curtand

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: asteen

---
# Obtention d’informations grâce aux rapports sur la gestion des mots de passe
> [!IMPORTANT]
> **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
> 
> 

Cette section explique comment utiliser les rapports de gestion des mots de passe Azure Active Directory pour savoir comment les utilisateurs utilisent la réinitialisation et la modification des mots de passe dans votre organisation.

* [**Vue d’ensemble des rapports de gestion des mots de passe**](#overview-of-password-management-reports)
* [**Comment visualiser les rapports de gestion des mots de passe**](#how-to-view-password-management-reports)
* [**Visualiser l’activité d’inscription à la réinitialisation de mot de passe dans votre organisation**](#view-password-reset-registration-activity)
* [**Visualiser l’activité de réinitialisation de mot de passe dans votre organisation**](#view-password-reset-activity)

## Vue d’ensemble des rapports de gestion des mots de passe
Une fois la réinitialisation des mots de passe déployée, l’une des étapes les plus courantes consiste à vérifier comment elle est utilisée dans votre organisation. Par exemple, vous souhaiterez peut-être savoir comment les utilisateurs s’inscrivent pour la réinitialisation des mots de passe ou combien de réinitialisations de mots de passe ont été effectuées au cours des derniers jours. Voici quelques-unes des questions auxquelles vous serez en mesure de répondre grâce aux rapports de gestion des mots de passe accessibles à l’heure actuelle dans le [portail de gestion Azure](https://manage.windowsazure.com) :

* Combien de personnes se sont inscrites pour la réinitialisation des mots de passe ?
* Qui s’est inscrit pour la réinitialisation des mots de passe ?
* Quelles sont les données inscrites par ces personnes ?
* Combien de personnes ont réinitialisé leurs mots de passe au cours des 7 derniers jours ?
* Quelles sont les méthodes les plus courantes employées par les utilisateurs ou les administrateurs pour réinitialiser leurs mots de passe ?
* Quels sont les problèmes courants rencontrés par les utilisateurs ou les administrateurs lors des tentatives d’utilisation de la fonctionnalité de réinitialisation des mots de passe ?
* Quels sont les administrateurs qui réinitialisent fréquemment leurs mots de passe ?
* Y a-t-il des activités suspectes en rapport avec la réinitialisation des mots de passe ?

## Comment visualiser les rapports de gestion des mots de passe
Pour accéder aux rapports de gestion des mots de passe, procédez comme suit :

1. Cliquez sur l’extension **Active Directory** dans le [portail de gestion Azure](https://manage.windowsazure.com).
2. Sélectionnez votre annuaire dans la liste qui apparaît dans le portail.
3. Cliquez sur l’onglet **Rapports**.
4. Regardez sous la section **Journaux d’activité**.
5. Sélectionnez le rapport **Activité de réinitialisation de mot de passe** ou **Activité d’inscription de réinitialisation de mot de passe**.
   
   ![][001]

## Comment accéder aux rapports de gestion des mots de passe depuis une API
Depuis août 2015, les rapports et les événements Azure AD prennent désormais en charge la récupération de toutes les informations incluses dans les rapports de réinitialisation des mots de passe et d’inscription de réinitialisation des mots de passe.

Pour accéder à ces données, vous devrez écrire une petite application ou un petit script permettant de les extraire de nos serveurs. [Procédure de prise en main de l'API de création de rapports Azure AD](active-directory-reporting-api-getting-started.md).

Une fois que votre script est opérationnel, vous devrez examiner les événements d’inscription et de réinitialisation des mots de passe que vous pouvez récupérer pour répondre à vos scénarios.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) : Répertorie les colonnes disponibles pour les événements de réinitialisation de mot de passe
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) : Répertorie les colonnes disponibles pour les événements d’inscription de réinitialisation de mot de passe

## Visualiser l’activité d’inscription à la réinitialisation de mot de passe dans votre organisation
Le rapport Activité d’inscription de réinitialisation de mot de passe montre toutes les inscriptions à la réinitialisation de mot de passe qui ont eu lieu dans votre organisation. Une inscription à la réinitialisation de mot de passe est affichée dans ce rapport pour tout utilisateur qui a inscrit des informations d’authentification dans le portail d’inscription à la réinitialisation de mot de passe ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

* **Période max** : 1 mois
* **Nombre maximal de lignes** : illimité
* **Téléchargeable** : Oui, via un fichier CSV
  
    ![][002]

### Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

* **Utilisateur** : utilisateur ayant tenté d’effectuer une opération d’inscription à la réinitialisation de mot de passe.
* **Rôle** : rôle de l’utilisateur dans l’annuaire.
* **Date et heure** : date et heure de la tentative.
* **Données inscrites** : données d’authentification fournies par l’utilisateur lors de l’inscription à la réinitialisation de mot de passe.

### Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

| des colonnes | Valeurs autorisées et leur signification |
| --- | --- |
| Données inscrites |**Adresse de messagerie de secours** : l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier<p><p>**Téléphone professionnel** : l’utilisateur a utilisé un téléphone professionnel pour s’authentifier<p>**Téléphone mobile** : l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier<p>**Questions de sécurité** : l’utilisateur a utilisé des questions de sécurité pour s’authentifier<p>**Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)** : se produit lorsqu’une stratégie à 2 voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe. |

## Visualiser l’activité de réinitialisation de mot de passe dans votre organisation
Ce rapport montre toutes les tentatives de réinitialisation de mot de passe qui se sont produites dans votre organisation.

* **Période max** : 1 mois
* **Nombre maximal de lignes** : illimité
* **Téléchargeable** : Oui, via un fichier CSV
  
    ![][003]

### Description des colonnes du rapport
La liste suivante décrit chacune des colonnes du rapport en détail :

1. **Utilisateur** : utilisateur ayant tenté d’effectuer une opération de réinitialisation de mot de passe (basé sur le champ ID utilisateur fourni quand l’utilisateur demande à réinitialiser un mot de passe).
2. **Rôle** : rôle de l’utilisateur dans l’annuaire.
3. **Date et heure** : date et heure de la tentative.
4. **Méthode(s) utilisée(s)** : méthodes d’authentification employées par l’utilisateur pour cette opération de réinitialisation.
5. **Résultat** : résultat final de l’opération de réinitialisation de mot de passe.
6. **Détails** : détails de la raison pour laquelle la réinitialisation de mot de passe a généré ce résultat. Inclut également les étapes d’atténuation que vous pouvez suivre pour résoudre une erreur inattendue.

### Description des valeurs du rapport
Le tableau suivant décrit les différentes valeurs autorisées pour chaque colonne :

| des colonnes | Valeurs autorisées et leur signification |
| --- | --- |
| Méthodes utilisées |**Adresse de messagerie de secours** : l’utilisateur a utilisé une autre adresse de messagerie électronique ou une adresse électronique d’authentification pour s’authentifier<p>**Téléphone professionnel** : l’utilisateur a utilisé un téléphone professionnel pour s’authentifier<p>**Téléphone mobile** : l’utilisateur a utilisé un téléphone portable ou un téléphone d’authentification pour s’authentifier<p>**Questions de sécurité** : l’utilisateur a utilisé des questions de sécurité pour s’authentifier<p>**Toute combinaison des éléments ci-dessus (par exemple, Adresse de messagerie de secours + Téléphone mobile)** : se produit lorsqu’une stratégie à 2 voies d’accès est spécifiée et indique les deux méthodes employées par l’utilisateur pour authentifier sa demande de réinitialisation de mot de passe. |
| Résultat |**Abandonné** : l’utilisateur a démarré la réinitialisation du mot de passe, puis s’est arrêté à mi-chemin sans terminer<p>**Bloqué** : le compte d’utilisateur n’a pas été autorisé à utiliser la réinitialisation de mot de passe, car il a tenté d’utiliser la page de réinitialisation de mot de passe ou une même méthode de réinitialisation de mot de passe un trop grand nombre de fois durant une période de 24 heures.<p>**Annulé** : l’utilisateur a démarré la réinitialisation du mot de passe, puis il a cliqué sur le bouton Annuler pour annuler la session en cours.<p>**Administrateur contacté** : l’utilisateur a rencontré un problème insoluble lors de sa session et il a cliqué sur le lien « Contactez votre administrateur » au lieu de terminer le flux de réinitialisation de mot de passe.<p>**Échec** : l’utilisateur n’a pas pu réinitialiser un mot de passe, probablement car il n’était pas configuré pour utiliser la fonctionnalité (par exemple, aucune licence, informations d’authentification manquantes, mot de passe géré localement mais écriture différée désactivée).<p>**Réussi** : la réinitialisation du mot de passe a réussi. |
| Détails |Voir le tableau ci-dessous |

### Valeurs autorisées pour la colonne Détails
Voici la liste des types de résultats que vous pouvez attendre lors de l’utilisation du rapport d’activité de réinitialisation du mot de passe :

| Détails | Type de résultat |
| --- | --- |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par e-mail. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par SMS sur mobile. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal sur mobile. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de vérification par appel vocal au bureau. |Abandonné |
| L’utilisateur a abandonné avant d’avoir effectué complètement l’option de questions de sécurité. |Abandonné |
| L’utilisateur a abandonné après avoir entré son ID utilisateur. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par e-mail. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par SMS sur mobile. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal sur mobile. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par appel vocal au bureau. |Abandonné |
| L’utilisateur a abandonné après avoir commencé l’option de vérification par questions de sécurité. |Abandonné |
| L’utilisateur a abandonné avant de choisir un nouveau mot de passe. |Abandonné |
| L’utilisateur a abandonné lors du choix d’un nouveau mot de passe. |Abandonné |
| L’utilisateur a entré un trop grand nombre de codes de vérification par SMS non valides et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel sur mobile et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois la vérification de la voix par appel au bureau et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois de répondre à des questions de sécurité et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a tenté un trop grand nombre de fois de vérifier un numéro de téléphone et est bloqué pour 24 heures. |Bloqué |
| L’utilisateur a abandonné avant de se soumettre aux méthodes d’authentification requises. |Annulé |
| L’utilisateur a abandonné avant de soumettre un nouveau mot de passe. |Annulé |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par e-mail. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par SMS sur mobile. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal sur mobile. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par appel vocal au bureau. |Administrateur contacté |
| L’utilisateur a contacté un administrateur après avoir essayé l’option de vérification par question de sécurité. |Administrateur contacté |
| La réinitialisation du mot de passe n’est pas activée pour cet utilisateur. Activez-la sous l’onglet Configuration pour résoudre ce problème. |Échec |
| L’utilisateur n’a pas de licence. Vous pouvez ajouter une licence à l’utilisateur pour résoudre ce problème. |Échec |
| L’utilisateur a tenté de réinitialiser depuis un appareil où les cookies ne sont pas activés. |Échec |
| Un nombre insuffisant de méthodes d’authentification sont définies pour le compte de l’utilisateur. Ajoutez des informations d’authentification pour résoudre ce problème. |Échec |
| Le mot de passe de l’utilisateur est géré localement. Vous pouvez activer l’écriture en différé du mot de passe pour résoudre ce problème. |Échec |
| Nous n’avons pas pu accéder au service de réinitialisation de votre mot de passe local. Vérifiez le journal des événements de votre ordinateur synchronisé. |Échec |
| Nous avons rencontré un problème lors de la réinitialisation du mot de passe local de l’utilisateur. Vérifiez le journal des événements de votre ordinateur synchronisé. |Échec |
| Cet utilisateur n’est pas membre du groupe utilisateurs de réinitialisation de mot de passe. Ajoutez cet utilisateur à ce groupe pour résoudre ce problème. |Échec |
| La réinitialisation du mot de passe a été désactivée pour ce locataire. Voir [ici](http://aka.ms/ssprtroubleshoot) pour résoudre ce problème. |Échec |
| L’utilisateur a réinitialisé le mot de passe. |Succeeded |

## Liens vers la documentation de réinitialisation du mot de passe
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux.
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**FAQ**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.

[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

<!---HONumber=AcomDC_0713_2016-->