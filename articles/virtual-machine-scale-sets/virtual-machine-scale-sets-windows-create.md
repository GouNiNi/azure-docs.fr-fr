---
title: "Création d’un jeu de mise à l’échelle de machine virtuelle Windows à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Création d’un groupe identique de machines virtuelles à l’aide de PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7bb03323-8bcc-4ee4-9a3e-144ca6d644e2
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 6d70338ebf918a3f9178a4f633dd46a607d72b1c


---
# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Créer un jeu de mise à l’échelle de machine virtuelle Windows à l’aide d’Azure PowerShell
Ces étapes utilisent une méthode de cases à remplir pour créer un groupe identique de machine virtuelle Azure. Consultez la rubrique [Vue d’ensemble des jeux de mise à l’échelle de machine virtuelle](virtual-machine-scale-sets-overview.md) pour en savoir plus sur les jeux de mise à l’échelle.

Il vous faudra environ 30 minutes pour effectuer les étapes décrites dans cet article.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : installer Azure PowerShell
Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## <a name="step-2-create-resources"></a>Étape 2 : Créer des ressources
Créez les ressources nécessaires à votre nouveau jeu de mise à l'échelle.

### <a name="resource-group"></a>Groupe de ressources
Un jeu de mise à l'échelle de machine virtuelle doit figurer dans un groupe de ressources.

1. Obtenez la liste des emplacements disponibles où placer des ressources :
   
        Get-AzureLocation | Sort Name | Select Name
2. Choisissez l’emplacement qui vous convient le mieux, remplacez la valeur de **$locName** par le nom de cet emplacement et créez la variable :
   
        $locName = "location name from the list, such as Central US"
3. Remplacez la valeur de **$rgName** par le nom que vous souhaitez utiliser pour le nouveau groupe de ressources, puis créez la variable : 
   
        $rgName = "resource group name"
4. Créez le groupe de ressources :
   
        New-AzureRmResourceGroup -Name $rgName -Location $locName
   
    Un résultat comme l’exemple suivant devrait s’afficher :
   
        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Compte de stockage
Un compte de stockage est utilisé par une machine virtuelle pour stocker le disque du système d’exploitation et les données de diagnostic utilisées pour la mise à l’échelle. Lorsque cela est possible, il est recommandé d’utiliser un compte de stockage pour chaque machine virtuelle créée dans un jeu identique. Dans le cas contraire, ne prévoyez pas plus de 20 machines virtuelles par compte de stockage. L’exemple présenté dans cet article montre 3 comptes de stockage créés pour 3 machines virtuelles.

1. Remplacez la valeur de **$stName** par le nom du compte de stockage. Testez l’unicité du nom choisi. 
   
        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName
   
    Si la réponse est **True**, le nom proposé est unique.
2. Remplacez la valeur de **$saType** par le type de compte de stockage, puis créez la variable :  
   
        $saType = "storage account type"
   
    Les valeurs possibles sont : Standard_LRS, Standard_GRS, Standard_RAGRS ou Premium_LRS.
3. Créez le compte :
   
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName
   
    Un résultat comme l’exemple suivant devrait s’afficher :
   
        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
4. Répétez les étapes 1 à 4 pour créer 3 comptes de stockage, par exemple myst1, myst2 et myst3.

### <a name="virtual-network"></a>réseau virtuel
Un réseau virtuel est requis pour les machines virtuelles dans le jeu de mise à l'échelle.

1. Remplacez la valeur de **$subName** par le nom que vous souhaitez utiliser pour le sous-réseau du réseau virtuel, puis créez la variable : 
   
        $subnetName = "subnet name"
2. Créez la configuration de sous-réseau :
   
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   
    Le préfixe d’adresse peut être différent dans votre réseau virtuel.
3. Remplacez la valeur de **$netName** par le nom que vous souhaitez utiliser pour le réseau virtuel, puis créez la variable : 
   
        $netName = "virtual network name"
4. Créez le réseau virtuel :
   
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuration du jeu de mise à l’échelle
Vous disposez de toutes les ressources dont vous avez besoin pour la configuration du jeu de mise à l’échelle. Nous allons donc le créer.  

1. Remplacez la valeur de **$ipName** par le nom que vous souhaitez utiliser pour la configuration de l’adresse IP, puis créez la variable : 
   
        $ipName = "IP configuration name"
2. Créez la configuration de l’adresse IP :
   
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id
3. Remplacez la valeur de **$vmssConfig** par le nom que vous souhaitez utiliser pour la configuration du groupe identique, puis créez la variable :   
   
        $vmssConfig = "Scale set configuration name"
4. Créez la configuration pour votre jeu de mise à l’échelle :
   
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
   
    Cet exemple montre un jeu de mise à l’échelle créé avec 3 machines virtuelles. Consultez la rubrique [Vue d’ensemble des groupes identiques dde machines virtuelles](virtual-machine-scale-sets-overview.md) pour en savoir plus sur la capacité des jeux de mise à l’échelle. Cette étape inclut également la définition de la taille (appelée SkuName) des machines virtuelles dans le jeu. Consultez la rubrique [Tailles des machines virtuelles dans Azure](../virtual-machines/virtual-machines-windows-sizes.md) pour trouver une taille adaptée à vos besoins.
5. Ajoutez la configuration de l’interface réseau à la configuration du jeu de mise à l’échelle :
   
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
   
    Un résultat comme l’exemple suivant devrait s’afficher :
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Profil de système d’exploitation
1. Remplacez la valeur de **$computerName** par le préfixe de nom d’ordinateur que vous souhaitez utiliser, puis créez la variable : 
   
        $computerName = "computer name prefix"
2. Remplacez la valeur de **$adminName** par le nom du compte d’administrateur sur les machines virtuelles, puis créez la variable :
   
        $adminName = "administrator account name"
3. Remplacez la valeur de **$adminPassword** par le mot de passe du compte, puis créez la variable :
   
        $adminPassword = "password for administrator accounts"
4. Créez le profil de système d’exploitation :
   
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Profil de stockage
1. Remplacez la valeur de **$storageProfile** par le nom que vous souhaitez utiliser pour le profil de stockage, puis créez la variable :  
   
        $storageProfile = "storage profile name"
2. Créez les variables qui définissent l’image à utiliser :  
   
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
   
    Consultez [Parcourir et sélectionner des images de machines virtuelles Windows dans Azure avec l’interface CLI ou PowerShell](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md) pour identifier les informations sur d’autres images à utiliser.
3. Remplacez la valeur de **$vhdContainer** par le chemin d’accès dans lequel les disques durs virtuels sont stockés, tels que « https://mystorage.blob.core.windows.net/vhds », puis créez la variable :
   
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
4. Créez le profil de stockage :
   
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle
Enfin, vous pouvez créer le jeu de la mise à l’échelle.

1. Remplacez la valeur de **$vmssName** par le nom du groupe identique de machine virtuelle, puis créez la variable :
   
        $vmssName = "scale set name"
2. Créez le jeu de mise à l’échelle :
   
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss
   
    Vous devriez obtenir quelque chose de similaire à cet exemple qui montre un déploiement réussi :
   
        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Étape 3 : Explorer les ressources
Utilisez ces ressources pour explorer le jeu de mise à l’échelle de machine virtuelle que vous venez de créer :

* Portail Azure : une quantité limitée d’informations est disponible via le portail.
* [Explorateur de ressources Azure](https://resources.azure.com/) : cet outil est le meilleur pour déterminer l’état actuel de votre jeu de mise à l’échelle.
* Azure PowerShell : utilisez cette commande pour obtenir des informations :
  
        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
  
        Or 
  
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## <a name="next-steps"></a>Étapes suivantes
* Gérer le jeu de mise à l’échelle que vous avez créé à l’aide des informations figurant dans [Gérer des machines dans un groupe identique de machines virtuelles (en anglais)](virtual-machine-scale-sets-windows-manage.md)
* Vous pouvez configurer la mise à l'échelle automatique de votre groupe identique à l'aide des informations fournies dans la rubrique [Mise à l’échelle automatique et groupes identiques de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md)
* Pour en savoir plus sur la mise à l’échelle verticale, consultez l’article [Mise à l’échelle verticale avec des groupes identiques de machines virtuelles](virtual-machine-scale-sets-vertical-scale-reprovision.md)




<!--HONumber=Nov16_HO2-->


