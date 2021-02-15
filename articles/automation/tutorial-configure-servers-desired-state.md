---
title: Configuração de computadores para um estado desejado na Automação do Azure
description: Este artigo descreve como configurar computadores para um estado desejado usando o serviço State Configuration da Automação do Azure.
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: f16db3f55ebd0f09e4d7b75750fa319daf03977e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053560"
---
# <a name="configure-machines-to-a-desired-state"></a>Configurar computadores para um estado desejado

A Configuração do Estado de Automação do Azure permite que você especifique configurações para seus servidores e garanta que esses servidores estejam no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM para ser gerenciada pela DSC de Automação do Azure
> - Carregar uma configuração para Automação do Azure
> - Compilar uma configuração em uma configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o status de conformidade de um nó gerenciado

Para este tutorial, usamos uma [configuração DSC](/powershell/scripting/dsc/configurations/configurations) simples que garanta que o IIS seja instalado na VM.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta de Automação do Azure. Para saber mais sobre uma conta de automação e seus requisitos, consulte [visão geral da autenticação da conta de automação](./automation-security-overview.md).
- Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre como criar uma VM, veja [Criação da primeira máquina virtual do Windows no portal do Azure](../virtual-machines/windows/quick-create-portal.md).
- Versão de módulo do Azure PowerShell 3.6 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Configuração do Estado Desejado (DSC). Para obter informações sobre a plataforma DSC, veja [Visão Geral da Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

## <a name="support-for-partial-configurations"></a>Suporte para configurações parciais

O serviço State Configuration da Automação do Azure dá suporte ao uso de [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs). Nesse cenário, a plataforma DSC é configurada para gerenciar várias configurações de forma independente, e cada configuração é recuperada da automação do Azure. No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação. Isso significa que se estiver usando duas configurações para um nó você precisará de duas contas de automação.

Para obter detalhes sobre como registrar uma configuração parcial de um serviço de pull, veja a documentação sobre [configurações parciais](/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipes podem trabalhar em conjunto para gerenciar servidores de forma colaborativa usando a configuração como código, veja [Noções básicas sobre a função da plataforma DSC em um pipeline de CI/CD](/powershell/scripting/dsc/overview/authoringadvanced).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon em sua assinatura do Azure com o cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) e siga as instruções na tela.

```powershell
Connect-AzAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração na Automação do Azure

Em um editor de texto, digite o seguinte e salve localmente como **TestConfig.ps1**.

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

> [!NOTE]
> Em cenários mais avançados que exigem a importação de vários módulos que forneçam recursos da plataforma DSC, verifique se cada módulo tem uma linha de `Import-DscResource` exclusiva em sua configuração.

Chame o cmdlet [Import-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Import-AzAutomationDscConfiguration) para carregar a configuração na conta de automação.

```powershell
 Import-AzAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração em uma configuração de nó

Uma configuração DSC deverá ser compilada em uma configuração de nó para que ela possa ser atribuída a um nó. Confira [Configurações DSC](/powershell/scripting/dsc/configurations/configurations).

Chame o cmdlet [Start-AzAutomationDscCompilationJob](/powershell/module/Az.Automation/Start-AzAutomationDscCompilationJob) para compilar a configuração de `TestConfig` em uma configuração de nó chamada `TestConfig.WebServer` em sua conta de automação.

```powershell
Start-AzAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrar uma VM a ser gerenciada pela Configuração do Estado

É possível usar a Configuração de Estado da Automação do Azure para gerenciar VMs do Azure (tanto Clássica quanto do Gerenciador de Recursos), VMs locais, computadores Linux, VMs de AWS e computadores físicos locais. Neste tópico, abordaremos como registrar somente VMs do Azure Resource Manager. Para obter mais informações sobre como registrar outros tipos de computadores, consulte [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md).

Chame o cmdlet [Register-AzAutomationDscNode](/powershell/module/Az.Automation/Register-AzAutomationDscNode) para registrar sua VM com State Configuration na Automação do Azure como um nó gerenciado. 

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Use o cmdlet [Register-AzAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode) para registrar uma VM como um nó gerenciado e especifique as propriedades de configuração. Por exemplo, você pode especificar que o estado do computador seja aplicado apenas uma vez, especificando `ApplyOnly` como o valor da propriedade `ConfigurationMode`. O serviço State Configuration não tenta aplicar a configuração após a verificação inicial.

```powershell
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar a frequência com a qual a plataforma DSC verifica o estado de configuração usando a propriedade `ConfigurationModeFrequencyMins`. Para obter mais informações sobre as definições de configuração de DSC, consulte [Configurando o Gerenciador de Configurações Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

```powershell
# Run a DSC check every 60 minutes
Register-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração de nó compilada à VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isso atribui a configuração de nó denominada `TestConfig.WebServer` ao nó de DSC registrado chamado `DscVm`. Por padrão, o nó de DSC é verificado quanto à conformidade com a configuração do nó a cada 30 minutos. Para obter informações de como alterar o intervalo de verificação de conformidade, consulte [Configurando System Center Configuration Manager Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Obtenha relatórios sobre o status de conformidade de um nó gerenciado usando o cmdlet [Get-AzAutomationDscNodeReport](/powershell/module/Az.Automation/Get-AzAutomationDscNodeReport).

```powershell
# Get the ID of the DSC node
$node = Get-AzAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="remove-nodes-from-service"></a>Remoção de nós do serviço

Quando um nó é adicionado ao serviço State Configuration da Automação do Azure, as configurações no Configuration Manager local são definidas para registro com o serviço e as configurações de pull e os módulos necessários para configurar o computador.
Se optar por remover o nó do serviço, você pode fazer isso usando os cmdlets Az ou do portal do Azure.

> [!NOTE]
> O cancelamento do registro de um nó do serviço apenas define as configurações do Configuration Manager local para que o nó não se conecte mais ao serviço.
> Isso não afeta a configuração atualmente aplicada ao nó.
> Para remover a configuração atual, use o [PowerShell](/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument) ou exclua o arquivo de configuração local (essa é a única opção para nós do Linux).

### <a name="azure-portal"></a>Portal do Azure

Na Automação do Azure, clique em **Configuração de estado (DSC)** no sumário.
Em seguida, clique em **Nós** para exibir a lista de nós que estão registrados no serviço.
Clique no nome do nó que você quer remover.
No modo de exibição Nó que abre, clique em **Cancelar o registro**.

### <a name="powershell"></a>PowerShell

Para cancelar o registro de um nó do serviço State Configuration da Automação do Azure usando o PowerShell, veja a documentação para o cmdlet [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode).

## <a name="next-steps"></a>Próximas etapas

- Para começar, veja [Introdução ao serviço State Configuration da Automação do Azure](automation-dsc-getting-started.md).
- Para saber como habilitar os nós, veja [Habilitar o serviço State Configuration da Automação do Azure](automation-dsc-onboarding.md).
- Para saber como compilar configurações da plataforma DSC para que elas possam ser atribuídas aos nós de destino, veja [Compilação de configurações de DSC no serviço State Configuration da Automação do Azure](automation-dsc-compile.md).
- Para ver um exemplo de uso do serviço State Configuration da Automação do Azure em um pipeline de implantação contínua, veja [Configuração de implantação contínua com o Chocolatey](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre preços, veja [Preços do serviço State Configuration da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
