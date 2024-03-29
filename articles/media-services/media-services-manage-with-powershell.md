---
title: Gérer des comptes Azure Media Services avec PowerShell
description: Apprenez à gérer des comptes Azure Media Services avec les applets de commande PowerShell.
author: Juliako
manager: erikre
editor: ''
services: media-services
documentationcenter: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2016
ms.author: juliako

---
# Gérer des comptes Azure Media Services avec PowerShell
> [!div class="op_single_selector"]
> * [Portail](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)
> 
> [!NOTE]
> Pour pouvoir créer un compte Azure Media Services, vous devez disposer d’un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Version d'évaluation gratuite d'Azure</a>.
> 
> 

## Vue d'ensemble
Cet article répertorie les applets de commande Azure PowerShell pour Azure Media Services (AMS) dans le framework Azure Resource Manager. Les applets de commande existent dans l'espace de noms **Microsoft.Azure.Commands.Media**.

## Versions
**ApiVersion** : "2015-10-01"

## New-AzureRmMediaService
Créez un service multimédia.

### Syntaxe
Jeu de paramètres : StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Jeu de paramètres : StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | Name |
| --- | --- |
| Requis ? |true |
| Position ? |1 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |false |
| Accepter les caractères génériques ? |false |

**-Location &lt;String&gt;**

Spécifie l’emplacement de la ressource du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |2 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-StorageAccountId &lt;String&gt;**

Spécifie un compte de stockage principal associé au service multimédia.

* Seul le nouveau compte de stockage (créé avec l’API Resource Manager) est pris en charge.
* Le compte de stockage doit exister et figurer dans le même emplacement que le service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |3 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Nom du jeu de paramètres |StorageAccountIdParamSet |
| Accepter les caractères génériques ? |false |

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Spécifie les comptes de stockage associés au service multimédia.

* Seul le nouveau compte de stockage (créé avec l’API Resource Manager) est pris en charge.
* Le compte de stockage doit exister et figurer dans le même emplacement que le service multimédia.
* Un seul compte de stockage peut être spécifié comme compte principal.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |3 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Nom du jeu de paramètres |StorageAccountsParamSet |
| Accepter les caractères génériques ? |false |

**-Tags &lt;Hashtable&gt;**

Spécifie une table de hachage des balises qui sont associées au service multimédia.

* Exemple : @{"tag1"="value1";"tag2"=:value2"}

| Alias | (aucun) |
| --- | --- |
| Requis ? |false |
| Position ? |named |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |false |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Set-AzureRmMediaService
Met à jour un service multimédia.

### Syntaxe
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | Name |
| --- | --- |
| Requis ? |True |
| Position ? |1 |
| Valeur par défaut |Aucun |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |False |

**-StorageAccounts &lt;PSStorageAccount[]&gt;**

Spécifie les comptes de stockage associés au service multimédia.

* Seul le nouveau compte de stockage (créé avec l’API Resource Manager) est pris en charge.
* Le compte de stockage doit exister et figurer dans le même emplacement que le service multimédia.
* Un seul compte de stockage peut être spécifié comme compte principal.

| Alias | (aucun) |
| --- | --- |
| Requis ? |false |
| Position ? |Nommé |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Nom du jeu de paramètres |StorageAccountsParamSet |
| Accepter les caractères génériques ? |false |

**-Tags &lt;Hashtable&gt;**

Spécifie une table de hachage des balises qui sont associées à ce service multimédia.

* Les balises associées au service multimédia sont remplacées par la valeur spécifiée par le client.

| Alias | (aucun) |
| --- | --- |
| Requis ? |False |
| Position ? |Nommé |
| Valeur par défaut |Aucun |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Remove-AzureRmMediaService
Supprime un service multimédia.

### Syntaxe
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |2 |
| Valeur par défaut |Aucun |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |False |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Get-AzureRmMediaService
Obtient tous les services multimédia d'un groupe de ressources ou un service multimédia avec un nom donné.

### Syntaxe
Jeu de paramètres : ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

Jeu de paramètres : AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Nom du jeu de paramètres |ResourceGroupParameterSet, AccountNameParameterSet |

Accepter les caractères génériques ? false

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |1 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Nom du jeu de paramètres |AccountNameParameterSet |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Get-AzureRmMediaServiceKeys
Obtient les clés d’un service multimédia.

### Syntaxe
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |1 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Set-AzureRmMediaServiceKey
Régénère une clé primaire ou secondaire d’un service multimédia.

### Syntaxe
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |1 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-KeyType &lt;KeyType&gt;**

Spécifie le type de clé du service multimédia.

* Principal ou secondaire

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |2 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |false |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Sync-AzureRmMediaServiceStorageKeys
Synchronise les clés de compte de stockage pour un compte de stockage associé au service multimédia.

### Syntaxe
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountName] <string>  [<CommonParameters>]

### Paramètres
**-ResourceGroupName &lt;String&gt;**

Spécifie le nom du groupe de ressources auquel appartient ce service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |0 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-AccountName &lt;String&gt;**

Spécifie le nom du service multimédia.

| Alias | (aucun) |
| --- | --- |
| Requis ? |true |
| Position ? |1 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**-StorageAccountId &lt;String&gt;**

Spécifie le compte de stockage associé au service multimédia.

| Alias | ID |
| --- | --- |
| Requis ? |true |
| Position ? |2 |
| Valeur par défaut |(aucun) |
| Accepter l'entrée de pipeline ? |True(ByPropertyName) |
| Accepter les caractères génériques ? |false |

**&lt;CommandParameters&gt;**

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable.

### Entrées
Le type d’entrée est le type des objets que vous pouvez envoyer à l’applet de commande.

### Sorties
Le type de sortie est le type des objets émis par l'applet de commande.

## Étape suivante
Consultez les chemins d’apprentissage de Media Services.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0907_2016-->