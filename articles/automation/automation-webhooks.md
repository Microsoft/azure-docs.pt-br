---
title: "Iniciar um runbook de Automação do Azure com um webhook | Microsoft Docs"
description: "Um webhook que permite que um cliente inicie um runbook na Automação do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9b20237c-a593-4299-bbdc-35c47ee9e55d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: magoedte;bwren;sngun
ms.openlocfilehash: d384a1f6e0f6bf49cf94020265fe5675ffc0029d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook de Automação do Azure com um webhook
O *webhook* permite que você inicie um runbook específico na Automação do Azure por meio de uma única solicitação HTTP. Isso permite que os serviços externos, como o Visual Studio Team Services, GitHub, Microsoft Operations Management Suite, Log Analytics ou aplicativos personalizados iniciem runbooks sem implementar uma solução completa usando a API da Automação do Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Você pode comparar os webhooks a outros métodos para iniciar um runbook em [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

## <a name="details-of-a-webhook"></a>Detalhes de um webhook
A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Você pode fornecer qualquer nome que desejar para um webhook já que o mesmo não aparece para o cliente.  Ele é usado apenas por você para identificar o runbook na Automação do Azure. <br>  Como prática recomendada, você deve atribuir ao webhook um nome relacionado ao cliente que irá usá-lo. |
| URL |A URL do webhook é o endereço exclusivo que um cliente chama um HTTP POST para iniciar o runbook vinculado ao webhook.  Ele é gerado automaticamente quando você cria o webhook.  Você não pode especificar uma URL personalizada. <br> <br>  A URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, ele deve ser tratado como uma senha.  Por motivos de segurança, você pode exibir apenas a URL no Portal do Azure no momento em que o webhook é criado. Você deve anotar a URL em um local seguro para uso futuro. |
| Data de validade |Como um certificado, cada webhook tem uma data de validade após a qual ele não pode mais ser usado.  A data de validade pode ser modificada após a criação do webhook. |
| Habilitado |Um webhook é habilitado por padrão quando ele é criado.  Se você defini-lo como Desabilitado, nenhum cliente poderá usá-lo.  Você pode definir a propriedade **Habilitado** quando você cria o webhook ou a qualquer momento quando ele é criado. |

### <a name="parameters"></a>Parâmetros
Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado por esse webhook. O webhook deve incluir valores de quaisquer parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Um valor de parâmetro configurado para um Webhook pode ser modificado até mesmo depois da criação do Webhoook. Vários webhooks vinculados a um único runbook podem usar valores de parâmetros diferentes.

Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook.  Para receber dados do cliente, o runbook pode aceitar um parâmetro único chamado **$WebhookData** do tipo [objeto] que conterá os dados que o cliente inclui na solicitação POST.

![Propriedades de Webhookdata](media/automation-webhooks/webhook-data-properties.png)

O objeto **$WebhookData** terá as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName |O nome do webhook. |
| RequestHeader |Tabela de hash contendo os cabeçalhos da solicitação de POST de entrada. |
| RequestBody |O corpo da solicitação de POST de entrada.  Isso manterá qualquer formatação, como a cadeia de caracteres, JSON, XML ou os dados codificados de formulário. O runbook deve ser escrito para trabalhar com o formato de dados esperado. O runbook deve ser escrito para trabalhar com o formato de dados esperado. |

Nenhuma configuração do webhook é necessária para suporte ao parâmetro **$WebhookData** e o runbook não é necessário para aceitá-lo.  Se o runbook não definir o parâmetro, os detalhes da solicitação enviada pelo cliente são ignorados.

Se você especificar um valor para $WebhookData quando você cria o webhook, esse valor será substituído quando o webhook inicia o runbook com os dados da solicitação POST do cliente, mesmo que o cliente não inclua todos os dados no corpo da solicitação.  Se você iniciar um runbook que tenha $WebhookData usando um método que não seja um webhook, você pode fornecer um valor para $Webhookdata que será reconhecidos pelo runbook.  Esse valor deve ser um objeto com as mesmas [propriedades](#details-of-a-webhook) que $Webhookdata, para que o runbook possa trabalhar corretamente com ele como se estivesse trabalhando com o WebhookData real transmitido por um webhook.

Por exemplo, se você estiver iniciando o runbook a seguir no Portal do Azure e desejar transmitir algum exemplo de WebhookData para teste, como WebhookData é um objeto, ele deve ser transmitido como JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o runbook acima, se você tiver as seguintes propriedades para o parâmetro WebhookData:

1. WebhookName: *MyWebhook*
2. RequestHeader: *From=Test User*
3. RequestBody: *[“VM1”, “VM2”]*

Em seguida, você transmitiria o seguinte valor JSON na interface do usuário para o parâmetro WebhookData:  

* {"WebhookName":"MyWebhook", "RequestHeader":{"From":"Test User"}, "RequestBody":"[\"VM1\",\"VM2\"]"}

![Iniciar o parâmetro WebhookData na interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Os valores de todos os parâmetros de entrada são registrados com o trabalho de runbook.  Isso significa que qualquer entrada fornecida pelo cliente na solicitação webhook será conectada e estará disponível para qualquer pessoa com acesso ao trabalho de automação.  Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.
>

## <a name="security"></a>Segurança
A segurança de um webhook conta com a privacidade da sua URL que contém um token de segurança que permite que ele seja invocado. A Automação do Azure não executa nenhuma autenticação na solicitação desde que ela seja feita para a URL correta. Por esse motivo, os webhooks não devem ser usados para runbooks que executam funções altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Você pode incluir a lógica no runbook para determinar o que foi chamado por um webhook verificando a propriedade **WebhookName** do parâmetro $WebhookData. O runbook pode executar uma validação adicional procurando por informações específicas nas propriedades **RequestHeader** ou **RequestBody**.

Outra estratégia é o runbook realizar alguma validação de uma condição externa quando ele receber uma solicitação webhook.  Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub.  O runbook pode se conectar ao GitHub para validar que uma nova confirmação realmente ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criando um webhook
Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no Portal do Azure.

1. Na **folha de Runbooks** no Portal do Azure, clique no runbook que o webhook começará a exibir a sua folha de detalhes.
2. Clique em **Webhook** na parte superior da folha para abrir a folha **Adicionar webhook**. <br>
   ![Botão Webhooks](media/automation-webhooks/webhooks-button.png)
3. Clique em **Criar novo webhook** para abrir a **folha Criar webhook**.
4. Especifique um **Nome** e uma **Data de validade** para o webhook e se ele deve ser habilitado. Consulte [Detalhes de um webhook](#details-of-a-webhook) para obter mais informações sobre essas propriedades.
5. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook.  Em seguida, anote-o em um local seguro.  **Quando você cria o webhook, não é possível recuperar a URL novamente.** <br>
   ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)
6. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook.  Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que os valores sejam fornecidos.
7. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook
Para usar um webhook após ele ter sido criado, o aplicativo cliente deve emitir um POST HTTP com a URL para o webhook.  A sintaxe do webhook será no formato a seguir.

    http://<Webhook Server>/token?=<Token Value>

O cliente receberá um dos seguintes códigos de retorno da solicitação POST.  

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceita |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Solicitação incorreta |A solicitação não foi aceita por um dos motivos a seguir. <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não encontrado |A solicitação não foi aceita por um dos motivos a seguir. <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro.  Envie a solicitação novamente. |

Supondo que a solicitação seja bem-sucedida, a resposta webhook contém a ID de trabalho no formato JSON da seguinte maneira. Ela conterá uma ID de trabalho única, mas o formato JSON permite possíveis aprimoramentos futuros.

    {"JobIds":["<JobId>"]}  

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído.  Ele pode determinar essas informações usando a ID de trabalho com outro método como [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) ou[ API de Automação do Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### <a name="example"></a>Exemplo
O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook.  Observe que qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook; o Windows PowerShell é usado aqui apenas como um exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. Também incluímos informações sobre quem está iniciando o runbook e a data e a hora que ele está sendo iniciado no cabeçalho da solicitação.      

    $uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
    $headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}

    $vms  = @(
                @{ Name="vm01";ServiceName="vm01"},
                @{ Name="vm02";ServiceName="vm02"}
            )
    $body = ConvertTo-Json -InputObject $vms

    $response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
    $jobid = ConvertFrom-Json $response


A imagem a seguir mostra as informações de cabeçalho (usando um rastreamento [Fiddler](http://www.telerik.com/fiddler) ) desta solicitação. Isso inclui os cabeçalhos padrão de uma solicitação HTTP, além dos cabeçalhos personalizados de Data e Origem que adicionamos.  Cada um desses valores está disponível para o runbook na propriedade **RequestHeaders** de **WebhookData**.

![Botão Webhooks](media/automation-webhooks/webhook-request-headers.png)

A imagem a seguir mostra o corpo da solicitação (usando um rastreamento [Fiddler](http://www.telerik.com/fiddler)) que está disponível para o runbook na propriedade **RequestBody** do **WebhookData**. Ela é formatada como JSON, pois esse era o formato que foi incluído no corpo da solicitação.     

![Botão Webhooks](media/automation-webhooks/webhook-request-body.png)

A imagem a seguir mostra a solicitação sendo enviada do Windows PowerShell e a resposta resultante.  A ID do trabalho é extraída da resposta e convertida em uma cadeia de caracteres.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

O seguinte exemplo de runbook aceita a solicitação do exemplo anterior e inicia as máquinas virtuais especificadas no corpo da solicitação.

    workflow Test-StartVirtualMachinesFromWebhook
    {
        param (
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {

            # Collect properties of WebhookData
            $WebhookName     =     $WebhookData.WebhookName
            $WebhookHeaders =     $WebhookData.RequestHeader
            $WebhookBody     =     $WebhookData.RequestBody

            # Collect individual headers. VMList converted from JSON.
            $From = $WebhookHeaders.From
            $VMList = ConvertFrom-Json -InputObject $WebhookBody
            Write-Output "Runbook started from webhook $WebhookName by $From."

            # Authenticate to Azure resources
            $Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
            Add-AzureAccount -Credential $Cred

            # Start each virtual machine
            foreach ($VM in $VMList)
            {
                $VMName = $VM.Name
                Write-Output "Starting $VMName"
                Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
            }
        }
        else {
            Write-Error "Runbook mean to be started only from webhook."
        }
    }


## <a name="starting-runbooks-in-response-to-azure-alerts"></a>Iniciar runbooks em resposta a um alerta do Azure
Runbooks habilitados para Webhook podem ser usados para reagir a [alertas do Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Recursos no Azure podem ser monitorados por meio da coleta de estatísticas, como desempenho, disponibilidade e uso, com a ajuda dos alertas do Azure. Você pode receber um alerta com base em métricas de monitoramento ou em eventos para seus recursos do Azure. No momento, as Contas de Automação oferecem suporte apenas para métricas. Quando o valor de uma métrica especificada ultrapassar o limite atribuído ou se o evento configurado for disparado e uma notificação for enviada ao administrador ou coadministradores do serviço para resolver o alerta, consulte [alertas do Azure](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) para obter mais informações sobre eventos e métricas.

Além de usar alertas do Azure como um sistema de notificação, você também pode disparar runbooks em resposta a alertas. A Automação do Azure fornece a capacidade de executar runbooks habilitados para webhook com alertas do Azure. Quando uma métrica excede o valor do limite configurado, a regra do alerta fica ativa e dispara o webhook de automação, que por sua vez executa o runbook.

![Webhooks](media/automation-webhooks/webhook-alert.jpg)

### <a name="alert-context"></a>Contexto do alerta
Considere um recurso do Azure como uma máquina virtual, a utilização da CPU dessa máquina é uma das principais métricas de desempenho. Se a utilização da CPU for 100% ou mais do que uma determinada quantidade por longo período de tempo, talvez você queira reiniciar a máquina virtual para corrigir o problema. Isso pode ser resolvido por meio da configuração de uma regra de alerta para a máquina virtual e essa regra usa o percentual de CPU como sua métrica. O percentual de CPU aqui é usado apenas como um exemplo, mas existem muitas outras métricas que podem ser configuradas para os recursos do Azure, e reiniciar a máquina virtual é uma ação executada para corrigir esse problema. Você pode configurar o runbook para executar outras ações.

Quando esta regra de alerta é ativada e dispara o runbook habilitado para webhook, ela envia o contexto do alerta para o runbook. O [contexto de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) contém detalhes que incluem **SubscriptionID**, **ResourceGroupName**, **ResourceName**, **ResourceType**, **ResourceId** e **Timestamp**, que são necessários para o runbook identificar o recurso em que ele realizará a ação. O contexto do alerta é inserido na parte de corpo do objeto **WebhookData** enviado para o runbook e pode ser acessado com a propriedade **Webhook.RequestBody**

### <a name="example"></a>Exemplo
Crie uma máquina virtual do Azure em sua assinatura e associe um [alerta para monitorar a métrica de percentual de CPU](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). Ao criar o alerta, certifique-se de preencher o campo webhook com a URL do webhook que foi gerada durante a criação do webhook.

O seguinte exemplo de runbook é acionado quando a regra de alerta é ativada e coleta os parâmetros de contexto do Alerta que são necessários para o runbook identificar o recurso em que ele vai realizar ação.

    workflow Invoke-RunbookUsingAlerts
    {
        param (      
            [object]$WebhookData
        )

        # If runbook was called from Webhook, WebhookData will not be null.
        if ($WebhookData -ne $null) {   
            # Collect properties of WebhookData.
            $WebhookName    =   $WebhookData.WebhookName
            $WebhookBody    =   $WebhookData.RequestBody
            $WebhookHeaders =   $WebhookData.RequestHeader

            # Outputs information on the webhook name that called This
            Write-Output "This runbook was started from webhook $WebhookName."


            # Obtain the WebhookBody containing the AlertContext
            $WebhookBody = (ConvertFrom-Json -InputObject $WebhookBody)
            Write-Output "`nWEBHOOK BODY"
            Write-Output "============="
            Write-Output $WebhookBody

            # Obtain the AlertContext     
            $AlertContext = [object]$WebhookBody.context

            # Some selected AlertContext information
            Write-Output "`nALERT CONTEXT DATA"
            Write-Output "==================="
            Write-Output $AlertContext.name
            Write-Output $AlertContext.subscriptionId
            Write-Output $AlertContext.resourceGroupName
            Write-Output $AlertContext.resourceName
            Write-Output $AlertContext.resourceType
            Write-Output $AlertContext.resourceId
            Write-Output $AlertContext.timestamp

            # Act on the AlertContext data, in our case restarting the VM.
            # Authenticate to your Azure subscription using Organization ID to be able to restart that Virtual Machine.
            $cred = Get-AutomationPSCredential -Name "MyAzureCredential"
            Add-AzureAccount -Credential $cred
            Select-AzureSubscription -subscriptionName "Visual Studio Ultimate with MSDN"

            #Check the status property of the VM
            Write-Output "Status of VM before taking action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
            Write-Output "Restarting VM"

            # Restart the VM by passing VM name and Service name which are same in this case
            Restart-AzureVM -ServiceName $AlertContext.resourceName -Name $AlertContext.resourceName
            Write-Output "Status of VM after alert is active and takes action"
            Get-AzureVM -Name $AlertContext.resourceName -ServiceName $AlertContext.resourceName
        }
        else  
        {
            Write-Error "This runbook is meant to only be started from a webhook."  
        }  
    }



## <a name="next-steps"></a>Próximas etapas
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, confira [Iniciar um runbook](automation-starting-a-runbook.md).
* Para saber mais sobre como exibir o Status de um Trabalho de Runbook, consulte [Execução de runbook na Automação do Azure](automation-runbook-execution.md).
* Para saber como usar a Automação do Azure para agir em Alertas do Azure, veja [Corrigir alertas de VM do Azure com runbooks da Automação](automation-azure-vm-alert-integration.md).
