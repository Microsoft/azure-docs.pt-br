---
title: Aprender sobre o Fluxo de Trabalho do PowerShell para a Automação do Azure
description: Este artigo ensina as diferenças entre o Fluxo de Trabalho do PowerShell e o PowerShell e os conceitos aplicáveis a runbooks de Automação.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896911"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Aprender sobre o Fluxo de Trabalho do PowerShell para a Automação do Azure

Os runbooks na Automação do Azure são implementados como fluxos de trabalho do Windows PowerShell, scripts do Windows PowerShell que usam o Windows Workflow Foundation. Um fluxo de trabalho é uma sequência de etapas programadas e conectadas que executam tarefas de execução longa ou exigem a coordenação de várias etapas em vários dispositivos ou nós gerenciados. 

Embora um fluxo de trabalho seja escrito com a sintaxe do Windows PowerShell e inicializado pelo Windows PowerShell, ele é processado pelo Windows Workflow Foundation. Os benefícios de um fluxo de trabalho comparado com um script normal incluem a capacidade de realizar simultaneamente uma ação em vários dispositivos e a capacidade de se recuperar automaticamente de falhas. 

> [!NOTE]
> Um Fluxo de Trabalho do PowerShell é muito semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo usuário. Portanto, recomendamos que você escreva seus runbooks usando o Fluxo de Trabalho do PowerShell somente se precisar usar [pontos de verificação](#use-checkpoints-in-a-workflow). 

Para obter detalhes completos sobre os tópicos nesse artigo, confira [Introdução ao Fluxo de Trabalho do Windows PowerShell](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Usar palavra-chave de fluxo de trabalho

A primeira etapa para converter um script do PowerShell para um Fluxo de Trabalho do PowerShell é circunscrevê-lo com a palavra-chave `Workflow`. Um fluxo de trabalho começa com a palavra-chave `Workflow` seguida do corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave `Workflow`, conforme mostra a sintaxe a seguir:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho deve corresponder ao nome do runbook de automação. Se o runbook está sendo importado, o nome do arquivo precisa corresponder ao nome do fluxo de trabalho e terminar em **.ps1**.

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave `Param`, exatamente como faria para um script.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Aprenda as diferenças entre o código de fluxo de trabalho do PowerShell e código de script do PowerShell

O código de fluxo de trabalho do PowerShell é praticamente idêntico ao código de script do PowerShell, exceto por algumas alterações significativas. As seções a seguir descrevem as alterações que você precisa fazer em um script do PowerShell para ele para executar em um fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica em um fluxo de trabalho que é executada em uma sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades ao executar um fluxo de trabalho. Quando você especifica um desses cmdlets em seu runbook, a atividade correspondente é executada pelo Windows Workflow Foundation. 

Se um cmdlet não tem nenhuma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa o cmdlet automaticamente dentro de uma atividade [InlineScript](#use-inlinescript). Alguns cmdlets são excluídos e não podem ser usados em um fluxo de trabalho, a menos que você os inclua explicitamente em um bloco de InlineScript. Para obter mais informações, confira [Usando atividades em fluxos de trabalho de script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

As atividades de fluxo de trabalho compartilham um conjunto de parâmetros comuns para configurar suas operações. Confira [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Parâmetros posicionais

Você não pode usar parâmetros posicionais com atividades e cmdlets em um fluxo de trabalho. Portanto, você precisa usar nomes de parâmetro. Considere o seguinte código, que obtém todos os serviços em execução:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se você tentar executar esse código em um fluxo de trabalho, receberá uma mensagem como `Parameter set cannot be resolved using the specified named parameters.` Para corrigir esse problema, forneça o nome do parâmetro, como no seguinte exemplo:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos desserializados

Os objetos nos fluxos de trabalho são desserializados, o que significa que suas propriedades ainda estão disponíveis, mas os métodos delas não estão.  Por exemplo, considere o código do PowerShell a seguir, que interrompe um serviço usando o método `Stop` do objeto `Service`.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se você tentar executá-lo em um fluxo de trabalho, receberá um erro com a mensagem `Method invocation is not supported in a Windows PowerShell Workflow.`

Uma opção é encapsular essas duas linhas de código em um bloco [InlineScript](#use-inlinescript). Nesse caso, `Service` representa um objeto de serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é usar outro cmdlet que tenha a mesma funcionalidade que o método, se houver um disponível. No nosso exemplo, o cmdlet `Stop-Service` fornece a mesma funcionalidade que o método `Stop`, sendo que você pode usar o código demonstrado a seguir para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Usar InlineScript

A atividade `InlineScript` é útil quando você precisa executar um ou mais comandos como um script do PowerShell tradicional em vez do Fluxo de Trabalho do PowerShell.  Enquanto os comandos em um fluxo de trabalho são enviados ao Windows Workflow Foundation para processamento, os comandos em um bloco de InlineScript são processados pelo Windows PowerShell.

O InlineScript usa a sintaxe mostrada abaixo.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Você pode retornar a saída de um InlineScript atribuindo a saída a uma variável. O exemplo a seguir para um serviço e, em seguida, gera o nome do serviço.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Você pode passar valores para um bloco InlineScript, mas deve usar o modificador de escopo **$Using** .  O exemplo a seguir é idêntico ao exemplo anterior, exceto que o nome do serviço é fornecido por uma variável.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Embora as atividades de InlineScript possam ser críticas em determinados fluxos de trabalho, elas não são compatíveis com constructos de fluxo de trabalho. Você deve usá-las somente quando necessário, pelos seguintes motivos:

* Não é possível usar [pontos de verificação](#use-checkpoints-in-a-workflow) dentro de um bloco InlineScript. Se uma falha ocorrer dentro do bloco, ele precisará retomar desde o início do bloco.
* Não é possível usar a [execução paralela](#use-parallel-processing) dentro de um bloco InlineScript.
* O InlineScript afeta a escalabilidade do fluxo de trabalho, uma vez que retém a sessão do Windows PowerShell por toda a duração do bloco de InlineScript.

Para obter mais informações sobre como usar o InlineScript, consulte [Executando comandos do Windows PowerShell em um fluxo de trabalho](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) e [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Usar processamento paralelo

Uma vantagem dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como com um script típico.

Você pode usar a palavra-chave `Parallel` para criar um bloco de script com vários comandos que são executados simultaneamente. Isso usa a sintaxe a seguir, mostrada abaixo. Nesse caso, Activity1 e a Activity2 começarão simultaneamente. A Activity3 começará somente após a conclusão da Activity1 e da Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos do PowerShell que copiam vários arquivos para um destino de rede. Esses comandos são executados em sequência, de forma que a cópia de um arquivo deve terminar antes que a do próximo seja iniciada.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

O fluxo de trabalho a seguir executa esses mesmos comandos em paralelo para que todos eles comecem a copiar ao mesmo tempo.  A mensagem de conclusão será exibida somente depois que todos eles tiverem sido copiados.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Você pode usar o constructo `ForEach -Parallel` para processar comandos de cada item em uma coleção simultaneamente. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script são executados em sequência. Esse processo usa a sintaxe a seguir, mostrada abaixo. Nesse caso, Activity1 será iniciada simultaneamente para todos os itens na coleção. Para cada item, a Activity2 será iniciada após a conclusão da Activity1. A Activity3 começará somente depois da conclusão da Activity1 e Activity2 para todos os itens. Nós usamos o parâmetro `ThrottleLimit` para limitar o paralelismo. Muito acima de um `ThrottleLimit` pode causar problemas. O valor ideal para o `ThrottleLimit` parâmetro depende de muitos fatores no ambiente. Comece com um valor baixo e experimente valores diferentes crescentes até encontrar um que funcione para a sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo a seguir é semelhante ao exemplo anterior copiando os arquivos em paralelo.  Nesse caso, uma mensagem é exibida para cada arquivo depois de copiar.  A mensagem de conclusão final será exibida somente depois que todos eles tiverem sido copiados.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Não recomendamos a execução de runbooks filho em paralelo, uma vez que isso demonstrou fornecer resultados não confiáveis. Às vezes, a saída do runbook filho não é exibida e as configurações em um runbook filho podem afetar os outros runbooks filho paralelos. Variáveis como `VerbosePreference`, `WarningPreference` e outras podem não ser propagadas para os runbooks filho. E se o runbook filho alterar esses valores, eles talvez não sejam restaurados adequadamente após a invocação.

## <a name="use-checkpoints-in-a-workflow"></a>Usar pontos de verificação em um fluxo de trabalho

Um ponto de verificação é um instantâneo do estado atual do fluxo de trabalho que inclui os valores atuais de variáveis e as saídas geradas para aquele ponto. Se um fluxo de trabalho terminar com erro ou for suspenso, ele será iniciado do seu último ponto de verificação na próxima vez em que for executado, em vez de começar do início. 

Você pode definir um ponto de verificação em um fluxo de trabalho com a atividade `Checkpoint-Workflow`. A Automação do Azure tem um recurso chamado [fair share](automation-runbook-execution.md#fair-share), com o qual qualquer runbook executado por três horas é descarregado para permitir que outros runbooks sejam executados. Eventualmente, o runbook descarregado é recarregado. Quando isso ocorre, ele retoma a execução do último ponto de verificação feito no runbook.

Para garantir que o runbook seja concluído eventualmente, você precisa adicionar pontos de verificação em intervalos executados por menos de três horas. Se durante cada execução um novo ponto de verificação for adicionado e se o runbook for removido após três horas devido a um erro, o processamento do runbook será retomado indefinidamente.

No exemplo a seguir, uma exceção ocorre após Activity2, fazendo com que o fluxo de trabalho seja encerrado. Quando o fluxo de trabalho é executado novamente, ele começa pela execução de Activity2, já que essa atividade foi logo após o último ponto de verificação definido.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Defina pontos de verificação em um fluxo de trabalho após atividades que possam estar sujeitas a exceção e não devam ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o fluxo de trabalho pode criar uma máquina virtual. Você pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos serão repetidos se o fluxo de trabalho for iniciado novamente. Se o fluxo de trabalho falhar após a criação ser bem-sucedida, a máquina virtual não será criada novamente quando o fluxo de trabalho for retomado.

O exemplo a seguir copia vários arquivos para um local de rede e define um ponto de verificação depois de cada arquivo.  Se o local de rede for perdido, o fluxo de trabalho terminará em erro.  Quando ele é iniciado novamente, ele continua no último ponto de verificação. Somente os arquivos que já foram copiados são ignorados.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Como as credenciais do nome de usuário não são mantidas depois de chamar a atividade [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) ou após o último ponto de verificação, você precisa definir as credenciais para null e recuperá-las novamente do repositório de ativos após `Suspend-Workflow` ou o ponto de verificação ser chamado.  Caso contrário, a seguinte mensagem de erro poderá ser exibida: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

O mesmo código a seguir demonstra como lidar com essa situação em seus runbooks do Fluxo de Trabalho do PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Para runbooks não gráficos do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação. O uso desses cmdlets não será necessário se você estiver autenticando usando uma conta Executar como configurada com uma entidade de serviço.

Para saber mais sobre pontos de verificação, confira [Adicionando pontos de verificação a um Fluxo de Trabalho de script](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os runbooks de Fluxo de Trabalho do PowerShell, veja o [Tutorial: Criação de um runbook de Fluxo de Trabalho do PowerShell](learn/automation-tutorial-runbook-textual.md).
