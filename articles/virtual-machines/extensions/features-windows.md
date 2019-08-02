---
title: Recursos e extensões da VM do Azure para Windows | Microsoft Docs
description: Saiba quais extensões estão disponíveis para as máquinas virtuais do Azure, agrupadas pelas funcionalidades fornecidas ou aperfeiçoadas.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2cd64b54b1a30080d7942a754bc0c0c72c59f88
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705981"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Recursos e extensões da máquina virtual para Windows

As extensões da máquina virtual (VM) do Azure são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, proteção antivírus ou a execução de um script dentro dela, uma extensão da VM poderá ser usada. As extensões da VM do Azure podem ser executadas com a CLI do Azure, o PowerShell, modelos do Azure Resource Manager e o portal do Azure. As extensões podem ser agrupadas com uma nova implantação de VM ou executar qualquer sistema existente.

Este artigo fornece uma visão geral das extensões da VM, pré-requisitos para utilização das extensões da VM do Azure e diretrizes sobre como detectar, gerenciar e remover as extensões da VM. Este artigo fornece informações generalizadas, pois há muitas extensões de VM disponíveis, cada uma delas tem uma configuração possivelmente exclusiva. Encontre detalhes específicos sobre cada extensão na documentação individual.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="use-cases-and-samples"></a>Casos de uso e exemplos

Há várias extensões de VM do Azure diferentes disponíveis, cada uma com um caso de uso específico. Eis alguns exemplos:

- Aplique as configurações de Estado Desejado do PowerShell a uma VM usando a extensão de DSC para Windows. Para saber mais, confira [Extensão de configuração de Estado Desejado do Azure](dsc-overview.md).
- Configure o monitoramento de uma VM com a extensão de VM do Microsoft Monitoring Agent. Para obter mais informações, consulte [conectar-se a VMs do Azure para logs do Azure Monitor](../../log-analytics/log-analytics-azure-vm-extension.md).
- Configure uma VM do Azure ao usar o Chef. Para obter mais informações, consulte [Automatizar a implantação de VM do Azure com o Chef](../windows/chef-automation.md).
- Configure o monitoramento de sua infraestrutura do Azure com a extensão Datadog. Para saber mais, confira [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Além de extensões específicas ao processo, uma extensão de Script Personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para Windows permite a execução de qualquer script do PowerShell em uma VM. Scripts personalizados são úteis para a criação de implantações do Azure que exigem uma configuração que vai além da capacidade das ferramentas nativas do Azure. Para saber mais, veja [Extensão de Script personalizado de VM do Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, você precisa ter o agente do Windows Azure instalado. Algumas extensões individuais têm pré-requisitos, como acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente de VM do Azure

O agente de VM do Azure gerencia a interação entre uma VM do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais de implantação e gerenciamento de VMs do Azure, incluindo a execução de extensões da VM. O agente de VM do Azure é pré-instalado em imagens do Microsoft Azure Marketplace e pode ser instalado manualmente em sistemas operacionais com suporte. O agente de VM do Azure para Windows é conhecido como o agente Convidado do Windows.

Para saber mais sobre os sistemas operacionais com suporte e as instruções de instalação, confira [Agente de máquina virtual do Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Versões do agente com suporte

Para fornecer a melhor experiência possível, há versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais com suporte

O agente Convidado do Windows é executado em vários sistemas operacionais. No entanto, a estrutura de extensões tem um limite para os sistemas operacionais com extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não têm suporte em todos os sistemas de operacionais e podem emitir *Código de Erro 51, 'SO sem suporte'* . Consulte a documentação da extensão individual sobre a capacidade de suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são baixados do repositório de extensão do Armazenamento do Microsoft Azure, e os carregamentos de status de extensão são postados no Armazenamento do Microsoft Azure. Se usar a versão com [suporte](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, você não precisará permitir o acesso ao Armazenamento do Microsoft Azure na região da VM, já que poderá usar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações do agente. Se você estiver usando uma versão sem suporte do agente, será necessário permitir o acesso de saída no armazenamento do Microsoft Azure nessa região por meio da VM.

> [!IMPORTANT]
> Se você tiver bloqueado o acesso a *168.63.129.16* usando o firewall convidado, as extensões falharão independentemente das informações acima.

Os agentes só podem ser usados para baixar os pacotes de extensão e o status do relatório. Por exemplo, se uma instalação da extensão precisar baixar um script do GitHub (Script Personalizado) ou precisar ter acesso ao Armazenamento do Microsoft Azure (Backup do Azure), então outras portas de firewall/Grupo de Segurança de Rede precisarão ser abertas. Diferentes extensões têm requisitos diferentes, já que são aplicativos por si só. Para extensões que exigem acesso ao Armazenamento do Microsoft Azure, você poderá permitir acesso usando Marcas de Serviço do NSG do Azure para [Armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

O Agente de Convidado do Windows não tem suporte de servidor proxy para redirecionar solicitações de tráfego de agente por meio dele.

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Muitas extensões de VM diferentes estão disponíveis para uso com as VMs do Azure. Para ver uma lista completa, use [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). O exemplo a seguir lista todas as extensões disponíveis no local *WestUS*:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões da VM do Azure podem ser executadas em VMs existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com implantações de modelo do Azure Resource Manager. Ao usar extensões com modelos do Resource Manager, as VMs do Azure podem ser implantadas e configuradas sem intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma VM existente.

### <a name="powershell"></a>PowerShell

Há vários comandos do PowerShell para a execução de extensões individuais. Para ver uma lista, use [Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) e filtre pela *Extensão*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

O resultado é semelhante ao seguinte:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

O exemplo a seguir usa a extensão de Script personalizado para baixar um script de um repositório do GitHub para a máquina virtual de destino e, em seguida, executa o script. Para saber mais sobre como usar a extensão de Script personalizado, veja [Visão geral da extensão de Script personalizado](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

No seguinte exemplo, a extensão de acesso à VM é usada para redefinir a senha administrativa de uma VM Windows para uma senha temporária. Para saber mais sobre a extensão de acesso à VM, veja [Serviço de redefinição de área de trabalho remota em uma VM do Windows](../windows/reset-rdp.md). Depois de ter executado isso, você deverá redefinir a senha no primeiro logon:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

O comando `Set-AzVMExtension` pode ser usado para iniciar qualquer extensão de VM. Para saber mais, veja a [referência de Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Portal do Azure

É possível aplicar extensões de VM a uma VM existente por meio do portal do Azure. Selecione a VM no portal, escolha **Extensões** e depois selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

O exemplo a seguir mostra a instalação da extensão Microsoft Antimalware no portal do Azure:

![Instalar a extensão de antimalware](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

É possível adicionar extensões de VM a um modelo do Azure Resource Manager e executá-las com a implantação do modelo. Ao implantar uma extensão com um modelo, você pode criar implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager que implanta um conjunto de VMs com balanceamento de carga e um banco de dados SQL do Azure e, em seguida, instala um aplicativo .NET Core em cada VM. A extensão da VM se encarrega da instalação do software.

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
    "typeHandlerVersion": "1.9",
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

Para obter mais informações sobre a criação de modelos do Resource Manager, consulte [Criando modelos do Azure Resource Manager com extensões da VM do Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão da VM

Ao executar uma extensão de VM, talvez seja necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e os descriptografa somente dentro da VM de destino. Cada extensão tem um esquema específico de configuração protegida, e cada um é detalhado na documentação específica à extensão.

O exemplo a seguir mostra uma instância da extensão Script personalizado para Windows. O comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não é criptografado:

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
    "typeHandlerVersion": "1.9",
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

A movimentação da propriedade **comando para execução** para a configuração **protegida** protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

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
    "typeHandlerVersion": "1.9",
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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como agentes e extensões são atualizados?

Os Agentes e as Extensões compartilham o mesmo mecanismo de atualização. Algumas atualizações não exigem regras de firewall adicionais.

Quando uma atualização estiver disponível, ela só será instalada na VM quando houver uma alteração nas extensões e outras alterações de Modelo de VM como:

- Discos de dados
- Extensões
- Contêiner de diagnóstico de inicialização
- Segredos do sistema operacional convidado
- Tamanho da VM
- Perfil de rede

Os editores tornam atualizações disponíveis para regiões em momentos diferentes, então é possível ter VMs em regiões e versões diferentes.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Listando extensões implantadas em uma VM

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Atualizações de agentes

O Agente de Convidado do Windows contém somente o *código de Tratamento de Extensão*. O *código de Provisionamento do Windows* é separado. Você pode desinstalar o Agente de Convidado do Windows. Não é possível desabilitar a atualização automática do agente de Convidado do Windows.

O *código de Tratamento de Extensão* é responsável por se comunicar com a malha do Azure e manipular as operações de extensões da VM, como instalações, relatando status, atualizando as extensões individuais e removendo-as. As atualizações contêm correções de segurança, correções de bug e aprimoramentos para o *código de Tratamento de Extensão*.

Para verificar qual versão você está executando, consulte [Detectando o Agente de Convidado do Windows instalado](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização da extensão estiver disponível, o Agente de Convidado do Windows baixará e atualizará a extensão. Atualizações automáticas de extensão são *Secundárias* ou *Hotfix*. Você pode aceitar ou recusar atualizações de extensões *Secundárias* ao provisionar a extensão. O exemplo a seguir mostra como atualizar automaticamente as versões secundárias em um modelo do Resource Manager com *autoUpgradeMinorVersion": true,'* :

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Para obter as correções de bug de versão secundária mais recentes, é altamente recomendável que você selecione sempre a atualização automática em suas implantações de extensão. As atualizações de hotfix que realizam correções de bug essenciais ou de segurança não podem ser recusadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar as atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão foi definida com autoUpgradeMinorVersion em uma VM

Você pode ver no modelo da VM se a extensão foi provisionada com 'autoUpgradeMinorVersion'. Para verificar, use [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) e forneça o grupo de recursos e o nome da VM da seguinte maneira:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

O seguinte exemplo de saída mostra que *autoUpgradeMinorVersion* foi definido como *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma autoUpgradeMinorVersion

Para ver quando ocorreu uma atualização da extensão, examine os registros de agente na VM em *C:\WindowsAzure\Logs\WaAppAgent.log*

No exemplo a seguir, *Microsoft.Compute.CustomScriptExtension 1.8* estava instalado na VM. Um hotfix estava disponível para a versão *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Permissões de agente

Para executar suas tarefas, o agente precisa executar como *Sistema Local*.

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas à extensão. Por exemplo, ao usar a extensão Script Personalizado, detalhes de execução do script poderão ser encontrados localmente na VM na qual a extensão foi executada. As etapas de solução de problemas específicas à extensão são detalhadas na documentação associada.

As seguintes etapas de solução de problemas aplicam-se a todas as extensões da VM.

1. Para verificar o Log do Agente Convidado do Windows, observe a atividade quando sua extensão estiver sendo provisionada em *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Verifique os logs de extensão reais para obter mais detalhes em *C:\WindowsAzure\Logs\Plugins\<extensionName>*

3. Verifique as seções de solução de problemas da documentação específica da extensão para códigos de erro, problemas conhecidos etc.

4. Examine os logs do sistema. Verifique se há outras operações que podem ter interferido na extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso exclusivo ao gerenciador de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas na extensão

1. As extensões têm 20 minutos para serem executadas (exceções são as extensões de CustomScript, Chef e DSC que têm 90 minutos). Se a implantação exceder esse tempo, será marcada como um tempo limite. Isso pode ocorrer devido a poucas VMs de recursos, outras configurações da VM/tarefas de inicialização consumindo grande quantidade de recursos durante a extensão que está tentando provisionar.

2. Pré-requisitos mínimos não atendidos. Algumas extensões têm dependências em SKUs da VM, como imagens HPC. As extensões podem exigir certos requisitos de acesso à rede, como comunicação com o Armazenamento do Microsoft Azure ou serviços públicos. Outros exemplos podem ser o acesso a repositórios de pacote, ficando sem espaço em disco, ou restrições de segurança.

3. Acesso exclusivo ao gerenciador de pacotes. Em alguns casos, você pode encontrar uma configuração da VM de longa execução e uma instalação da extensão em conflito, nas quais ambas precisam de acesso exclusivo ao gerenciador de pacotes.

### <a name="view-extension-status"></a>Exibir o status da extensão

Após executar uma extensão de VM em uma máquina virtual, use [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) para retornar o status da extensão. O *Substatus [0]* mostra que o provisionamento de extensão foi bem-sucedido, o que significa que foi implantado com sucesso na VM, mas que houve falha na execução da extensão dentro da VM, *Substatus [1]* .

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

A saída é semelhante ao seguinte exemplo de saída:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

O status de execução da extensão também pode ser encontrado no Portal do Azure. Para exibir o status de uma extensão, selecione a VM, escolha **Extensões** e selecione a extensão desejada.

### <a name="rerun-vm-extensions"></a>Executar extensões de VM novamente

Pode haver casos nos quais uma extensão da VM precisa ser executada novamente. Você pode executar novamente uma extensão removendo-a e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, use [Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) da seguinte maneira:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Você também pode remover uma extensão no portal do Azure da seguinte maneira:

1. Selecione uma VM.
2. Escolha **Extensões**.
3. Selecione a extensão desejada.
4. Escolha **Desinstalar**.

## <a name="common-vm-extensions-reference"></a>Referência a extensões de VM comuns
| Nome da extensão | DESCRIÇÃO | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para o Windows |Executar scripts em uma máquina virtual do Azure |[Extensão de script personalizado para o Windows](custom-script-windows.md) |
| Extensão de DSC para o Windows |Extensão PowerShell DSC (Configuração de Estado Desejado) |[Extensão DSC para Windows](dsc-overview.md) |
| Extensão de Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de Acesso à VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre extensões de VM, consulte [Visão geral de recursos e extensões de máquina virtual do Azure](overview.md).
