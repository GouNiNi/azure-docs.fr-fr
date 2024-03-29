---
title: 'Azure AD et applications : affectations de groupes à une application | Microsoft Docs'
description: Implémentation de l’attribution de groupe pour les applications Azure.
services: active-directory
documentationcenter: ''
author: IHenkel
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2015
ms.author: inhenk

---
# Azure AD et applications : affectation de groupes à une application
Avant d'affecter des utilisateurs et des groupes à une application, vous devez demander l'affectation de l'utilisateur. Pour savoir comment demander l’affectation d’utilisateurs, consultez l’article [Demande de l’affectation de l’utilisateur](active-directory-applications-guiding-developers-requiring-user-assignment.md).

Cet article suppose que vous avez déjà créé des groupes dans le répertoire actif que vous utilisez pour cette application.

## Affectation de groupes à une application
1. Connectez-vous au portail Azure avec un compte administrateur.
2. Dans le menu principal, cliquez sur **Tous les éléments**.
3. Choisissez le répertoire que vous utilisez pour l’application.
4. Cliquez sur l’onglet **APPLICATIONS**.
5. Sélectionnez l'application dans la liste des applications associées à ce répertoire.
6. Cliquez sur l’onglet **UTILISATEURS ET GROUPES**.
7. Filtrez la liste des groupes dans le répertoire actif à l’aide de la liste déroulante **Groupes**.
8. Sélectionnez le groupe.
9. Cliquez sur **AFFECTER**.
10. Cliquez sur **Oui** lorsque vous y êtes invité.

## Étapes suivantes
[!INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]

<!---HONumber=AcomDC_0928_2016-->