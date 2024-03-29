---
title: Gestion de la sécurité après la restauration d’une base de données vers un nouveau serveur ou le basculement d’une base de données vers une copie de base de données secondaire | Microsoft Docs
description: Cette rubrique décrit les considérations de sécurité pour la gestion de la sécurité après un basculement ou une restauration de la base de données.
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: monicar

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/16/2016
ms.author: carlrab

---
# Gestion de la sécurité de la base de données SQL Azure après la récupération d’urgence
> [!NOTE]
> [Active Geo-Replication](sql-database-geo-replication-overview.md) est désormais disponible pour toutes les bases de données de tous les niveaux de service.
> 
> 

## Vue d’ensemble des exigences d’authentification pour la récupération d’urgence
Cette rubrique décrit les exigences d’authentification requises pour configurer et contrôler la [géo-réplication active](sql-database-geo-replication-overview.md) et les opérations requises pour configurer l’accès utilisateur à la base de données secondaire. Elle explique également comment activer l’accès à la base de données restaurée après l’utilisation de la [géo-restauration](sql-database-recovery-using-backups.md#geo-restore). Pour plus d’informations sur les options de récupération, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).

## Récupération d’urgence avec des utilisateurs contenus
Contrairement aux utilisateurs classiques, qui doivent être mappés sur les connexions dans la base de données master, un utilisateur contenu est géré entièrement par la base de données elle-même. Cela a deux avantages. Dans le scénario de récupération d’urgence, les utilisateurs peuvent continuer de se connecter à la nouvelle base de données primaire ou à la base de données restaurée à l’aide de la géo-restauration sans configuration supplémentaire, car c’est la base de données qui gère les utilisateurs. Du point de vue de la connexion, cette configuration présente également des possibilités de mise à l’échelle et d’amélioration des performances. Pour plus d’informations, voir [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

L’inconvénient principal est que la gestion du processus de récupération d’urgence à grande échelle est plus difficile. Lorsque plusieurs de vos bases de données utilisent la même connexion, maintenir les informations d'identification avec des utilisateurs contenus dans plusieurs bases de données peut anéantir les avantages des utilisateurs contenus. Par exemple, la stratégie de rotation des mots de passe nécessite de faire les modifications de façon cohérente dans plusieurs bases de données plutôt que de modifier le mot de passe de l’identifiant de connexion une fois dans la base de données principale. Pour cette raison, si vous avez plusieurs bases de données qui utilisent le même nom d’utilisateur et le même mot de passe, l’utilisation d’utilisateurs contenus est déconseillée.

## Configuration des identifiants de connexion et des utilisateurs
Si vous utilisez des identifiants de connexion et des utilisateurs (et non des utilisateurs contenus), vous devez prendre des mesures supplémentaires pour vous assurer que les mêmes identifiants de connexion existent dans la base de données principale. Les sections suivantes décrivent les étapes impliquées et d’autres considérations relatives.

### Configurer l’accès utilisateur à une base de données secondaire ou restaurée
Pour que la base de données secondaire puisse être utilisable en tant que base de données en lecture seule et pour garantir un accès approprié à la nouvelle base de données primaire ou à la base de données restaurée à l’aide de la géo-restauration, la configuration de sécurité appropriée doit être mise en œuvre sur la base de données principale du serveur cible avant la restauration.

Les autorisations spécifiques de chaque étape sont décrites plus tard dans cette rubrique.

La préparation de l’accès utilisateur à une base de données secondaire de géoréplication doit être effectuée dans le cadre de la configuration de la géoréplication. La préparation de l’accès utilisateur aux bases de données géo-restaurées doit être effectuée à tout moment lorsque le serveur d’origine est en ligne (par exemple dans le cadre du test de récupération d’urgence).

> [!NOTE]
> Si vous effectuez un basculement ou une géo-restauration vers un serveur sur lequel les identifiants de connexion ne sont pas configurés correctement, l’accès à ce serveur sera limité au compte d’administrateur du serveur.
> 
> 

La configuration des identifiants de connexion sur le serveur cible implique les trois étapes que voici :

#### 1\. Déterminez les connexions ayant accès à la base de données principale :
La première étape du processus consiste à déterminer les noms de connexion qui doivent être dupliqués sur le serveur cible. Pour ce faire, vous devez disposer d’une paire d’instructions SELECT, l’une dans la base de données master logique sur le serveur source et l’autre dans la base de données principale elle-même.

Seuls l’administrateur du serveur ou un membre avec le rôle serveur **LoginManager** peut déterminer les connexions sur le serveur source avec l’instruction SELECT suivante.

    SELECT [name], [sid] 
    FROM [sys].[sql_logins] 
    WHERE [type_desc] = 'SQL_Login'

Seul un membre du rôle db\_owner, l’utilisateur dbo ou l’administrateur du serveur peuvent déterminer toutes les entités de base de données utilisateur dans la base de données principale.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

#### 2\. Recherchez SID pour les connexions identifiées à l’étape 1 :
En comparant le résultat des requêtes issues de la section précédente et en faisant correspondre les SID, vous pouvez mapper la connexion serveur sur l’utilisateur de base de données. Les connexions dont l’utilisateur de base de données correspond au SID ont un accès utilisateur à cette base de données en tant que qu’utilisateur principal de base de données.

La requête suivante peut être utilisée pour voir toutes les entités utilisateur et leur SID dans une base de données. Seul un membre du rôle de base de données db\_owner ou un administrateur serveur peut exécuter cette requête.

    SELECT [name], [sid]
    FROM [sys].[database_principals]
    WHERE [type_desc] = 'SQL_USER'

> [!NOTE]
> Les utilisateurs de **INFORMATION\_SCHEMA** et **sys** ont des SID *NULL*, et le SID **invité** est **0x00**. Le SID **dbo** peut commencer par *0x01060000000001648000000000048454*, si le créateur de la base de données est l’administrateur serveur et non un membre de **DbManager**.
> 
> 

#### 3\. Créez les connexions sur le serveur cible :
La dernière étape consiste à accéder au(x) serveur(s) cible, et à générer les connexions avec les SID appropriés. La syntaxe de base est la suivante :

    CREATE LOGIN [<login name>]
    WITH PASSWORD = <login password>,
    SID = <desired login SID>

> [!NOTE]
> Si vous souhaitez accorder un accès utilisateur à la base de données secondaire, mais pas au serveur principal, vous pouvez le faire en modifiant la connexion de l’utilisateur sur le serveur principal à l’aide de la syntaxe suivante.
> 
> ALTER LOGIN <login name> DISABLE
> 
> DISABLE ne modifie pas le mot de passe, pour que vous puissiez toujours l’activer si nécessaire.
> 
> 

## Étapes suivantes
* Pour plus d’informations sur la gestion de l’accès aux bases de données et des identifiants de connexion, consultez [Sécurité SQL Database : gérer la sécurité d’accès et de connexion aux bases de données](sql-database-manage-logins.md).
* Pour plus d’informations sur les utilisateurs de base de données à relation contenant-contenu, consultez [Utilisateurs de base de données à relation contenant-contenu - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).
* Pour plus d’informations sur l’utilisation et la configuration de la géo-réplication active, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
* Pour plus d’informations sur l’utilisation de la restauration géographique, consultez [Restauration géographique](sql-database-recovery-using-backups.md#geo-restore)

## Ressources supplémentaires
<!---HONumber=AcomDC_0803_2016-->