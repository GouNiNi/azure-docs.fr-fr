---
title: Gestion du Contrôle d'accès en fonction du rôle avec l'interface de ligne de commande Azure | Microsoft Docs
description: Découvrez comment gérer le contrôle d'accès en fonction du rôle avec l'interface de ligne de commande Azure en répertoriant les rôles et les actions de rôle, et en affectant des rôles pour l'abonnement et l'application.
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2016
ms.author: kgremban

---
# Gestion du contrôle d’accès en fonction du rôle avec l’interface de ligne de commande Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)
> 
> 

Le contrôle d’accès en fonction du rôle (RBAC) disponible dans le portail Azure et l’API Azure Resource Manager permet une gestion très fine de l’accès à votre abonnement et à vos ressources. Cette fonctionnalité vous permet d’accorder l’accès aux utilisateurs, groupes et principaux du service Active Directory en leur affectant certains rôles avec une étendue spécifique.

Pour pouvoir utiliser l'interface de ligne de commande Azure (CLI) pour gérer le contrôle d’accès en fonction du rôle, vous devez disposer des composants suivants :

* Azure CLI version 0.8.8 ou ultérieure. Pour installer la dernière version et l’associer à votre abonnement Azure, consultez [Installer et configurer Azure CLI](../xplat-cli-install.md).
* Azure Resource Manager dans l’interface de ligne de commande Azure. Pour plus d’informations, consultez [Utilisation de l’interface de ligne de commande Azure avec Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

## Répertorier les rôles
### Répertorier tous les rôles disponibles
Pour répertorier tous les rôles, utilisez :

        azure role list

L'exemple suivant affiche la liste de *tous les rôles disponibles*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Ligne de commande Azure RBAC - liste des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### Répertorier les actions d'un rôle
Pour répertorier les actions d'un rôle, utilisez :

    azure role show "<role name>"

L'exemple suivant montre les actions des rôles *Collaborateur* et *Collaborateur de machine virtuelle*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Ligne de commande Azure RBAC - affichage des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## Répertorier les accès
### Répertorier les affectations de rôles valables dans un groupe de ressources
Pour répertorier les attributions de rôles qui existent dans un groupe de ressources, utilisez :

    azure role assignment list --resource-group <resource group name>

L’exemple suivant illustre les attributions de rôle dans le groupe *pharma-sales-projecforcast*.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Ligne de commande Azure RBAC - liste des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### Répertorier les attributions de rôles pour un utilisateur
Pour répertorier les attributions de rôle pour un utilisateur spécifique et les attributions affectées aux groupes d’un utilisateur, utilisez :

    azure role assignment list --signInName <user email>

Vous pouvez également afficher les affectations de rôles héritées de groupes en modifiant la commande :

    azure role assignment list --expandPrincipalGroups --signInName <user email>

L’exemple suivant montre les attributions de rôles octroyées à l’utilisateur *sameert@aaddemo.com*. Cela inclut les rôles attribués directement à l’utilisateur et ceux hérités des groupes.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Ligne de commande Azure RBAC - liste des affectations de rôle azure par utilisateur - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## Accorder l'accès
Pour accorder l'accès après avoir identifié le rôle que vous souhaitez affecter, utilisez :

    azure role assignment create

### Affecter un rôle à un groupe pour l'abonnement
Pour affecter un rôle à un groupe pour l'abonnement, utilisez :

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L'exemple suivant affecte le rôle *Lecteur* à l'*équipe de Christine Koch* pour l'*abonnement*.

![Ligne de commande Azure RBAC - création des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### Affectation d'un rôle à une application pour l'abonnement
Pour affecter un rôle à une application pour l'abonnement, utilisez :

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L’exemple suivant affecte le rôle *Collaborateur* à une application *Azure AD* pour l’abonnement sélectionné.

 ![Ligne de commande Azure RBAC - création de liste des affectations de rôle azure par utilisateur](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### Affectation d'un rôle à un utilisateur pour le groupe de ressources
Pour affecter un rôle à un utilisateur pour le groupe de ressources, utilisez :

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

L’exemple suivant affecte le rôle *Collaborateur de machine virtuelle* à l’utilisateur *samert@aaddemo.com* au groupe de ressources *Pharma-Sales-ProjectForcast*.

![Ligne de commande Azure RBAC - création d’affectation de rôle azure par utilisateur - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### Affectation d'un rôle à un utilisateur pour des ressources
Pour affecter un rôle à un groupe au niveau des ressources, utilisez :

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L’exemple suivant affecte le rôle *Collaborateur de machine virtuelle* à un groupe *Azure AD* dans un *sous-réseau*.

![Ligne de commande Azure RBAC - création des affectations de rôle azure par groupe - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## Suppression d'accès
Pour supprimer une affectation de rôle

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

L’exemple suivant supprime l’affectation du rôle *Collaborateur de machine virtuelle* de *sammert@aaddemo.com* pour le groupe de ressources *Pharma-Sales-ProjectForcast*. L'exemple supprime ensuite l'affectation de rôle du groupe pour l'abonnement.

![Ligne de commande Azure RBAC - suppression d’affectation de rôle - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Créer un rôle personnalisé
Pour créer un rôle personnalisé, utilisez :

    azure role create --inputfile <file path>

L’exemple suivant crée un rôle personnalisé appelé *Opérateur de machine virtuelle*. Le rôle personnalisé accorde l’accès à toutes les opérations des fournisseurs de ressources *Microsoft.Compute*, *Microsoft.Storage* et *Microsoft.Network* ainsi que l’accès pour démarrer, redémarrer et surveiller des machines virtuelles. Le rôle personnalisé peut être utilisé dans deux abonnements. Cet exemple utilise un fichier JSON en tant qu’entrée.

![JSON - définition de rôle personnalisé - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Ligne de commande Azure RBAC - création d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## Modifier un rôle personnalisé
Pour modifier un rôle personnalisé, utilisez d’abord la commande `azure role show` pour récupérer la définition de rôle. Apportez ensuite les modifications souhaitées au fichier de définition de rôle. Enfin, utilisez `azure role set` pour enregistrer la définition de rôle modifiée.

    azure role set --inputfile <file path>

L’exemple suivant ajoute l’opération *Microsoft.Insights/diagnosticSettings/* à **Actions** et un abonnement Azure à **AssignableScopes** du rôle personnalisé Opérateur de machine virtuelle.

![JSON - modifier la définition de rôle personnalisé - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Ligne de commande Azure RBAC - définition d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## Supprimer un rôle personnalisé
Pour supprimer un rôle personnalisé, utilisez tout d’abord la commande `azure role show` afin de déterminer la propriété **ID** du rôle. Ensuite, utilisez la commande `azure role delete` pour supprimer le rôle en spécifiant la propriété **ID**.

L’exemple suivant supprime le rôle personnalisé *Opérateur de machine virtuelle*.

![Ligne de commande Azure RBAC - suppression d’un rôle azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## Répertorier les rôles personnalisés
Pour répertorier les rôles pouvant être affectés dans une étendue, utilisez la commande `azure role list`.

L’exemple suivant répertorie tous les rôles pouvant être affectés dans l’abonnement sélectionné.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Ligne de commande Azure RBAC - liste des rôles azure - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Dans l’exemple suivant, le rôle personnalisé *Opérateur de machine virtuelle* n’est pas disponible dans l’abonnement *Production4*, car cet abonnement ne figure pas dans l’élément **AssignableScopes** du rôle.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Ligne de commande Azure RBAC - liste des rôles azure pour les rôles personnalisés - capture d’écran](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## Rubriques RBAC
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0810_2016-->
