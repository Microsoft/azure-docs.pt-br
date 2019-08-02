---
title: Configurar servidores para um estado desejado e gerenciar o descompasso com a Automação do Azure
description: Tutorial – Gerenciar configurações de servidor com a Configuração do Estado de Automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 3bcdb667ee649b9bbf32ad33e74e876cdd2b5cbf
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144188"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado desejado e gerenciar dessincronização

A Configuração do Estado de Automação do Azure permite que você especifique configurações para seus servidores e garanta que esses servidores estejam no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM para ser gerenciada pela DSC de Automação do Azure
> - Carregar uma configuração para Automação do Azure
> - Compilar uma configuração em uma configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o status de conformidade de um nó gerenciado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:

- Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Versão de módulo do Azure PowerShell 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Configuração do Estado Desejado (DSC). Para obter informações sobre a DSC, consulte [Visão Geral da Desired State Configuration do Windows PowerShell](https://docs.microsoft.com/powershell/dsc/overview)

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração na Automação do Azure

Para este tutorial, usaremos uma configuração DSC simples que garanta que o IIS seja instalado na VM.

Para obter informações sobre as configurações DSC, consulte [Configurações DSC](/powershell/dsc/configurations).

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

Para obter informações de como compilar configurações, consulte [Configurações DSC](/powershell/dsc/configurations).

Chame o cmdlet `Start-AzureRmAutomationDscCompilationJob` para compilar a configuração `TestConfig` em uma configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Isso cria uma configuração de nó chamada `TestConfig.WebServer` na conta de Automação.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrar uma VM a ser gerenciada pela Configuração do Estado

É possível usar a Configuração de Estado da Automação do Azure para gerenciar VMs do Azure (tanto Clássica quanto do Gerenciador de Recursos), VMs locais, computadores Linux, VMs de AWS e computadores físicos locais. Neste tópico, abordaremos como registrar somente VMs do Azure Resource Manager. Para obter mais informações sobre como registrar outros tipos de computadores, consulte [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md).

Chame o `Register-AzureRmAutomationDscNode` cmdlet para registrar a VM na Configuração do Estado de Automação do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Isso registra a VM especificada como um nó gerenciado na Configuração do Estado.

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Ao registrar uma VM como um nó gerenciado, também é possível especificar as propriedades da configuração. Por exemplo, você pode especificar que o estado do computador deve ser aplicado somente uma vez (a DSC não tentará aplicar a configuração após a verificação inicial), especificando `ApplyOnly` como o valor da propriedade **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar a frequência em que a DSC verifica o estado de configuração usando a propriedade **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Para obter mais informações de como definir as propriedades de configuração para um nó gerenciado, consulte [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Para obter mais informações sobre as definições de configuração de DSC, consulte [Configurando o Gerenciador de Configurações Local](/powershell/dsc/metaconfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração de nó compilada à VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isso atribuirá a configuração de nó denominada `TestConfig.WebServer` ao nó de DSC registrado chamado `DscVm`.
Por padrão, o nó de DSC é verificado quanto à conformidade com a configuração do nó a cada 30 minutos.
Para obter informações de como alterar o intervalo de verificação de conformidade, consulte [Configurando System Center Configuration Manager Local](/PowerShell/DSC/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Você pode obter relatórios sobre o status de conformidade de um nó gerenciado chamando o cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Remover nós de serviço

Quando você adiciona um nó a configuração de estado de automação do Azure, as configurações no Gerenciador de configurações Local são definidas para registrar as configurações de serviço e pull e os módulos necessários para configurar a máquina.
Se você optar por remover o nó do serviço, você pode fazer isso usando o portal do Azure ou os cmdlets do Az.

> [!NOTE]
> Cancelar o registro de um nó do serviço apenas define as configurações do Gerenciador de configurações Local para que o nó não está se conectando ao serviço.
> Isso não afeta a configuração atualmente aplicado ao nó.
> Para remover a configuração atual, use o [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou exclua o arquivo de configuração local (essa é a única opção para nós do Linux).

### <a name="azure-portal"></a>Portal do Azure

Automação do Azure, clique em **State configuration (DSC)** no sumário.
Em seguida clique **nós** para exibir a lista de nós que estão registrados com o serviço.
Clique no nome do nó que você deseja remover.
Na exibição do nó que é aberta, clique em **Unregister**.

### <a name="powershell"></a>PowerShell

Para cancelar o registro de um nó do serviço de configuração de estado de automação do Azure usando o PowerShell, siga a documentação para o cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Próximas etapas

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
