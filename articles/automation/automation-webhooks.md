---
title: Iniciar um runbook da Automação do Azure a partir de um webhook
description: Este artigo informa como usar um webhook para iniciar um runbook na Automação do Azure a partir de uma chamada HTTP.
services: automation
ms.subservice: process-automation
ms.date: 06/24/2020
ms.topic: conceptual
ms.openlocfilehash: df19f32be41b17e13a9da575e828830e29da4e55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98894755"
---
# <a name="start-a-runbook-from-a-webhook"></a>Iniciar runbook a partir de um webhook

Um webhook permite que um serviço externo inicie um runbook específico na Automação do Azure por meio de uma única solicitação HTTP. Os serviços externos incluem Azure DevOps Services, GitHub, logs do Azure Monitor e aplicativos personalizados. Esse serviço pode usar um webhook para iniciar um runbook sem implementar a API de Automação do Azure completa. Você pode comparar os webhooks a outros métodos para iniciar um runbook em [Como iniciar um runbook na Automação do Azure](./start-runbooks.md).

> [!NOTE]
> O uso de um webhook para iniciar um runbook do Python não é compatível.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Para entender os requisitos do cliente para o TLS 1,2 com WebHooks, confira [imposição TLS 1,2 para a automação do Azure](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Propriedades de webhook

A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:--- |:--- |
| Nome |Nome do webhook. É possível fornecer qualquer nome desejado, já que ele não é exposto ao cliente. Ele é usado apenas por você para identificar o runbook na Automação do Azure. Como melhor prática, você deve atribuir ao webhook um nome relacionado ao cliente que o utiliza. |
| URL |URL do webhook. É o endereço exclusivo que um cliente chama com um HTTP POST para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, você deve tratar a URL como uma senha. Por motivos de segurança, só é possível exibir a URL no portal do Azure ao criar o webhook. Anote a URL em um local seguro para uso futuro. |
| Data de validade | Data de validade do webhook, após a qual ele não pode mais ser usado. É possível mudar a data de validade após a criação do webhook, desde que o webhook não tenha expirado. |
| habilitado | Configuração que indica se o webhook está habilitado por padrão quando é criado. Se você definir essa propriedade como desabilitada, nenhum cliente poderá usar o webhook. É possível definir essa propriedade ao criar o webhook ou em qualquer outro momento após sua criação. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parâmetros usados quando o webhook inicia um runbook

Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado. O webhook deve incluir valores de parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Um valor de parâmetro configurado para um webhook pode ser modificado até mesmo depois da criação do webhook. Vários webhooks vinculados a um único runbook podem usar valores de parâmetros diferentes de runbook. Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook.

Para receber dados do cliente, o runbook pode aceitar um único parâmetro chamado `WebhookData`. Esse parâmetro define um objeto que contém dados que o cliente inclui em uma solicitação POST.

![Propriedades de WebhookData](media/automation-webhooks/webhook-data-properties.png)

O parâmetro `WebhookData` tem as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:--- |
| `WebhookName` | Nome do webhook. |
| `RequestHeader` | Tabela de hash contendo os cabeçalhos da solicitação POST de entrada. |
| `RequestBody` | Corpo da solicitação POST de entrada. Esse corpo mantém qualquer formatação de dados, como cadeia de caracteres, JSON, XML ou dados codificados de formulário. O runbook deve ser escrito para trabalhar com o formato de dados esperado. O runbook deve ser escrito para trabalhar com o formato de dados esperado. |

Nenhuma configuração do webhook é necessária para compatibilidade com o parâmetro `WebhookData`, e o runbook não precisa aceitá-lo. Se o runbook não definir o parâmetro, os detalhes da solicitação enviada pelo cliente serão ignorados.

> [!NOTE]
> Ao chamar um webhook, o cliente sempre deve armazenar qualquer valor de parâmetro em caso de falha da chamada. Se houver uma interrupção de rede ou um problema de conexão, o aplicativo não poderá recuperar chamadas de webhook com falha.

Se você especificar um valor para `WebhookData` na criação do webhook, ele será substituído quando o webhook iniciar o runbook com os dados da solicitação POST do cliente. Isso acontece mesmo que o aplicativo não inclua dados no corpo da solicitação. 

Se você iniciar um runbook que define `WebhookData` usando um mecanismo diferente de um webhook, poderá fornecer um valor para `WebhookData` que o runbook reconheça. Esse valor deve ser um objeto com as mesmas [propriedades](#webhook-properties) que o parâmetro `WebhookData` para que o runbook possa trabalhar com ele da mesma forma que funciona com os objetos de `WebhookData` reais transmitidos por um webhook.

Por exemplo, se você estiver iniciando o runbook a seguir no portal do Azure e desejar transmitir alguns dados de webhook de exemplo para teste, deverá transmitir os dados em JSON na interface do usuário.

![Parâmetro WebhookData da interface do usuário](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Para o próximo exemplo de runbook, vamos definir as seguintes propriedades para `WebhookData`:

* **WebhookName**: MyWebhook
* **RequestBody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Agora, transmitimos o seguinte objeto JSON na interface do usuário para o parâmetro `WebhookData`. Este exemplo, com retornos de carro e caracteres de nova linha, corresponde ao formato que é transmitido de um webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Iniciar o parâmetro WebhookData na interface do usuário](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> A Automação do Azure registra em log os valores de todos os parâmetros de entrada com o trabalho de runbook. Isso significa que qualquer entrada fornecida pelo cliente na solicitação webhook será conectada e estará disponível para qualquer pessoa com acesso ao trabalho de automação. Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

## <a name="webhook-security"></a>Segurança de webhook

A segurança de um webhook conta com a privacidade da sua URL, que contém um token de segurança que permite que ele seja invocado. A Automação do Azure não executa nenhuma autenticação em uma solicitação desde que ela seja feita para a URL correta. Por esse motivo, os clientes não devem usar webhooks para runbooks que executam operações altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Considere as seguintes estratégias:

* Será possível incluir lógica em um runbook para determinar se ele é chamado por um webhook. Faça com que o runbook verifique a propriedade `WebhookName` do parâmetro `WebhookData`. O runbook pode executar uma validação adicional procurando informações específicas nas propriedades `RequestHeader` e `RequestBody`.

* Faça com que o runbook execute algumas validações de uma condição externa quando receber uma solicitação de webhook. Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub. O runbook pode se conectar ao GitHub para validar se uma nova confirmação ocorreu antes de continuar.

* A automação do Azure dá suporte a marcas de serviço de rede virtual do Azure, especificamente [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Você pode usar marcas de serviço para definir os controles de acesso à rede em [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) ou no [Firewall do Azure](../firewall/service-tags.md) e disparar WebHooks de dentro de sua rede virtual. As marcas de serviço podem ser usadas no lugar de endereços IP específicos quando você cria regras de segurança. Ao especificar o nome da marca de serviço **GuestAndHybridManagement**  no campo de origem ou de destino apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço de automação. Essa marca de serviço não dá suporte à permissão de controle mais granular restringindo intervalos de IP para uma região específica.

## <a name="create-a-webhook"></a>Criar um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no Portal do Azure.

1. Na página Runbooks no portal do Azure, clique no runbook que o webhook inicia para exibir seus detalhes. Verifique se o campo **Status** do runbook está definido como **Publicado**.
2. Clique em **Webhook** na parte superior da página para abrir a página Adicionar Webhook.
3. Clique em **Criar novo webhook** para abrir a página Criar Webhook.
4. Preencha os campos **Nome** e **Data de Validade** para o webhook e especifique se ele deve ser habilitado. Veja [Propriedades do webhook](#webhook-properties) para obter mais informações sobre essas propriedades.
5. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook. Em seguida, anote-o em um local seguro. 

    > [!NOTE]
    > Quando você cria o webhook, não é possível recuperar a URL novamente.

   ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que você forneça valores.

2. Clique em **Criar** para criar o webhook.

## <a name="use-a-webhook"></a>Usar um webhook

Para usar um webhook depois que ele tiver sido criado, o cliente deverá emitir uma solicitação HTTP `POST` com a URL para o webhook. A sintaxe do é:

```http
http://<Webhook Server>/token?=<Token Value>
```

O cliente recebe um dos seguintes códigos de retorno da solicitação `POST`.

| Código | Texto | Descrição |
|:--- |:--- |:--- |
| 202 |Aceita |A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 |Solicitação incorreta |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li>  </ul> |
| 404 |Não encontrado |A solicitação não foi aceita por um dos motivos a seguir: <ul> <li>O webhook não foi encontrado.</li> <li>O runbook não foi encontrado.</li> <li>A conta não foi encontrada.</li>  </ul> |
| 500 |Erro interno do servidor |A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação seja bem-sucedida, a resposta do webhook conterá a ID de trabalho no formato JSON conforme mostrado abaixo. Ela contém uma ID de trabalho única, mas o formato JSON permite possíveis aprimoramentos futuros.

```json
{"JobIds":["<JobId>"]}
```

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído. Ele pode descobrir essas informações usando a ID de trabalho com outro mecanismo, como [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) ou a [API de Automação do Azure](/rest/api/automation/job).

## <a name="renew-a-webhook"></a>Renovar um webhook

Quando um webhook é criado, ele tem um período de validade de dez anos, após o qual ele expira automaticamente. Depois que um webhook vencer, você não poderá reativá-lo. Só é possível removê-lo e, depois, recriá-lo. 

Você pode estender um webhook que não atingiu seu prazo de validade. Para estender um webhook:

1. Navegue até o runbook que contém o webhook. 
2. Selecione **Webhooks** em **Recursos**. 
3. Clique no webhook que você deseja estender. 
4. Na página Webhook, escolha uma nova data e hora de validade e clique em **Salvar**.

## <a name="sample-runbook"></a>Runbook de exemplo

O runbook de exemplo a seguir aceita os dados do webhook e inicia as máquinas virtuais especificadas no corpo da solicitação. Para testar esse runbook em sua conta de Automação, em **Runbooks**, clique em **Criar um Runbook**. Se você não souber como criar um runbook, confira [Criando um runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> Para runbooks não gráficos do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Você pode usar esses cmdlets ou pode [atualizar seus módulos](automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação.

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

    # Retrieve VMs from Webhook request body
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
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>O exemplo de teste

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook. O Windows PowerShell é usado aqui como exemplo.

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

O exemplo a seguir mostra o corpo da solicitação que está disponível para o runbook na propriedade `RequestBody` de `WebhookData`. Esse valor é formatado em JSON para ser compatível com o formato incluído no corpo da solicitação.

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

* Para disparar um runbook usando um alerta, confira [Usar um alerta para disparar um runbook de Automação do Azure](automation-create-alert-triggered-runbook.md).