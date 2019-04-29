---
title: Aprender sobre Fluxo de Trabalho do PowerShell para Automação do Azure
description: Este artigo é concebido como uma lição rápida para autores familiarizados com o PowerShell para entender as diferenças entre o PowerShell e o fluxo de trabalho do PowerShell e conceitos aplicáveis aos runbooks de Automação.
services: automation
ms.service: automation
ms.subservice: process-automation
author: WenJason
ms.author: v-jay
origin.date: 12/14/2018
ms.date: 04/01/2019
ms.topic: conceptual
manager: digimobile
ms.openlocfilehash: c5764c36a646b9639c0eb6463c39b9f014c4272d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738325"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Aprender sobre os principais conceitos de Fluxo de Trabalho do Windows PowerShell para runbooks de Automação

Os runbooks na Automação do Azure são implementados como Fluxos de Trabalho do Windows PowerShell.  Um fluxo de trabalho do Windows PowerShell é semelhante a um script do Windows PowerShell, mas tem algumas diferenças significativas que podem ser confusas para um novo usuário.  Embora este artigo sirva para ajudar você a escrever runbooks usando o Fluxo de Trabalho do PowerShell, recomendamos que você escreva os runbooks usando o PowerShell, a menos que você precise de pontos de verificação.  Há diversas diferenças de sintaxe quando se cria runbooks de Fluxo de trabalho do PowerShell, e essas diferenças exigem um pouco mais de trabalho para escrever fluxos de trabalho efetivos.

Um fluxo de trabalho é uma sequência de etapas programadas e conectadas que executam tarefas de longa duração ou exigem a coordenação de várias etapas em vários dispositivos ou nós gerenciados. Os benefícios de um fluxo de trabalho comparado com um script normal incluem a capacidade de realizar simultaneamente uma ação em vários dispositivos e a capacidade de se recuperar automaticamente de falhas. Um Fluxo de Trabalho do Windows PowerShell é um script do Windows PowerShell que usa o Windows Workflow Foundation. Embora o fluxo de trabalho seja escrito com a sintaxe do Windows PowerShell e inicializado pelo Windows PowerShell, ele é processado pelo Windows Workflow Foundation.

Para obter detalhes completos sobre os tópicos nesse artigo, consulte [Introdução ao fluxo de trabalho do Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Estrutura básica de um fluxo de trabalho

A primeira etapa para converter um script do PowerShell para um fluxo de trabalho do PowerShell é circunscrevê-lo com a palavra-chave **Workflow** .  Um fluxo de trabalho começa com a palavra-chave **Workflow** seguida do corpo do script entre chaves. O nome do fluxo de trabalho segue a palavra-chave **Workflow**, conforme mostra a sintaxe a seguir:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

O nome do fluxo de trabalho deve corresponder ao nome do runbook de automação. Se o runbook está sendo importado, o nome do arquivo deve corresponder ao nome do fluxo de trabalho e deve terminar em *.ps1*.

Para adicionar parâmetros ao fluxo de trabalho, use a palavra-chave **Param** , exatamente como faria para um script.

## <a name="code-changes"></a>Alterações de código

O código de fluxo de trabalho do PowerShell é praticamente idêntico ao código de script do PowerShell, exceto por algumas alterações significativas.  As seções a seguir descrevem as alterações que você precisa fazer em um script do PowerShell para ele para executar em um fluxo de trabalho.

### <a name="activities"></a>Atividades

Uma atividade é uma tarefa específica em um fluxo de trabalho. Assim como um script é composto de um ou mais comandos, um fluxo de trabalho é composto de uma ou mais atividades que são executadas em uma sequência. O fluxo de trabalho do Windows PowerShell converte automaticamente muitos dos cmdlets do Windows PowerShell para atividades ao executar um fluxo de trabalho. Quando você especifica um desses cmdlets em seu runbook, a atividade correspondente é executada pelo Windows Workflow Foundation. Para esses cmdlets sem uma atividade correspondente, o Fluxo de Trabalho do Windows PowerShell executa o cmdlet automaticamente dentro de uma atividade de [InlineScript](#inlinescript) . Há um conjunto de cmdlets que são excluídos e não podem ser usados em um fluxo de trabalho, a menos que você o inclua explicitamente em um bloco de InlineScript. Para obter mais detalhes sobre esses conceitos, confira [Usando atividades em fluxos de trabalho de script](https://technet.microsoft.com/library/jj574194.aspx).

As atividades de fluxo de trabalho compartilham um conjunto de parâmetros comuns para configurar suas operações. Para obter detalhes sobre os parâmetros comuns do fluxo de trabalho, consulte [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Parâmetros posicionais

Você não pode usar parâmetros posicionais com atividades e cmdlets em um fluxo de trabalho.  Tudo o que isso significa é que você deve usar nomes de parâmetro.

Por exemplo, considere o código a seguir que obtém todos os serviços em execução.

```powershell
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Se você tentar executar esse mesmo código em um fluxo de trabalho, receberá uma mensagem como "O conjunto de parâmetros não pode ser resolvido usando os parâmetros nomeados especificados".  Para corrigir isso, basta fornecer o nome do parâmetro como demonstrado a seguir.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objetos desserializados

Os objetos em fluxos de trabalho são desserializados.  Isso significa que suas propriedades ainda estão disponíveis, mas não seus métodos.  Por exemplo, considere código do PowerShell a seguir que para um serviço usando o método Stop do objeto Service.

```powershell
$Service = Get-Service -Name MyService
$Service.Stop()
```

Se você tentar executá-lo em um fluxo de trabalho, receberá um erro dizendo "Não há suporte para a invocação de método em um Fluxo de Trabalho do Windows PowerShell".

Uma opção é encapsular essas duas linhas de código em um bloco [InlineScript](#inlinescript); neste caso, $Service seria um objeto de serviço dentro do bloco.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Outra opção é usar outro cmdlet que executa a mesma funcionalidade que o método, se houver um disponível.  No nosso exemplo, o cmdlet Stop-Service fornece a mesma funcionalidade que o método Stop, sendo que você pode usar o demonstrado a seguir para um fluxo de trabalho.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

A atividade **InlineScript** é útil quando você precisa executar um ou mais comandos como um script do PowerShell tradicional em vez do fluxo de trabalho do PowerShell.  Enquanto os comandos em um fluxo de trabalho são enviados ao Windows Workflow Foundation para processamento, os comandos em um bloco de InlineScript são processados pelo Windows PowerShell.

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

Embora as atividades InlineScript possam ser essenciais em determinados fluxos de trabalho, elas não dão suporte a construtores de fluxo de trabalho e devem ser usadas somente quando necessário pelos seguintes motivos:

* Não é possível usar [pontos de verificação](#checkpoints) dentro de um bloco InlineScript. Se uma falha ocorre dentro do bloco, ele deve ser retomado desde o início do bloco.
* Não é possível usar a [execução paralela](#parallel-processing) dentro de um InlineScriptBlock.
* O InlineScript afeta a escalabilidade do fluxo de trabalho, uma vez que retém a sessão do Windows PowerShell por toda a duração do bloco de InlineScript.

Para obter mais informações sobre como usar o InlineScript, consulte [Executando comandos do Windows PowerShell em um fluxo de trabalho](https://technet.microsoft.com/library/jj574197.aspx) e [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Processamento paralelo

Uma vantagem dos Fluxos de Trabalho do Windows PowerShell é a capacidade de executar um conjunto de comandos em paralelo em vez de sequencialmente como com um script típico.

Você pode usar a palavra-chave **Parallel** para criar um bloco de script com vários comandos que são executados simultaneamente. Isso usa a sintaxe a seguir, mostrada abaixo. Nesse caso, Activity1 e a Activity2 começarão simultaneamente. A Activity3 começará somente após a conclusão da Activity1 e da Activity2.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Por exemplo, considere os seguintes comandos do PowerShell que copiam vários arquivos para um destino de rede.  Esses comandos são executados em sequência, de forma que a cópia de um arquivo deve terminar antes que a do próximo seja iniciada.

```powershell
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

Você pode usar o construct **ForEach-Parallel** para processar comandos de cada item em uma coleção simultaneamente. Os itens na coleção são processados em paralelo, enquanto os comandos no bloco de script são executados em sequência. Isso usa a sintaxe a seguir, mostrada abaixo. Nesse caso, Activity1 será iniciada simultaneamente para todos os itens na coleção. Para cada item, a Activity2 será iniciada após a conclusão da Activity1. A Activity3 começará somente depois da conclusão da Activity1 e Activity2 para todos os itens. Nós usamos o parâmetro `ThrottleLimit` para limitar o paralelismo. Muito acima de um `ThrottleLimit` pode causar problemas. O valor ideal para o `ThrottleLimit` parâmetro depende de muitos fatores no ambiente. Você deve experimentar começando com um valor baixo e tentar diferentes valores crescentes até localizar um que funcione para a sua circunstância específica.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

O exemplo a seguir é semelhante ao exemplo anterior copiando os arquivos em paralelo.  Nesse caso, uma mensagem é exibida para cada arquivo depois de copiar.  Somente depois que todos estiverem completamente copiados a mensagem de conclusão final é exibida.

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
> Não recomendamos a execução de runbooks filho em paralelo, uma vez que isso demonstrou fornecer resultados não confiáveis. Às vezes, a saída do runbook filho não é exibida e as configurações em um runbook filho podem afetar os outros runbooks filho paralelos. Variáveis como $VerbosePreference, $WarningPreference, e outras, podem não ser propagadas para os runbooks filho. E se o runbook filho alterar esses valores, talvez não sejam restaurados adequadamente após a invocação.

## <a name="checkpoints"></a>Pontos de verificação

Um *ponto de verificação* é um instantâneo do estado atual do fluxo de trabalho que inclui o valor atual de variáveis e as saídas geradas para aquele ponto. Se um fluxo de trabalho terminar com erro ou se for suspenso, na próxima vez que for executado, ele iniciará em seu último ponto de verificação e não no início do fluxo de trabalho.  Você pode definir um ponto de verificação em um fluxo de trabalho com a atividade **Checkpoint-Workflow** . A automação do Azure tem um recurso chamado [justa](automation-runbook-execution.md#fair-share), onde qualquer runbook que é executado por 3 horas é descarregado para permitir que outros runbooks para serem executados. Eventualmente, o runbook descarregado será recarregado e quando ele estiver, ele retomará a execução do último ponto de verificação executada no runbook. Para garantir que o runbook será concluída, você deve adicionar os pontos de verificação em intervalos que são executados por menos de 3 horas. Se durante cada execução de um novo ponto de verificação é adicionado, e se o runbook obtém removido após três horas devido a um erro, em seguida, o runbook será retomado indefinidamente.

No código de exemplo a seguir, uma exceção ocorre após Activity2, fazendo com que o fluxo de trabalho seja encerrado. Quando o fluxo de trabalho é executado novamente, ele começa pela execução de Activity2, já que isso foi logo após o último ponto de verificação definido.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Você deve definir pontos de verificação em um fluxo de trabalho para depois de atividades que possam estar sujeitas a exceção e não devam ser repetidas se o fluxo de trabalho for retomado. Por exemplo, o fluxo de trabalho pode criar uma máquina virtual. Você pode definir um ponto de verificação antes e depois dos comandos para criar a máquina virtual. Se a criação falhar, os comandos serão repetidos se o fluxo de trabalho for iniciado novamente. Se o fluxo de trabalho falhar após a criação ser bem-sucedida, a máquina virtual não será criada novamente quando o fluxo de trabalho for retomado.

O exemplo a seguir copia vários arquivos para um local de rede e define um ponto de verificação depois de cada arquivo.  Se o local de rede for perdido, o fluxo de trabalho terminará em erro.  Quando ele for iniciado novamente, ele retomará do último ponto de verificação, o que significa que somente os arquivos que já tiverem sido copiados serão ignorados.

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

Como as credenciais do nome de usuário não são mantidas depois de chamar a atividade [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) ou após o último ponto de verificação, você precisa definir as credenciais para null e recuperá-las novamente no armazenamento de ativos após **Suspend-Workflow** ou o ponto de verificação ser chamado.  Caso contrário, a seguinte mensagem de erro será exibida: *O trabalho de fluxo de trabalho não pode ser retomado porque os dados de persistência não puderam ser salvos completamente, ou os dados de persistência salvos foram corrompidos. Você deve reiniciar o fluxo de trabalho.*

O mesmo código a seguir demonstra como lidar com isso em seus runbooks do Fluxo de Trabalho do PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "ChinaNorth" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -EnvironmentName AzureChinaCloud -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

Isso não é necessário se você estiver autenticando usando uma conta Executar como configurada com uma entidade de serviço.

Para saber mais sobre pontos de verificação, confira [Adicionando pontos de verificação a um Fluxo de Trabalho de script](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)

