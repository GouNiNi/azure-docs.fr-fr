---
title: 'Considérations relatives à la conception d’identités hybrides Azure Active Directory : déterminer les exigences en matière d’authentification multifacteur'
description: Avec le contrôle d’accès conditionnel, Azure Active Directory vérifie les conditions spécifiques que vous choisissez lors de l’authentification de l’utilisateur et avant d’autoriser l’accès à l’application. Une fois que ces conditions sont remplies, l’utilisateur est authentifié et autorisé à accéder à l’application.
documentationcenter: ''
services: active-directory
author: femila
manager: billmath
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2016
ms.author: billmath

---
# Déterminer les besoins d'authentification multifacteur (Multi-Factor Authentication) pour votre solution d'identités hybrides
Dans un monde basé sur la mobilité, avec des utilisateurs qui accèdent à des données et à des applications dans le cloud et à partir de n'importe quel périphérique, la sécurisation de ces informations est devenue prééminente. On entend parler chaque jour de nouvelles failles de sécurité. Bien qu'il n'existe aucune garantie contre ces violations, l'authentification multifacteur (Multi-Factor Authentication), fournit une couche supplémentaire de sécurité afin de les empêcher. Commencez par évaluer les besoins des organisations pour en matière d'authentification multifacteur. Il s'agit de comprendre ce que l'organisation veut sécuriser. Cette version d'évaluation est importante pour définir les exigences techniques pour la configuration et l'actualisation des utilisateurs de l'organisation pour l'authentification multifacteur.

> [!NOTE]
> Si vous n'êtes pas familiarisé avec l'authentification multifacteur (MFA) et ses fonctionnalités, il est fortement recommandé de lire l'article [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) avant de poursuivre la lecture de cette section.
> 
> 

Veillez à répondre aux questions suivantes :

* Votre société tente-t-elle de sécuriser des applications Microsoft ?
* Comment ces applications sont-elles publiées ?
* Votre société fournit-elle un accès à distance pour permettre aux employés d'accéder aux applications locales ?

Si c'est le cas, de quel type d'accès à distance s'agit-il ? Vous devez également évaluer où les utilisateurs qui accèdent à ces applications se trouvent. Cette version d'évaluation est une autre étape importante pour définir la stratégie d'authentification multifacteur appropriée. Veillez à répondre aux questions suivantes :

* Où les utilisateurs se trouveront-ils ?
* Peuvent-ils se trouver à un endroit quelconque ?
* Votre société souhaite-t-elle établir des restrictions en fonction de la localisation de l'utilisateur ?

Une fois que vous avez compris ces exigences, il est également important d'évaluer les besoins de l'utilisateur en matière d'authentification multifacteur. Cette version d'évaluation est importante, car elle définit les conditions requises pour le déploiement de l'authentification multifacteur. Veillez à répondre aux questions suivantes :

* Les utilisateurs sont-ils familiers avec l'authentification multifacteur ?
* Certains utilisateurs auront-ils besoin d'une authentification supplémentaire ?
  * Si c'est le cas : tout le temps, lorsqu'ils proviennent de réseaux externes, ou accèdent à des applications spécifiques, ou sous d'autres conditions ?
* Les utilisateurs nécessiteront-ils une formation sur l'installation et la mise en œuvre de l'authentification multifacteur ?
* Quels sont les principaux scénarios pour lesquels votre société souhaite activer l'authentification multifacteur pour ses utilisateurs ?

Après avoir répondu à ces questions, vous serez en mesure de déterminer s'il une authentification multifacteur est déjà mise en œuvre localement. Cette version d'évaluation est importante pour définir les exigences techniques pour la configuration et l'actualisation des utilisateurs de l'organisation pour l'authentification multifacteur. Veillez à répondre aux questions suivantes :

* Votre société a-t-elle besoin de protéger des comptes privilégiés avec l'authentification multifacteur ?
* Votre société a-t-elle besoin d'activer l'authentification multifacteur pour certaines applications pour des raisons de conformité ?
* Votre société a-t-elle besoin d'activer l'authentification multifacteur pour tous les utilisateurs autorisés de ces applications ou uniquement pour les administrateurs ?
* L'authentification multifacteur doit-elle être toujours activée ou uniquement lorsque les utilisateurs se connectent en dehors votre réseau d'entreprise ?

## Étapes suivantes
[Définir une stratégie d’adoption des identités hybrides](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## Voir aussi
[Présentation des considérations relatives à la conception](active-directory-hybrid-identity-design-considerations-overview.md)

<!---HONumber=AcomDC_0810_2016-->