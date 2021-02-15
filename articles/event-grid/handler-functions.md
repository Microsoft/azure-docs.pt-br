---
title: Usar uma função no Azure como um manipulador de eventos para eventos da grade de eventos do Azure
description: Descreve como você pode usar funções criadas no e hospedadas pelo Azure Functions como manipuladores de eventos para eventos de grade de eventos.
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: beddc35f2dd8db974492d14aec27ce754a74737c
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632505"
---
# <a name="use-a-function-as-an-event-handler-for-event-grid-events"></a>Usar uma função como um manipulador de eventos para eventos de grade de eventos

Um manipulador de eventos é o local para o qual o evento é enviado. O manipulador usa uma ação para processar o evento. Vários serviços do Azure são automaticamente configurados para lidar com os eventos e o **Azure Functions** é um deles. 


Para usar uma função no Azure como um manipulador para eventos, siga uma destas abordagens: 

-   Use o [gatilho de grade de eventos](../azure-functions/functions-bindings-event-grid-trigger.md).  Especifique o **Azure function** como o **tipo de ponto de extremidade**. Em seguida, especifique o aplicativo de funções e a função que manipulará eventos. 
-   Use o [gatilho http](../azure-functions/functions-bindings-http-webhook.md).  Especifique o **gancho da Web** como o **tipo de ponto de extremidade**. Em seguida, especifique a URL para a função que tratará os eventos. 

Recomendamos que você use a primeira abordagem (gatilho de grade de eventos), pois ela tem as seguintes vantagens em relação à segunda abordagem:
-   A Grade de Eventos valida automaticamente os próprios gatilhos. Com os gatilhos HTTP genéricos, você deve implementar a [resposta de validação](webhook-event-delivery.md).
-   A grade de eventos ajusta automaticamente a taxa na qual os eventos são entregues a uma função disparada por um evento de grade de eventos com base na taxa percebida na qual a função pode processar eventos. Esse recurso de correspondência de taxa evita erros de entrega que resultam da incapacidade de uma função de processar eventos à medida que a taxa de processamento de eventos da função pode variar ao longo do tempo. Para melhorar a eficiência em alta taxa de transferência, habilite o envio em lote na assinatura do evento. Para obter mais informações, consulte [habilitar o envio em lote](#enable-batching).

    > [!NOTE]
    > No momento, você não pode usar um gatilho de grade de eventos para um aplicativo de funções quando o evento é entregue no esquema **CloudEvents** . Em vez disso, use um gatilho HTTP.

## <a name="tutorials"></a>Tutoriais

|Title  |Descrição  |
|---------|---------|
| [Início Rápido: Manipular eventos com função](custom-event-to-function.md) | Envia um evento personalizado para uma função para processamento. |
| [Tutorial: automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](resize-images-on-storage-blob-upload-event.md) | Os usuários fazem o upload de imagens por meio do aplicativo Web para a conta de armazenamento. Quando um blob de armazenamento é criado, a Grade de Eventos envia um evento para o aplicativo de função, que redimensiona a imagem carregada. |
| [Tutorial: transmitir Big Data para um data warehouse](event-grid-event-hubs-integration.md) | Quando os Hubs de Eventos criam um arquivo de Captura, a Grade de Eventos envia um evento para um aplicativo de função. O aplicativo recupera o arquivo de Captura e migra dados para um data warehouse. |
| [Tutorial: Exemplos de integração do Barramento de Serviço do Azure para a Grade de Eventos do Azure](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | A Grade de Eventos envia mensagens do tópico do Barramento de Serviço para um aplicativo de funções e um aplicativo lógico. |

## <a name="rest-example-for-put"></a>Exemplo REST (para PUT)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 6400
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="enable-batching"></a>Habilitar o envio em lote
Para obter uma taxa de transferência mais alta, habilite o envio em lote na assinatura. Se você estiver usando o portal do Azure, poderá definir o máximo de eventos por lote e o tamanho de lote preferencial em quilobytes bytes no momento da criação de uma assinatura ou após a criação. 

Você pode definir configurações de lote usando o modelo portal do Azure, PowerShell, CLI ou Resource Manager. 

### <a name="azure-portal"></a>Portal do Azure
No momento da criação de uma assinatura na interface do usuário, na página **criar assinatura de evento** , alterne para a guia **recursos avançados** e defina valores para **máximo de eventos por lote** e **tamanho de lote preferencial em kilobytes**. 
    
:::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Habilitar o envio em lote no momento da criação de uma assinatura":::

Você pode atualizar esses valores para uma assinatura existente na guia **recursos** da página de **Tópicos da grade de eventos** . 

:::image type="content" source="./media/custom-event-to-function/features-batch-settings.png" alt-text="Habilitar o envio em lote após a criação":::

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Você pode definir **maxEventsPerBatch** e **preferredBatchSizeInKilobytes** em um modelo de Azure Resource Manager. Para obter mais informações, consulte [referência de modelo do Microsoft. EventGrid eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions).

### <a name="azure-cli"></a>CLI do Azure
Você pode usar o comando [AZ eventgrid Event-Subscription Create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create&preserve-view=true) ou [AZ eventgrid Event-Subscription Update](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update&preserve-view=true) para definir configurações relacionadas ao lote usando os seguintes parâmetros: `--max-events-per-batch` ou `--preferred-batch-size-in-kilobytes` .

### <a name="azure-powershell"></a>Azure PowerShell
Você pode usar o cmdlet [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) ou [Update-AzEventGridSubscription](/powershell/module/az.eventgrid/update-azeventgridsubscription) para definir configurações relacionadas ao lote usando os seguintes parâmetros: `-MaxEventsPerBatch` ou `-PreferredBatchSizeInKiloBytes` .

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo [Manipuladores de eventos](event-handlers.md) para obter uma lista dos manipuladores de eventos compatíveis.
