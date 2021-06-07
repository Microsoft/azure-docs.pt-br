---
title: 'Tutorial: Rotear eventos de alteração de estado da política para a Grade de Eventos com a CLI do Azure'
description: Neste tutorial, você configura a Grade de Eventos para escutar eventos de alteração de estado de política e chamar um webhook.
ms.date: 03/29/2021
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1fe87e4fd3349df7d8f5d57b2b2d95f95ed3fba8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105734868"
---
# <a name="tutorial-route-policy-state-change-events-to-event-grid-with-azure-cli"></a>Tutorial: Rotear eventos de alteração de estado da política para a Grade de Eventos com a CLI do Azure

Neste artigo, você aprende a configurar assinaturas de evento do Azure Policy para enviar eventos de alteração de estado de política a um ponto de extremidade da Web. Usuários do Azure Policy podem assinar eventos emitidos quando ocorrem alterações de estado da política em recursos. Esses eventos podem disparar webhooks, [Azure Functions](../../../azure-functions/index.yml), [Filas do Armazenamento do Azure](../../../storage/queues/index.yml) ou qualquer outro manipulador de eventos que seja compatível com a [Grade de Eventos do Azure](../../../event-grid/index.yml). Normalmente, você envia eventos para um ponto de extremidade que processa os dados de evento e realiza ações. No entanto, para simplificar este tutorial, você enviará os eventos para um aplicativo Web que coleta e exibe as mensagens.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

- Este guia de início rápido exige que você execute a CLI do Azure versão 2.0.76 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

- Mesmo que já tenha usado o Azure Policy ou a Grade de Eventos, registre novamente seus respectivos provedores de recursos:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Provider register: Register the Azure Event Grid provider
  az provider register --namespace Microsoft.EventGrid
  ```

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Os tópicos de Grade de Eventos são recursos do Azure e devem ser colocados em um grupo de recursos do Azure. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). 

O exemplo a seguir cria um grupo de recursos chamado `<resource_group_name>` na localização _westus_. Substitua `<resource_group_name>` por um nome exclusivo para o grupo de recursos.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az group create --name <resource_group_name> --location westus
```

## <a name="create-an-event-grid-system-topic"></a>Criar um tópico do sistema da Grade de Eventos

Agora que temos um grupo de recursos, criamos um [tópico do sistema](../../../event-grid/system-topics.md). Um tópico do sistema na Grade de Eventos representa um ou mais eventos publicados pelos serviços do Azure, como o Azure Policy e os Hubs de Eventos do Azure. Este tópico do sistema usa o tipo de tópico `Microsoft.PolicyInsights.PolicyStates` para alterações de estado do Azure Policy. Substitua `<SubscriptionID>` no parâmetro **scope** pela ID de sua assinatura e `<resource_group_name>` no parâmetro **resource-grouo** pelo grupo de recursos criado anteriormente.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az eventgrid system-topic create --name PolicyStateChanges --location global --topic-type Microsoft.PolicyInsights.PolicyStates --source "/subscriptions/<SubscriptionID>" --resource-group "<resource_group_name>"
```

## <a name="create-a-message-endpoint"></a>Criar um ponto de extremidade de mensagem

Antes de assinar o tópico, vamos criar o ponto de extremidade para a mensagem do evento. Normalmente, o ponto de extremidade executa ações com base nos dados de evento. Para simplificar este início rápido, você implanta um [aplicativo Web criado previamente](https://github.com/Azure-Samples/azure-event-grid-viewer) que exibe as mensagens de eventos. A solução implantada inclui um plano do Serviço de Aplicativo, um aplicativo Web do Aplicativo do Serviço de e o código-fonte do GitHub.

Substitua `<your-site-name>` por um nome exclusivo para o aplicativo Web. O nome do aplicativo Web deve ser exclusivo, pois é parte de uma entrada DNS.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=<your-site-name> hostingPlanName=viewerhost
```

A implantação pode levar alguns minutos para ser concluída. Depois que a implantação for bem-sucedida, exiba seu aplicativo Web para garantir que ele esteja em execução. Em um navegador da Web, navegue até: `https://<your-site-name>.azurewebsites.net`

Você deve ver o site sem mensagens exibidas no momento.

## <a name="subscribe-to-the-system-topic"></a>Assinar o tópico do sistema

Assine um tópico para indicar à Grade de Eventos quais eventos você deseja acompanhar e para onde enviar esses eventos. O exemplo a seguir assina o tópico do sistema que você criou e transmite a URL do aplicativo Web como o ponto de extremidade para receber notificação de eventos. Substitua `<event_subscription_name>` por um nome para a assinatura de evento. Em `<resource_group_name>` e `<your-site-name>`, use os valores criados anteriormente.

O ponto de extremidade para seu aplicativo Web deve incluir o sufixo `/api/updates/`.

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Create the subscription
az eventgrid system-topic event-subscription create \
  --name <event_subscription_name> \
  --resource-group <resource_group_name> \
  --system-topic-name PolicyStateChanges \
  --endpoint https://<your-site-name>.azurewebsites.net/api/updates
```

Exiba novamente o seu aplicativo Web e observe que um evento de validação de assinatura foi enviado a ele. Selecione o ícone de olho para expandir os dados de evento. A Grade de Eventos envia o evento de validação de modo que o ponto de extremidade possa verificar se ele deseja receber os dados de evento. O aplicativo Web inclui o código para validar a assinatura.

:::image type="content" source="../media/route-state-change-events/view-subscription-event.png" alt-text="Captura de tela do evento de validação da assinatura da Grade de Eventos no aplicativo Web predefinido.":::

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, você cria uma atribuição de política e atribui a definição **Rxigir uma marca em grupos de recursos**. Essa definição de política identifica grupos de recursos que não têm a marca configurada durante a atribuição de política.

Execute o seguinte comando para criar uma atribuição de política com escopo para o grupo de recursos criado para manter o tópico da grade de eventos:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az policy assignment create --name 'requiredtags-events' --display-name 'Require tag on RG' --scope '<ResourceGroupScope>' --policy '<policy definition ID>' --params '{ "tagName": { "value": "EventTest" } }'
```

O comando anterior usa as seguintes informações:

- **Nome** - O nome real da atribuição. Neste exemplo, foi usado _requiredtags-events_.
- **DisplayName** - O nome de exibição da atribuição de política. Nesse caso, você está usando _Exigir marca no RG_.
- **Escopo**: um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos. Substitua o &lt;escopo&gt; pelo nome do seu grupo de recursos. O formato do escopo de um grupo de recursos é `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>`.
- **Política**: a ID de definição da política, com base naquela que você está usando para criar a atribuição. Nesse caso, a ID da definição de política _Exigir uma marca em grupos de recursos_. Para obter a ID de definição da política, execute este comando: `az policy definition list --query "[?displayName=='Require a tag on resource groups']"`

Depois de criar a atribuição de política, aguarde a exibição de uma notificação de evento **Microsoft.PolicyInsights.PolicyStateCreated** no aplicativo Web. O grupo de recursos que criamos mostra um valor `data.complianceState` de _NonCompliant_ para iniciar.

:::image type="content" source="../media/route-state-change-events/view-policystatecreated-event.png" alt-text="Captura de tela do evento Estado de Política Criado da assinatura da Grade de Eventos criado para o grupo de recursos no aplicativo Web predefinido.":::

> [!NOTE]
> Se o grupo de recursos herdar outras atribuições de política da assinatura ou da hierarquia do grupo de gerenciamento, eventos para cada uma também serão exibidos. Confirme se o evento é referente à atribuição neste tutorial avaliando a propriedade `data.policyDefinitionId`.

## <a name="trigger-a-change-on-the-resource-group"></a>Disparar uma alteração no grupo de recursos

Para tornar o grupo de recursos compatível, uma marca com o nome **EventTest** é necessária. Adicione a marca ao grupo de recursos com o seguinte comando substituindo `<SubscriptionID>` pela sua ID de assinatura e `<ResourceGroup>` pelo nome do grupo de recursos:

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az tag create --resource-id '/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup>' --tags EventTest=true
```

Depois de adicionar a marca necessária ao grupo de recursos, aguarde até que uma notificação de evento **Microsoft.PolicyInsights.PolicyStateChanged** apareça no aplicativo Web. Expanda o evento e o valor `data.complianceState` agora mostra _Compliant_.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com esse aplicativo Web e essa assinatura de evento do Azure Policy, não limpe os recursos criados neste artigo. Caso contrário, use os comandos a seguir para excluir os recursos criados neste artigo.

Substitua `<resource_group_name>` pelo recurso de grupo criado acima.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como criar tópicos e assinaturas de eventos para o Azure Policy, saiba mais sobre os eventos do alteração do estado da política e como a Grade de Eventos pode ajudar você:

- [Reagindo a eventos de alteração de estado do Azure Policy](../concepts/event-overview.md)
- [Detalhes do esquema do Azure Policy para a Grade de Eventos](../../../event-grid/event-schema-policy.md)
- [Sobre a Grade de Eventos](../../../event-grid/overview.md)