---
title: Execução de runbook na Automação do Azure
description: Descreve os detalhes de como um runbook na Automação do Azure é processado.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3ea6c4a0fa9c112788a717f14e2fc92e5fbc7700
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476983"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbook na Automação do Azure

Quando você inicia um runbook na Automação do Azure, um trabalho é criado. Um trabalho é uma instância única de execução de um runbook. Um trabalhador da Automação do Azure é atribuído para executar cada tarefa. Enquanto os trabalhadores são compartilhados por muitas contas do Azure, os trabalhos de diferentes contas de automação ficam isolados uns dos outros. Você não tem controle sobre qual trabalhador atende a solicitação do seu trabalho. Um único runbook pode ter muitos trabalhos em execução ao mesmo tempo. O ambiente de execução para trabalhos da mesma conta de Automação do Azure pode ser reutilizado. Quanto mais trabalhos você executar ao mesmo tempo, mais frequentemente eles poderão ser enviados à mesma área restrita. Trabalhos em execução no mesmo processo de área restrita podem afetar uns aos outros, sendo um exemplo a execução do cmdlet `Disconnect-AzureRMAccount`. Executar este cmdlet desconectaria cada trabalho de runbook no processo de área restrita compartilhado. Quando você exibe a lista de runbooks no Portal do Azure, ela lista o status de todos os trabalhos iniciados para cada runbook. Você pode exibir a lista de trabalhos para cada runbook para acompanhar o status de cada um. Os logs de trabalho são armazenados por 30 dias, no máximo. Para obter uma descrição das diferentes opções de status de trabalho, confira [Status de trabalho](#job-statuses).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

O diagrama a seguir mostra o ciclo de vida de um trabalho de runbook para [runbooks do PowerShell](automation-runbook-types.md#powershell-runbooks), [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) e [runbooks de fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

![Status de trabalho - Fluxo de trabalho do PowerShell](./media/automation-runbook-execution/job-statuses.png)

Os trabalhos têm acesso aos recursos do Azure fazendo uma conexão à sua assinatura do Azure. Eles só têm acesso aos recursos no seu data center se estes puderem ser acessados da nuvem pública.

## <a name="where-to-run-your-runbooks"></a>Onde executar seus runbooks

Os runbooks na Automação do Azure podem ser executados em uma área restrita no Azure ou em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md). Uma área restrita é um ambiente compartilhado no Azure que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são restringidos pelas limitações de recurso da área restrita. Hybrid Runbook Workers podem executar runbooks diretamente no computador que está hospedando a função e em relação aos recursos no ambiente para gerenciar esses recursos locais. Runbooks são armazenados e gerenciados na Automação do Azure e, em seguida, entregues a um ou mais computadores atribuídos. A maioria dos runbooks facilmente podem ser executados em áreas restritas do Azure. Há cenários específicos em que pode ser recomendada a escolha de um Hybrid Runbook ao invés de uma área restrita do Azure para execução do seu runbook. Veja a seguinte tabela com uma lista de alguns exemplos de cenário:

|Tarefa|Melhor opção|Observações|
|---|---|---|
|Integração com serviços do Azure|Área restrita do Azure|Com hospedagem no Azure, a autenticação é mais simples. Se você está usando o Hybrid Runbook Worker em uma VM do Azure, é possível usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)|
|Desempenho ideal para gerenciar recursos do Azure|Área restrita do Azure|Script é executado no mesmo ambiente, que por sua vez, tem menos latência|
|Redução de custos operacionais|Área restrita do Azure|Não há sobrecarga de computação, nem a necessidade de uma VM|
|Script de execução prolongada|Hybrid Runbook Worker|Áreas restritas do Azure têm [limitação de recursos](../azure-subscription-service-limits.md#automation-limits)|
|Interação com serviços locais|Hybrid Runbook Worker|Pode ter acesso diretamente ao computador host|
|Exigência de software de terceiros e executáveis|Hybrid Runbook Worker|Você gerencia o SO e pode instalar software|
|Monitoramento de um arquivo ou uma pasta com um runbook|Hybrid Runbook Worker|Use uma [tarefa do Inspetor](automation-watchers-tutorial.md) em um Hybrid Runbook Worker|
|O script usa muitos recursos|Hybrid Runbook Worker| Áreas restritas do Azure têm [limitação de recursos](../azure-subscription-service-limits.md#automation-limits)|
|Uso de módulos com requisitos específicos| Hybrid Runbook Worker|Alguns exemplos incluem:</br> **WinSCP**: dependência de winscp.exe </br> **IISAdministration**: necessidade de que o IIS seja desabilitado|
|Instalação do módulo que requer o instalador|Hybrid Runbook Worker|Módulos para a área restrita devem ser pode ser copiados|
|Uso de runbooks ou módulos que exigem o .NET Framework diferente da versão 4.7.2|Hybrid Runbook Worker|Áreas restritas da Automação têm o .NET Framework 4.7.2 e não há maneira de atualizá-lo|
|Scripts que exigem elevação|Hybrid Runbook Worker|As áreas restritas não permitir a elevação. Para resolver isso, use um Hybrid Runbook Worker e você pode desativar o UAC e usar `Invoke-Command` quando executar o comando que exige elevação|
|Scripts que exigem acesso ao WMI|Hybrid Runbook Worker|Trabalhos executados em áreas de segurança na nuvem [não tem acesso ao WMI](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Comportamento do runbook

Os runbooks são executados com base na lógica que é definida dentro deles. Se um runbook é interrompido, ele é reiniciado no início. Esse comportamento exige que os runbooks sejam escritos de maneira a permitir a reinicialização em caso de problemas transitórios.

Trabalhos do PowerShell iniciados a partir de um Runbook foi executado no Azure área restrita não podem ser executadas no modo de linguagem completa. Para saber mais sobre os modos de linguagem do PowerShell, consulte [modos de linguagem do PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). Para obter detalhes adicionais sobre como interagir com os trabalhos na automação do Azure, consulte [recuperando o status do trabalho com o PowerShell](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>Criar recursos

Se o seu script cria recursos, você deve verificar se o recurso já existe antes de tentar criá-lo novamente. Um exemplo básico é mostrado no seguinte exemplo:

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>Scripts dependentes de tempo

É preciso ponderar cuidadosamente ao criar runbooks. Como mencionado anteriormente, os runbooks precisam ser criados de maneira que sejam robustos e possam tratar de erros transitórios que podem fazer com que o runbook reinicie ou falhe. Se um runbook falhar, ela será repetida. Se um runbook normalmente é executado dentro de uma restrição de tempo, a lógica para verificar o tempo de execução deve ser implementado no runbook para garantir operações como início, desligadas ou escalar horizontalmente são executados somente durante horários específicos.

> [!NOTE]
> A hora local em que o processo de área restrita do Azure é definida como hora UTC. Cálculos de data e hora em seus runbooks precisam levar isso em consideração.

### <a name="tracking-progress"></a>Acompanhar o progresso

Uma boa prática é criar runbooks para que sejam modulares por natureza. Isso significa estruturar a lógica no runbook de modo que ele possa ser reutilizado e reiniciado facilmente. Acompanhar o progresso em um runbook é uma boa maneira de garantir que a lógica em um runbook execute corretamente se houver problemas. Algumas maneiras possíveis de acompanhar o progresso do runbook é usar uma fonte externa, como contas de armazenamento, um banco de dados ou arquivos compartilhados. Acompanhando o estado externamente, você pode criar lógica em seu runbook para primeiro verificar o estado da última ação que levou o runbook. Em seguida, com base em resultados, ignorar ou continuar tarefas específicas no runbook.

### <a name="prevent-concurrent-jobs"></a>Evitar trabalhos simultâneos

Alguns runbooks podem se comportar de forma estranha se estiverem sendo executados em vários trabalhos ao mesmo tempo. Nesse caso, é importante implementar a lógica para verificar se um runbook já tem um trabalho em execução. Um exemplo básico de como você pode reproduzir esse comportamento é mostrado no seguinte exemplo:

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>Trabalhando com várias assinaturas

Quando se cria runbooks que lidam com várias assinaturas, o runbook precisa usar o [Disable-AzureRmContextAutosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) cmdlet para garantir que seu contexto de autenticação não é recuperado de outro runbook que pode ser em execução na mesma área de segurança. Em seguida, você precisa usar o `-AzureRmContext` parâmetro no seu `AzureRM` cmdlets e passá-lo em seu contexto adequado.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>Tratamento de exceções

Ao criar scripts, é importante ser capaz de lidar com exceções e falhas potenciais de intermitentes. A seguir está algumas maneiras diferentes para lidar com problemas de intermitentes com seus runbooks ou exceções:

#### <a name="erroractionpreference"></a>$ErrorActionPreference

O [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) variável de preferência determina como o PowerShell responde a um erro não fatal. Erros de finalização não são afetados por `$ErrorActionPreference`, eles sempre encerra. Usando `$ErrorActionPreference`, como um erro não fatal normalmente `PathNotFound` do `Get-ChildItem` cmdlet irá parar o runbook de ser concluída. O exemplo a seguir mostra o uso `$ErrorActionPreference`. O último `Write-Output` linha nunca será executado conforme o script será interrompido.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try-Catch Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts do PowerShell para ajudá-lo a lidar com erros de finalização. Usando Try Catch, você pode capturar exceções específicas ou exceções gerais. A instrução Catch deve ser usada para rastrear erros ou para tentar tratar o erro. O exemplo a seguir tenta baixar um arquivo que não existe. Ele captura o `System.Net.WebException` exceção, se houver outra exceção, o último valor é retornado.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Lançar](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de encerramento. Isso pode ser útil ao definir sua própria lógica em um runbook. Se um determinado critério for atendido que deve interromper o script, você pode usar `throw` para interromper o script. A exemplo a seguir mostra um parâmetro de função necessário por meio da máquina `throw`.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>Usar executáveis ou chamar processos

Runbooks executados em áreas restritas do Azure não dão suporte a processos de chamada (por exemplo, um .exe ou subprocess.call). Isso ocorre porque as áreas restritas do Azure são processos compartilhados que são executados em contêineres, que podem não ter acesso a todas as APIs subjacentes. Para cenários em que são necessários softwares de terceiros ou a chamada de subprocessos, é recomendável executar o runbook em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).

### <a name="device-and-application-characteristics"></a>Características de dispositivo e aplicativo

Trabalhos de runbook executados em áreas restritas do Azure não tem acesso a quaisquer características de dispositivo ou aplicativo. A API mais comuns usada para métricas de desempenho de consulta no Windows é o WMI. Algumas dessas métricas comuns são o uso da memória e CPU. No entanto, não importa qual API é usada. Trabalhos em execução na nuvem não tem acesso à implementação Microsoft da Web com base em WBEM (Enterprise Management), que é criado sobre o Common informações de CIM (modelo), que são os padrões do setor para definir as características de dispositivo e aplicativo.

## <a name="job-statuses"></a>Status de trabalho

A tabela a seguir descreve os diferentes status possíveis para um trabalho. O PowerShell tem dois tipos de erros, erros de finalização e sem finalização. Erros de finalização definem o status do runbook como **Falhou**, caso ocorram. Erros sem finalização permitem que o script continue mesmo depois de ocorrerem. Um exemplo de um erro sem finalização é usar o cmdlet `Get-ChildItem` com um caminho que não existe. O PowerShell vê que o caminho não existe, gera um erro e continua até a próxima pasta. Esse erro não definiria o status do runbook como **Falhou** e poderia ser marcado como **Concluído**. Para forçar um runbook a parar se houver um erro sem finalização, você pode usar `-ErrorAction Stop` no cmdlet.

| Status | DESCRIÇÃO |
|:--- |:--- |
| Concluído |Operação concluída com sucesso. |
| Com falha |Para [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md), o runbook falhou ao compilar. Para [runbooks de Script do PowerShell](automation-runbook-types.md), o runbook falhou ao iniciar ou o trabalho sofreu uma exceção. |
| Erro, aguardando recursos |O trabalho falhou porque atingiu o limite de [fração justa](#fair-share) três vezes e iniciou do mesmo ponto de verificação ou desde o início do runbook em cada uma das vezes. |
| Em fila |O trabalho está aguardando recursos de um trabalho do Automation ficar disponível para que ele possa ser iniciado. |
| Iniciando |O trabalho foi atribuído a um trabalhador e o sistema está iniciando-o. |
| Continuando |O sistema está retomando o trabalho depois que ele ter sido suspenso. |
| Executando |O trabalho está em execução. |
| Executando, aguardando recursos |O trabalho foi descarregado, pois atingiu o limite de [fração justa](#fair-share) . Ele será retomado em breve do seu último ponto de verificação. |
| Parado |O trabalho foi interrompido pelo usuário antes de ser concluído. |
| Parando |O sistema está parando o trabalho. |
| Suspenso |O trabalho foi suspenso pelo usuário, pelo sistema ou por um comando no runbook. Se um runbook não tiver um ponto de verificação, ele começará desde o início do runbook. Se ele tiver um ponto de verificação, poderá iniciar novamente e retomar no último ponto de verificação. O runbook só é suspenso pelo sistema quando ocorre uma exceção. Por padrão, ErrorActionPreference é definido como **Continuar** o que significa que o trabalho continua a ser executado no caso de um erro. Se essa variável de preferência for definida como **Parar**, o trabalho será suspenso no caso de um erro. Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md). |
| Suspensão |O sistema está tentando suspender o trabalho por solicitação do usuário. O runbook precisa atingir seu próximo ponto de verificação antes de poder ser suspenso. Se já passou de seu último ponto de verificação, ele será concluído antes de ser suspenso. Aplica-se a somente [runbooks gráficos e de fluxo de trabalho do PowerShell](automation-runbook-types.md). |

## <a name="viewing-job-status-from-the-azure-portal"></a>Exibindo o status do trabalho no portal do Azure

Você pode exibir um status resumido de todos os trabalhos do runbook ou analisar os detalhes de um trabalho específico do runbook no portal do Azure. Você também pode configurar a integração com o seu espaço de trabalho do Log Analytics a fim de encaminhar fluxos de trabalho e status do trabalho do runbook. Para obter mais informações sobre a integração com os logs do Azure Monitor, consulte [encaminhar status do trabalho e fluxos de trabalho de automação para logs do Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

### <a name="automation-runbook-jobs-summary"></a>Resumo dos trabalhos de runbook da Automação

À direita da conta de Automação selecionada, é possível ver um resumo de todos os trabalhos de runbook no bloco **Estatísticas de Trabalho**.

![Bloco Estatísticas de Trabalho](./media/automation-runbook-execution/automation-account-job-status-summary.png)

Esse bloco exibe a contagem e a representação gráfica do status do trabalho para todos os trabalhos executados.

Clicar no bloco apresenta a página **Trabalhos**, que contém uma lista resumida de todos os trabalhos executados. Esta página mostra o status, os horários de início e os horários de término.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos** e filtrar em um runbook específico, status de trabalho ou lista suspensa pelo intervalo de hora dentro do qual pesquisar.

![Filtrar por status do Trabalho](./media/automation-runbook-execution/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na página **Runbooks** da sua conta de Automação e selecionando o bloco **Trabalhos**. Essa ação apresenta a página **Trabalhos** e, nela, é possível clicar no registro do trabalho para exibir seus detalhes e seu resultado.

![Página Trabalhos da conta de automação](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>Resumo do trabalho

Você pode exibir uma lista de todos os trabalhos que foram criados para um determinado runbook e o status mais recente deles. Você pode filtrar essa lista pelo status do trabalho e o intervalo de datas para a última alteração no trabalho. Para exibir as informações detalhadas e o resultado, clique no nome de um trabalho. A exibição detalhada do trabalho inclui os valores para os parâmetros de runbook que foram fornecidos para esse trabalho.

Você pode usar as etapas a seguir para exibir os trabalhos de um runbook.

1. No Portal do Azure, selecione **Automação** e, em seguida, selecione no nome de uma Conta de automação.
2. No hub, selecione **Runbooks** e, em seguida, na página **Runbooks**, selecione um runbook da lista.
3. Na página do runbook selecionado, clique no bloco **Trabalhos**.
4. Clique em um dos trabalhos na lista e, na página de detalhes do trabalho do runbook, você pode exibir seus detalhes e resultados.

## <a name="retrieving-job-status-using-powershell"></a>Recuperando o status do trabalho usando o PowerShell

Você pode usar o [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook com o PowerShell usando [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), ele retornará o trabalho resultante. Use [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para obter a saída de um trabalho.

Os comandos de exemplo a seguir recuperam o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. No caso de que houve uma exceção para um dos registros, a exceção é gravada em vez do valor. Esse comportamento é útil como exceções podem fornecer informações adicionais que não podem ser registradas normalmente durante a saída.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>Obter detalhes do log de atividades

Outros detalhes, como o usuário ou conta que iniciou o runbook, podem ser recuperados do log de atividades da conta de automação. O exemplo do PowerShell a seguir fornece o último usuário a executar o runbook em questão:

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>fração justa

Para compartilhar recursos entre todos os runbooks na nuvem, a automação do Azure temporariamente descarrega ou interrompe qualquer trabalho que foi executado por mais de três horas. Os trabalhos para [runbooks com base em PowerShell](automation-runbook-types.md#powershell-runbooks) e [runbooks Python](automation-runbook-types.md#python-runbooks) são interrompidos e não são reiniciados, e o status do trabalho é mostrado como Parado.

Para tarefas de execução prolongada, é recomendável usar um [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior). Os Hybrid Runbook Workers não são limitados por fração justa e não limitam o tempo de execução de um runbook. Os outros [limites](../azure-subscription-service-limits.md#automation-limits) do trabalho se aplicam a áreas restritas do Azure e ao Hybrid Runbook Workers. Enquanto o Hybrid Runbook Workers não são limitados pelo limite de fração justa de 3 horas, runbooks executados nesses computadores devem ser desenvolvidos para oferecer suporte a comportamentos de reinicialização de problemas de infraestrutura local inesperada.

Outra opção é otimizar o runbook usando runbooks filho. Se o runbook percorre continuamente a mesma função em diversos recursos, como uma operação de banco de dados em vários bancos de dados, você pode mover essa função para um [runbook filho](automation-child-runbooks.md) e chamá-la com o cmdlet [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). Cada um desses runbooks filho é executado em paralelo em processos separados. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir. Você pode usar o [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) cmdlet em seu runbook para verificar o status do trabalho para cada filho, se houver operações que executar após a conclusão do runbook filho.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

