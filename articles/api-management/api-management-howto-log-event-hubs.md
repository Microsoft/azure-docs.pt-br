---
title: Como registrar eventos nos Hubs de Eventos do Azure no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 14f84b5380a1c106114cdab425de7f69f4e19825
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657536"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure
Hub de Eventos do Azure é um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Hub de Eventos age como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um hub de eventos, ele pode ser transformado e armazenado usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma.

Este artigo é um complemento para o [integrar o Gerenciamento de API do Azure com vídeos dos Hubs de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registrar eventos de Gerenciamento de API usando Hubs de eventos do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para saber as etapas detalhadas sobre como criar um hub de eventos e obter cadeias de conexão que você precisa para enviar e receber eventos de e para o Hub de Eventos, consulte [Criar um namespace de Hubs de Eventos e um hub de eventos usando o portal do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-api-management-logger"></a>Criar um agente de Gerenciamento de API
Agora que você tem um Hub de Eventos, a próxima etapa será configurar um [Agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) no seu serviço de Gerenciamento de API para que ele possa registrar eventos em log para o Hub de Eventos.

Os agentes do Gerenciamento de API são configurados usando a [API REST do Gerenciamento de API](https://aka.ms/smapi). Antes de usar a API REST pela primeira vez, examine os [pré-requisitos](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) e verifique se você [habilitou o acesso à API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Para criar um agente de log, faça uma solicitação HTTP PUT usando o modelo de URL a seguir:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Substitua `{your service}` pelo nome da sua instância do serviço de Gerenciamento de API.
* Substitua `{new logger name}` pelo nome desejado para o novo agente. Você referencia este nome quando configura a política [log-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub)

Adicione os seguintes cabeçalhos à solicitação:

* Content-Type : application/json
* Autorização: SharedAccessSignature 58...
  * Para saber mais sobre como gerar a `SharedAccessSignature` , confira [Autenticação da API REST do Gerenciamento de API do Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Especifique o corpo da solicitação usando o modelo a seguir:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` deve ser definido como `AzureEventHub`.
* `description` fornece uma descrição opcional do agente e pode ser uma cadeia de caracteres de comprimento zero, se desejado.
* `credentials` contém `name` e `connectionString` do seu Hub de Eventos do Azure.

Quando você fizer a solicitação, se o agente for criado, um código de status `201 Created` retornará. Veja abaixo um exemplo de resposta com base no exemplo de solicitação acima.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Para outros códigos de retorno possíveis e seus motivos, confira [Criar um agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Para ver como executar outras operações como listar, atualizar e excluir, consulte a documetação de entidade do [Agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity).
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurar políticas log-to-eventhubs

Depois que o agente de log estiver configurado no Gerenciamento de API, você poderá configurar suas políticas log-to-eventhubs para registrar os eventos desejados em log. A política log-to-eventhubs pode ser usada na seção de política de entrada ou na seção de política de saída.

1. Navegue até sua instância de APIM.
2. Selecione a guia API.
3. Selecione a API para a qual você deseja adicionar a política. Neste exemplo, estamos adicionando uma política para a **API Echo API** no produto **Unlimited**.
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia Design.
6. Na janela de Processamento de entrada ou de saída, clique no triângulo (ao lado do lápis).
7. Selecione o Editor de códigos. Para obter mais informações, consulte [Como definir ou editar políticas](set-edit-policies.md).
8. Posicione o cursor na seção da política `inbound` ou `outbound`.
9. Na janela à direita, selecione **Políticas avançadas** > **Log EventHub**. Isso insere o modelo de instrução da política `log-to-eventhub`.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Substitua `logger-id` pelo valor usado para `{new logger name}` na URL ao criar o agente na etapa anterior.

Você pode usar qualquer expressão que retorne uma cadeia de caracteres como o valor do elemento `log-to-eventhub` . Neste exemplo, uma cadeia de caracteres que contém a data e a hora, o nome do serviço, a ID da solicitação, endereço IP da solicitação e o nome da operação é registrada.

Clique em **Salvar** para salvar a configuração da política atualizada. Assim que for salva, a política estará ativa e os eventos serão registrados em log para o Hub de Eventos designado.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre Hubs de Eventos do Azure
  * [Introdução aos Hubs de Eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração do Gerenciamento de API e Hubs de eventos
  * [Referência de entidade do agente](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [referência de política de log ao hub de eventos](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Monitorar suas APIs com gerenciamento de API do Azure, Hubs de Eventos e Moesif](api-management-log-to-eventhub-sample.md)  
* Saiba mais sobre a [integração com o Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
