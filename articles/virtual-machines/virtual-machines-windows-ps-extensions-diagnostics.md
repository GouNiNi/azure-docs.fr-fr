---
title: Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows | Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: Utilisation de PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows
author: sbtron
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh

---
# Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Azure Diagnostics est la fonctionnalité Azure qui active la collecte de données de diagnostic dans une application déployée. Vous pouvez utiliser l'extension de diagnostics pour collecter des données de diagnostic telles que les journaux des applications ou les compteurs de performances à partir d'une machine virtuelle Azure exécutant Windows. Cet article décrit comment utiliser Windows PowerShell activer l’extension de diagnostics pour une machine virtuelle. Consultez [Installer et configurer Azure PowerShell Azure](../powershell-install-configure.md) pour connaître les conditions requises pour cet article.

## Activer l'extension de diagnostics si vous utilisez le modèle de déploiement Resource Manager
Vous pouvez activer l'extension de diagnostics lors de la création d'une machine virtuelle Windows avec le modèle de déploiement Azure Resource Manager en ajoutant la configuration de l'extension au modèle Resource Manager. Consultez [Créer une machine virtuelle Windows avec des fonctionnalités de surveillance et de diagnostics à l’aide d’un modèle Azure Resource Manager](virtual-machines-windows-extensions-diagnostics-template.md).

Pour activer l'extension de diagnostics sur une machine virtuelle existante qui a été créée avec le modèle de déploiement Resource Manager, vous pouvez utiliser l'applet de commande PowerShell [Set-AzureRMVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt603499.aspx) comme indiqué ci-dessous.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig\_path* est le chemin d'accès au fichier contenant la configuration des diagnostics dans XML, comme décrit dans l'[exemple](#sample-diagnostics-configuration) ci-dessous.

Si le fichier de configuration des diagnostics spécifie un élément **StorageAccount** avec un nom de compte de stockage, le script *Set-AzureRMVMDiagnosticsExtension* définit automatiquement l'extension de diagnostics pour envoyer des données de diagnostic à ce compte de stockage. Pour que cela fonctionne, le compte de stockage doit appartenir au même abonnement que la machine virtuelle.

Si aucun **StorageAccount** n’a été spécifié dans la configuration des diagnostics, vous devez transmettre le paramètre *StorageAccountName* à l’applet de commande. Si le paramètre *StorageAccountName* est spécifié, l’applet de commande utilise toujours le compte de stockage spécifié dans le paramètre et non celui spécifié dans le fichier de configuration des diagnostics.

Si le compte de stockage de diagnostics appartient à un autre abonnement que celui de la machine virtuelle, vous devez transmettre explicitement les paramètres *StorageAccountName* et *StorageAccountKey* à l’applet de commande. Le paramètre *StorageAccountKey* n’est pas nécessaire lorsque le compte de stockage de diagnostics appartient au même abonnement si l’applet de commande peut interroger et définir automatiquement la valeur clé lors de l’activation de l’extension de diagnostics. Toutefois, si le compte de stockage de diagnostics appartient à un autre abonnement, l’applet de commande n’est peut-être pas en mesure d’obtenir automatiquement la clé, et vous devez explicitement spécifier celle-ci par le biais du paramètre *StorageAccountKey*.

    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Une fois que l'extension de diagnostics est activée sur une machine virtuelle, vous pouvez obtenir les paramètres actuels à l'aide de l'applet de commande [Get-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603678.aspx).

    Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

L'applet de commande renvoie *PublicSettings*, qui contient la configuration XML dans un format encodé en Base64. Pour lire le code CXML, vous devez le décoder.

    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

L’applet de commande [Remove-AzureRMVmDiagnosticsExtension](https://msdn.microsoft.com/library/mt603782.aspx) peut être utilisé pour supprimer l’extension de diagnostics à partir de la machine virtuelle.

## Activer l'extension de diagnostics si vous utilisez le modèle de déploiement classique
Vous pouvez utiliser l'applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx) pour activer une extension de diagnostics sur une machine virtuelle créée à l'aide du modèle de déploiement classique. L'exemple suivant indique comment créer une machine virtuelle à l'aide du modèle de déploiement classique avec l'extension de diagnostics activée.

    $VM = New-AzureVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzureVM -Location $Location -ServiceName $Service_Name -VM $VM

Pour activer l'extension de diagnostics sur une machine virtuelle existante créée avec le modèle de déploiement classique, utilisez d'abord l'applet de commande [Get-AzureVM](https://msdn.microsoft.com/library/mt589152.aspx) pour obtenir la configuration de la machine virtuelle. Mettez ensuite à jour la configuration de la machine virtuelle afin d'inclure l'extension de diagnostics à l'aide de l'applet de commande [Set-AzureVMDiagnosticsExtension](https://msdn.microsoft.com/library/mt589189.aspx). Pour finir, appliquez la configuration mise à jour à la machine virtuelle à l'aide de [Update-AzureVM](https://msdn.microsoft.com/library/mt589121.aspx).

    $VM = Get-AzureVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzureVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## Effectuer un échantillon de configuration de diagnostics
Le code XML suivant peut être utilisé pour la configuration publique de diagnostics avec les scripts ci-dessus. Cet exemple de configuration transférera les différents compteurs de performance pour le compte de stockage de diagnostics, ainsi que les erreurs d'application, de sécurité et de canaux de système dans les journaux d'événement Windows, ainsi que toutes les erreurs dans les journaux d'infrastructure de diagnostics.

La configuration doit être mise à jour pour inclure les éléments suivants :

* L'attribut *resourceID* de l'élément **Mesures** doit être mis à jour avec l'ID de ressource pour la machine virtuelle.
  
  * L’ID de ressource peut être construit à l’aide du modèle suivant : « /subscriptions/{*ID d’abonnement pour l’abonnement avec la machine virtuelle*}/resourceGroups/{*Nom du groupe de ressources pour la machine virtuelle*}/providers/Microsoft.Compute/virtualMachines/{*Nom de la machine virtuelle*} ».
  * Par exemple, si l’ID d’abonnement pour l’abonnement dans lequel la machine virtuelle est en cours d’exécution est **11111111-1111-1111-1111-111111111111**, le nom du groupe de ressources pour le groupe de ressources est **MyResourceGroup** et le nom de la machine virtuelle est **MyWindowsVM**. La valeur pour *resourceID* serait alors :
    
      ```
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Pour plus d’informations sur la façon dont les mesures sont générées en fonction de la configuration des compteurs de performances et des mesures, consultez [Table des mesures Diagnostics Azure pour le stockage](virtual-machines-windows-extensions-diagnostics-template.md#wadmetrics-tables-in-storage).
* L’élément **StorageAccount** doit être mis à jour avec le nom du compte de stockage de diagnostics.
  
    ```
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="fr-FR"/>
          </PerformanceCounterConfiguration>
        </PerformanceCounters>
        <Metrics resourceId="(Update with resource ID for the VM)" >
            <MetricAggregation scheduledTransferPeriod="PT1H"/>
            <MetricAggregation scheduledTransferPeriod="PT1M"/>
        </Metrics>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]"/>
          <DataSource name="Security!*[System[(Level = 1 or Level = 2)]"/>
          <DataSource name="System!*[System[(Level = 1 or Level = 2)]]"/>
        </WindowsEventLog>
          </DiagnosticMonitorConfiguration>
        </WadCfg>
        <StorageAccount>(Update with diagnostics storage account name)</StorageAccount>
    </PublicConfig>
    ```

## Étapes suivantes
* Pour obtenir une aide supplémentaire sur l’utilisation de la fonction Diagnostics Azure et d’autres techniques pour résoudre les problèmes, consultez la page [Activation de Diagnostics dans Azure Cloud Services et Azure Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md).
* Le [schéma de configuration des diagnostics](https://msdn.microsoft.com/library/azure/mt634524.aspx) explique les différentes options de configuration XML pour l'extension de diagnostics.

<!---HONumber=AcomDC_0824_2016-->