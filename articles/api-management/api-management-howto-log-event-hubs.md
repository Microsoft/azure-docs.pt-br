---
title: Como registrar eventos nos Hubs de Eventos do Azure no Gerenciamento de API do Azure | Microsoft Docs
description: Saiba como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure Os hubs de eventos são um serviço de entrada de dados altamente escalonável.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 5066169951409fa86aa75a64e8fc6d4189947f27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072400"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Como registrar eventos em log para Hubs de Eventos do Azure no Gerenciamento de API do Azure
Hub de Eventos do Azure é um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Hub de Eventos age como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um hub de eventos, ele pode ser transformado e armazenado usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma.

Este artigo é um complemento para o [integrar o Gerenciamento de API do Azure com vídeos dos Hubs de eventos](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) e descreve como registrar eventos de Gerenciamento de API usando Hubs de eventos do Azure.

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Para saber as etapas detalhadas sobre como criar um hub de eventos e obter cadeias de conexão que você precisa para enviar e receber eventos de e para o Hub de Eventos, consulte [Criar um namespace de Hubs de Eventos e um hub de eventos usando o portal do Azure](../event-hubs/event-hubs-create.md).

## <a name="create-an-api-management-logger"></a>Criar um agente de Gerenciamento de API
Agora que você tem um Hub de Eventos, a próxima etapa será configurar um [Agente](/rest/api/apimanagement/2019-12-01/logger) no seu serviço de Gerenciamento de API para que ele possa registrar eventos em log para o Hub de Eventos.

Os agentes do Gerenciamento de API são configurados usando a [API REST do Gerenciamento de API](/rest/api/apimanagement/ApiManagementREST/API-Management-REST). Para obter exemplos de solicitação detalhados, consulte [como criar agentes](/rest/api/apimanagement/2019-12-01/logger/createorupdate).

## <a name="configure-log-to-eventhub-policies"></a>Configurar políticas de log para eventhub

Depois que o agente de log estiver configurado no gerenciamento de API, você poderá configurar a política de logon para eventhub para registrar os eventos desejados. A política de log para eventhub pode ser usada na seção política de entrada ou na seção política de saída.

1. Navegue até sua instância de APIM.
2. Selecione a guia API.
3. Selecione a API para a qual você deseja adicionar a política. Neste exemplo, estamos adicionando uma política para a **API Echo API** no produto **Unlimited**.
4. Selecione **Todas as operações**.
5. Na parte superior da tela, selecione a guia Design.
6. Na janela de Processamento de entrada ou de saída, clique no triângulo (ao lado do lápis).
7. Selecione o Editor de códigos. Para obter mais informações, consulte [Como definir ou editar políticas](set-edit-policies.md).
8. Posicione o cursor na seção da política `inbound` ou `outbound`.
9. Na janela à direita, selecione **políticas avançadas**  >  **log no EventHub**. Isso insere o modelo de instrução da política `log-to-eventhub`.

```xml
<log-to-eventhub logger-id="logger-id">
    @{
        return new JObject(
            new JProperty("EventTime", DateTime.UtcNow.ToString()),
            new JProperty("ServiceName", context.Deployment.ServiceName),
            new JProperty("RequestId", context.RequestId),
            new JProperty("RequestIp", context.Request.IpAddress),
            new JProperty("OperationName", context.Operation.Name)
        ).ToString();
    }
</log-to-eventhub>
```
Substitua `logger-id` pelo valor usado para `{loggerId}` na URL da solicitação para criar o agente de log na etapa anterior.

Você pode usar qualquer expressão que retorne uma cadeia de caracteres como o valor do elemento `log-to-eventhub` . Neste exemplo, uma cadeia de caracteres no formato JSON que contém a data e a hora, o nome do serviço, a ID da solicitação, o endereço IP da solicitação e o nome da operação é registrado.

Clique em **Salvar** para salvar a configuração da política atualizada. Assim que for salva, a política estará ativa e os eventos serão registrados em log para o Hub de Eventos designado.

> [!NOTE]
> O tamanho máximo de mensagem com suporte que pode ser enviado a um hub de eventos dessa política de gerenciamento de API é de 200 kilobytes (KB). Se uma mensagem enviada para um hub de eventos for maior que 200 KB, ela será truncada automaticamente e a mensagem truncada será transferida para os hubs de eventos.

## <a name="preview-the-log-in-event-hubs-by-using-azure-stream-analytics"></a>Visualizar o log nos hubs de eventos usando Azure Stream Analytics

Você pode visualizar o log nos hubs de eventos usando [consultas Azure Stream Analytics](../event-hubs/process-data-azure-stream-analytics.md). 

1. Na portal do Azure, navegue até o Hub de eventos para o qual o agente envia eventos. 
2. Em **recursos**, selecione a guia **processar dados** .
3. No cartão **habilitar percepções em tempo real do evento** , selecione **explorar**.
4. Você deve ser capaz de visualizar o log na guia **visualização de entrada** . Se os dados mostrados não estiverem atuais, selecione **Atualizar** para ver os eventos mais recentes.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre Hubs de Eventos do Azure
  * [Introdução aos Hubs de Eventos do Azure](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Receber mensagens com EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
  * [Guia de programação dos Hubs de Eventos](../event-hubs/event-hubs-programming-guide.md)
* Saiba mais sobre a integração do Gerenciamento de API e Hubs de eventos
  * [Referência de entidade do agente](/rest/api/apimanagement/2019-12-01/logger)
  * [referência de política de log ao hub de eventos](./api-management-advanced-policies.md#log-to-eventhub)
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