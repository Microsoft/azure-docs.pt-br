---
title: Iniciar um runbook de Automação do Azure com um webhook
description: Um webhook que permite que um cliente inicie um runbook na Automação do Azure a partir de uma chamada HTTP.  Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 153bb0304102906f7be64ae55dd0e0f6bb8d7146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304522"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Iniciar um runbook de Automação do Azure com um webhook

O *webhook* permite que você inicie um runbook específico na Automação do Azure por meio de uma única solicitação HTTP. Isso permite que os serviços externos, como serviços de DevOps do Azure, GitHub, logs do Azure Monitor ou aplicativos personalizados iniciem runbooks sem implementar uma solução completa usando a API de automação do Azure.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Você pode comparar os webhooks a outros métodos para iniciar um runbook em [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

> [!NOTE]
> Não há suporte para usar um webhook para iniciar um runbook Python.

## <a name="details-of-a-webhook"></a>Detalhes de um webhook

A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| NOME |Você pode fornecer qualquer nome que desejar para um webhook já que ele não aparece para o cliente. Ele é usado apenas por você para identificar o runbook na Automação do Azure. <br> Como melhor prática, você deve atribuir ao webhook um nome relacionado ao cliente que o utiliza. |
| URL |A URL do webhook é o endereço exclusivo que um cliente chama um HTTP POST para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, ele deve ser tratado como uma senha. Por motivos de segurança, você pode exibir apenas a URL no Portal do Azure no momento em que o webhook é criado. Anote a URL em um local seguro para uso futuro. |
| Data de validade |Como um certificado, cada webhook tem uma data de validade após a qual ele não pode mais ser usado. A data de expiração pode ser modificada após a criação do webhook, contanto que ele não esteja expirado. |
| Enabled |Um webhook é habilitado por padrão quando ele é criado. Se você defini-lo como Desabilitado, nenhum cliente poderá usá-lo. Você pode definir a propriedade **Habilitado** quando você cria o webhook ou a qualquer momento quando ele é criado. |

### <a name="parameters"></a>parâmetros

Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado por esse webhook. O webhook deve incluir valores de quaisquer parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado até mesmo depois da criação do webhoook. Vários webhooks vinculados a um único runbook podem usar valores de parâmetros diferentes.

Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook. Para receber dados do cliente, o runbook pode aceitar um parâmetro único chamado **$WebhookData**. Esse parâmetro é do tipo [objeto] que contém dados que o cliente inclui na solicitação POST.

![Propriedades de Webhookdata](media/automation-webhooks/webhook-data-properties.png)

O objeto **$WebhookData** tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| WebhookName |O nome do webhook. |
| RequestHeader |Tabela de hash contendo os cabeçalhos da solicitação de POST de entrada. |
| RequestBody |O corpo da solicitação de POST de entrada. Isso mantém qualquer formatação, como a cadeia de caracteres, JSON, XML ou dados codificados de formulário. O runbook deve ser escrito para trabalhar com o formato de dados esperado. O runbook deve ser escrito para trabalhar com o formato de dados esperado. |

Nenhuma configuração do webhook é necessária para suporte ao parâmetro **$WebhookData**, e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, os detalhes da solicitação enviada pelo cliente serão ignorados.

Se você especificar um valor para $WebhookData quando criar o webhook, esse valor será substituído quando o webhook iniciar o runbook com os dados da solicitação POST do cliente, mesmo que o cliente não inclua todos os dados no corpo da solicitação. Se você iniciar um runbook que tenha $WebhookData usando um método que não seja um webhook, você poderá fornecer um valor para $Webhookdata que será reconhecido pelo runbook. Esse valor deve ser um objeto com as mesmas [propriedades](#details-of-a-webhook) que $Webhookdata, para que o runbook possa trabalhar corretamente com ele como se estivesse trabalhando com o WebhookData real transmitido por um webhook.

Por exemplo, se você estiver iniciando o runbook a seguir no portal do Azure e desejar transmitir algum exemplo de WebhookData para teste, como WebhookData é um objeto, ele deverá ser transmitido como JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o runbook a seguir, se você tiver as seguintes propriedades para o parâmetro WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

Em seguida, você transmitiria o seguinte valor JSON na interface do usuário para o parâmetro WebhookData. O exemplo a seguir com o carro retorna e caracteres de nova linha correspondem ao formato que é transmitido de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData na interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Os valores de todos os parâmetros de entrada são registrados com o trabalho de runbook. Isso significa que qualquer entrada fornecida pelo cliente na solicitação webhook será conectada e estará disponível para qualquer pessoa com acesso ao trabalho de automação.  Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

## <a name="security"></a>Segurança

A segurança de um webhook conta com a privacidade da sua URL, que contém um token de segurança que permite que ele seja invocado. A Automação do Azure não executa nenhuma autenticação na solicitação desde que ela seja feita para a URL correta. Por esse motivo, os webhooks não devem ser usados para runbooks que executam funções altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Você pode incluir a lógica no runbook para determinar o que foi chamado por um webhook verificando a propriedade **WebhookName** do parâmetro $WebhookData. O runbook pode executar uma validação adicional procurando por informações específicas nas propriedades **RequestHeader** ou **RequestBody**.

Outra estratégia é o runbook realizar alguma validação de uma condição externa quando ele receber uma solicitação webhook. Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub. O runbook pode se conectar ao GitHub para validar que uma nova confirmação ocorreu antes de continuar.

## <a name="creating-a-webhook"></a>Criando um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no Portal do Azure.

1. Na **página de Runbooks** no portal do Azure, clique no runbook que o webhook começa para exibir sua página de detalhes. Verifique se o runbook **Status** é **publicado**.
2. Clique em **Webhook** na parte superior da página para abrir a página **Adicionar webhook**.
3. Clique em **Criar novo webhook** para abrir a **página Criar webhook**.
4. Especifique um **Nome** e uma **Data de validade** para o webhook e se ele deve ser habilitado. Consulte [Detalhes de um webhook](#details-of-a-webhook) para obter mais informações sobre essas propriedades.
5. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook. Em seguida, anote-o em um local seguro. **Quando você cria o webhook, não é possível recuperar a URL novamente.**

   ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que os valores sejam fornecidos.
1. Clique em **Criar** para criar o webhook.

## <a name="using-a-webhook"></a>Usando um webhook

Para usar um webhook após ele ter sido criado, o aplicativo cliente deve emitir um POST HTTP com a URL para o webhook. A sintaxe do webhook será no formato a seguir:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de retorno da solicitação POST.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceita |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Solicitação incorreta |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não encontrado |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação seja bem-sucedida, a resposta webhook contém a ID de trabalho no formato JSON da seguinte maneira. Ela conterá uma ID de trabalho única, mas o formato JSON permite possíveis aprimoramentos futuros.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído. Ele pode determinar essas informações usando a ID de trabalho com outro método como [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) ou[ API de Automação do Azure](/rest/api/automation/job).

## <a name="renew-webhook"></a>Renovar um webhook

Quando um webhook é criado, ele tem validade de um ano. Depois desse ano, o webhook expira automaticamente. Depois que um webhook expirar, ele não pode ser reativado. Deve ser removido e recriado. Se um webhook não alcançar seu vencimento, este poderá ser estendido.

Para estender um webhook, navegue até o runbook que contém o webhook. Selecione **Webhooks** em **Recursos**. Clique no webhook que você deseja estender; essa ação abre a página **Webhook**.  Escolha uma nova data e a hora de vencimento e clique em **Salvar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O runbook de exemplo a seguir aceita os dados do webhook e inicia as máquinas virtuais especificadas no corpo da solicitação. Para testar esse runbook em sua Conta de Automação em **Runbooks**, clique em **+ Adicionar um runbook**. Se você não souber como criar um runbook, confira [Criando um runbook](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Testar o exemplo

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook. O Windows PowerShell é usado aqui como um exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. O runbook também valida que os cabeçalhos contêm uma mensagem definida para confirmar que o chamador de webhook é válido.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

O exemplo a seguir mostra o corpo da solicitação que está disponível para o runbook na propriedade **RequestBody** do **WebhookData**. Este valor é formatado como JSON, pois esse era o formato que foi incluído no corpo da solicitação.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

A imagem a seguir mostra a solicitação sendo enviada do Windows PowerShell e a resposta resultante. A ID do trabalho é extraída da resposta e convertida em uma cadeia de caracteres.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber como usar a Automação do Azure para agir em Alertas do Azure, confira [Usar um alerta para disparar um runbook da Automação do Azure](automation-create-alert-triggered-runbook.md).

