---
title: Grade de eventos do Azure – definir cabeçalhos personalizados em eventos entregues
description: Descreve como você pode definir cabeçalhos personalizados (ou propriedades de entrega) em eventos entregues.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630338"
---
# <a name="delivery-with-custom-headers"></a>Entrega com cabeçalhos personalizados
As assinaturas de evento permitem que você configure cabeçalhos HTTP que estão incluídos nos eventos entregues. Essa funcionalidade permite que você defina cabeçalhos personalizados que são exigidos por um destino. Você pode configurar até 10 cabeçalhos ao criar uma assinatura de evento. Cada valor de cabeçalho não deve ser maior que 4.096 (4K) bytes.

Você pode definir cabeçalhos personalizados nos eventos que são entregues aos seguintes destinos:

- Webhooks
- Tópicos e filas do barramento de serviço do Azure
- Hubs de eventos do Azure
- Conexões Híbridas de retransmissão

Ao criar uma assinatura de evento no portal do Azure, você pode usar a guia **Propriedades de entrega** para definir cabeçalhos HTTP personalizados. Esta página permite que você defina valores de cabeçalho fixos e dinâmicos.

## <a name="setting-static-header-values"></a>Definindo valores de cabeçalho estáticos
Para definir cabeçalhos com um valor fixo, forneça o nome do cabeçalho e seu valor nos campos correspondentes:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Propriedades de entrega – estático":::

Talvez você queira que a verificação **seja secreta?** ao fornecer dados confidenciais. Os dados confidenciais não serão exibidos no portal do Azure. 

## <a name="setting-dynamic-header-values"></a>Definindo valores de cabeçalho dinâmico
Você pode definir o valor de um cabeçalho com base em uma propriedade em um evento de entrada. Use a sintaxe JsonPath para fazer referência a um valor de Propriedade do evento de entrada a ser usado como o valor de um cabeçalho em solicitações de saída. Por exemplo, para definir o valor de um cabeçalho chamado **canal** usando o valor do **sistema** de propriedades de evento de entrada nos dados do evento, configure sua assinatura de evento da seguinte maneira:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Propriedades de entrega-dinâmica":::

## <a name="examples"></a>Exemplos
Esta seção fornece alguns exemplos de como usar as propriedades de entrega.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Configurando o cabeçalho de autorização com um token de portador (exemplo não normativas)

Defina um valor para um cabeçalho de autorização para identificar a solicitação com o manipulador de webhook. Um cabeçalho de autorização poderá ser definido se você não estiver [protegendo seu webhook com Azure Active Directory](secure-webhook-delivery.md).

| Nome do cabeçalho   | Tipo de cabeçalho | Valor do cabeçalho |
| :--           | :--         | :--            |
|`Authorization` | Estático | `BEARER SlAV32hkKG...`|

As solicitações de saída agora devem conter o cabeçalho definido na assinatura do evento:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Definir cabeçalhos de autorização é uma opção sensata quando o destino é um webhook. Ele não deve ser usado para [funções assinadas com uma ID de recurso](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), barramento de serviço, hubs de eventos e conexões híbridas, pois esses destinos dão suporte a seus próprios esquemas de autenticação quando usados com a grade de eventos.

### <a name="service-bus-example"></a>Exemplo de barramento de serviço
O barramento de serviço do Azure dá suporte ao uso de um [cabeçalho http brokerproperties](/rest/api/servicebus/message-headers-and-properties#message-headers) para definir propriedades de mensagem ao enviar mensagens únicas. O valor do `BrokerProperties` cabeçalho deve ser fornecido no formato JSON. Por exemplo, se você precisar definir propriedades de mensagem ao enviar uma única mensagem para o barramento de serviço, defina o cabeçalho da seguinte maneira:

| Nome do cabeçalho | Tipo de cabeçalho | Valor do cabeçalho |
| :-- | :-- | :-- |
|`BrokerProperties` | Estático     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Exemplo de hubs de eventos

Se você precisar publicar eventos em uma partição específica dentro de um hub de eventos, defina um [cabeçalho http brokerproperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) na sua assinatura de evento para especificar a chave de partição que identifica a partição do hub de eventos de destino.

| Nome do cabeçalho | Tipo de cabeçalho | Valor do cabeçalho                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Estático | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configurar a vida útil em eventos de saída para filas do armazenamento do Azure
Para o destino de filas do armazenamento do Azure, você só pode configurar a vida útil da mensagem de saída terá uma vez entregue a uma fila de armazenamento do Azure. Se nenhum tempo for fornecido, a vida útil padrão da mensagem será de 7 dias. Você também pode definir que o evento nunca expire.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Propriedades de entrega – fila de armazenamento":::

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre a entrega de eventos, consulte o seguinte artigo:

- [Entregar e tentar novamente](delivery-and-retry.md)
- [Entrega de eventos do webhook](webhook-event-delivery.md)
- [Filtro de eventos](event-filtering.md)
