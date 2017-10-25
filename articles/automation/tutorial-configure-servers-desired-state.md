---
title: "Configurar servidores para um estado desejado e gerenciar a dessincronização na automação do Azure | Microsoft Docs"
description: "Tutorial – gerenciar configurações de servidor com a DSC de Automação do Azure"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 63a83e35ce29541de578cb264464448fb6ee3e1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado desejado e gerenciar dessincronização

A DSC (Desired State Configuration) de Automação do Azure permite que você especifique configurações para seus servidores e garanta que esses servidores estejam no estado especificado ao longo do tempo.



> [!div class="checklist"]
> * Carregar uma VM para ser gerenciada pela DSC de Automação do Azure
> * Carregar uma configuração para Automação do Azure
> * Compilar uma configuração em uma configuração de nó
> * Atribuir uma configuração de nó a um nó gerenciado
> * Verificar o status de conformidade de um nó gerenciado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:

* Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
* Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
* Versão de módulo do Azure PowerShell 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Familiaridade com a DSC. Para obter informações sobre a DSC, consulte [Visão Geral da Desired State Configuration do Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração na Automação do Azure

Para este tutorial, usaremos uma configuração DSC simples que garanta que o IIS seja instalado na VM.

Para obter informações sobre as configurações DSC, consulte [Configurações DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Em um editor de texto, digite o seguinte e salve localmente como `TestConfig.ps1`.

```powershell
configuration TestConfig {

   Node WebServer {

      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

Chame o cmdlet `Import-AzureRmAutomationDscConfiguration` para carregar a configuração em sua conta de Automação:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração em uma configuração de nó

Uma configuração DSC deverá ser compilada em uma configuração de nó para que ela possa ser atribuída a um nó.

Para obter informações de como compilar configurações, consulte [Configurações DSC](https://docs.microsoft.com/powershell/dsc/configurations).

Chame o cmdlet `Start-AzureRmAutomationDscCompilationJob` para compilar a configuração `TestConfig` em uma configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Isso cria uma configuração de nó chamada `TestConfig.WebServer` na conta de Automação.

## <a name="register-a-vm-to-be-managed-by-dsc"></a>Registrar uma VM a ser gerenciada pela DSC

Você pode usar o DSC de Automação do Azure para gerenciar VMs do Azure (Clássicas e Resource Manager), VMs locais, computadores Linux, VMs AWS e computadores físicos locais. Neste tópico, abordaremos como registrar somente VMs do Azure Resource Manager.
Para obter informações sobre o registro de outros tipos de máquinas, consulte [Integrando máquinas para serem gerenciadas pela DSC de Automação do Azure](automation-dsc-onboarding.md).

Chame o cmdlet `Register-AzureRmAutomationDscNode` para registrar a VM na DSC de Automação do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName "MyResourceGroup" -AutomationAccountName "myAutomationAccount" -AzureVMName "DscVm"
```

Isso registra a VM especificada como um nó de DSC em sua conta de Automação do Azure.

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Ao registrar uma VM como um nó gerenciado, também é possível especificar as propriedades da configuração.
Por exemplo, você pode especificar que o estado do computador deve ser aplicado somente uma vez (a DSC não tentará aplicar a configuração após a verificação inicial), especificando `ApplyOnly` como o valor da propriedade **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar a frequência em que a DSC verifica o estado de configuração usando a propriedade **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName "DscVm" -ConfigurationModeFrequencyMins 60
```

Para obter mais informações de como definir as propriedades de configuração para um nó gerenciado, consulte [Register-AzureRmAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-4.3.1&viewFallbackFrom=azurermps-4.2.0).

Para obter mais informações sobre as definições de configuração de DSC, consulte [Configurando o Gerenciador de Configurações Local](https://docs.microsoft.com/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração de nó compilada à VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -Id $node.Id
```

Isso atribuirá a configuração de nó denominada `TestConfig.WebServer` ao nó de DSC registrado chamado `DscVm`.
Por padrão, o nó de DSC é verificado quanto à conformidade com a configuração do nó a cada 30 minutos.
Para obter informações de como alterar o intervalo de verificação de conformidade, consulte [Configurando o Gerenciador de Configurações Local](https://docs.microsoft.com/PowerShell/DSC/metaConfig)

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Você pode obter relatórios sobre o status de conformidade de um nó de DSC chamando o cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports Get-Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Id $node.Id

# Display the most recent report
$report[0]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber como integrar nós a serem gerenciados com DSC de Automação do Azure, consulte [Integração de computadores para gerenciamento de DSC de Automação do Azure](automation-dsc-onboarding.md)
* Para saber como usar o portal do Azure para usar a DSC de Automação, consulte [Introdução à DSC de Automação do Azure](automation-dsc-getting-started.md)
* Para saber mais sobre configurações da DSC de compilação para que você possa atribuí-las a nós de destino, consulte [Como compilar configurações na DSC de Automação do Azure](automation-dsc-compile.md)
* Para referência de cmdlet do PowerShell para a DSC de Automação do Azure, consulte [cmdlets da DSC de Automação do Azure](/powershell/module/azurerm.automation/#automation)
* Para obter informações sobre preços, consulte [Preços da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
* Para ver um exemplo de como usar a DSC de Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua em VMs de IaaS usando a DSC de Automação do Azure e o Chocolatey](automation-dsc-cd-chocolatey.md)