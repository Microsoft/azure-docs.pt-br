---
title: Ativos variáveis na Automação do Azure
description: Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC na Automação do Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas na criação de textos e gráficos.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 39282e816be875e598d7e0599eeb358a79941be7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478058"
---
# <a name="variable-assets-in-azure-automation"></a>Ativos variáveis na Automação do Azure

Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC em sua conta de automação. Eles podem ser gerenciados no portal do Azure, PowerShell, dentro de um runbook ou configuração DSC. As variáveis de automação são úteis para os seguintes cenários:

- Compartilhe um valor entre vários runbooks ou configurações DSC.

- Compartilhe um valor entre vários trabalhos do mesmo runbook ou configuração DSC.

- Gerencie um valor a partir do portal ou da linha de comando do PowerShell que é usada pelos runbooks ou configurações de DSC, como um conjunto comum de itens de configuração como específico da lista de nomes de VM, um grupo de recursos específico, um nome de domínio do AD e muito mais.  

Uma vez que as variáveis de automação são mantidas, eles estão disponíveis, mesmo se o runbook ou configuração DSC falha. Esse comportamento permite que um valor a ser definido por um runbook e, em seguida, é usado por outro, ou é usado pelo mesmo runbook ou configuração de DSC na próxima vez que for executado.

Durante a criação de uma variável, você pode especificar o armazenamento criptografado. Variáveis criptografadas são armazenadas com segurança na automação do Azure e seu valor não pode ser recuperado com o [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) cmdlet que é fornecido como parte do módulo do PowerShell do Azure. A única maneira de recuperar um valor criptografado é por meio da atividade **Get-AutomationVariable** em um runbook ou configuração DSC. Se você quiser alterar uma variável criptografada para não criptografados, você pode deve excluir e recriar a variável como não criptografados.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

## <a name="variable-types"></a>Tipos de variável

Quando você cria uma variável com o portal do Azure, deve especificar um tipo de dados na lista suspensa para que o portal possa exibir o controle adequado para a inserção do valor da variável. A variável não é restrita a esse tipo de dados. Você deve definir a variável usando o Windows PowerShell se você quiser especificar um valor de um tipo diferente. Se você especificar **não definido**, em seguida, o valor da variável define **$null**, e você deve definir o valor com o [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) cmdlet ou **Set-AutomationVariable** atividade. É possível criar ou alterar o valor para um tipo de variável complexa no portal, mas você pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos retornam como um [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Você pode armazenar vários valores para uma única variável criando uma matriz ou hashtable e salvando-a na variável.

A seguir está uma lista de tipos de variáveis disponíveis na automação:

* Cadeia de caracteres
* Número inteiro
* DateTime
* Boolean
* Nulo

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell

Para o AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte dos [módulo Azurerm](/powershell/azure/overview), que está disponível para uso em runbooks de automação e configurações de DSC.

| Cmdlets | DESCRIÇÃO |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Recupera o valor de uma variável existente.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Cria uma nova variável e define o seu valor.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Remove uma variável existente.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Define o valor de uma variável existente.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar credenciais em um runbook ou em uma configuração DSC.

| Atividades | DESCRIÇÃO |
|:---|:---|
|Get-AutomationVariable|Recupera o valor de uma variável existente.|
|Set-AutomationVariable|Define o valor de uma variável existente.|

> [!NOTE]
> Evite usar variáveis no parâmetro –Name de **Get-AutomationVariable** em um runbook ou na configuração DSC, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e variáveis da Automação no momento do design.

As funções na tabela a seguir são usadas para acessar e recuperar variáveis em um runbook Python2.

|Funções Python2|DESCRIÇÃO|
|:---|:---|
|automationassets.get_automation_variable|Recupera o valor de uma variável existente. |
|automationassets.set_automation_variable|Define o valor de uma variável existente. |

> [!NOTE]
> É necessário importar o módulo "automationassets", na parte superior do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-automation-variable"></a>Criando uma nova variável de automação

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Para criar uma nova variável com o portal do Azure

1. Na sua conta de automação, clique no bloco **Ativos** e então, na folha, **Ativos**, selecione **Variáveis**.
2. No bloco **Variáveis**, clique em **Adicionar uma variável**.
3. Complete as opções na folha **Nova Variável** e clique em **Criar** para salvar a nova variável.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Para criar uma nova variável com o Windows PowerShell

O cmdlet [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) cria uma nova variável e define o seu valor inicial. Você pode recuperar o valor usando [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Se o valor for um tipo simples, é retornado o mesmo tipo. Se for um tipo complexo, um **PSCustomObject** é retornado.

Os comandos de exemplo a seguir mostram como criar uma variável do tipo cadeia de caracteres e retornar o seu valor.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Os comandos de exemplo a seguir mostram como criar uma variável do tipo complexo e retornar as suas propriedades. Nesse caso, é usado um objeto máquina virtual de **Get-AzureRmVm** .

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Usando uma variável em um runbook ou configuração DSC

Use a atividade **Set-AutomationVariable** para definir o valor de uma variável da Automação em um runbook do PowerShell ou uma configuração DSC e **Get-AutomationVariable** para recuperá-la. Você não deve usar os cmdlets **Set-AzureRMAutomationVariable** ou **Get-AzureRMAutomationVariable** em um runbook ou configuração DSC, já que eles são menos eficientes do que as atividades de fluxo de trabalho. Também não é possível recuperar o valor de variáveis protegidas com o **Get-AzureRMAutomationVariable**. A única maneira de criar uma nova variável de dentro de um runbook ou configuração DSC é usar o cmdlet [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable).

### <a name="textual-runbook-samples"></a>Exemplos de runbook textual

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Definindo e recuperando um valor simples de uma variável

Os comandos de exemplo a seguir mostram como definir e recuperar uma variável em um runbook textual. Neste exemplo, supõe-se que as variáveis do tipo inteiro nomeadas *NumberOfIterations* e *NumberOfRunnings* e uma variável do tipo cadeia de caracteres denominada *SampleMessage* ter foi criado.

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Configurando e recuperando uma variável em Python2

O código de exemplo a seguir mostra como usar uma variável, defini-la e manipular uma exceção de uma variável não existente em um runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Em um runbook gráfico, você adiciona o **Get-AutomationVariable** ou o **Set-AutomationVariable** clicando na variável com o botão direito do mouse no painel Biblioteca do editor gráfico e selecionando a atividade desejada.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Definindo valores em uma variável

A imagem a seguir mostra as atividades de exemplo para atualizar uma variável com um valor simples em um runbook gráfico. Neste exemplo, **Get-AzureRmVM** recupera uma única máquina virtual do Azure e o nome do computador salva em uma variável de automação existente com um tipo de cadeia de caracteres. Não importa se o [link é um pipeline ou uma sequência](../automation-graphical-authoring-intro.md#links-and-workflow), já que você espera apenas um objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como interligar as atividades na criação gráfica, confira [Links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow)
- Para começar a usar os runbooks Gráficos, consulte [Meu primeiro runbook gráfico](../automation-first-runbook-graphical.md)
