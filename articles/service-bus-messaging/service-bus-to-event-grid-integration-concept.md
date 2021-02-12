---
title: Barramento de Serviço do Azure para a visão geral da integração da Grade de Eventos | Microsoft Docs
description: Este artigo fornece uma descrição de como o sistema de mensagens do barramento de serviço do Azure se integra à grade de eventos do Azure.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 71ee21c971b71c4000a123d1561e7e93d21203e1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629140"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Visão geral da integração do Barramento de Serviço com a Grade de Eventos

O Barramento de Serviço do Azure lançou uma nova integração à Grade de Eventos do Azure. O principal cenário que esse recurso permite é que as filas ou assinaturas do Barramento de Serviço que têm um baixo volume de mensagens não precisam ter um receptor que verifica continuamente a existência de mensagens. 

Agora, o Barramento de Serviço pode emitir eventos para a Grade de Eventos quando houver mensagens em uma fila ou assinatura, quando nenhum receptor estiver presente. É possível criar assinaturas da Grade de Eventos para seus namespaces do Barramento de Serviço, escutar esses eventos e reagir aos eventos iniciando um receptor. Com esse recurso, você pode usar o Barramento de Serviço em modelos de programação reativos.

Para habilitar o recurso, você precisa dos itens a seguir:

* Um namespace Premium de Barramento de Serviço com pelo menos uma fila ou um tópico do Barramento de Serviço com pelo menos uma assinatura.
* Acesso de colaborador para o namespace do Barramento de Serviço.
* Além disso, você precisa de uma assinatura da Grade de Eventos para o namespace do Barramento de Serviço. Essa assinatura recebe uma notificação da Grade de Eventos sobre mensagens a serem coletadas. Os assinantes típicos podem ser o recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure, o Azure Functions ou um webhook em contato com um aplicativo Web. Em seguida, o assinante processa as mensagens. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Verifique se você tem acesso de colaborador
Vá para o namespace do barramento de serviço e selecione **controle de acesso (iam)** e selecione a guia **atribuições de função** . Verifique se você tem o acesso de colaborador ao namespace. 

### <a name="events-and-event-schemas"></a>Eventos e esquemas de evento

Hoje, o Barramento de Serviço envia eventos para dois cenários:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#deadletter-messages-available-event)
* [ActiveMessagesAvailablePeriodicNotifications](#active-messages-available-periodic-notifications)
* [DeadletterMessagesAvailablePeriodicNotifications](#deadletter-messages-available-periodic-notifications)

Além disso, o Barramento de Serviço usa a segurança padrão da Grade de Eventos e [mecanismos de autenticação](../event-grid/security-authentication.md).

Para saber mais, confira [Esquemas de evento da Grade de Eventos do Azure](../event-grid/event-schema.md).

#### <a name="active-messages-available-event"></a>Evento Mensagens Ativas Disponíveis

Esse evento é gerado se houver mensagens ativas em uma fila ou assinatura, e nenhum receptor escutando.

O esquema desse evento é este:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="deadletter-messages-available-event"></a>Evento de mensagens mortas disponíveis

Você tem pelo menos um evento por fila de mensagens mortas, que tem mensagens e receptores não ativos.

O esquema desse evento é este:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Notificações periódicas disponíveis de mensagens ativas

Esse evento será gerado periodicamente se você tiver mensagens ativas na fila ou assinatura específica, mesmo se houver ouvintes ativos nessa fila ou assinatura específica.

O esquema para o evento é o seguinte.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Notificações periódicas disponíveis de mensagens mortas

Esse evento será gerado periodicamente se você tiver mensagens mortas na fila ou assinatura específica, mesmo se houver ouvintes ativos na entidade de mensagens mortas dessa fila ou assinatura específica.

O esquema para o evento é o seguinte.

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Quantos eventos são emitidos e com que frequência?

Se você tiver várias filas e tópicos ou assinaturas no namespace, terá pelo menos um evento por fila e um por assinatura. Os eventos serão emitidos imediatamente se não houver mensagens na entidade do Barramento de Serviço e uma nova mensagem chegar. Ou, os eventos serão emitidos a cada dois minutos, a menos que o Barramento de Serviço detecte um receptor ativo. A procura de mensagens não interrompe os eventos.

Por padrão, o Barramento de Serviço emite eventos para todas as entidades no namespace. Se você quiser obter eventos somente para entidades específicas, consulte a próxima seção.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Use filtros para limitar de onde obter eventos

Caso queira obter apenas eventos, por exemplo, de uma fila ou assinatura em seu namespace, use filtros *Começa com* ou *Termina com* fornecidos pela Grade de Eventos. Em algumas interfaces, os filtros são chamados de filtros de *pré* e *sufixo* . Caso você queira obter eventos de várias, mas não todas, filas e assinaturas, é possível criar várias assinaturas diferentes da Grade de Eventos e fornecer um filtro para cada uma.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Criar assinaturas da Grade de Eventos para namespaces do Barramento de Serviço

Há três maneiras de criar assinaturas da Grade de Eventos para namespaces do Barramento de Serviço:

* No portal do Azure
* Em [CLI do Azure](#azure-cli-instructions)
* No [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instruções do Portal do Azure

Para criar uma nova assinatura da Grade de Eventos, faça o seguinte:
1. No Portal do Azure, acesse suas mensagens.
2. No painel esquerdo, selecione a **Grade de Eventos**. 
3. Selecione **assinatura de evento**.  

   A imagem a seguir exibe um namespace que tem uma assinatura da Grade de Eventos:

   ![Assinaturas de Grade de Eventos](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   A imagem a seguir mostra como assinar uma função ou um webhook sem qualquer filtro específico:

   ![21][]

## <a name="azure-cli-instructions"></a>Instruções da CLI do Azure

Primeiro, certifique-se de que tenha a CLI do Azure versão 2.0 ou posterior instalada. [Baixe o instalador](/cli/azure/install-azure-cli). Selecione **Windows + X** e, em seguida, abra um novo console do PowerShell com permissões de administrador. Como alternativa, use um shell de comando dentro do Portal do Azure.

Execute o seguinte código:

 ```azurecli-interactive
az login

az account set -s "<Azure subscription name>"

namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv

az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" --endpoint "<your_function_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Se você estiver usando o BASH 

## <a name="powershell-instructions"></a>Instruções do PowerShell

Verifique se o Azure PowerShell está instalado. [Baixe o instalador](/powershell/azure/install-Az-ps). Selecione **Windows + X** e abra um novo console do PowerShell com permissões de Administrador. Como alternativa, use um shell de comando dentro do Portal do Azure.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>" -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
```

Daqui em diante, você poderá explorar as outras opções de instalação ou testar os eventos que estão fluindo.

## <a name="next-steps"></a>Próximas etapas

* Obter [exemplos](service-bus-to-event-grid-integration-example.md) de Barramento de Serviço e da Grade de Eventos.
* Saiba mais sobre a [grade de eventos](../event-grid/index.yml).
* Saiba mais sobre o [Azure Functions](../azure-functions/index.yml).
* Saiba mais sobre o [Aplicativos Lógicos](../logic-apps/index.yml).
* Saiba mais sobre o [Barramento de Serviço](/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[aprimora]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[Abril]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
