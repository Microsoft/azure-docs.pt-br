---
title: Criar runbooks modulares na Automação do Azure
description: Este artigo informa como criar um runbook que é chamado por outro runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 338de996b06769b9d2891c7208b9050cc3acc7ed
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167287"
---
# <a name="create-modular-runbooks"></a>Criar runbooks modulares

É uma prática recomendada na Automação do Azure escrever runbooks reutilizáveis e modulares com uma função discreta que é chamada por outros runbooks. Um runbook pai geralmente chama um ou mais runbooks filho para executar a funcionalidade necessária. 

Há duas maneiras de chamar um runbook filho, e há diferenças que você deve entender para poder determinar qual é a melhor para seus cenários. A tabela a seguir resume as diferenças entre os dois métodos para chamar um runbook de outro runbook.

|  | Embutido | Cmdlet |
|:--- |:--- |:--- |
| **Trabalho** |Os runbooks filhos são executados no mesmo trabalho que o pai. |Um trabalho separado é criado para o runbook filho. |
| **Execução** |O runbook pai aguarda a conclusão do runbook filho antes de continuar. |O runbook pai continua imediatamente após o runbook filho ser iniciado *ou* o runbook pai aguarda a conclusão do trabalho filho. |
| **Saída** |O runbook pai pode obter saída diretamente do runbook filho. |O runbook pai deve recuperar a saída do trabalho do runbook filho *ou* o runbook pai pode obter a saída diretamente do runbook filho. |
| **Parâmetros** |Os valores para os parâmetros de runbook filho são especificados separadamente e podem usar qualquer tipo de dados. |Os valores para os parâmetros do runbook filho devem ser combinados em uma única tabela de hash. Essa tabela de hash pode apenas incluir tipos de dados simples, de matriz e de objeto que usam a serialização JSON. |
| **Conta de Automação** |O runbook pai pode usar o runbook filho somente na mesma conta de Automação. |Os runbooks pai podem usar um runbook filho de qualquer conta de Automação da mesma assinatura do Azure e, até mesmo, de uma assinatura diferente com a qual você tenha uma conexão. |
| **Publicando** |O runbook filho deve ser publicado antes da publicação do runbook pai. |O runbook filho deve ser publicado antes da inicialização do runbook pai. |

## <a name="invoke-a-child-runbook-using-inline-execution"></a>Invocar um runbook filho usando a execução integrada

Para invocar um runbook integrado de outro runbook, use o nome do runbook e dê valores para os parâmetros exatamente como você faria ao usar uma atividade ou um cmdlet. Todos os runbooks na mesma conta de automação estão disponíveis para todos os outros para serem usados dessa maneira. O runbook pai aguardará o runbook filho concluir antes de passar para a próxima linha, e nenhuma saída será retornada diretamente ao pai.

Quando você chama um runbook embutido, ele é executado no mesmo trabalho que o runbook pai. Não há nenhuma indicação no histórico de trabalhos do runbook filho. Todas as exceções e saídas de fluxo do runbook filho são associadas ao pai. Esse comportamento resulta em menos trabalhos e facilita o controle e a solução de problemas.

Quando um runbook é publicado, todos os runbooks filhos que ele chamar já devem ter sido publicados. Isso ocorre porque a Automação do Azure cria uma associação com os runbooks filhos quando um runbook é compilado. Se os runbooks filho ainda não tiverem sido publicados, o runbook pai parecerá publicar corretamente, mas gerará uma exceção quando for iniciado. Se isso acontecer, você poderá publicar o runbook pai novamente para referenciar de forma correta os runbooks filho. Não será preciso publicar o runbook pai novamente se um dos runbooks filho forem alterados, porque a associação já foi criada.

Os parâmetros de um runbook filho chamado embutido podem ser qualquer tipo de dados, incluindo objetos complexos. Não há nenhuma [serialização JSON](start-runbooks.md#work-with-runbook-parameters), como quando você inicia o runbook usando o portal do Azure ou com o cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

### <a name="runbook-types"></a>Tipos de runbook

Quais tipos de runbook podem chamar uns aos outros?

* Um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) e os [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) podem chamar uns aos outros de maneira embutida (ambos são baseados no PowerShell).
* Um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) e runbooks gráficos do Fluxo de Trabalho do PowerShell podem chamar uns aos outros de maneira embutida, já que ambos são baseados no Fluxo de Trabalho do PowerShell.
* Os tipos do PowerShell e os tipos do Fluxo de Trabalho do PowerShell não podem chamar uns aos outros de forma embutida e precisam usar `Start-AzAutomationRunbook`.

Quando a ordem de publicação é importante?

A ordem de publicação de runbooks é importante apenas para runbooks do Fluxo de Trabalho do PowerShell e runbooks gráficos do Fluxo de Trabalho do PowerShell.

Quando o runbook chama um runbook filho do Fluxo de Trabalho gráfico ou do PowerShell usando a execução embutida, ele usa o nome do runbook. O nome deve começar com `.\\` para especificar que o script está localizado no diretório local.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho de teste que aceita um objeto complexo, um valor inteiro e um valor booliano. A saída do runbook filho é atribuída a uma variável. Nesse caso, o runbook filho é um runbook de Fluxo de Trabalho do PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

A seguir, temos o mesmo exemplo usando um runbook do PowerShell como o filho.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-using-a-cmdlet"></a>Iniciar um runbook filho usando um cmdlet

> [!IMPORTANT]
> Se o seu runbook invocar um runbook filho com o cmdlet `Start-AzAutomationRunbook` com o parâmetro `Wait` e o runbook filho produzir um resultado de objeto, a operação talvez encontre um erro. Para resolver o erro, confira [Runbooks filho com saída de objeto](troubleshoot/runbooks.md#child-runbook-object) para saber como implementar a lógica para sondar os resultados usando o cmdlet [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord).

É possível usar o cmdlet `Start-AzAutomationRunbook` para iniciar um runbook, conforme descrito em [Iniciar um runbook com o Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Há dois modos de uso para esse cmdlet. Em um modo, o cmdlet retorna a ID do trabalho quando o trabalho é criado para o runbook filho. No outro modo, que seu script habilita pela especificação do parâmetro *Wait*, o cmdlet aguarda até que o trabalho filho seja concluído e retorna a saída do runbook filho.

O trabalho de um runbook filho iniciado com um cmdlet é executado separado do runbook pai. Esse comportamento resulta em mais trabalhos do que ao iniciar o runbook embutido e dificulta o acompanhamento deles. O pai pode iniciar mais de um runbook filho de forma assíncrona sem aguardar a conclusão de cada um. Para essa execução paralela chamando os runbooks filho embutidos, o runbook pai deve usar o [palavra-chave parallel](automation-powershell-workflow.md#use-parallel-processing).

A saída do runbook filho não retorna ao runbook pai de modo confiável devido ao tempo. Além disso, variáveis como `$VerbosePreference`, `$WarningPreference` e outras podem não ser propagadas para os runbooks filho. Para evitar esses problemas, inicie os runbooks filho como trabalhos de Automação separados usando `Start-AzAutomationRunbook` com a opção `Wait`. Essa técnica bloqueia o runbook pai até que o runbook filho seja concluído.

Caso não deseje que o runbook pai seja bloqueado em espera, inicie o runbook filho usando `Start-AzAutomationRunbook` sem o parâmetro `Wait`. Nesse caso, o runbook deve usar [ Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para aguardar a conclusão do trabalho. Ele também deve usar [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) e [ Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) para recuperar os resultados.

Parâmetros de um runbook filho iniciados com um cmdlet são fornecidos como uma tabela de hash, conforme descrito em [Parâmetros de runbook](start-runbooks.md#work-with-runbook-parameters). Somente tipos de dados simples podem ser usados. Se o runbook tiver um parâmetro com um tipo de dados complexos, ele deve ser chamado embutido.

O contexto da assinatura pode ser perdido ao iniciar runbooks filho como trabalhos separados. Para que o runbook filho execute cmdlets do módulo Az em uma assinatura específica do Azure, o runbook filho precisa se autenticar nessa assinatura independentemente do runbook pai.

Se os trabalhos na mesma conta de Automação funcionarem com mais de uma assinatura, a seleção de uma assinatura em um trabalho poderá mudar o contexto da assinatura atualmente selecionada para outros trabalhos. Para evitar esse problema, use `Disable-AzContextAutosave -Scope Process` no início de cada runbook. Essa ação apenas salva o contexto nessa execução de runbook.

### <a name="example"></a>Exemplo

O exemplo a seguir inicia um runbook filho com parâmetros e, em seguida, aguarda que ele seja concluído usando o cmdlet `Start-AzAutomationRunbook` com o parâmetro `Wait`. Depois de concluído, o exemplo coleta a saída do cmdlet do runbook filho. Para usar `Start-AzAutomationRunbook`, o script precisa se autenticar na sua assinatura do Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Set-AzContext -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    -Parameters $params -Wait
```

## <a name="next-steps"></a>Próximas etapas

* Para executar o runbook, veja [Iniciar um runbook na Automação do Azure](start-runbooks.md).
* Para o monitoramento da operação de runbook, veja [Saída de runbook e mensagens na Automação do Azure](automation-runbook-output-and-messages.md).
