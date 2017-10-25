---
title: "Recursos e extensões da máquina virtual para Windows no Azure | Microsoft Docs"
description: "Saiba quais extensões estão disponíveis para as máquinas virtuais do Azure, agrupadas pelas funcionalidades fornecidas ou aperfeiçoadas."
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/06/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ce0eebd2585c9457d7f922898d7f2fa3e7ffad7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Recursos e extensões da máquina virtual para Windows

Extensões da máquina virtual do Azure são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas máquinas virtuais do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, proteção antivírus ou a configuração do Docker, uma extensão da VM poderá ser usada para concluir essas tarefas. As extensões da VM do Azure podem ser executadas usando a CLI do Azure, o PowerShell, modelos do Azure Resource Manager e o Portal do Azure. Extensões podem ser agrupadas com uma nova implantação de máquina virtual ou executar qualquer sistema existente.

Este documento fornece uma visão geral das extensões da máquina virtual, pré-requisitos para utilização das extensões da máquina virtual e orientação sobre como detectar, gerenciar e remover extensões da máquina virtual. Este documento fornece informações generalizadas, pois há muitas extensões de VM disponíveis e cada uma delas tem uma configuração possivelmente exclusiva. Encontre detalhes específicos sobre cada extensão na documentação individual.

## <a name="use-cases-and-samples"></a>Casos de uso e exemplos

Há várias extensões de VM do Azure diferentes disponíveis, cada uma com um caso de uso específico. Alguns exemplos de caso de uso são:

- Aplique as configurações de Estado Desejado do PowerShell a uma máquina virtual usando a extensão de DSC para Windows. Para saber mais, confira [Extensão de configuração de Estado Desejado do Azure](extensions-dsc-overview.md).
- Configure o monitoramento da máquina virtual usando a extensão de VM do Microsoft Monitoring Agent. Para saber mais, veja [Conectar máquinas virtuais do Azure ao Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure o monitoramento de sua infraestrutura do Azure com a extensão Datadog. Para saber mais, confira [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configure uma máquina virtual do Azure usando o Chef. Para saber mais, veja [Automatizar a implantação de máquina virtual do Azure com o Chef](chef-automation.md).

Além de extensões específicas ao processo, uma extensão de Script Personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para Windows permite a execução de qualquer script do PowerShell em uma máquina virtual. Isso é útil para a criação de implantações do Azure que exigem uma configurações que vão além da capacidade das ferramentas nativas do Azure. Para saber mais, veja [Extensão de Script personalizado de VM do Windows](extensions-customscript.md).


## <a name="prerequisites"></a>Pré-requisitos

Cada extensão da máquina virtual pode ter seu próprio conjunto de pré-requisitos. Por exemplo, a extensão de VM do Docker tem um pré-requisito de uma distribuição Linux com suporte. Os requisitos específicos das extensões estão detalhados na documentação associada.

### <a name="azure-vm-agent"></a>Agente de VM do Azure
O Agente de VM do Azure gerencia a interação entre uma máquina virtual do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais de implantação e gerenciamento de máquinas virtuais do Azure, incluindo a execução de extensões da VM. O agente de VM do Azure é pré-instalado em imagens do Azure Marketplace e pode ser instalado em sistemas operacionais compatíveis.

Para saber mais sobre os sistemas operacionais com suporte e as instruções de instalação, confira [Agente de máquina virtual do Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM
Muitas extensões de VM diferentes estão disponíveis para uso com as máquinas virtuais do Azure. Para ver uma lista completa, execute o comando a seguir com o módulo do Azure Resource Manager PowerShell. Especifique o local desejado ao executar esse comando.

```powershell
Get-AzureRmVmImagePublisher -Location WestUS | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões da máquina virtual do Azure podem ser executadas em máquinas virtuais existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com implantações de modelo do Azure Resource Manager. Ao usar extensões com modelos do Resource Manager, as máquinas virtuais do Azure podem ser implantadas e configuradas sem a necessidade de intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma máquina virtual existente.

### <a name="powershell"></a>PowerShell

Há vários comandos do PowerShell para a execução de extensões individuais. Para ver uma lista, execute os comandos do PowerShell a seguir.

```powershell
get-command Set-AzureRM*Extension* -Module AzureRM.Compute
```

O resultado é semelhante ao seguinte:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             2.2.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    2.2.0      AzureRM.Compute
```

O exemplo a seguir usa a extensão de Script personalizado para baixar um script de um repositório do GitHub para a máquina virtual de destino e, em seguida, executa o script. Para saber mais sobre como usar a extensão de Script personalizado, veja [Visão geral da extensão de Script personalizado](extensions-customscript.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Neste exemplo, a extensão de acesso à VM é usada para redefinir a senha administrativa de uma máquina virtual Windows. Para saber mais sobre a extensão de acesso à VM, veja [Serviço de redefinição de área de trabalho remota em uma VM do Windows](reset-rdp.md).

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O comando `Set-AzureRmVMExtension` pode ser usado para iniciar qualquer extensão de VM. Para saber mais, veja a [referência de Set-AzureRmVMExtension](https://msdn.microsoft.com/en-us/library/mt603745.aspx).


### <a name="azure-portal"></a>Portal do Azure

É possível aplicar extensões de VM a uma máquina virtual existente por meio do Portal do Azure. Para fazer isso, selecione a máquina virtual que deseja usar, escolha **Extensões** e clique em **Adicionar**. Isso fornece uma lista de extensões disponíveis. Selecione a desejada e siga as instruções no assistente.

A imagem a seguir mostra a instalação da extensão Microsoft Antimalware no Portal do Azure.

![Instalar a extensão de antimalware](./media/extensions-features/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure

É possível adicionar extensões de VM a um modelo do Azure Resource Manager e executá-las com a implantação do modelo. Implantar extensões com um modelo é útil para a criação de implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager que implanta um conjunto de máquinas virtuais com balanceamento de carga e um banco de dados SQL do Azure e, em seguida, instala um aplicativo .NET Core em cada VM. A extensão da VM se encarrega da instalação do software.

Para saber mais, confira o [Modelo completo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Para saber mais, veja [Criação de modelos do Azure Resource Manager com extensões de VM do Windows](template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão da VM

Quando você estiver executando uma extensão de VM, talvez seja necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e os descriptografa somente dentro da máquina virtual de destino. Cada extensão tem um esquema específico de configuração protegida, e cada um é detalhado na documentação específica associada à extensão.

O exemplo a seguir mostra uma instância da extensão Script personalizado para Windows. Observe que o comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não será criptografado.


```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Proteja a cadeia de caracteres de execução movendo a propriedade **command to execute** para a configuração **protected**.

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.4",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas. Por exemplo, quando você está usando a extensão Script personalizado, é possível encontrar detalhes da execução do script localmente na máquina virtual na qual a extensão foi executada. As etapas de solução de problemas específicas à extensão são detalhadas na documentação associada.

As etapas de solução de problemas a seguir aplicam-se a todas as extensões da máquina virtual.

### <a name="view-extension-status"></a>Exibir o status da extensão

Após a execução da extensão da máquina virtual em uma máquina virtual, use o seguinte comando do PowerShell para retornar o status da extensão. Substitua os nomes de parâmetro de exemplo por seus próprios valores. O parâmetro `Name` usa o nome fornecido à extensão no momento da execução.

```PowerShell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A saída se parece com o seguinte:

```json
ResourceGroupName       : myResourceGroup
VMName                  : myVM
Name                    : myExtensionName
Location                : westus
Etag                    : null
Publisher               : Microsoft.Azure.Extensions
ExtensionType           : DockerExtension
TypeHandlerVersion      : 1.0
Id                      : /subscriptions/mySubscriptionIS/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/myExtensionName
PublicSettings          :
ProtectedSettings       :
ProvisioningState       : Succeeded
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : False
ForceUpdateTag          :
```

O status de execução da extensão também pode ser encontrado no Portal do Azure. Para exibir o status de uma extensão, selecione a máquina virtual, escolha **Extensões** e selecione a extensão desejada.

### <a name="rerun-vm-extensions"></a>Executar extensões de VM novamente

Pode haver casos nos quais uma extensão da máquina virtual precisa ser executada novamente. Faça isso removendo a extensão e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, execute o seguinte comando com o módulo do Azure PowerShell. Substitua os nomes de parâmetro de exemplo por seus próprios valores.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Uma extensão também pode ser removida usando o Portal do Azure. Para fazer isso:

1. Selecione uma máquina virtual.
2. Selecione **Extensões**.
3. Escolha a extensão desejada.
4. Selecione **Desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência a extensões de VM comuns
| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para o Windows |Executar scripts em uma máquina virtual do Azure |[Extensão de script personalizado para o Windows](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensão de DSC para o Windows |Extensão PowerShell DSC (Configuração de Estado Desejado) |[Extensão DSC para Windows](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Extensão de Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de Acesso à VM para Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
