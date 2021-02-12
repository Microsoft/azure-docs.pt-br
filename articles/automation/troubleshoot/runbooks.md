---
title: Solucionar problemas de runbook da Automação do Azure
description: Este artigo informa como solucionar problemas e resolver dúvidas com runbooks de Automação do Azure.
services: automation
ms.subservice: ''
ms.date: 11/03/2020
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: e154284df8eaad798c5cfaf4de69c40601863cf4
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053662"
---
# <a name="troubleshoot-runbook-issues"></a>Solucionar problemas de runbook

 Este artigo descreve problemas de runbook que podem ocorrer e como resolvê-los. Para obter informações gerais, consulte [Execução de runbooks na Automação do Azure](../automation-runbook-execution.md).

## <a name="diagnose-runbook-issues"></a>Diagnosticar problemas de runbook

Ao receber erros durante a execução do runbook na Automação do Azure, você pode usar as seguintes etapas para ajudar a diagnosticar os problemas:

1. Verifique se o script de runbook é executado com êxito no computador local.

    Para obter referências de linguagem de programação e módulos de aprendizado, consulte a [Documentação do PowerShell](/powershell/scripting/overview) ou [Documentação do Python](https://docs.python.org/3/). Executar o script localmente pode descobrir e resolver erros comuns, como:

      * Módulos ausentes
      * Erros de sintaxe
      * Erros de lógica

1. Investigue o runbook [fluxos de erro](../automation-runbook-output-and-messages.md#runbook-output).

    Examine se há mensagens específicas nestes fluxos e compare-as com os erros documentados neste artigo.

1. Verifique se seus nós e o workspace da Automação têm os módulos necessários.

    Se o runbook importar módulos, verifique se eles estão disponíveis para sua conta de Automação usando as etapas em [Importar módulos](../shared-resources/modules.md#import-modules). Atualize seus módulos do PowerShell para a versão mais recente seguindo as instruções em [Atualizar módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md). Para obter mais informações sobre a solução de problemas, confira [Solucionar problemas de módulos](shared-resources.md#modules).

1. Se o runbook for suspenso ou falhar inesperadamente:

    * [Renovar o certificado](../manage-runas-account.md#cert-renewal) se a conta Executar como expirou.
    * [Renove o de webhook](../automation-webhooks.md#renew-a-webhook) se você estiver tentando usar um webhook expirado para iniciar o runbook.
    * [Verifique os status do trabalho](../automation-runbook-execution.md#job-statuses) para determinar os status atuais do runbook e algumas das possíveis causas do problema.
    * [Adicione outra saída](../automation-runbook-output-and-messages.md#working-with-message-streams) ao runbook para identificar o que acontece antes da suspensão do runbook.
    * [Lidar com exceções](../automation-runbook-execution.md#exceptions) lançadas por seu trabalho.

1. Execute esta etapa se o trabalho de runbook ou o ambiente em Hybrid Runbook Worker não responder.

    Se você estiver executando seus runbooks em um Hybrid Runbook Worker em vez de na Automação do Azure, talvez seja necessário [solucionar problemas do próprio trabalhador híbrido](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Cenário: Falha no runbook com um erro Sem permissão ou Proibido 403

### <a name="issue"></a>Problema

Falha no runbook com um erro Sem permissão ou Proibido 403 ou equivalente.

### <a name="cause"></a>Causa

As contas Executar Como não podem ter as mesmas permissões da sua conta atual de Automação, em relação aos recursos do Azure. 

### <a name="resolution"></a>Resolução

Verifique se a conta Executar Como tem [permissões para acessar recursos](../../role-based-access-control/role-assignments-portal.md) usados no script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Cenário: Falha ao entrar na conta do Azure

### <a name="issue"></a>Problema

Você recebe um dos seguintes erros ao trabalhar com o cmdlet `Connect-AzAccount`:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Causa

Esses erros ocorrem se o ativo da credencial é inválido. Eles também podem ocorrer se o nome de usuário e a senha usados para configurar o ativo da credencial de Automação são inválidos.

### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga estas etapas:

1. Não use caracteres especiais. Esses caracteres incluem o caractere `\@` no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.
1. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial da Automação do Azure no editor do ISE do PowerShell local. Execute os seguintes cmdlets no ISE do PowerShell.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Se a sua autenticação falhar localmente, você não configurou as credenciais do Azure Active Directory (Azure AD) corretamente. Para que a conta do Azure AD seja configurada corretamente, consulte o artigo [autenticar no Azure usando Azure Active Directory](../automation-use-azure-ad.md).

1. Se o erro parecer ser transitório, tente adicionar lógica de nova tentativa à sua rotina de autenticação para tornar a autenticação mais robusta.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Cenário: Executar Login-AzureRMAccount para fazer logon

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao executar um runbook:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você não está usando uma conta Executar Como ou quando a conta Executar Como expirou. Para obter mais informações, consulte [visão geral das contas Executar como da automação do Azure](../automation-security-overview.md#run-as-accounts).

Esse erro tem duas causas principais:

* Há versões diferentes do módulo AzureRM ou Az.
* Você está tentando acessar recursos em uma assinatura separada.

### <a name="resolution"></a>Resolução

Se você receber esse erro depois de atualizar um módulo AzureRM ou AZ, atualize todos os seus módulos para a mesma versão.

Se você estiver tentando acessar recursos em outra assinatura, siga estas etapas para configurar permissões:

1. Vá para a conta Executar Como de Automação e copie a **ID do aplicativo** e **Impressão digital**.

    ![Copiar a ID do Aplicativo e a Impressão digital](../media/troubleshoot-runbooks/collect-app-id.png)

1. Acesse o **Controle de acesso** da assinatura em que a conta da Automação do Azure *não* está hospedada e adicione uma nova atribuição de função.

    ![Controle de acesso](../media/troubleshoot-runbooks/access-control.png)

1. Adicionar a **ID do aplicativo** coletada anteriormente. Selecionar permissões do **Colaborador**.

    ![Adicionar atribuição de função](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Copiar o nome da assinatura.

1. Agora você pode usar o seguinte código de runbook para testar as permissões de sua conta de Automação para a outra assinatura. Substitua `"\<CertificateThumbprint\>"` pelo valor copiado na etapa 1. Substitua `"\<SubscriptionName\>"` pelo valor copiado na etapa 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Cenário: Não é possível encontrar a assinatura do Azure

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com os cmdlets `Select-AzureSubscription`, `Select-AzureRMSubscription` ou `Select-AzSubscription`:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Erro

Esse erro pode acontecer se:

* O nome da assinatura não é válido.
* O usuário do Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.
* O cmdlet não está disponível.

### <a name="resolution"></a>Resolução

Siga estas etapas para determinar se você se autenticou no Azure e se tem acesso à assinatura que está tentando selecionar:

1. Para ter certeza de que o seu script funciona de maneira independente, teste-o fora da Automação do Azure.
1. Verifique se o script executa o cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) antes de executar o cmdlet `Select-*`.
1. Adicione `Disable-AzContextAutosave –Scope Process` ao início do seu runbook. Esse cmdlet garante que todas as credenciais sejam aplicadas apenas à execução do runbook atual.
1. Se essa mensagem de erro ainda for exibida, modifique o código adicionando o parâmetro `AzContext` para `Connect-AzAccount` e depois execute o código.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Cenário: Os runbooks falham ao lidar com várias assinaturas

### <a name="issue"></a>Problema

Ao executar runbooks, o runbook falha ao gerenciar recursos do Azure.

### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

### <a name="resolution"></a>Resolução

O contexto de assinatura pode ser perdido quando um runbook invoca vários runbooks. Para garantir que o contexto de assinatura seja passado para os runbooks, faça com que o runbook do cliente passe o contexto para o cmdlet `Start-AzureRmAutomationRunbook` no parâmetro `AzureRmContext`. Use o cmdlet `Disable-AzureRmContextAutosave` com o parâmetro `Scope` definido como `Process` para garantir que as credenciais especificadas sejam usadas somente para o runbook atual. Para saber mais, confira [Assinaturas](../automation-runbook-execution.md#subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Cenário: A Autenticação do Azure falhou porque a autenticação multifator está habilitada

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com seu nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Azure Active Directory para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar.

### <a name="resolution"></a>Resolução

Para usar uma conta Executar como clássica com os cmdlets do modelo de implantação clássico do Azure, consulte [criar uma conta Executar como clássica para gerenciar os serviços do Azure](../automation-create-standalone-account.md#create-a-classic-run-as-account). Para usar uma entidade de serviço com os cmdlets do Azure Resource Manager, veja [Criando entidade de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [Autenticando uma entidade de serviço com o Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Cenário: O runbook falha com a mensagem de erro "Uma tarefa foi cancelada"

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido atualizando os módulos do Azure para a versão mais recente:

1. Na sua conta de Automação, selecione **Módulos** e depois **Atualizar módulos do Azure**.
1. A atualização leva aproximadamente 15 minutos. Após a conclusão, execute novamente o runbook que falhou.

Para saber mais sobre como atualizar seus módulos, consulte [Atualizar os módulos do Azure na Automação do Azure](../automation-update-azure-modules.md).

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Cenário: Termo não reconhecido como o nome de um cmdlet, uma função ou um script

### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer pelos seguintes motivos:

* O módulo que contém o cmdlet não é importado para a conta de Automação.
* O módulo que contém o cmdlet é importado, mas está desatualizado.

### <a name="resolution"></a>Resolução

Execute uma das seguintes tarefas para resolver esse erro:

* Para um módulo do Azure, consulte [Como atualizar módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md) para saber como atualizar os módulos em sua conta de Automação.
* Para um módulo não Azure, verifique que o módulo seja importado para sua conta de Automação.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Cenário: Falha do cmdlet no runbook do PnP PowerShell na Automação do Azure

### <a name="issue"></a>Problema

Quando um runbook grava um objeto gerado por PnP PowerShell na saída de Automação do Azure diretamente, a saída do cmdlet não pode transmitir de volta para a Automação.

### <a name="cause"></a>Causa

Esse problema geralmente ocorre quando a Automação do Azure processa runbooks que invocam cmdlets do PnP PowerShell, por exemplo, `add-pnplistitem`, sem capturar os objetos de retorno.

### <a name="resolution"></a>Resolução

Edite seus scripts para atribuir quaisquer valores de retorno às variáveis para que os cmdlets não tentem gravar objetos inteiros na saída padrão. Um script pode redirecionar o fluxo de saída para um cmdlet, como mostrado aqui.

```azurecli
  $null = add-pnplistitem
```

Se o script analisar a saída do cmdlet, o script precisa armazenar a saída em uma variável e manipular a variável em vez de simplesmente transmitir a saída.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. É possível que o módulo que contém o cmdlet está ausente da conta, que há um conflito de nome com um nome de runbook ou que o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

### <a name="resolution"></a>Resolução

Use qualquer uma das seguintes soluções para corrigir o problema:

* Verifique se você inseriu o nome do cmdlet corretamente.
* Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet está presente, abra um runbook no modo de edição e a pesquise o cmdlet que você deseja localizar na biblioteca, ou execute `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há conflitos de nome com outros cmdlets ou runbooks, adicione o cmdlet à tela. Verifique se você está usando um conjunto de parâmetros válido em seu runbook.
* Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, resolva o problema usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar `ModuleName\CmdletName`.
* Se você estiver executando o runbook localmente em um grupo de trabalhadores híbridos, verifique se o módulo e cmdlet estão instalados no computador que hospeda o trabalhador híbrido.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Cenário: Referência de objeto incorreta na chamada para Add-AzAccount

### <a name="issue"></a>Problema

Você recebe esse erro quando trabalha com `Add-AzAccount`, que é um alias para o cmdlet `Connect-AzAccount`:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer se o runbook não executar as etapas adequadas antes de chamar `Add-AzAccount` para adicionar a conta de Automação. Um exemplo de uma das etapas necessárias é entrar com uma conta Executar Como. Para as operações corretas a serem usadas em seu runbook, consulte [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Cenário: Referência de objeto não definida para uma instância de objeto

### <a name="issue"></a>Problema

Você recebe o seguinte erro ao chamar um runbook filho com o parâmetro `Wait`, e o Fluxo de saída contém um objeto:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Causa

Se o fluxo contiver objetos, `Start-AzAutomationRunbook` não tratará o Fluxo de saída corretamente.

### <a name="resolution"></a>Resolução

Implemente uma lógica de sondagem e use o cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar a saída. Um exemplo dessa lógica é definido aqui:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Cenário: O Runbook falha devido a objeto desserializado

### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Causa

Se o seu runbook for um Fluxo de Trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para manter seu estado de runbook se o fluxo de trabalho estiver suspenso.

### <a name="resolution"></a>Resolução

Use qualquer uma das seguintes soluções para corrigir esse problema:

* Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em uma atividade `InlineScript`.
* Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
* Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Cenário: 400 status de Solicitação inadequada ao chamar um webhook

### <a name="issue"></a>Problema

Ao tentar chamar um webhook para um runbook de Automação do Azure, você recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou. 

### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você pode habilitá-lo novamente por meio do portal do Azure. Se o webhook tiver expirado, você deverá excluí-lo e recriá-lo. Só é possível [renovar um webhook](../automation-webhooks.md#renew-a-webhook) se ele ainda não expirou. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Cenário: 429: A taxa de solicitação no momento é muito grande

### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tenha muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Resolução

Para resolver esse erro, execute um destes procedimentos:

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.
* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode definir o valor do parâmetro `Stream` para o cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) para recuperar somente os Fluxos de saída. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Cenário: O trabalho de runbook falhou porque excedeu a cota alocada

### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas estão testando um trabalho, iniciando um trabalho no portal, executando um trabalho usando webhooks ou agendando um trabalho para execução usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços para Automação, confira os [Preços de automação](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Resolução

Se quiser usar mais de 500 minutos de processamento por mês, altere sua assinatura da Camada gratuita para a camada Básica:

1. Entre em sua assinatura do Azure.
1. Selecione a conta de Automação a ser atualizada.
1. Selecione **Configurações** e, em seguida, selecione **Preço**.
1. Clique em **Habilitar** na parte inferior da página para atualizar sua conta para a camada Básica.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Cenário: fluxo de saída do runbook maior que 1 MB

### <a name="issue"></a>Problema

O runbook em execução na área restrita do Azure falha com o seguinte erro:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Causa

Esse erro ocorre porque o runbook tentou gravar muitos dados de exceção no fluxo de saída.

### <a name="resolution"></a>Resolução

Há um limite de 1 MB no fluxo de saída do trabalho. Verifique se seu runbook inclui chamadas para um executável ou subprocesso usando blocos `try` e `catch`. Se as operações lançarem uma exceção, faça com que o código grave a mensagem da exceção em uma variável de Automação. Essa técnica impede que a mensagem seja gravada no fluxo de saída do trabalho. Para Hybrid Runbook Worker trabalhos executados, o fluxo de saída truncado para 1 MB é exibido sem nenhuma mensagem de erro.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Cenário: O início do trabalho de runbook foi tentado três vezes, mas todas as vezes o início falhou

### <a name="issue"></a>Problema

Seu runbook falha com o seguinte erro:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

* **Limite de memória.** Um trabalho pode falhar se estiver usando mais de 400 MB de memória. Os limites documentados sobre a memória alocada em uma área restrita são encontrados em [Limites do serviço de automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* **Soquetes de rede.** As áreas restritas do Azure são limitadas a 1.000 soquetes de rede simultâneos. Para saber mais, confira [Limites do serviço de Automação](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Módulo incompatível.** As dependências de módulo podem não estar corretas. Nesse caso, o runbook normalmente retorna uma mensagem `Command not found` ou `Cannot bind parameter`.

* **Sem autenticação com o Active Directory para área restrita.** Seu runbook tentou chamar um executável ou subprocesso que é executado em uma área restrita do Azure. Não há suporte para a configuração de runbooks para autenticar com o Azure Active Directory usando a ADAL (Biblioteca de Autenticação do Azure Active Directory).

### <a name="resolution"></a>Resolução

* **Limite de memória, soquetes de rede.** Métodos sugeridos de trabalhar nos limites de memória são dividir a carga de trabalho entre vários runbooks, processar menos dados na memória, evitar a gravação de saída desnecessária de seus runbooks e considerar quantos pontos de verificação são gravados em seus runbooks de fluxo de trabalho do PowerShell. Use o método de limpeza, como `$myVar.clear`, para limpar variáveis e use `[GC]::Collect` para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o runtime.

* **Módulo incompatível.** Atualize os módulos do Azure seguindo as etapas em [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).

* **Sem autenticação com o Active Directory para área restrita.** Ao autenticar no Azure AD com um runbook, verifique se o módulo do Azure Active Directory está disponível na sua conta de Automação. Certifique-se de conceder à conta Executar Como as permissões necessárias para executar as tarefas que o runbook automatiza.

  Se o runbook não puder chamar um executável ou subprocesso em execução em uma área restrita do Azure, use o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Trabalhadores híbridos não têm os limites de memória e rede que as áreas restritas do Azure têm.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Cenário: O trabalho do PowerShell falha com a mensagem de erro "Não é possível invocar o método"

### <a name="issue"></a>Problema

Você recebe a seguinte mensagem de erro ao iniciar um trabalho do PowerShell em um runbook que é executado no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Causa

Esse erro pode indicar que os runbooks executados em uma área restrita do Azure não podem ser executados no [Modo de Linguagem Completa](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Em vez de usar [Start-Job](/powershell/module/microsoft.powershell.core/start-job), use [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar o runbook.
* Tente executar o runbook em um Hybrid Runbook Worker.

Para saber mais sobre esse comportamento e outros comportamentos dos runbooks de Automação do Azure, confira [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Cenário: Falha de conclusão de um runbook de execução longa

### <a name="issue"></a>Problema

Seu runbook é exibido em um estado Parado após a execução de 3 horas. Você também pode ver este erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Esse comportamento existe, por natureza, nas áreas restritas do Azure devido ao monitoramento de [compartilhamento justo](../automation-runbook-execution.md#fair-share) dos processos na Automação do Azure. Se um processo for executado por mais de três horas, o compartilhamento justo interrompe automaticamente um runbook. O status de um runbook que ultrapassa o limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos com um status Parado. Os runbooks do Fluxo de trabalho do PowerShell estão definidos como Falha.

### <a name="cause"></a>Causa

O runbook ultrapassou o limite de três horas permitido pelo compartilhamento justo em uma área restrita do Azure.

### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Os trabalhadores híbridos não têm limite de runbook de compartilhamento justo de três horas que as áreas restritas do Azure têm. Os runbooks executados em Hybrid Runbook Workers devem ser desenvolvidos para dar suporte a comportamentos de reinicialização, caso haja problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook criando [runbooks filhos](../automation-child-runbooks.md). Se o seu runbook faz loop da mesma função em vários recursos, por exemplo, uma operação de banco de dados em vários bancos de dados, é possível mover essa função para um runbook filho. Cada um desses runbooks filhos é executado em paralelo em processos separados. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). Esse cmdlet permite que você inicie um runbook e passe parâmetros para o runbook.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Se há operações que precisam ser executadas após a conclusão do runbook filho, este cmdlet permite verificar o status de trabalho para cada filho.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Cenário: Erro em fluxos de trabalho sobre o método get_SerializationSettings

### <a name="issue"></a>Problema

Você verá o seguinte erro em seus fluxos de trabalho para um runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Causa

Esse erro é provavelmente causado pelo uso de uma migração incompleta do AzureRM para módulos AZ em seu runbook. Essa situação pode fazer com que a Automação do Azure inicie um trabalho de runbook usando apenas módulos AzureRM e, em seguida, inicie outro trabalho usando apenas módulos Az, o que leva a uma falha de área restrita.

### <a name="resolution"></a>Resolução

Não recomendamos o uso de cmdlets Az e AzureRM no mesmo runbook. Para saber mais sobre o uso correto dos módulos, consulte [Migrar para módulos Az](../shared-resources/modules.md#migrate-to-az-modules).

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Cenário: Acesso negado ao usar a área restrita do Azure para runbook ou aplicativo

### <a name="issue"></a>Problema

Quando o runbook ou aplicativo tenta ser executado em uma área restrita do Azure, o ambiente nega acesso.

### <a name="cause"></a>Causa

Esse problema pode ocorrer porque as áreas restritas do Azure impedem o acesso a todos os servidores COM fora do processo. Por exemplo, um aplicativo ou runbook em área restrita não pode fazer chamada em Instrumentação de Gerenciamento do Windows (WMI) ou no serviço Windows Installer (msiserver.exe).

### <a name="resolution"></a>Resolução

Para obter detalhes sobre o uso de áreas restritas do Azure, consulte [Ambiente de execução de runbook](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Cenário: Código de status Proibido Inválido ao usar Key Vault dentro de um runbook

### <a name="issue"></a>Problema

Ao tentar acessar Azure Key Vault por meio de um runbook de Automação do Azure, você obtém o seguinte erro:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Causa

As possíveis causas desse problema são:

* Não está usando uma conta Executar Como.
* Permissões insuficientes

### <a name="resolution"></a>Resolução

#### <a name="not-using-a-run-as-account"></a>Não usar uma conta Executar Como

Siga [Etapa 5 – Adicionar autenticação para gerenciar recursos do Azure](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) para garantir que você esteja usando uma conta Executar Como para acessar Key Vault.

#### <a name="insufficient-permissions"></a>Permissões insuficientes

[Adicione permissões ao Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) para garantir que sua conta Executar Como tenha permissões suficientes para acessar Key Vault.

## <a name="recommended-documents"></a>Documentos recomendados

* [Execução de Runbook na Automação do Azure](../automation-runbook-execution.md)
* [Como iniciar um Runbook na Automação do Azure](../start-runbooks.md)

## <a name="next-steps"></a>Próximas etapas

Se você não encontrar seu problema aqui ou não conseguir resolvê-lo, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial do Microsoft Azure para melhorar a experiência do cliente. O Suporte do Azure conecta você com a comunidade do Azure para obter as respostas, suporte e falar com os especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
