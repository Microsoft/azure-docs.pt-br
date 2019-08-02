---
title: Usar o Azure PowerShell para habilitar o diagnóstico em uma máquina virtual Windows | Microsoft Docs
services: virtual-machines-windows
documentationcenter: ''
description: Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows
author: sbtron
manager: gwallace
editor: ''
ms.assetid: 2e6d88f2-1980-4a24-827e-a81616a0d247
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: saurabh
ms.openlocfilehash: 19d30d207e67e1dfd8cdec2fe9951c763a921a0e
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67706064"
---
# <a name="use-powershell-to-enable-azure-diagnostics-in-a-virtual-machine-running-windows"></a>Usar o PowerShell para habilitar o Diagnóstico do Azure em uma máquina virtual que executa o Windows

O Diagnóstico do Azure é a capacidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico para coletar dados de diagnóstico como logs de aplicativo ou contadores de desempenho de uma máquina virtual (VM) do Azure que executa o Windows. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="enable-the-diagnostics-extension-if-you-use-the-resource-manager-deployment-model"></a>Habilitar a extensão de diagnóstico se você usar o modelo de implantação do Gerenciador de Recursos
Você pode habilitar a extensão de diagnóstico enquanto cria uma VM do Windows por meio do modelo de implantação do Gerenciador de Recursos do Azure adicionando a configuração da extensão ao modelo do Gerenciador de Recursos. Consulte [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Azure Resource Manager](diagnostics-template.md).

Para habilitar a extensão de diagnóstico em uma VM existente criada por meio do modelo de implantação do Resource Manager, é possível usar o cmdlet do PowerShell [Set-AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiagnosticsextension) conforme mostrado abaixo.

    $vm_resourcegroup = "myvmresourcegroup"
    $vm_name = "myvm"
    $diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path


*$diagnosticsconfig_path* é o caminho para o arquivo que contém a configuração de diagnóstico em XML, conforme descrito no [exemplo](#sample-diagnostics-configuration) abaixo.  

Se o arquivo de configuração de diagnóstico especificar um elemento **StorageAccount** com um nome de conta de armazenamento, o script *Set-AzVMDiagnosticsExtension* definirá automaticamente a extensão de diagnóstico para enviar dados de diagnóstico para essa conta de armazenamento. Para isso funcionar, a conta de armazenamento precisa estar na mesma assinatura que a VM.

Se nenhuma **StorageAccount** tiver sido especificada na configuração de diagnóstico, você precisará passar o parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, o cmdlet sempre usará a conta de armazenamento que está especificada no parâmetro, não aquela que está especificada no arquivo de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente da assinatura da VM, você precisará passar explicitamente os parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnóstico está na mesma assinatura, uma vez que o cmdlet pode consultar e definir automaticamente o valor de chave ao habilitar a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente, o cmdlet talvez não consiga obter a chave automaticamente e você precisará especificá-la explicitamente por meio do parâmetro *StorageAccountKey* .  

    Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key

Depois que a extensão de diagnóstico estiver habilitada em uma VM, você poderá obter as configurações atuais usando o cmdlet [Get-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiagnosticsextension) .

    Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name

O cmdlet retorna *PublicSettings*, que contém a configuração de diagnóstico. Há dois tipos de configuração com suporte, WadCfg e xmlCfg. WadCfg é a configuração JSON e xmlCfg é a configuração XML em um formato codificado na Base64. Para ler o XML, você precisa decodificá-lo.

    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup -VMName $vm_name).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

O cmdlet [Remove-AzVmDiagnosticsExtension](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdiagnosticsextension) pode ser usado para remover a extensão de diagnóstico da VM.  

## <a name="enable-the-diagnostics-extension-if-you-use-the-classic-deployment-model"></a>Habilitar a extensão de diagnóstico se você usar o modelo de implantação clássico
É possível usar o cmdlet [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) para habilitar a extensão de diagnóstico em uma VM criada usando o modelo de implantação clássico. O exemplo a seguir mostra como criar uma nova VM por meio do modelo de implantação clássico com a extensão de diagnóstico habilitada.

    $VM = New-AzVMConfig -Name $VM -InstanceSize Small -ImageName $VMImage
    $VM = Add-AzureProvisioningConfig -VM $VM -AdminUsername $Username -Password $Password -Windows
    $VM = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    New-AzVM -Location $Location -ServiceName $Service_Name -VM $VM

Para habilitar a extensão de diagnóstico em uma VM existente que foi criada por meio do modelo de implantação clássico, primeiramente use o cmdlet [Get-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azurevm) para obter a configuração da VM. Em seguida, atualize a configuração de VM para incluir a extensão de diagnóstico usando o cmdlet [Set-AzureVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) . Por fim, aplique a configuração atualizada à VM usando [Update-AzureVM](https://docs.microsoft.com/powershell/module/servicemanagement/azure/update-azurevm).

    $VM = Get-AzVM -ServiceName $Service_Name -Name $VM_Name
    $VM_Update = Set-AzVMDiagnosticsExtension -DiagnosticsConfigurationPath $Config_Path -VM $VM -StorageContext $Storage_Context
    Update-AzVM -ServiceName $Service_Name -Name $VM_Name -VM $VM_Update.VM

## <a name="sample-diagnostics-configuration"></a>Exemplo de configuração de diagnóstico
O XML a seguir pode ser usado para a configuração de diagnóstico público com os scripts acima. Este exemplo de configuração transferirá vários contadores de desempenho para a conta de armazenamento de diagnóstico junto com erros de aplicativo, segurança e canais do sistema nos logs de eventos do Windows e quaisquer erros dos logs de infraestrutura do diagnóstico.

A configuração precisa ser atualizada para incluir o seguinte:

* O atributo *resourceID* do elemento **Métricas** precisa ser atualizado com a ID de recurso para a VM.
  
  * A ID do recurso pode ser criada usando o seguinte padrão: "/subscriptions/{*ID da assinatura para a assinatura com a VM*}/resourceGroups/{*O nome do grupo de recursos para a VM*}/providers/Microsoft.Compute/virtualMachines/{*O nome da VM*}".
  * Por exemplo, se a ID de assinatura para a assinatura em que a VM está em execução for **11111111-1111-1111-1111-111111111111**, o nome do grupo de recursos para o grupo de recursos for **MyResourceGroup** e o nome da VM for **MyWindowsVM**, o valor de *resourceID* será:
    
      ```xml
      <Metrics resourceId="/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/MyWindowsVM" >
      ```
  * Para obter mais informações sobre como as métricas são geradas com base na configuração de métricas e contadores de desempenho, veja a [Tabela de métricas do Diagnóstico do Azure no armazenamento](diagnostics-template.md#wadmetrics-tables-in-storage).
* O elemento **StorageAccount** precisa ser atualizado com o nome da conta de armazenamento de diagnóstico.
  
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
          <DiagnosticMonitorConfiguration overallQuotaInMB="4096">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU utilization" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Privileged Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU privileged time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% User Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="CPU user time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Processor Information(_Total)\Processor Frequency" sampleRate="PT15S" unit="Count">
            <annotation displayName="CPU frequency" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\System\Processes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Processes" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Thread Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Threads" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Process(_Total)\Handle Count" sampleRate="PT15S" unit="Count">
            <annotation displayName="Handles" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes In Use" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Memory usage" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory available" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory committed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Commit Limit" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory commit limit" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Paged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Pool Nonpaged Bytes" sampleRate="PT15S" unit="Bytes">
            <annotation displayName="Memory non-paged pool" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Read Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active read time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\% Disk Write Time" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk active write time" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Transfers/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Reads/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk read operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Writes/sec" sampleRate="PT15S" unit="CountPerSecond">
            <annotation displayName="Disk write operations" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Read Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk read speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Disk Write Bytes/sec" sampleRate="PT15S" unit="BytesPerSecond">
            <annotation displayName="Disk write speed" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Read Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average read queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\PhysicalDisk(_Total)\Avg. Disk Write Queue Length" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk average write queue length" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\% Free Space" sampleRate="PT15S" unit="Percent">
            <annotation displayName="Disk free space (percentage)" locale="en-us"/>
          </PerformanceCounterConfiguration>
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(_Total)\Free Megabytes" sampleRate="PT15S" unit="Count">
            <annotation displayName="Disk free space (MB)" locale="en-us"/>
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

## <a name="next-steps"></a>Próximas etapas
* Para obter orientações adicionais sobre como usar a funcionalidade do Diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [Habilitar o diagnóstico nos Serviços de Nuvem e nas Máquinas Virtuais do Azure](../../cloud-services/cloud-services-dotnet-diagnostics.md).
* [esquema de configuração de diagnóstico](https://msdn.microsoft.com/library/azure/mt634524.aspx) explica as várias opções de configurações de XML para a extensão de diagnóstico.

