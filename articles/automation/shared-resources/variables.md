---
title: Gerenciar variáveis na Automação do Azure
description: Este artigo informa como trabalhar com variáveis em runbooks e configurações DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: 6db0c82c034aab97deee1be4aa8bdc54368521bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98131518"
---
# <a name="manage-variables-in-azure-automation"></a>Gerenciar variáveis na Automação do Azure

Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC em sua conta de Automação. Você pode gerenciá-los no portal do Azure, no PowerShell, em um runbook ou em uma configuração DSC.

As variáveis de automação são úteis para os seguintes cenários:

- Compartilhando um valor entre vários runbooks ou configurações DSC.

- Compartilhando um valor entre vários trabalhos do mesmo runbook ou configuração DSC.

- Gerenciando um valor usado por runbooks ou configurações DSC acessando o portal ou a linha de comando do PowerShell. Um exemplo é um conjunto de itens de configuração comuns, como uma lista específica de nomes de VM, um grupo de recursos específico, um nome de domínio do AD e muito mais.  

A Automação do Azure persiste variáveis e as disponibiliza mesmo quando um runbook ou uma configuração DSC falha. Esse comportamento permite que um runbook ou uma configuração DSC defina um valor que será usado por outro runbook ou pelo mesmo runbook ou configuração DSC na próxima vez em que for executado.

A Automação do Azure armazena cada variável criptografada com segurança. Ao criar uma variável, você pode especificar sua criptografia e armazenamento pela automação do Azure como um ativo seguro. Após criar a variável, você não pode alterar seu status de criptografia sem recriá-la. Se tiver variáveis de conta de Automação que armazenam dados confidenciais que ainda não foram criptografados, você precisará excluí-los e recriá-los como variáveis criptografadas. Uma recomendação da Central de Segurança do Azure é criptografar todas as variáveis de Automação do Azure, conforme descrito em [As variáveis da conta de automação devem ser criptografadas](../../security-center/recommendations-reference.md#recs-compute). Se você tiver variáveis não criptografadas que deseja excluir dessa recomendação de segurança, confira [Isentar um recurso das recomendações e da classificação de segurança](../../security-center/exempt-resource.md) para criar uma regra de isenção.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta da Automação do Azure. A Automação do Azure armazena a chave no Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a Automação do Azure carrega a chave do Key Vault e depois a usa para criptografar o ativo.

## <a name="variable-types"></a>Tipos de variável

Quando você cria uma variável com o portal do Azure, deve especificar um tipo de dados na lista suspensa para que o portal possa exibir o controle adequado para a inserção do valor da variável. Veja a seguir os tipos de variáveis disponíveis na Automação do Azure:

* String
* Integer
* Datetime
* Boolean
* Nulo

A variável não está restrita ao tipo de dados especificado. Você deve definir a variável usando o Windows PowerShell para especificar um valor de tipo diferente. Se você indicar `Not defined`, o valor da variável será definido como NULL. Você deve definir o valor com o cmdlet [Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) ou o cmdlet `Set-AutomationVariable` interno.

Você não pode usar o portal do Azure para criar ou alterar o valor de um tipo de variável complexa. No entanto, você pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos são recuperados como um [Newtonsoft.Jsem. LINQ. JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) para um tipo de objeto complexo em vez de um tipo de PSObject [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Você pode armazenar vários valores para uma única variável criando uma matriz ou hashtable e salvando-a na variável.

>[!NOTE]
>As variáveis de nome da VM podem ter, no máximo, 80 caracteres. As variáveis do grupo de recursos podem ter, no máximo, 90 caracteres. Confira as [Regras de nomenclatura e restrições para recursos do Azure](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>Cmdlets do PowerShell para acessar variáveis

Os cmdlets na tabela a seguir são usados para criar e gerenciar variáveis de Automação com o PowerShell. Eles são fornecidos como parte dos [módulos Az](modules.md#az-modules).

| Cmdlet | Descrição |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Recupera o valor de uma variável existente. Se o valor for de um tipo simples, o mesmo tipo será recuperado. Se for de um tipo complexo, um tipo `PSCustomObject` será recuperado. <br>**Observação:** Você não pode usar este cmdlet para recuperar o valor de uma variável criptografada. A única maneira de fazer isso é usando o cmdlet interno `Get-AutomationVariable` em um runbook ou configuração DSC. Confira [cmdlets internos para acessar variáveis](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Cria uma nova variável e define o seu valor.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Remove uma variável existente.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Define o valor de uma variável existente. |

## <a name="internal-cmdlets-to-access-variables"></a>Cmdlets internos para acessar variáveis

Os cmdlets internos na tabela a seguir são usados para acessar variáveis em seus runbooks ou configurações DSC. Esses cmdlets são fornecidos com o módulo global `Orchestrator.AssetManagement.Cmdlets`. Para obter mais informações, confira [Cmdlets internos](modules.md#internal-cmdlets).

| Cmdlet Interno | Descrição |
|:---|:---|
|`Get-AutomationVariable`|Recupera o valor de uma variável existente.|
|`Set-AutomationVariable`|Define o valor de uma variável existente.|

> [!NOTE]
> Evite usar variáveis no parâmetro `Name` de `Get-AutomationVariable` em um runbook ou configuração DSC. O uso das variáveis pode complicar a descoberta das dependências entre runbooks e variáveis de Automação no tempo de design.

`Get-AutomationVariable` não funciona no PowerShell, mas apenas em um runbook ou configuração DSC. Por exemplo, para ver o valor de uma variável criptografada, você pode criar um runbook para obter a variável e, em seguida, gravá-la no fluxo de saída:

```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-functions-to-access-variables"></a>Funções do Python para acessar variáveis

As funções na tabela a seguir são usadas para acessar variáveis em um runbook do Python 2 e 3. Atualmente, os runbooks do Python 3 estão em versão prévia.

|Funções do Python|Descrição|
|:---|:---|
|`automationassets.get_automation_variable`|Recupera o valor de uma variável existente. |
|`automationassets.set_automation_variable`|Define o valor de uma variável existente. |

> [!NOTE]
> É necessário importar o módulo `automationassets`, na parte superior do runbook Python, para acessar as funções do ativo.

## <a name="create-and-get-a-variable"></a>Criar e obter uma variável

>[!NOTE]
>Para remover a criptografia de uma variável, exclua a variável e crie novamente como não criptografada.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Criar e obter uma variável usando o portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **variáveis** em **recursos compartilhados**.
2. Na página **variáveis** , selecione **Adicionar uma variável**.
3. Conclua as opções na página **nova variável** e, em seguida, selecione **criar** para salvar a nova variável.

> [!NOTE]
> Depois de salva, variável criptografada não pode ser exibida no Portal. Ela só pode ser atualizada.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Criar e obter uma variável no Windows PowerShell

O runbook ou a configuração DSC usa o cmdlet `New-AzAutomationVariable` para criar uma nova variável e definir seu valor inicial. Se a variável for criptografada, a chamada deverá usar o parâmetro `Encrypted`. O script pode recuperar o valor da variável usando `Get-AzAutomationVariable`.

>[!NOTE]
>Um script do PowerShell não pode recuperar um valor criptografado. A única maneira de fazer isso é usar o cmdlet interno `Get-AutomationVariable`.

O exemplo a seguir mostra como criar uma variável de cadeia de caracteres e retornar seu valor.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

O exemplo a seguir mostram como criar uma variável de tipo complexo e recuperar as suas propriedades. Nesse caso, um objeto de máquina virtual de [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) é usado especificando um subconjunto de suas propriedades.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable"

$vmName = $vmValue.Name
$vmExtensions = $vmValue.Extensions
```

## <a name="textual-runbook-examples"></a>Exemplos de runbook textual

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como definir e recuperar uma variável em um runbook textual. Este exemplo pressupõe a criação de variáveis de inteiro chamadas `NumberOfIterations` e `NumberOfRunnings` e uma variável de cadeia de caracteres denominada `SampleMessage`.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

# <a name="python-2"></a>[Python 2](#tab/python2)

O exemplo a seguir mostra como obter uma variável, defini-la e processar uma exceção de uma variável não existente em um runbook Python 2.

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

# <a name="python-3"></a>[Python 3](#tab/python3)

O exemplo a seguir mostra como obter uma variável, definir uma variável e manipular uma exceção para uma variável inexistente em um runbook do Python 3 (visualização).

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
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Exemplos de runbook gráfico

Em um runbook gráfico, você pode adicionar atividades do cmdlet interno `Get-AutomationVariable` ou `Set-AutomationVariable`. Basta clicar com o botão direito do mouse em cada variável no painel Biblioteca do editor gráfico e selecionar a atividade desejada.

![Adicionar variável à tela](../media/variables/runbook-variable-add-canvas.png)

A imagem a seguir mostra exemplos de atividades para atualizar uma variável com um valor simples em um runbook gráfico. Neste exemplo, a atividade de `Get-AzVM` recupera uma única máquina virtual do Azure e salva o nome do computador em uma variável de cadeia de caracteres existente na Automação. Não importa se o [link é um pipeline ou uma sequência](../automation-graphical-authoring-intro.md#use-links-for-workflow), já que esperamos somente um objeto na saída.

![Definir variável simples](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar variáveis, consulte [Gerenciar módulos na Automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações de DSC, consulte [visão geral do State Configuration da Automação do Azure](../automation-dsc-overview.md).
