---
title: Saída de runbook e mensagens na Automação do Azure
description: Descreve como criar e recuperar a saída e mensagens de erro de runbooks na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c129391c0830e0194c32a041853482f92340bbb9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405780"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Saída e mensagens do runbook na Automação do Azure
A maioria dos runbooks de Automação do Azure tem alguma forma de saída. Essa saída pode ser uma mensagem de erro para o usuário ou um objeto complexo que você pretende usar com outro runbook. O Windows PowerShell oferece [vários fluxos](/powershell/module/microsoft.powershell.core/about/about_redirection) para o envio da saída de um script ou de um de fluxo de trabalho. A Automação do Azure trabalha com cada um desses fluxos de maneira diferente. Siga as melhores práticas sobre como usar cada uma delas ao criar um runbook.

A tabela a seguir fornece uma breve descrição de cada um dos fluxos e o comportamento no portal do Azure dos runbooks publicados e quando [testar um runbook](automation-testing-runbook.md). Mais detalhes sobre cada fluxo são fornecidos nas seções posteriores.

| Stream | Descrição | Publicado | Teste |
|:--- |:--- |:--- |:--- |
| Saída |Objetos que se destinam a consumo de outros runbooks. |Gravado no histórico do trabalho. |Exibido no Painel de Saída do Teste. |
| Aviso |Mensagem de aviso para o usuário. |Gravado no histórico do trabalho. |Exibido no Painel de Saída do Teste. |
| Erro |Mensagem de erro para o usuário. Ao contrário de uma exceção, o runbook continua após uma mensagem de erro por padrão. |Gravado no histórico do trabalho. |Exibido no Painel de Saída do Teste. |
| Detalhado |Mensagens que fornecem informações gerais ou de depuração. |Gravado no histórico do trabalho somente se o log detalhado estiver ativado para o runbook. |Exibido no painel Saída de Teste somente se $VerbosePreference for definido como Continuar no runbook. |
| Andamento |Os registros são automaticamente gerados antes e depois de cada atividade no runbook. O runbook não deve tentar criar seus próprios registros de progresso, pois são destinados a um usuário interativo. |Gravado no histórico do trabalho somente se o log de andamento estiver ativado para o runbook. |Não exibido no Painel Saída de Teste. |
| Depurar |As mensagens destinadas a um usuário interativo. Não deve ser usado em runbooks. |Não gravado no histórico do trabalho. |Não gravado no Painel Saída de Teste. |

## <a name="output-stream"></a>Fluxo de saída
O fluxo de saída é destinado à saída de objetos, que são criados por um script ou fluxo de trabalho quando executado corretamente. Na Automação do Azure, esse fluxo é usado principalmente para objetos que se destinam a ser consumidos por [runbooks pais que chamam o runbook atual](automation-child-runbooks.md). Quando você [chamar um runbook embutido](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) de um runbook pai, ele retornará dados do fluxo de saída para o pai. Use somente o fluxo de saída para comunicar informações gerais ao usuário, se você souber que o runbook nunca será chamado por outro runbook. Como prática recomendada, no entanto, você normalmente deverá usar o [Fluxo Detalhado](#verbose-stream) para comunicar informações gerais para o usuário.

Você pode gravar dados no fluxo de saída usando [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) ou colocando o objeto em sua própria linha no runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Saída de uma função
Quando você gravar no fluxo de saída em uma função incluída no runbook, a saída será passada de volta para o runbook. Se o runbook atribuir essa saída a uma variável, então ela não será gravada no fluxo de saída. A gravação de qualquer outro fluxo de dentro da função grava ao fluxo correspondente para o runbook.

Considere o exemplo de runbook a seguir:

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

O fluxo de saída para o trabalho de runbook deveria ser:

```output
Output inside of function
Output outside of function
```

O fluxo detalhado para o trabalho de runbook deveria ser:

```output
Verbose outside of function
Verbose inside of function
```

Após publicar o runbook e antes de iniciá-lo, você também deverá ativar o registro detalhado de log nas configurações do runbook para obter a saída do fluxo detalhado.

### <a name="declaring-output-data-type"></a>Declarando o tipo de dados de saída
Um fluxo de trabalho pode especificar o tipo de dados de sua saída usando o [atributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Esse atributo não tem nenhum efeito em tempo de execução, mas oferece uma indicação para o autor do runbook no tempo de design da saída esperada do runbook. Como o conjunto de ferramentas para runbooks continua a evoluir, cresce a importância de declarar tipos de dados de saída em tempo de design. Como resultado, é uma melhor prática incluir essa declaração em qualquer runbook criado por você.

Veja uma lista de tipos de saída de exemplo:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

O exemplo de runbook a seguir gera um objeto de cadeia de caracteres e inclui uma declaração de seu tipo de saída. Se seu runbook gerar uma matriz de um determinado tipo, você ainda deverá especificar o tipo como oposto a uma matriz do tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Para declarar um tipo de saída em runbooks Gráficos ou de Fluxo de Trabalho Gráfico do PowerShell, você pode selecionar a opção de menu **Entrada e Saída** e digitar o nome do tipo de saída. É recomendável usar o nome completo da classe .NET para torná-lo facilmente identificável ao fazer referência a ele de um runbook pai. Isso expõe todas as propriedades da classe para o barramento de dados no runbook e oferece muita flexibilidade ao usá-los para a lógica condicional, registro em log e referenciamento como valores de outras atividades no runbook.<br> ![Opção Entrada e Saída de Runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

No exemplo a seguir, você tem dois runbooks gráficos para demonstrar esse recurso. Se você aplicar o modelo de design modular do runbook, você tem um runbook que serve como o *modelo de Autenticação do Runbook* que gerencia a autenticação com o Azure usando a conta Executar Como. Nosso segundo runbook, que normalmente executaria a lógica principal para automatizar um determinado cenário, nesse caso executará o *modelo de Autenticação de Runbook* e exibirá os resultados para o painel de saída **Teste** . Sob circunstâncias normais, esse runbook teria de fazer algo em um recurso aproveitando a saída do runbook filho.

Veja a lógica básica do runbook **AuthenticateTo-Azure**.<br> ![Exemplo de modelo de runbook de autenticação](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Ele inclui o tipo de saída *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, que retorna as propriedades de perfil de autenticação.<br> ![Exemplo de tipo de saída de runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Embora esse runbook seja direto, há um item de configuração para chamar aqui. A última atividade está executando o cmdlet **Write-Output** e grava os dados de perfil em uma variável $_ usando uma expressão do PowerShell para o parâmetro **Inputobject**, que é necessário para esse cmdlet.  

Para o segundo runbook neste exemplo, chamado *Test-ChildOutputType*, você tem duas atividades.<br> ![Runbook de tipo de saída de exemplo filho](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

A primeira atividade chama o runbook **AuthenticateTo-Azure** e a segunda atividade está executando o cmdlet **Write-Verbose** com a **Fonte de dados** de **Saída de atividade** e o valor de **Caminho de campo** é **Context.Subscription.SubscriptionName**, que está especificando a saída de contexto do runbook **AuthenticateTo-Azure**.<br> ![Fonte de dados de parâmetro do cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

A saída resultante é o nome da assinatura.<br> ![Resultados do runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Uma observação sobre o comportamento do controle Tipo de Saída. Quando você digita um valor no campo Tipo de Saída na folha Propriedades de Entrada e Saída, precisa clicar fora do controle depois de digitá-lo, para que a entrada seja reconhecida pelo controle.  

## <a name="message-streams"></a>Fluxos de mensagens
Ao contrário do fluxo de saída, os fluxos de mensagens destinam-se a comunicar informações ao usuário. Existem vários fluxos de mensagens para diferentes tipos de informação, e cada um deles é manipulado de forma diferente pela Automação do Azure.

### <a name="warning-and-error-streams"></a>Fluxos de aviso e de erro
Os fluxos de Aviso e de Erro pretendem registrar problemas que ocorrem em um runbook. Eles são gravados no histórico do trabalho quando um runbook é executado e são incluídos no Painel de Saída de Teste no Portal do Azure quando um runbook é testado. Por padrão, o runbook continuará a ser executado após um aviso ou um erro. Você pode especificar que o runbook seja suspenso em um aviso ou um erro ao definir uma [variável de preferência](#preference-variables) no runbook antes de criar a mensagem. Por exemplo, para fazer com que um runbook seja suspenso em um erro como o faria em uma exceção, defina **$ErrorActionPreference** como Stop.

Crie uma mensagem de aviso ou de erro usando o cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) ou [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). As atividades também podem ser gravadas nesses fluxos.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Fluxo Detalhado
O fluxo de mensagens Detalhado destina-se a informações gerais sobre a operação do runbook. Como o [Fluxo de Depuração](#debug-stream) não está disponível em um runbook, as mensagens detalhadas deverão ser usadas para informações de depuração. Por padrão, as mensagens detalhadas de runbooks publicados não são armazenadas no histórico de trabalhos. Para armazenar as mensagens detalhadas, configure runbooks publicados como Registros Detalhados de Log na guia Configurar do runbook no Portal do Azure. Na maioria dos casos, você deve manter a configuração padrão de não obter o log de registros detalhados para um runbook por motivos de desempenho. Só ative essa opção para solucionar problemas ou depurar um runbook.

Quando [testar um runbook](automation-testing-runbook.md), as mensagens detalhadas não serão exibidas mesmo que o runbook esteja configurado para registros detalhados de log. Para exibir as mensagens detalhadas enquanto [testa um runbook](automation-testing-runbook.md), você deverá definir a variável $VerbosePreference como Continue. Com esse conjunto de variáveis, as mensagens detalhadas serão exibidas no Painel de Saída de Teste do Portal do Azure.

Criar uma mensagem detalhada usando o cmdlet [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Fluxo de Depuração
O fluxo de Depuração deve ser usado por um usuário interativo e não deve ser usado em runbooks.

## <a name="progress-records"></a>Registros de andamento
Se você configurar um runbook para criar um log de registros de andamento (na guia Configurar do runbook no Portal do Azure), um registro será gravado no histórico de trabalhos antes e após a execução de cada atividade. Na maioria dos casos, você deverá manter a configuração padrão de não obter o log de registros detalhados para um runbook para maximizar o desempenho. Só ative essa opção para solucionar problemas ou depurar um runbook. Quando você estiver testando um runbook, as mensagens de andamento não serão exibidas, mesmo se o runbook estiver configurado para log de registros de andamento.

O cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) não é válido em um runbook, já que esse cmdlet é destinado ao uso com um usuário interativo.

## <a name="preference-variables"></a>Variáveis de preferência
O Windows PowerShell usa [variáveis de preferência](https://technet.microsoft.com/library/hh847796.aspx) para determinar como responder aos dados enviados para fluxos de saída diferentes. Você pode definir essas variáveis em um runbook para controlar como ele responde aos dados enviados em diferentes fluxos.

A tabela a seguir lista as variáveis de preferência que podem ser usadas em runbooks com seus valores válidos e padrão. Essa tabela inclui somente os valores que são válidos em um runbook. Os valores adicionais serão válidos para as variáveis de preferência quando usados no Windows PowerShell fora da Automação do Azure.

| Variável | Default Value | Valores Válidos |
|:--- |:--- |:--- |
| WarningPreference |Continue |Parar<br>Continuar<br>SilentlyContinue |
| ErrorActionPreference |Continuar |Parar<br>Continuar<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Parar<br>Continuar<br>SilentlyContinue |

A tabela a seguir lista o comportamento para os valores de variáveis de preferência válidos em runbooks.

| Valor | Comportamento |
|:--- |:--- |
| Continuar |Registra em log a mensagem e continua executando o runbook. |
| SilentlyContinue |Continua executando o runbook sem registrar em log a mensagem. Este valor tem o efeito de ignorar a mensagem. |
| Parar |Registra em log a mensagem e suspende o runbook. |

## <a name="runbook-output"></a>Recuperar saída e mensagens do runbook
### <a name="azure-portal"></a>Portal do Azure
Você pode exibir os detalhes de um trabalho de runbook no Portal do Azure na guia Trabalhos de um runbook. O Resumo do trabalho exibe os parâmetros de entrada e o [Fluxo de Saída](#output-stream), além de informações gerais sobre o trabalho e todas as exceções que tenham ocorrido. O Histórico inclui mensagens do [Fluxo de Saída](#output-stream) e dos [Fluxos de Erro e de Aviso](#warning-and-error-streams), além do [Fluxo Detalhado](#verbose-stream) e dos [Registros de Progresso](#progress-records), caso o runbook esteja configurado para o log de registros detalhados e de progresso.

### <a name="windows-powershell"></a>Windows PowerShell
No Windows PowerShell, você pode recuperar saída e mensagens de um runbook usando o cmdlet [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . Esse cmdlet exige a ID do trabalho e tem um parâmetro chamado Stream, onde você pode especificar qual fluxo será retornado. Você pode especificar **Any** para retornar todos os fluxos do trabalho.

O exemplo a seguir inicia um runbook de exemplo e aguarda a sua conclusão. Depois de concluído, seu fluxo de saída será coletado do trabalho.

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

### <a name="graphical-authoring"></a>Criação gráfica
Para runbooks gráficos, um log extra está disponível na forma de rastreamento em nível de atividade. Há dois níveis de rastreamento: Básico e Detalhado. No rastreamento Básico, é possível ver o horário de início e encerramento de cada atividade no runbook, além de informações relacionadas a qualquer nova atividade. Alguns exemplos são, o número de tentativas e o horário de início da atividade. No rastreamento Detalhado, você obtém o rastreamento Básico, além de dados de entrada e de saída para cada atividade. Atualmente, os registros de rastreamento são gravados usando a transmissão detalhada; portanto, é necessário, habilitar o log Detalhado ao habilitar o rastreamento. Para runbooks gráficos com rastreamento habilitado, não há necessidade de registros de progresso do log. O rastreamento básico serve para o mesmo propósito e é mais informativo.

![Modo de exibição Transmissões de Trabalho da criação gráfica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Na captura de tela anterior, é possível ver que, ao habilitar o log Detalhado e o rastreamento para runbooks gráficos, muito mais informações estão disponíveis no modo de exibição Transmissões de Trabalho de produção. Essas informações extra podem ser essenciais para solucionar problemas de produção com um runbook e, portanto, você só deve habilitá-lo para essa finalidade e não como uma prática geral. Especificamente, os registros de Rastreamento podem ser inúmeros. Com o rastreamento de runbook Gráfico, é possível obter de dois a quatro registros por atividade, dependendo se você configurou o rastreamento como Básico ou Detalhado. A menos que você precise dessas informações para acompanhar o progresso de um runbook com relação à solução de problemas, convém manter o Rastreamento desligado.

**Para habilitar o rastreamento em nível de atividade, execute as etapas a seguir:**

1. No portal do Azure, abra sua conta da Automação.
2. Em **Automação de Processos**, selecione **Runbooks** para abrir a lista de runbooks.
3. Na página Runbooks, clique para selecionar um runbook gráfico na lista de runbooks.
4. Em **Configurações**, clique em **Log e rastreamento**.
5. Na página Log e Rastreamento, sob registros detalhados de Log, clique em **Ativado** para habilitar o log detalhado e, sob o Rastreamento em nível de atividade, altere o nível de rastreamento para **Básico** ou **Detalhado** com base no nível de rastreamento necessário.<br>
   
   ![Página Registro e Rastreamento de Criação Gráfica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure monitorar logs
A Automação pode enviar status de trabalho do runbook e fluxos de trabalho para o espaço de trabalho do Log Analytics. Com os logs de Azure Monitor que você pode,

* Obter informações sobre os trabalhos de Automação 
* Disparar um email ou um alerta com base no status do trabalho de runbook (por exemplo, com falha ou suspenso) 
* Escrever consultas avançadas em seus fluxos de trabalho 
* Correlacionar trabalhos em contas de Automação 
* Visualizar o histórico de trabalho ao longo do tempo    

Para obter mais informações sobre como configurar a integração com os logs de Azure Monitor para coletar, correlacionar e agir sobre os dados do trabalho, consulte [encaminhar fluxos de trabalho e status do trabalho da automação para Azure monitor logs](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a execução de runbooks, como monitorar trabalhos de runbook e outros detalhes técnicos, confira [Acompanhar um trabalho de runbook](automation-runbook-execution.md)
* Para entender como criar e usar runbooks filho, consulte [Runbooks filho na Automação do Azure](automation-child-runbooks.md)


