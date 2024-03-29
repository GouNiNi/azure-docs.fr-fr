---
title: Référence d’API d’audit Azure Active Directory | Microsoft Docs
description: Prise en main de l’API d’audit Azure Active Directory
services: active-directory
documentationcenter: ''
author: dhanyahk
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: dhanyahk;markvi

---
# <a name="azure-active-directory-audit-api-reference"></a>Référence d’API d’audit Azure Active Directory
Cette rubrique fait partie d’un ensemble de rubriques relatives à l’API de création de rapports Azure Active Directory.  
La création de rapports Azure AD fournit une API qui vous permet d’accéder aux données d’audit à l’aide de code ou d’outils associés.
Cette rubrique a pour but de vous fournir des informations de référence sur **l’API d’audit**.

Consultez l'article :

* [Journaux d’audit](active-directory-reporting-azure-portal.md#audit-logs) pour plus d’informations conceptuelles
* [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md) pour plus d’informations sur l’API de création de rapports.

Si vous avez des questions, des problèmes ou des commentaires, veuillez contacter [Aide à la création de rapports AAD](mailto:aadreportinghelp@microsoft.com).

## <a name="who-can-access-the-data?"></a>Qui peut accéder aux données ?
* Utilisateurs ayant le rôle d’administrateur de sécurité ou de lecteur de la sécurité
* Administrateurs généraux
* Toute application qui a l’autorisation d’accéder à l’API (l’autorisation de l’application peut être configurée uniquement en fonction de l’autorisation Administrateur général)

## <a name="prerequisites"></a>Composants requis
Pour accéder à ce rapport via l’API de création de rapports, vous devez disposer des éléments suivants :

* Une [édition Azure Active Directory gratuite ou une édition récente](active-directory-editions.md)
* Avoir respecté la [configuration requise pour accéder à l’API de création de rapports Azure AD](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Accès à l’API
Vous pouvez soit accéder à cette API via [l’Afficheur Graph](https://graphexplorer2.cloudapp.net) , soit par programme à l’aide, par exemple, de PowerShell. Pour que PowerShell puisse interpréter correctement la syntaxe de filtre OData utilisée dans les appels REST Graph AAD, vous devez utiliser le caractère accent grave (`) pour « échapper » au caractère $. Le caractère accent grave sert de [caractère d’échappement de PowerShell](https://technet.microsoft.com/library/hh847755.aspx), ce qui permet à PowerShell d’effectuer une interprétation littérale du caractère $ et de ne pas le confondre avec un nom de variable PowerShell (par exemple : $filter).

Cette rubrique porte sur l’Afficheur Graph. Pour obtenir un exemple PowerShell, consultez ce [script PowerShell](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>Point de terminaison d’API
Vous pouvez accéder à cette API à l’aide de l’URI suivant :  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Il n’existe aucune limite quant au nombre d’enregistrements retournés par l’API d’audit Azure AD (à l’aide de la pagination OData).
Pour connaître les limites de rétention de données de rapports, consultez [Stratégies de rétention des rapports](active-directory-reporting-retention.md).

Cet appel renvoie les données par lots. Chaque lot comporte un maximum de 1 000 enregistrements.  
Pour obtenir le lot d’enregistrements suivant, cliquez sur le lien Suivant. Obtenez les informations du jeton d’évitement dans le premier jeu d’enregistrements retournés. Le jeton d’évitement se trouve à la fin du jeu de résultats.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Filtres pris en charge
Vous pouvez réduire le nombre d’enregistrements qui sont retournés par un appel d’API à l’aide d’un filtre.  
Pour les données liées à l’API de connexion, les filtres suivants sont pris en charge :

* **$top=\<<nombre d’enregistrements à retourner>\>** : pour limiter le nombre d’enregistrements retournés. Il s’agit d’une opération coûteuse. N’utilisez pas ce filtre si vous souhaitez retourner des milliers d’objets.     
* **$filter=\<<votre instruction de filtre>\>** : pour spécifier, en fonction des champs de filtre pris en charge, les types d’enregistrements qui vous intéressent

## <a name="supported-filter-fields-and-operators"></a>Opérateurs et champs de filtre pris en charge
Pour indiquer le type d’enregistrements qui vous intéressent, vous pouvez créer une déclaration de filtre contenant l’un des champs de filtre suivants ou une combinaison de ceux-ci :

* [activityDate](#activitydate) : définit une date ou une plage de dates
* [activityType](#activitytype) : définit le type d’une activité
* [activity](#activity) : définit l’activité en tant que chaîne  
* [actor/name](#actorname) : définit l’acteur sous forme de nom de l’acteur
* [actor/objectid](#actorobjectid) : définit l’acteur sous forme de l’ID de l’acteur   
* [actor/upn](#actorupn) : définit l’acteur sous forme de nom d’utilisateur principal (UPN) de l’acteur 
* [target/name](#targetname) : définit la cible sous forme de nom de l’acteur
* [target/objectid](#targetobjectid) : définit la cible sous forme de l’ID de la cible  
* [target/upn](#targetupn) : définit l’acteur sous forme de nom d’utilisateur principal (UPN) de l’acteur   

- - -
### <a name="activitydate"></a>activityDate
**Opérateurs pris en charge**: eq, ge, le, gt, lt

**Exemple**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**Remarques**:

datetime doit être au format UTC

- - -
### <a name="activitytype"></a>activityType
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=activityType eq 'User'  

**Remarques**:

respecte la casse

- - -
### <a name="activity"></a>activity
**Opérateurs pris en charge**: eq, contains, startsWith

**Exemple**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**Remarques**:

respecte la casse

- - -
### <a name="actor/name"></a>actor/name
**Opérateurs pris en charge**: eq, contains, startsWith

**Exemple**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**Remarques**:

ne respecte pas la casse

- - -
### <a name="actor/objectid"></a>actor/objectid
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

- - -
### <a name="target/name"></a>target/name
**Opérateurs pris en charge**: eq, contains, startsWith

**Exemple**:

    $filter=targets/any(t: t/name eq 'some name')   

**Remarques**:

ne respecte pas la casse

- - -
### <a name="target/upn"></a>target/upn
**Opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**Remarques**:

* Non-respect de la casse
* Vous devez ajouter l’espace de noms complet lors de l’interrogation de Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity

- - -
### <a name="target/objectid"></a>target/objectid
**Opérateurs pris en charge**: eq

**Exemple**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actor/upn"></a>actor/upn
**Opérateurs pris en charge**: eq, startsWith

**Exemple**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**Remarques**:

* ne respecte pas la casse 
* Vous devez ajouter l’espace de noms complet lors de l’interrogation de Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity

- - -
## <a name="next-steps"></a>Étapes suivantes
* Voulez-vous voir des exemples d’activités système filtrées ? Consultez les [exemples d’API d’audit Azure Active Directory](active-directory-reporting-api-audit-samples.md).
* Vous souhaitez en savoir plus sur l’API de création de rapports Azure AD ? Consultez [Prise en main de l’API de création de rapports Azure Active Directory](active-directory-reporting-api-getting-started.md).

<!--HONumber=Oct16_HO2-->


