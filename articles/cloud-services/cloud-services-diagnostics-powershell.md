---
title: Habilitar o diagnóstico nos serviços de nuvem do Azure (clássico) usando o PowerShell | Microsoft Docs
description: Saiba como usar o PowerShell para habilitar a coleta de dados de diagnóstico de um serviço de nuvem do Azure com a extensão Diagnóstico do Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618433"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Habilitar o diagnóstico nos serviços de nuvem do Azure (clássico) usando o PowerShell

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

Você pode coletar dados de diagnóstico, como logs de aplicativo, contador de desempenho, etc., a partir de um Serviço de Nuvem usando a extensão de Diagnóstico do Azure. Este artigo descreve como habilitar a extensão de Diagnóstico do Azure para um Serviço de Nuvem usando o PowerShell.  Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/) para os pré-requisitos necessários para este artigo.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>Habilitar a extensão de diagnóstico como parte da implantação de um Serviço de Nuvem
Essa abordagem é aplicável ao tipo de cenários de integração contínua no qual a extensão de diagnóstico pode ser habilitada como parte da implantação do serviço de nuvem. Ao criar uma nova implantação de serviço de nuvem, você pode habilitar a extensão de diagnóstico passando o parâmetro *ExtensionConfiguration* para o cmdlet [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment) . O parâmetro *ExtensionConfiguration* obtém uma série de configurações de diagnóstico que podem ser criadas usando o cmdlet [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) .

O exemplo a seguir mostra como você pode habilitar o diagnóstico para um serviço de nuvem com um WebRole e WorkerRole, cada um com uma configuração de diagnóstico diferente.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Se o arquivo de configuração do diagnóstico especificar um elemento `StorageAccount` com um nome de conta de armazenamento, o cmdlet `New-AzureServiceDiagnosticsExtensionConfig` usará automaticamente essa conta de armazenamento. Para que isso funcione, a conta de armazenamento precisa estar na mesma assinatura que o Serviço de Nuvem que está sendo implantado.

A partir do SDK 2.6 do Azure, os arquivos de configuração de extensão gerados pela saída de destino de publicação do MSBuild incluirá o nome da conta de armazenamento, com base na cadeia de configuração de diagnóstico especificada no arquivo de configuração de serviço (.cscfg). O script a seguir mostra como analisar os arquivos de configuração de extensão da saída de destino de publicação, e como configurar a extensão de diagnóstico para cada função durante a implantação do serviço de nuvem.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

O Visual Studio Online usa uma abordagem parecida para implantações automatizadas dos Serviços de Nuvem com a extensão de diagnóstico. Confira [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) para obter um exemplo completo.

Se nenhuma `StorageAccount` tiver sido especificada na configuração de diagnóstico, você precisará passar o parâmetro *StorageAccountName* para o cmdlet. Se o parâmetro *StorageAccountName* for especificado, o cmdlet sempre usará a conta de armazenamento que está especificada no parâmetro, não aquela que está especificada no arquivo de configuração de diagnóstico.

Se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente do Serviço de Nuvem, será necessário transmitir explicitamente os parâmetros *StorageAccountName* e *StorageAccountKey* para o cmdlet. O parâmetro *StorageAccountKey* não é necessário quando a conta de armazenamento de diagnóstico está na mesma assinatura, uma vez que o cmdlet pode consultar e definir automaticamente o valor de chave ao habilitar a extensão de diagnóstico. No entanto, se a conta de armazenamento de diagnóstico estiver em uma assinatura diferente, o cmdlet poderá não conseguir obter a chave automaticamente e você precisará especificar explicitamente a chave por meio do parâmetro *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>Habilitar extensão de diagnóstico em um Serviço de Nuvem existente
Você pode usar o cmdlet [Set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) para habilitar ou atualizar a configuração do diagnóstico em um Serviço de Nuvem que já está em execução.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>Obter a configuração de extensão de diagnóstico atual
Usar o cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) para obter a configuração de diagnóstico atual para um serviço de nuvem.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>Remover extensão de diagnóstico
Para desativar o diagnóstico em um serviço de nuvem, você pode usar o cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) .

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Se você habilitou a extensão de diagnóstico usando *set-AzureServiceDiagnosticsExtension* ou *New-AzureServiceDiagnosticsExtensionConfig* sem o parâmetro *role* , poderá remover a extensão usando *Remove-AzureServiceDiagnosticsExtension* sem o parâmetro *role* . Se o parâmetro de *função* foi usado ao habilitar a extensão, ele também deve ser usado ao remover a extensão.

Para remover a extensão de diagnóstico de cada função individual:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Próximas etapas
* Para obter orientação adicional sobre como usar o diagnóstico do Azure e outras técnicas para solucionar problemas, consulte [Habilitar o Diagnóstico nos Serviços de Nuvem do Azure e Máquinas Virtuais](cloud-services-dotnet-diagnostics.md).
* O [Esquema de Configuração de Diagnóstico](../azure-monitor/agents/diagnostics-extension-schema-windows.md) explica as várias opções de configurações de XML para a extensão de diagnóstico.
* Para saber como habilitar a extensão de diagnóstico para Máquinas Virtuais, consulte [Criar uma Máquina Virtual do Windows com monitoramento e diagnóstico usando o Modelo do Gerenciador de Recursos do Azure](../virtual-machines/extensions/diagnostics-template.md)