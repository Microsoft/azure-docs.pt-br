---
title: Gerenciar runbooks na Automação do Azure
description: Este artigo informa como gerenciar os runbooks na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/24/2021
ms.topic: conceptual
ms.openlocfilehash: 1e8df39cc836890526b0dad1885f31dc15eaa837
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629199"
---
# <a name="manage-runbooks-in-azure-automation"></a>Gerenciar runbooks na Automação do Azure

Você pode adicionar um runbook à Automação do Azure criando um novo ou importando um existente de um arquivo ou da [Galeria de Runbooks](automation-runbook-gallery.md). Este artigo fornece informações para gerenciar um runbook importado de um arquivo. Você pode encontrar todos os detalhes sobre o acesso a runbooks e a módulos da comunidade em [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Criar um runbook

Crie um novo runbook na Automação do Azure usando o portal do Azure ou o Windows PowerShell. Quando o runbook tiver sido criado, você pode editá-lo usando informações contidas em:

* [Editar runbooks de texto na Automação do Azure](automation-edit-textual-runbook.md)
* [Aprender sobre os principais conceitos de Fluxo de Trabalho do Windows PowerShell para runbooks de Automação](automation-powershell-workflow.md)
* [Gerenciar pacotes do Python 2 no Automação do Azure](python-packages.md)
* [Gerenciar pacotes python 3 (versão prévia) na automação do Azure](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Criar um runbook no portal do Azure

1. No portal do Azure, abra sua conta da Automação.
2. No hub, selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique em **Criar um runbook**.
4. Insira um nome para o runbook e selecione seu [tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode conter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### <a name="create-a-runbook-with-powershell"></a>Criar um runbook com o PowerShell

Use o cmdlet [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook) para criar um runbook vazio. Use o parâmetro `Type` para especificar um dos tipos de runbook definidos para `New-AzAutomationRunbook`.

O exemplo a seguir mostra como criar um novo runbook vazio.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importar um runbook

Você pode importar um script do PowerShell ou de fluxo de trabalho do PowerShell (**. ps1**), um runbook gráfico (**. graphrunbook**) ou um script Python 2 ou Python 3 (**. py**) para criar seu próprio runbook. Você precisa especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração os aspectos a seguir.

* Você pode importar um arquivo **.ps1** que não contém um fluxo de trabalho em um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou em um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se você importá-lo para um runbook do Fluxo de Trabalho do PowerShell, ele será convertido em um fluxo de trabalho. Nesse caso, os comentários são incluídos no runbook para descrever as alterações feitas.

* Você pode importar apenas um arquivo **.ps1** que contenha um Fluxo de Trabalho do PowerShell em um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o arquivo contiver vários fluxos de trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.

* Não importe um arquivo **.ps1** que contenha um fluxo de trabalho do PowerShell para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks), pois o mecanismo de script do PowerShell não pode reconhecê-lo.

* Importe somente um arquivo **.graphrunbook** para um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importar um runbook do portal do Azure

Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.

> [!NOTE]
> Você só poderá importar um arquivo **.ps1** para um runbook do Fluxo de Trabalho do PowerShell usando o portal.

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Clique em **Importar um runbook**.
4. Clique em **Arquivo de runbook** para selecionar o arquivo para importar.
5. Se o campo **Nome** estiver ativado, você poderá alterar o nome do runbook. O nome deve começar com uma letra e pode conter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterá-lo depois levar as restrições aplicáveis em consideração.
7. Clique em **Criar**. O novo runbook é exibido na lista de runbooks da conta de Automação.
8. Será necessário [publicar o runbook](#publish-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico, você pode convertê-lo em outro tipo. No entanto, não é possível converter esses runbooks gráficos em runbooks textuais.

### <a name="import-a-runbook-with-windows-powershell"></a>Importar um runbook com o Windows PowerShell

Use o cmdlet [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook) para importar um arquivo de script como um rascunho do runbook. Se o runbook já existir, a importação falhará a menos que você use o parâmetro `Force` com o cmdlet.

O exemplo a seguir mostra como importar um arquivo de script para um runbook.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Lidar com recursos

Se o seu runbook criar um [recurso](automation-runbook-execution.md#resources), o script deverá verificar se o recurso já existe antes de tentar criá-lo. Aqui está um exemplo básico.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Recuperar detalhes do log de atividades

Você pode recuperar detalhes do runbook, como a pessoa ou conta que iniciou um runbook, no [Log de atividades](automation-runbook-execution.md#activity-logging) da conta da Automação. O exemplo a seguir do PowerShell fornece o último usuário a executar o runbook especificado.

```powershell-interactive
$SubID = '00000000-0000-0000-0000-000000000000'
$AutoRgName = 'MyResourceGroup'
$aaName = 'MyAutomationAccount'
$RunbookName = 'MyRunbook'
$StartTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $AutoRgName
    StartTime         = $StartTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $RunbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $AutoRgName
            AutomationAccountName = $aaName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $RunbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Controlar o progresso

É uma boa prática criar seus runbooks de natureza modular, com lógica que pode ser reutilizada e reiniciada facilmente. O acompanhamento do progresso em um runbook garante que a lógica do runbook seja executada corretamente se houver problemas.

Você pode acompanhar o progresso do runbook usando uma fonte externa, como contas de armazenamento, um banco de dados ou arquivos compartilhados. Crie lógica no seu runbook para verificar primeiro o estado da última ação executada. Em seguida, com base nos resultados da verificação, a lógica pode ignorar ou continuar tarefas específicas no runbook.

## <a name="prevent-concurrent-jobs"></a>Evitar trabalhos simultâneos

Alguns runbooks se comportam de forma estranha se estiverem sendo executados em vários trabalhos ao mesmo tempo. Nesse caso, é importante que um runbook implemente a lógica para determinar se já existe um trabalho em execução. Aqui está um exemplo básico.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams
$AzureContext = Get-AzSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzContext $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook [$runbookName] is already running"
    exit 1
} else {
    # Insert Your code here
}
```

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Lidar com erros transitórios em um script dependente de tempo

Seus runbooks devem ser robustos e capazes de lidar com [erros](automation-runbook-execution.md#errors), incluindo erros transitórios que podem causar a reinicialização ou falha. Se um runbook falhar, a automação do Azure tentará novamente.

Se seu runbook normalmente for executado dentro de uma restrição de tempo, faça com que o script implemente a lógica para verificar o tempo de execução. Essa verificação garante a execução de operações como inicialização, desligamento ou expansão somente durante horários específicos.

> [!NOTE]
> A hora local no processo de área restrita do Azure é definida como UTC. Os cálculos de data e hora em seus runbooks devem levar esse fato em consideração.

## <a name="work-with-multiple-subscriptions"></a>Como trabalhar com várias assinaturas

Seu runbook deve ser capaz de trabalhar com [assinaturas](automation-runbook-execution.md#subscriptions). Por exemplo, para lidar com várias assinaturas, o runbook usa o cmdlet [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave). Esse cmdlet garante que o contexto de autenticação não seja recuperado de outro runbook em execução na mesma área restrita. O runbook também usa o `Get-AzContext` cmdlet para recuperar o contexto da sessão atual e atribuí-lo à variável `$AzureContext` .

```powershell
Disable-AzContextAutosave -Scope Process

$connection = Get-AutomationConnection -Name AzureRunAsConnection
$cnParams = @{
    ServicePrincipal      = $true
    Tenant                = $connection.TenantId
    ApplicationId         = $connection.ApplicationId
    CertificateThumbprint = $connection.CertificateThumbprint
}
Connect-AzAccount @cnParams

$ChildRunbookName = 'ChildRunbookDemo'
$aaName = 'MyAutomationAccount'
$rgName = 'MyResourceGroup'

$startParams = @{
    ResourceGroupName     = $rgName
    AutomationAccountName = $aaName
    Name                  = $ChildRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

## <a name="work-with-a-custom-script"></a>Trabalhar com um script personalizado

> [!NOTE]
> Normalmente, você não pode executar scripts e runbooks personalizados no host com um agente do Log Analytics instalado.

Para usar um script personalizado:

1. Crie uma conta da Automação e obtenha uma [Função de colaborador](automation-role-based-access-control.md).
2. [Vincule a conta ao workspace do Azure](../security-center/security-center-enable-data-collection.md).
3. Habilite o [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), [Gerenciamento de Atualizações](./update-management/overview.md) ou outro recurso da Automação. 
4. Se estiver em um computador Linux, você precisa de altas permissões. Faça logon para [desativar as verificações de assinatura](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testar um runbook

Quando você testa um runbook, a [Versão de rascunho](#publish-a-runbook) é executada e as ações que ela realiza são concluídas. Nenhum histórico de trabalho é criado, mas os fluxos de [saída](automation-runbook-output-and-messages.md#use-the-output-stream) e [aviso e erro](automation-runbook-output-and-messages.md#working-with-message-streams) são exibidos no painel de saída de Teste. As mensagens para o [fluxo detalhado](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) serão exibidas no painel Saída somente se a variável [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) for definida para `Continue`.

Mesmo que a versão de rascunho esteja em execução, o runbook ainda será executado normalmente e executará qualquer ação nos recursos do ambiente. Por esse motivo, você deve testar apenas runbooks nos recursos de não produção.

O procedimento para testar cada [tipo de runbook](automation-runbook-types.md) é o mesmo. Não há nenhuma diferença entre testar o editor de texto e o editor gráfico no portal do Azure.

1. Abra a versão de Rascunho do runbook no [editor de texto](automation-edit-textual-runbook.md) ou no [editor gráfico](automation-graphical-authoring-intro.md).
1. Clique em **Testar** para abrir a página de Teste.
1. Se o runbook tiver parâmetros, eles serão listados no painel à esquerda, onde você pode fornecer valores a serem usados para o teste.
1. Se você quiser executar o teste em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), altere **Configurações da Execução** para **Hybrid Worker** e selecione o nome do grupo de destino.  Caso contrário, mantenha o padrão **Azure** para executar o teste na nuvem.
1. Clique em **Iniciar** para iniciar o teste.
1. Você pode usar os botões no painel Saída para interromper ou suspender um [Fluxo de trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) ou um runbook [gráfico](automation-runbook-types.md#graphical-runbooks) enquanto está sendo testado. Quando você suspende o runbook, ele conclui a atividade atual antes de ser suspenso. Após o runbook ser suspenso, você pode interrompê-lo ou reiniciá-lo.
1. Inspecione a saída do runbook no painel Saída.

## <a name="publish-a-runbook"></a>Publicar um runbook

Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo. Cada runbook na Automação do Azure tem uma versão de Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho deve ser disponibilizada, você a publica, substituindo a versão Publicada atual pela versão de Rascunho.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicar um runbook no portal do Azure

1. Abra o runbook no portal do Azure.
2. Clique em **Editar**.
3. Clique em **Publicar** e em **Sim** ne resposta à mensagem de verificação.

### <a name="publish-a-runbook-using-powershell"></a>Publicar um runbook usando o PowerShell

Use o cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook) para publicar seu runbook. 

```azurepowershell-interactive
$aaName = "MyAutomationAccount"
$RunbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $aaName
    ResourceGroupName     = $rgName
    Name                  = $RunbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Agendar um runbook no portal do Azure

Quando o runbook tiver sido publicado, você poderá agendá-lo para a operação:

1. Abra o runbook no portal do Azure.
2. Selecione **Agendamentos** em **Recursos**.
3. Selecione **Adicionar um agendamento**.
4. No painel Agendar Runbook, selecione **Vincular um agendamento ao runbook**.
5. Escolha **Criar um novo agendamento** no Painel de Agendamento.
6. Insira um nome, uma descrição e outros parâmetros no Painel de Novo Agendamento.
7. Depois que o agendamento for criado, realce-o e clique em **OK**. Agora ele deve estar vinculado ao seu runbook.
8. Procure um email na sua caixa de correio para notificá-lo sobre o status do runbook.

## <a name="obtain-job-statuses"></a>Obter status de trabalho

### <a name="view-statuses-in-the-azure-portal"></a>Exibir status no portal do Azure

Detalhes do tratamento de trabalhos na Automação do Azure são fornecidos em [Trabalhos](automation-runbook-execution.md#jobs). Quando estiver pronto para ver seus trabalhos de runbook, use o portal do Azure e acesse sua conta da Automação. À direita, você pode ver um resumo de todos os trabalhos do runbook em **Estatísticas de Trabalho**.

![Bloco Estatísticas de Trabalho](./media/manage-runbooks/automation-account-job-status-summary.png)

O resumo exibe a contagem e a representação gráfica do status do trabalho para todos os trabalhos executados.

Clicar no bloco apresenta a página Trabalhos, que contém uma lista resumida de todos os trabalhos executados. Esta página mostra o status, o nome do runbook, a hora de início e o tempo de conclusão de cada trabalho.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Captura de tela da página trabalhos.":::

Você pode filtrar a lista de trabalhos selecionando **Filtrar trabalhos**. Filtre um runbook específico, status do trabalho ou uma opção na lista suspensa e forneça o intervalo de tempo para a pesquisa.

![Filtrar por status do trabalho](./media/manage-runbooks/automation-account-jobs-filter.png)

Como alternativa, você pode exibir detalhes de resumo do trabalho para um runbook específico selecionando esse runbook na página Runbooks da sua conta da Automação e selecionando **Trabalhos**. Essa ação apresenta a página Trabalhos. A partir daqui, você pode clicar em um registro de trabalho para exibir seus detalhes e saída.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Captura de tela da página trabalhos com o botão erros realçado.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Recuperar status de trabalho usando o PowerShell

Use o cmdlet [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) para recuperar os trabalhos criados para um runbook e os detalhes de um trabalho específico. Se você iniciar um runbook usando `Start-AzAutomationRunbook`, ele retornará o trabalho resultante. Use [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar a saída do trabalho.

O exemplo a seguir obtém o último trabalho para um exemplo de runbook e exibe seu status, os valores fornecidos para os parâmetros de runbook e a saída do trabalho.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate –Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

O exemplo a seguir recupera a saída de um trabalho específico e retorna cada registro. Se houver uma [exceção](automation-runbook-execution.md#exceptions) para um dos registros, o script gravará a exceção em vez do valor. Esse comportamento é útil, pois as exceções podem fornecer informações adicionais que podem não ser registradas normalmente durante a saída.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre o gerenciamento de runbooks, confira [Execução de runbooks na Automação do Azure](automation-runbook-execution.md).
* Para preparar um runbook do PowerShell, confira [Editar runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md).
* Para solucionar problemas com a execução do runbook, confira [Solução de problemas de runbook](troubleshoot/runbooks.md).
