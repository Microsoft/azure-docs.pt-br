---
title: Runbooks filhos na Automação do Azure
description: Descreve os diferentes métodos para iniciar um runbook na Automação do Azure de outro runbook e compartilhar informações entre eles.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64d9246284be58c8378ab102db25ab7e5220c9eb
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477960"
---
# <a name="child-runbooks-in-azure-automation"></a>Runbooks filhos na Automação do Azure

É uma prática recomendada na Automação do Azure escrever runbooks reutilizáveis e modulares com uma função discreta que é usada por outros runbooks. Um runbook pai geralmente chama um ou mais runbooks filho para executar a funcionalidade necessária. Há duas maneiras de chamar um runbook filho e cada uma tem diferenças marcantes que você deve compreender para determinar qual é melhor para seus diferentes cenários.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando a execução embutida

Para invocar um runbook embutido de outro runbook, use o nome do runbook e forneça valores para os parâmetros exatamente como você faria ao usar uma atividade ou um cmdlet.  Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai aguardará o runbook filho concluir antes de passar para a próxima linha e nenhuma saída é retornada diretamente para o pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não há nenhuma indicação no histórico de trabalhos do runbook filho de que ele foi executado. Todas as exceções e saídas de fluxo do runbook filho são associadas ao pai. Esse comportamento resulta em menos trabalhos e facilita o acompanhamento e a solução de problemas, já que as exceções geradas pelo runbook filho e por suas saídas de fluxo são associadas ao trabalho pai.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. Isso ocorre porque a Automação do Azure cria uma associação com os runbooks filhos quando um runbook é compilado. Caso contrário, o runbook pai parecerá como se estivesse sendo publicado corretamente, mas gerará uma exceção quando for iniciado. Se isso acontecer, você poderá publicar o runbook pai novamente para referenciar de forma correta os runbooks filho. Você não precisará publicar o runbook pai novamente se um dos runbooks filho forem alterados porque a associação já foi criada.

Os parâmetros de um runbook filho chamado embutido podem ser qualquer tipo de dados, incluindo objetos complexos. Não há nenhuma [serialização JSON](start-runbooks.md#runbook-parameters) como quando você inicia o runbook usando o portal do Azure ou com o cmdlet Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos podem chamar um ao outro:

* Um [runbook do PowerShell ](automation-runbook-types.md#powershell-runbooks) e os [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) podem chamar uns aos outros em linha (ambos são baseados no PowerShell).
* Um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks gráficos do Fluxo de Trabalho do PowerShell podem chamar uns aos outros de forma embutida (ambos são baseados no Fluxo de Trabalho do PowerShell)
* Os tipos de PowerShell e os tipos de Fluxo de Trabalho do PowerShell não podem chamar uns aos outros de forma embutida e precisam usar Start-AzureRmAutomationRunbook.

Quando a ordem de publicação é importante:

* a ordem de publicação de runbooks é importante apenas para runbooks do Fluxo de Trabalho do PowerShell e runbooks gráficos do Fluxo de Trabalho do PowerShell.

Quando você chama um runbook filho Gráfico ou de Fluxo de Trabalho do PowerShell usando a execução embutida, você usa o nome do runbook.  Quando chamar um runbook filho do PowerShell, você deverá iniciar seu nome com *.\\* para especificar que o script seja localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho de teste que aceita três parâmetros, um objeto complexo, um inteiro e um booliano. A saída do runbook filho é atribuída a uma variável.  Nesse caso, o runbook filho é um runbook de Fluxo de Trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

A seguir, temos o mesmo exemplo usando um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Iniciar um runbook filho usando cmdlet

> [!IMPORTANT]
> Se você estiver invocando um runbook filho com o cmdlet `Start-AzureRmAutomationRunbook` e a opção `-Wait` e os resultados do runbook filho forem um objeto, poderá encontrar erros. Para resolver o erro, confira [Runbooks filho com a saída de objeto](troubleshoot/runbooks.md#child-runbook-object) para saber como implementar a lógica para sondar os resultados e usar o [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Você pode usar o cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) para iniciar um runbook, como descrito em [Para iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Há dois modos de uso para esse cmdlet.  Em um modo, o cmdlet retorna a ID do trabalho quando o trabalho filho é criado para o runbook filho.  No outro modo, habilitado pela especificação do parâmetro **-wait**, o cmdlet aguarda até que o trabalho filho seja concluído e retorna a saída do runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet executa um trabalho separado do runbook pai. Esse comportamento resulta em mais trabalhos do que iniciar o runbook embutido e dificulta seu acompanhamento. O pai pode iniciar mais de um runbook filho de forma assíncrona sem aguardar a conclusão de cada um. Para a mesma variante de execução paralela que chama runbooks filho embutidos, o runbook pai precisaria usar a [palavra-chave paralela](automation-powershell-workflow.md#parallel-processing).

A saída dos runbooks filho não é retornada para o runbook pai de modo confiável devido ao tempo. Além disso, determinadas variáveis como $VerbosePreference, $WarningPreference e outras podem não ser propagadas para os runbooks filho. Para evitar esses problemas, inicie os runbooks filho como trabalhos de Automação separados usando o cmdlet `Start-AzureRmAutomationRunbook` com a opção `-Wait`. Isso bloqueia o runbook pai até que o runbook filho seja concluído.

Caso não deseje que o runbook pai seja bloqueado na espera, inicie o runbook filho usando o cmdlet `Start-AzureRmAutomationRunbook` sem a opção `-Wait`. É necessário usar `Get-AzureRmAutomationJob` para aguardar a conclusão do trabalho e `Get-AzureRmAutomationJobOutput` e `Get-AzureRmAutomationJobOutputRecord` para recuperar os resultados.

Parâmetros de um runbook filho iniciados com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [Parâmetros de Runbook](start-runbooks.md#runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

O contexto da assinatura pode ser perdido ao iniciar runbooks filho como trabalhos separados. Para que o runbook filho execute cmdlets do Azure RM em uma assinatura específica do Azure, o runbook filho precisa se autenticar nessa assinatura independentemente do runbook pai.

Se os trabalhos na mesma conta de Automação funcionarem com mais de uma assinatura, a seleção de uma assinatura em um trabalho poderá alterar o contexto de assinatura atualmente selecionado para outros trabalhos. Para evitar esse problema, use `Disable-AzureRmContextAutosave –Scope Processsave` no início de cada runbook. Essa ação apenas salva o contexto nessa execução de runbook.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho com parâmetros e aguarda a sua conclusão usando o parâmetro Start-AzureRmAutomationRunbook -wait. Depois de concluído, sua saída é coletada do runbook filho. Para usar `Start-AzureRmAutomationRunbook` é necessário autenticar-se na assinatura do Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Comparação de métodos para chamar um runbook filho

A tabela a seguir resume as diferenças entre os dois métodos para chamar um runbook de outro runbook.

|  | Embutido | Cmdlet |
|:--- |:--- |:--- |
| Trabalho |Os runbooks filhos são executados no mesmo trabalho que o pai. |Um trabalho separado é criado para o runbook filho. |
| Execução |O runbook pai aguarda a conclusão do runbook filho antes de continuar. |O runbook pai continua imediatamente após o runbook filho ser iniciado *ou* o runbook pai aguarda a conclusão do trabalho filho. |
| Output |O runbook pai pode obter saída diretamente do runbook filho. |O runbook pai deve recuperar a saída do trabalho do runbook filho *ou* o runbook pai pode obter a saída diretamente do runbook filho. |
| parâmetros |Os valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados. |Os valores para os parâmetros do runbook filho devem ser combinados em uma única tabela de hash. Essa tabela de hash pode apenas incluir tipos de dados simples, de matriz e de objeto que usam a serialização JSON. |
| Conta de Automação |O runbook pai somente pode usar o runbook filho na mesma conta de automação. |Os runbooks pai podem usar um runbook filho de qualquer conta de automação da mesma assinatura do Azure e, até mesmo, de uma assinatura diferente com a qual você tem uma conexão. |
| Publicação |O runbook filho deve ser publicado antes da publicação do runbook pai. |O runbook filho precisa ser publicado a qualquer momento antes da inicialização do runbook pai. |

## <a name="next-steps"></a>Próximas etapas

* [Como iniciar um Runbook na Automação do Azure](start-runbooks.md)
* [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md)

