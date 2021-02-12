---
title: Configurar fluxos de saída e mensagens de runbook
description: Este artigo informa como implementar a lógica de tratamento de erros e descreve os fluxos de saída e de mensagens nos runbooks de automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: beed3ec50d0c7990168ee75976c732796cdbe246
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324432"
---
# <a name="configure-runbook-output-and-message-streams"></a>Configurar fluxos de saída e mensagens de runbook

A maioria dos runbooks de Automação do Azure tem alguma forma de saída. Essa saída pode ser uma mensagem de erro para o usuário ou um objeto complexo destinado a ser usado com outro runbook. O Windows PowerShell oferece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para o envio da saída de um script ou de um de fluxo de trabalho. A Automação do Azure trabalha com cada um desses fluxos de maneira diferente. Siga as melhores práticas sobre como usar os fluxos ao criar um runbook.

A tabela a seguir descreve brevemente cada fluxo com seu comportamento no portal do Azure para runbooks publicados e durante o [teste de um runbook](./manage-runbooks.md). O fluxo de saída é o fluxo principal usado para a comunicação entre runbooks. Os outros fluxos são classificados como fluxos de mensagens, destinados a comunicar informações ao usuário.

| STREAM | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Erro |Mensagem de erro para o usuário. Diferente de quando há uma exceção, o runbook continua após uma mensagem de erro por padrão. |Gravado no histórico do trabalho |Exibido no painel Saída de teste |
| Depurar |As mensagens destinadas a um usuário interativo. Não deve ser usado em runbooks. |Não gravado no histórico do trabalho |Não exibido no painel Saída de teste |
| Saída |Objetos que se destinam a consumo de outros runbooks. |Gravado no histórico do trabalho |Exibido no painel Saída de teste |
| Andamento |Os registros são automaticamente gerados antes e depois de cada atividade no runbook. O runbook não deve tentar criar seus registros de progresso, pois eles são destinados a um usuário interativo. |Gravado no histórico do trabalho somente se o log de andamento estiver ativado para o runbook |Não exibido no painel Saída de teste |
| Detalhado |Mensagens que fornecem informações gerais ou de depuração. |Gravado no histórico do trabalho somente se o log detalhado estiver ativado para o runbook |Exibido no painel Saída de teste somente se a variável `VerbosePreference` estiver definida como Continuar no runbook |
| Aviso |Mensagem de aviso para o usuário. |Gravado no histórico do trabalho |Exibido no painel Saída de teste |

## <a name="use-the-output-stream"></a>Usar o fluxo de saída

O fluxo de saída é usado para a saída de objetos criados por um script ou um fluxo de trabalho quando executado corretamente. A Automação do Azure usa principalmente esse fluxo para objetos a serem consumidos pelos runbooks pais que chamam o [runbook atual](automation-child-runbooks.md). Quando um pai [chama um runbook em linha](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), o filho retorna dados do fluxo de saída para o pai.

Seu runbook usa o fluxo de saída para comunicar informações gerais ao cliente somente se nunca for chamado por outro runbook. Como prática recomendada, no entanto, seus runbooks normalmente devem usar o [fluxo detalhado](#write-output-to-verbose-stream) para comunicar informações gerais ao usuário.

Faça seu runbook gravar dados no fluxo de saída usando [Write-Output](/powershell/module/microsoft.powershell.utility/write-output). Como alternativa, você pode colocar o objeto em sua própria linha no script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Tratar a saída de uma função

Quando uma função de runbook grava no fluxo de saída, a saída é retornada ao runbook. Se o runbook atribuir essa saída a uma variável, a saída não será gravada no fluxo de saída. A gravação de qualquer outro fluxo de dentro da função grava ao fluxo correspondente para o runbook. Considere o seguinte exemplo de runbook de fluxo de trabalho do PowerShell.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

O fluxo de saída para o trabalho de runbook é:

```output
Output inside of function
Output outside of function
```

O fluxo detalhado para o trabalho de runbook é:

```output
Verbose outside of function
Verbose inside of function
```

Após publicar o runbook e antes de iniciá-lo, você também deverá ativar o registro detalhado de log nas configurações do runbook para obter a saída do fluxo detalhado.

### <a name="declare-output-data-type"></a>Declarar o tipo de dados de saída

A seguir, exemplos de tipos de dados de saída:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Declarar o tipo de dados de saída em um fluxo de trabalho

Um fluxo de trabalho especifica o tipo de dados de sua saída usando o [atributo OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Esse atributo não tem nenhum efeito durante o runtime, mas ele fornece a você uma indicação no momento de criação da saída esperada do runbook. Como o conjunto de ferramentas para runbooks continua a evoluir, cresce a importância de declarar tipos de dados de saída em tempo de design. Portanto, é uma melhor prática incluir essa declaração em qualquer runbook criado por você.

O exemplo de runbook a seguir gera um objeto de cadeia de caracteres e inclui uma declaração de seu tipo de saída. Se seu runbook gerar uma matriz de um determinado tipo, você ainda deverá especificar o tipo como oposto a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Declarar o tipo de dados de saída em runbook gráfico

Para declarar um tipo de saída em um runbook gráfico ou gráfico do fluxo de trabalho do PowerShell, você pode selecionar a opção de menu **Entrada e Saída** e inserir o tipo de saída. É recomendável usar o nome completo da classe .NET para facilitar a identificação do tipo quando um runbook pai faz referência a ele. O uso do nome completo expõe todas as propriedades da classe para o barramento de dados no runbook e aumenta a flexibilidade quando as propriedades são usadas para a lógica condicional, registro em log e referência como valores para outras atividades do runbook.<br> ![Opção Entrada e Saída de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Depois de inserir um valor no campo **Tipo de Saída** no painel de propriedades Entrada e Saída, clique fora do controle para que ele reconheça sua entrada.

O exemplo a seguir mostra dois runbooks gráficos para demonstrar o recurso de Entrada e Saída. Ao aplicar o modelo de design modular do runbook, você tem um runbook como o modelo de Autenticação do Runbook que gerencia a autenticação com o Azure usando a conta Executar Como. O segundo runbook, que normalmente executa a lógica principal para automatizar um determinado cenário, nesse caso, executa o modelo Autenticar Runbook. Exibe os resultados no seu painel Saída de teste. Sob circunstâncias normais, esse runbook teria de fazer algo em um recurso aproveitando a saída do runbook filho.

Veja a lógica básica do runbook **AuthenticateTo-Azure**.<br> ![Exemplo de modelo de runbook de autenticação](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

O runbook inclui o tipo de saída `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, que retorna as propriedades do perfil de autenticação.<br> ![Exemplo de tipo de saída de runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Embora esse runbook seja direto, há um item de configuração para chamar aqui. A última atividade executa o cmdlet `Write-Output` para gravar dados do perfil em uma variável usando uma expressão do PowerShell para o parâmetro `Inputobject`. Este parâmetro é obrigatório para `Write-Output`.

O segundo runbook neste exemplo, chamado **Test-ChildOutputType** , simplesmente define duas atividades.<br> ![Runbook de tipo de saída de exemplo filho](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

A primeira atividade chama o runbook **AuthenticateTo-Azure**. A segunda atividade executa o cmdlet `Write-Verbose` com a **Fonte de dados** definida como **Saída de atividade**. Além disso, **Caminho de campo** é definido para **Context.Subscription.SubscriptionName** , a saída de contexto do runbook **AuthenticateTo-Azure**.<br> ![Fonte de dados de parâmetro do cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

A saída resultante é o nome da assinatura.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Trabalhando com fluxos de mensagens

Ao contrário do fluxo de saída, os fluxos de mensagens comunicam informações ao usuário. Existem vários fluxos de mensagens para diferentes tipos de informação, e a Automação do Azure lida com cada fluxo de maneira diferente.

### <a name="write-output-to-warning-and-error-streams"></a>Gravar saída em fluxos de aviso e de erro

Os fluxos de aviso e de erro registram problemas que ocorrem em um runbook. A Automação do Azure grava esses fluxos no histórico de trabalhos ao executar um runbook. A Automação inclui os fluxos no painel Saída de teste no portal do Azure quando um runbook é testado.

Por padrão, um runbook continua a ser executado após um aviso ou erro. Você pode especificar que o seu runbook deve suspender um aviso ou erro, solicitando que o runbook defina uma [variável de preferência](#work-with-preference-variables) antes de criar a mensagem. Por exemplo, para fazer com que o runbook seja suspenso em um erro, como ocorre em uma exceção, defina a variável `ErrorActionPreference` como Stop.

Crie uma mensagem de aviso ou de erro usando o cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) ou [Write-Error](/powershell/module/microsoft.powershell.utility/write-error). As atividades também podem gravar nos fluxos de aviso e erro.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Gravar saída no fluxo de depuração

A Automação do Azure usa o fluxo de mensagens de depuração para usuários interativos. Por padrão, a automação do Azure não captura nenhum dado de fluxo de depuração, somente dados de saída, de erro e de aviso são capturados, bem como dados detalhados se o runbook estiver configurado para capturá-lo.

Para capturar dados de fluxo de depuração, você precisa executar duas ações em seus runbooks:

1. Defina a variável `$GLOBAL:DebugPreference="Continue"` , que informa ao PowerShell para continuar sempre que uma mensagem de depuração for encontrada.  A parte **$global:** informa ao PowerShell para fazer isso no escopo global em vez de qualquer escopo local no qual o script está no momento em que a instrução é executada.

1. Redirecione o fluxo de depuração que não capturamos para um fluxo que capturamos como *saída*. Isso é feito definindo o redirecionamento do PowerShell em relação à instrução a ser executada. Para obter mais informações sobre o redirecionamento do PowerShell, consulte [sobre o redirecionamento](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Exemplos

Neste exemplo, o runbook é configurado usando os `Write-Output` `Write-Debug` cmdlets e com a intenção de produzir dois fluxos diferentes.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Se esse runbook fosse executado como está, o painel de saída do trabalho de runbook transmitiria a seguinte saída:

```output
This is an output message.
```

Neste exemplo, o runbook é configurado de forma semelhante ao exemplo anterior, exceto que a instrução `$GLOBAL:DebugPreference="Continue"` é incluída com a adição de `5>&1` no final da `Write-Debug` instrução.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Se esse runbook fosse executado, o painel de saída do trabalho de runbook transmitiria a seguinte saída:

```output
This is an output message.
This is a debug message.
```

Isso ocorre porque a `$GLOBAL:DebugPreference="Continue"` instrução informa ao PowerShell para exibir mensagens de depuração, e a adição de `5>&1` ao final da `Write-Debug` instrução informa ao PowerShell para redirecionar o fluxo 5 (Depurar) para o fluxo 1 (saída).

### <a name="write-output-to-verbose-stream"></a>Gravar saída no fluxo detalhado

O fluxo detalhado de mensagens oferece suporte a informações gerais sobre a operação do runbook. Como o fluxo de depuração não está disponível para um runbook, seu runbook deve usar mensagens detalhadas para obter informações sobre depuração.

Por padrão, o histórico de tarefas não armazena mensagens detalhadas de runbooks publicados, por motivos de desempenho. Para armazenar mensagens detalhadas, use a guia **Configurar** do portal do Azure com a configuração **Registros detalhados de log** para configurar seus runbooks publicados para registrar mensagens detalhadas. Só ative essa opção para solucionar problemas ou depurar um runbook. Na maioria dos casos, você deve manter a configuração padrão de não obter o log de registros detalhados.

Quando [testar um runbook](./manage-runbooks.md), as mensagens detalhadas não serão exibidas mesmo que o runbook esteja configurado para registros detalhados de log. Para exibir as mensagens detalhadas enquanto [testa um runbook](./manage-runbooks.md), você deverá definir a variável `VerbosePreference` como Continue. Com esse conjunto de variáveis, as mensagens detalhadas serão exibidas no painel Saída de teste do portal do Azure.

O código a seguir cria uma mensagem detalhada usando o cmdlet [Write-Verbose](/powershell/module/microsoft.powershell.utility/write-verbose).

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Tratar registros de progresso

Você pode usar a guia **Configurar** do portal do Azure para configurar um runbook para registrar em log os registros de andamento. A configuração padrão é não registrar os registros, para maximizar o desempenho. Na maioria dos casos, você deve manter a configuração padrão. Só ative essa opção para solucionar problemas ou depurar um runbook.

Se você habilitar o log de registro de progresso, seu runbook gravará um registro no histórico de tarefas antes e depois da execução de cada atividade. O teste de um runbook não exibe mensagens de progresso, mesmo que o runbook esteja configurado para registrar registros de progresso.

>[!NOTE]
>O cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) não é válido em um runbook, já que esse cmdlet é destinado ao uso com um usuário interativo.

## <a name="work-with-preference-variables"></a>Trabalhar com variáveis de preferência

Você pode definir determinadas [variáveis de preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables) do Windows PowerShell em seus runbooks para controlar a resposta aos dados enviados para diferentes fluxos de saída. A tabela a seguir lista as variáveis de preferência que podem ser usadas em runbooks com seus valores padrão e válidos. Os valores adicionais estão disponíveis para as variáveis de preferência quando usados no Windows PowerShell fora da Automação do Azure.

| Variável | Valor Padrão | Valores válidos |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Stop<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Stop<br>Continue<br>SilentlyContinue |

A tabela a seguir lista o comportamento para os valores de variáveis de preferência válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continue |Registra em log a mensagem e continua executando o runbook. |
| SilentlyContinue |Continua executando o runbook sem registrar em log a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Stop |Registra em log a mensagem e suspende o runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperar saída e mensagens do runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperar saída e mensagens do runbook na Automação do Azure

Você pode exibir os detalhes de um trabalho de runbook no portal do Azure usando a guia **Trabalhos** para o runbook. O resumo do trabalho exibe os parâmetros de entrada e o [fluxo de saída](#use-the-output-stream), além de informações gerais sobre o trabalho e todas as exceções ocorridas. O histórico de trabalhos inclui mensagens de fluxos de saída, de [aviso e de erro](#write-output-to-warning-and-error-streams). Ele também inclui mensagens do [fluxo detalhado](#write-output-to-verbose-stream) e dos [registros de progresso](#handle-progress-records), caso o runbook esteja configurado para o log de registros detalhados e de progresso.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperar mensagens e saída do runbook no Windows PowerShell

No Windows PowerShell, você pode recuperar saída e mensagens de um runbook usando o cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput). Esse cmdlet exige a ID do trabalho e tem um parâmetro chamado `Stream`, onde você pode especificar o fluxo a ser recuperado. Você pode especificar um valor Qualquer para esse parâmetro para recuperar todos os fluxos para o trabalho.

O exemplo a seguir inicia um runbook de exemplo e aguarda a sua conclusão. Depois que o runbook conclui a execução, o script coleta o fluxo de saída do runbook do trabalho.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperar mensagens e saída do runbook em runbooks gráficos

Para runbooks gráficos, o log extra de saída e mensagens está disponível na forma de rastreamento no nível da atividade. Há dois níveis de rastreamento: Básico e Detalhado. O rastreamento Básico exibe o horário de início e encerramento de cada atividade no runbook, além de informações relacionadas a qualquer nova atividade. Alguns exemplos são o número de tentativas e o horário de início da atividade. O rastreamento Detalhado inclui o rastreamento Básico, além de dados de entrada e de saída para cada atividade. 

Atualmente, o rastreamento de nível de atividade grava registros usando o fluxo detalhado. Portanto, você deve habilitar o log detalhado ao habilitar o rastreamento. Para runbooks gráficos com rastreamento habilitado, não há necessidade de registros de progresso do log. O rastreamento básico serve para o mesmo propósito e é mais informativo.

![Exibição de fluxos de trabalho de autoria gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Você pode ver na imagem que ativar o log detalhado e o rastreio para runbooks gráficos disponibiliza muito mais informações na visualização de produção **Fluxos de trabalho**. Essas informações extras podem ser essenciais para solucionar problemas de produção com um runbook. 

Entretanto, a menos que você precise dessas informações para acompanhar o progresso de um runbook com relação à solução de problemas, convém manter o rastreamento desligado como uma prática geral. Especificamente, os registros de rastreamento podem ser inúmeros. Com o rastreamento de runbook gráfico, você pode obter de dois a quatro registros por atividade, dependendo da sua configuração de rastreamento Básico ou Detalhado.

**Para habilitar o rastreamento no nível da atividade:**

1. No portal do Azure, abra sua conta da Automação.
2. Selecione **Runbooks** em **Automação de Processos** para abrir a lista de runbooks.
3. Na página Runbooks, selecione um runbook gráfico na lista de runbooks.
4. Em **Configurações** , clique em **Log e rastreamento**.
5. Na página Log e rastreamento, em **Registros detalhados de log** , clique em **Ativar** para ativar o log detalhado.
6. Em **Rastreamento no nível da atividade** , altere o nível de rastreamento para **Básico** ou **Detalhado** , com base no nível de rastreamento necessário.<br>

   ![Página Registro e Rastreamento de Criação Gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperar saída e mensagens do runbook nos logs do Microsoft Azure Monitor

A Automação do Azure pode enviar fluxos de trabalho e status de trabalho de runbook para seu workspace do Log Analytics. O Azure Monitor oferece suporte a logs que permitem:

* Obter informações sobre os Trabalhos de automação.
* Disparar um email ou um alerta com base no status de trabalho de runbook, por exemplo, com falha ou suspenso.
* Escrever consultas avançadas em fluxos de trabalho.
* Correlacionar trabalhos em Contas de automação.
* Visualize o histórico de trabalhos.

Para obter mais informações sobre como configurar a integração com os logs de Azure Monitor para coletar, correlacionar e agir sobre os dados do trabalho, consulte [encaminhar o status do trabalho e fluxos de trabalho de automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* Para se familiarizar com os runbooks, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).
* Se você não estiver familiarizado com o script do PowerShell, consulte a documentação do [PowerShell](/powershell/scripting/overview) .
* Para obter a referência de cmdlet do PowerShell de automação do Azure, consulte [AZ. Automation](/powershell/module/az.automation).
