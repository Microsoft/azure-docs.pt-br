---
title: Solucionar erros com os livros de execução de automação do Azure
description: Saiba como solucionar problemas com runbooks de automação do Azure
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 5a9bd554ec3b7ae4f84d6a0a4726af7ffea89e89
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477466"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Solucionar problemas de erros com runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erros de autenticação ao trabalhar com runbooks da Automação do Azure

### <a name="sign-in-failed"></a>Cenário: Entrar na conta do Azure falha

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com o `Add-AzureAccount` ou `Connect-AzureRmAccount` cmdlets.
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Causa

Esse erro ocorre se o nome do ativo da credencial é inválido. Esse erro também pode ocorrer se o nome de usuário e a senha usados para configurar o ativo da credencial de Automação são inválidos.

#### <a name="resolution"></a>Resolução

Para determinar o que está errado, siga os seguintes passos:  

1. Não use caracteres especiais. Esses caracteres incluem a **\@** no nome do ativo da credencial de Automação que você está usando para se conectar ao Azure.  
2. Verifique se você pode usar o nome de usuário e a senha que estão armazenados na credencial da Automação do Azure no editor do ISE do PowerShell local. Você pode verificar se o nome de usuário e a senha estão corretos executando os seguintes cmdlets no PowerShell ISE:  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Se a sua autenticação falhar localmente, isso significa que você não configurou as credenciais do Azure Active Directory corretamente. Confira a postagem de blog [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) (Autenticação no Azure usando o Azure Active Directory) para configurar corretamente a conta do Azure Active Directory.  

4. Se parecer um erro temporário, tente adicionar lógica de nova tentativa à sua rotina de autenticação para tornar a autenticação mais robusta.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Cenário: Não é possível encontrar a assinatura do Azure

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao trabalhar com os cmdlets `Select-AzureSubscription` ou `Select-AzureRmSubscription`:

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Erro

Esse erro pode ocorrer se:

* O nome da assinatura é inválido

* O usuário do Azure Active Directory que está tentando obter os detalhes da assinatura não está configurado como um administrador da assinatura.

#### <a name="resolution"></a>Resolução

Siga estas etapas para determinar se você se autenticou no Azure e tem acesso à assinatura que está tentando selecionar:  

1. Para ter certeza de que o seu script funciona de maneira independente, teste-o fora da Automação do Azure.
2. Antes de executar o cmdlet `Select-AzureSubscription`, verifique se você executou o cmdlet `Add-AzureAccount`. 
3. Adicione `Disable-AzureRmContextAutosave –Scope Process` ao início do seu runbook. Esse cmdlet garante que todas as credenciais sejam aplicadas apenas à execução do runbook atual.
4. Se essa mensagem de erro ainda for exibida, modifique o código adicionando o parâmetro **-AzureRmContext** após o cmdlet `Add-AzureAccount` e execute o código.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Cenário: Autenticação do Azure falhou porque a autenticação multifator é habilitada

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao autenticar no Azure com o nome de usuário e senha do Azure:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Causa

Se você tiver a autenticação multifator em sua conta do Azure, você não pode usar um usuário do Active Directory do Azure para autenticar no Azure. Em vez disso, você precisa usar um certificado ou uma entidade de serviço para se autenticar no Azure.

#### <a name="resolution"></a>Resolução

Para usar um certificado com os cmdlets do modelo de implantação clássico do Azure, consulte [criando e adicionando um certificado para gerenciar os serviços do Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Para usar uma entidade de serviço com os cmdlets do Azure Resource Manager, veja [criando entidades de serviço usando o portal do Azure](../../active-directory/develop/howto-create-service-principal-portal.md) e [autenticando uma entidade de serviço com o Azure Resource Manager.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="common-errors-when-working-with-runbooks"></a>Erros comuns ao trabalhar com runbooks

### <a name="child-runbook-object"></a>O runbook filho retorna erro quando o fluxo de saída contém objetos em vez de tipos de dados simples

#### <a name="issue"></a>Problema

Você recebe o seguinte erro ao invocar um runbook filho com o `-Wait` comutador e o fluxo de saída contém e o objeto:

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Causa

Há um problema conhecido em que [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) não processa o fluxo de saída corretamente se ele contém objetos.

#### <a name="resolution"></a>Resolução

Para resolver o problema, recomenda-se implementar uma lógica de sondagem e usar o cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) para recuperar a saída. Um exemplo dessa lógica é definida no exemplo a seguir.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Cenário: Você verá um erro em seus fluxos de trabalho sobre o método get_SerializationSettings

#### <a name="issue"></a>Problema

Você verá o erro em seus fluxos de trabalho para um runbook com a seguinte mensagem:

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Causa

Esse erro é causado ao usar ambos os cmdlets AzureRM e Az em um runbook. Ele ocorre quando você importa `Az` antes de importar `AzureRM`.

#### <a name="resolution"></a>Resolução

Os cmdlets de Az e AzureRM não podem ser importados e usados no mesmo runbook. Para saber mais sobre o suporte para Az na Automação do Azure, confira [suporte do módulo Az na Automação do Azure](../az-modules.md).

### <a name="task-was-cancelled"></a>Cenário: O runbook falhar com o erro: Uma tarefa foi cancelada

#### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Causa

Esse erro pode ser causado por meio de módulos do Azure desatualizados.

#### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido atualizando os módulos do Azure para a versão mais recente.

Na sua conta de automação, clique em **módulos**e clique em **módulos do Azure atualização**. A atualização leva aproximadamente 15 minutos. Depois de concluída, execute novamente o runbook que apresentou falha. Para saber mais sobre como atualizar seus módulos, consulte [Atualizar os módulos do Azure na Automação do Azure](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Cenário: Os runbooks falham ao lidar com várias assinaturas

#### <a name="issue"></a>Problema

Ao executar runbooks filho com `Start-AzureRmAutomationRunbook`, o runbook filho não consegue gerenciar recursos do Azure.

#### <a name="cause"></a>Causa

O runbook não está usando o contexto correto durante a execução.

#### <a name="resolution"></a>Resolução

Ao trabalhar com várias assinaturas, o contexto de assinatura pode ser perdido ao invocar runbooks. Para garantir que o contexto da assinatura seja passado para os runbooks, adicione o parâmetro `AzureRmContext` ao cmdlet e passe o contexto para ele. Também é recomendável usar o cmdlet `Disable-AzureRmContextAutosave` com o escopo **Process** para garantir que as credenciais que você usa somente sejam usadas para o runbook atual.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
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
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Cenário: O runbook falha devido a um cmdlet ausente

#### <a name="issue"></a>Problema

Seu runbook falha com um erro semelhante ao exemplo a seguir:

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Causa

Esse erro pode ocorrer devido a um dos seguintes motivos:

1. O módulo que contém o cmdlet não é importado para a conta de automação
2. O módulo que contém o cmdlet é importado, mas está desatualizado

#### <a name="resolution"></a>Resolução

Esse erro pode ser resolvido executando uma das seguintes tarefas:

Se o módulo for um módulo do Azure, consulte [Como atualizar módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md) para saber como atualizar os módulos em sua conta de automação.

Se for um módulo separado, verifique se o módulo foi importado na sua conta de automação.

### <a name="job-attempted-3-times"></a>Cenário: O início do trabalho de runbook foi tentado três vezes, mas falhou ao iniciar a cada vez

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Causa

Esse erro ocorre devido a um dos seguintes problemas:

1. Limite de Memória. Os limites documentados sobre quanta memória é alocada em uma área restrita é encontrado em [Limites do serviço de automação](../../azure-subscription-service-limits.md#automation-limits). Um trabalho pode falhar se ele está usando mais de 400 MB de memória.

2. Soquetes de rede. Áreas restritas do Azure são limitadas a 1.000 soquetes de rede simultâneos, conforme descrito em [Limites de serviço de automação](../../azure-subscription-service-limits.md#automation-limits).

3. Módulo Incompatível. Esse erro pode ocorrer se as dependências do módulo não estiverem corretas e, se não o forem, o seu runbook normalmente retornará uma mensagem "Comando não encontrado" ou "Não é possível ligar o parâmetro".

4. Seu runbook tentou chamar um executável ou subprocesso em um runbook que é executado em uma área restrita do Azure. Não há suporte para esse cenário em áreas restritas do Azure.

5. Seu runbook tentou gravar muitos dados de exceção no fluxo de saída.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:

* Métodos sugeridos para trabalhar dentro do limite de memória são dividir a carga de trabalho entre vários runbooks, não processar muitos dados na memória, não gravar saída desnecessária de seus runbooks nem considerar quantos pontos de verificação você grava nos runbooks de fluxo de trabalho do PowerShell. Você pode usar o método de limpeza, como `$myVar.clear()` para limpar a variável e usar `[GC]::Collect()` para executar a coleta de lixo imediatamente. Essas ações reduzem o volume de memória do seu runbook durante o tempo de execução.

* Atualize os módulos do Azure seguindo as etapas [Como atualizar os módulos do Azure PowerShell na Automação do Azure](../automation-update-azure-modules.md).  

* Outra solução é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers não são limitados pelos limites de memória e rede que são áreas de segurança do Azure.

* Se precisar chamar um processo (como .exe ou subprocess.call) em um runbook, é preciso executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

* Há um limite de 1MB no fluxo de saída do trabalho. Certifique-se de que você coloque chamadas para um executável ou um subprocesso em um bloco try/catch. Se elas geram uma exceção, grave a mensagem de exceção em uma variável de automação. Isso impedirá que ele está sendo gravado no fluxo de saída de trabalho.

### <a name="fails-deserialized-object"></a>Cenário: O Runbook falha devido a objeto desserializado

#### <a name="issue"></a>Problema

O runbook falhar com o erro:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Causa

Se o seu runbook for um Fluxo de Trabalho do PowerShell, ele armazenará objetos complexos em um formato desserializado para manter seu estado de runbook se o fluxo de trabalho estiver suspenso.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes três soluções corrigir esse problema:

1. Se você estiver direcionando objetos complexos de um cmdlet para outro, encapsule os cmdlets em um InlineScript.
2. Passe o nome ou o valor necessário do objeto complexo em vez de passar o objeto inteiro.
3. Use um runbook do PowerShell em vez de um runbook de Fluxo de Trabalho do PowerShell.

### <a name="runbook-fails"></a>Cenário: Meu runbook falha, mas funciona quando é executado localmente

#### <a name="issue"></a>Problema

O script falha quando executado como um runbook, mas funciona quando é executado localmente.

#### <a name="cause"></a>Causa

O script pode falhar durante a execução como um runbook por um dos seguintes motivos:

1. Problemas de autenticação
2. Os módulos necessários não são importados ou estão desatualizados.
3. O script pode estar solicitando intervenção do usuário.
4. Alguns módulos fazem suposições sobre bibliotecas que estão presentes em computadores Windows. Essas bibliotecas podem não estar presentes em uma área restrita.
5. Alguns módulos contam com uma versão do .NET diferente da que está disponível na área restrita.

#### <a name="resolution"></a>Resolução

Qualquer uma das seguintes soluções pode corrigir esse problema:

1. Verifique se você está se [autenticando corretamente no Azure](../manage-runas-account.md).
2. Seus [modelos do Azure devem ser importados e atualizados](../automation-update-azure-modules.md).
3. Verifique se nenhum dos seus cmdlets está solicitando informações. Esse comportamento não é compatível com runbooks.
4. Verifique se algo que faz parte do seu módulo tem uma dependência de algo que não está incluído no módulo.
5. As áreas restritas do Azure usam o .NET Framework 4.7.2; se um módulo usar uma versão superior, ele não funcionará. Nesse caso, você deve usar um [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md)

Se nenhuma dessas soluções resolver o problema, examine os [logs de trabalho](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para obter detalhes específicos sobre o porquê do seu runbook ter falhado.

### <a name="quota-exceeded"></a>Cenário: O trabalho de Runbook falhou porque excedeu a cota alocada

#### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Causa

Esse erro ocorre quando a execução do trabalho excede a cota de livre de 500 minutos para sua conta. Essa cota aplica-se a todos os tipos de tarefa de execução de trabalho. Algumas dessas tarefas podem ser testar um trabalho, iniciar um trabalho no portal, executar um trabalho usando webhooks ou agendar um trabalho para ser executado usando o portal do Azure ou seu datacenter. Para saber mais sobre os preços para Automação, confira os [Preços de automação](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Resolução

Se você quiser usar mais de 500 minutos de processamento por mês, você precisa alterar sua assinatura de gratuita camada para a camada básica. Para atualizar para o tipo Básico, execute as seguintes etapas:  

1. Entre em sua assinatura do Azure  
2. Selecione a conta de Automação que deseja atualizar  
3. Clique em **Configurações** > **Preço**.
4. Clique em **Habilitar** na parte inferior da página para atualizar sua conta para o tipo **Básico**.

### <a name="cmdlet-not-recognized"></a>Cenário: Cmdlet não reconhecida ao executar um runbook

#### <a name="issue"></a>Problema

O trabalho de runbook falha com o erro:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Causa

Esse erro é causado quando o mecanismo do PowerShell não consegue encontrar o cmdlet que você está usando no seu runbook. Esse erro pode ocorrer porque o módulo que contém o cmdlet não está na conta, há um conflito de nome com um nome de runbook ou o cmdlet também existe em outro módulo e a Automação não pode resolver o nome.

#### <a name="resolution"></a>Resolução

Qualquer uma das soluções a seguir corrige o problema:  

* Verifique se você inseriu o nome do cmdlet corretamente.  
* Verifique se o cmdlet existe em sua conta de Automação e se não há conflitos. Para verificar se o cmdlet estiver presente, abra um runbook no modo de edição e a pesquisa para o cmdlet que você deseja localizar na biblioteca ou execução `Get-Command <CommandName>`. Depois de validar que o cmdlet está disponível para a conta e que não há conflitos de nome com outros cmdlets ou runbooks, adicione-o à tela e verifique se está usando um conjunto de parâmetros válido no seu runbook.  
* Se houver um conflito de nomes e o cmdlet estiver disponível em dois módulos diferentes, é possível resolver o problema usando o nome totalmente qualificado do cmdlet. Por exemplo, você pode usar **NomeDoMódulo\NomeDoCmdlet**.  
* Se você estiver executando o runbook local em um grupo de trabalhadores híbrido, verifique se o módulo e cmdlet está instalado na máquina que hospeda o trabalhador híbrido.

### <a name="long-running-runbook"></a>Cenário: Falha de conclusão de um runbook de execução longa

#### <a name="issue"></a>Problema

Seu runbook é exibido em um estado **Parado** após a execução por 3 horas. Você também pode receber o erro:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Esse comportamento existe, por natureza, nas áreas restritas do Azure devido ao monitoramento de "Fração Justa" dos processos na Automação do Azure. Se for executado por mais de três horas, a Fração Justa interrompe automaticamente um runbook. O status de um runbook que ultrapassa o limite de tempo de compartilhamento justo difere por tipo de runbook. Os runbooks do PowerShell e do Python são definidos para um status **Parado**. Os runbooks do fluxo de trabalho do PowerShell estão definidos como **falha**.

#### <a name="cause"></a>Causa

O runbook foi executado durante o limite de 3 horas permitido pelo compartilhamento justo em uma caixa de proteção do Azure.

#### <a name="resolution"></a>Resolução

Uma solução recomendada é executar o runbook em um [Hybrid Runbook Worker](../automation-hrw-run-runbooks.md).

Hybrid Workers não são limitados pela [justa](../automation-runbook-execution.md#fair-share) limite de runbook de 3 horas que são áreas de segurança do Azure. Os runbooks executados em Hybrid Runbook Workers devem ser desenvolvidos para oferecer suporte a comportamentos de reinicialização, caso haja problemas de infraestrutura local inesperados.

Outra opção é otimizar o runbook criando [runbooks filho](../automation-child-runbooks.md). Se o seu runbook percorre continuamente a mesma função em vários recursos, como uma operação de banco de dados em vários bancos de dados, é possível mover essa função para um runbook filho. Cada um desses runbooks filho é executado em paralelo em processos separados. Esse comportamento reduz a quantidade total de tempo para o runbook pai concluir.

Os cmdlets do PowerShell que habilitam o cenário do runbook filho são:

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) -este cmdlet permite que você iniciar um runbook e passar parâmetros para o runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob): se houver operações que precisem ser executadas depois que o runbook filho for concluído, esse cmdlet permite verificar o status do trabalho de cada filho.

### <a name="expired webhook"></a>Cenário: Status: 400 Solicitação incorreta ao chamar um webhook

#### <a name="issue"></a>Problema

Ao tentar chamar um webhook para um runbook de Automação do Azure, você recebe o seguinte erro:

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Causa

O webhook que você está tentando chamar está desabilitado ou expirou.

#### <a name="resolution"></a>Resolução

Se o webhook estiver desabilitado, você pode habilitar novamente o webhook por meio do portal do Azure. Se o webhook expirou, o webhook precisa ser excluído e recriado. Só é possível [renovar um webhook](../automation-webhooks.md#renew-webhook) se ele ainda não expirou.

### <a name="429"></a>Cenário: 429: A taxa de solicitação no momento, é muito grande. Tente novamente

#### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao executar o cmdlet `Get-AzureRmAutomationJobOutput`:

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Causa

Esse erro pode ocorrer ao recuperar a saída do trabalho de um runbook que tenha muitos [fluxos detalhados](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Edite o runbook e reduza o número de fluxos de trabalho que ele emite.
* Reduza o número de fluxos a ser recuperado ao executar o cmdlet. Para fazer isso, você pode especificar o parâmetro `-Stream Output` para o cmdlet `Get-AzureRmAutomationJobOutput` para recuperar apenas os fluxos de saída. 

### <a name="cannot-invoke-method"></a>Cenário: Trabalho do PowerShell falha com erro: Não é possível invocar o método

#### <a name="issue"></a>Problema

Você receberá a seguinte mensagem de erro ao iniciar um Job do PowerShell em um runbook em execução no Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Causa

Esse erro pode ocorrer quando você inicia um trabalho em um runbook foi executado no Azure no PowerShell. Esse comportamento pode ocorrer porque os runbooks são executados no Azure área restrita não podem ser executadas na [modo de linguagem completa](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>Resolução

Há três maneiras de resolver esse erro:

* Em vez de usar `Start-Job`, use `Start-AzureRmAutomationRunbook` para iniciar um runbook
* Se o runbook tiver essa mensagem de erro, execute-o em um Hybrid Runbook Worker

Para saber mais sobre esse comportamento e outros comportamentos de Runbooks de automação do Azure, consulte [Runbook comportamento](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
* Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
