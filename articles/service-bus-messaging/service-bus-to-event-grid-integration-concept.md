---
title: Barramento de Serviço do Azure para a visão geral da integração da Grade de Eventos | Microsoft Docs
description: Este artigo fornece uma descrição de como o sistema de mensagens do barramento de serviço do Azure se integra à grade de eventos do Azure.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 658107bb74396891c8e6e05a9e8074a9416a5f6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369655"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Visão geral da integração do Barramento de Serviço com a Grade de Eventos
Agora, o Barramento de Serviço pode emitir eventos para a Grade de Eventos quando houver mensagens em uma fila ou assinatura, quando nenhum receptor estiver presente. É possível criar assinaturas da Grade de Eventos para seus namespaces do Barramento de Serviço, escutar esses eventos e reagir aos eventos iniciando um receptor. Com esse recurso, você pode usar o Barramento de Serviço em modelos de programação reativos. O principal cenário que esse recurso permite é que as filas ou assinaturas do Barramento de Serviço que têm um baixo volume de mensagens não precisam ter um receptor que verifica continuamente a existência de mensagens. 

Para habilitar o recurso, você precisa dos itens a seguir:

* Um namespace Premium de Barramento de Serviço com pelo menos uma fila ou um tópico do Barramento de Serviço com pelo menos uma assinatura.
* Acesso de colaborador para o namespace do Barramento de Serviço. Navegue até o namespace do barramento de serviço na portal do Azure e, em seguida, selecione **controle de acesso (iam)** e selecione a guia **atribuições de função** . Verifique se você tem o acesso de colaborador ao namespace. 
* Além disso, você precisa de uma assinatura da Grade de Eventos para o namespace do Barramento de Serviço. Essa assinatura recebe uma notificação da Grade de Eventos sobre mensagens a serem coletadas. Os assinantes típicos podem ser o recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure, o Azure Functions ou um webhook em contato com um aplicativo Web. Em seguida, o assinante processa as mensagens. 

![19][]

[!INCLUDE [event-grid-service-bus.md](../../includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Assinaturas da grade de eventos para namespaces do barramento de serviço
Há três maneiras de criar assinaturas da Grade de Eventos para namespaces do Barramento de Serviço:

- portal do Azure. Consulte os tutoriais a seguir para saber como usar portal do Azure para criar assinaturas de grade de eventos para eventos do barramento de serviço com aplicativos lógicos do Azure e Azure Functions como manipuladores. 
    - [Aplicativos Lógicos do Azure](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Azure Functions](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* CLI do Azure. O exemplo de CLI a seguir mostra como criar uma assinatura Azure Functions para um [tópico do sistema](../event-grid/system-topics.md) criado por um namespace do barramento de serviço.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Aqui está um exemplo:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>Quantos eventos são emitidos e com que frequência?

Se você tiver várias filas e tópicos ou assinaturas no namespace, terá pelo menos um evento por fila e um por assinatura. Os eventos serão emitidos imediatamente se não houver mensagens na entidade do Barramento de Serviço e uma nova mensagem chegar. Ou, os eventos serão emitidos a cada dois minutos, a menos que o Barramento de Serviço detecte um receptor ativo. A procura de mensagens não interrompe os eventos.

Por padrão, o Barramento de Serviço emite eventos para todas as entidades no namespace. Se você quiser obter eventos somente para entidades específicas, consulte a próxima seção.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Use filtros para limitar de onde obter eventos

Caso queira obter apenas eventos, por exemplo, de uma fila ou assinatura em seu namespace, use filtros *Começa com* ou *Termina com* fornecidos pela Grade de Eventos. Em algumas interfaces, os filtros são chamados de filtros de *pré* e *sufixo* . Caso você queira obter eventos de várias, mas não todas, filas e assinaturas, é possível criar várias assinaturas diferentes da Grade de Eventos e fornecer um filtro para cada uma.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes tutoriais: 
- [Aplicativos lógicos do Azure para manipular mensagens do barramento de serviço recebidas por meio da grade de eventos](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions manipular mensagens do barramento de serviço recebidas por meio da grade de eventos](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[aprimora]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
