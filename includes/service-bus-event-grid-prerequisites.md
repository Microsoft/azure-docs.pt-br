---
title: incluir arquivo
description: incluir arquivo
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d12df7197945a514ed8d3d0dca77271fb4bd0903
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96509523"
---
## <a name="prerequisites"></a>Pré-requisitos
Se você não tiver uma [assinatura do Azure](../articles/guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-a-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço
Siga as instruções deste tutorial: [Início Rápido: Usar o portal do Azure para criar um tópico do Barramento de Serviço e assinaturas para o tópico](../articles/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) para executar as seguintes tarefas:

- Criar um namespace **premium** do Barramento de Serviço. 
- Obter a cadeia de conexão. 
- Criar um tópico de Barramento de Serviço.
- Crie uma assinatura para o tópico. Você precisa de apenas uma assinatura neste tutorial, portanto, não há necessidade de criar assinaturas S2 e S3. 

## <a name="send-messages-to-the-service-bus-topic"></a>Enviar mensagens para o tópico do Barramento de Serviço
Nesta etapa, você usa um aplicativo de exemplo para enviar mensagens para o tópico do Barramento de Serviço criado na etapa anterior. 

1. Clone o [repositório azure-service-bus do GitHub](https://github.com/Azure/azure-service-bus/).
2. No Visual Studio, acesse a pasta *\samples\DotNet\Azure.Messaging.ServiceBus\ServiceBusEventGridIntegration* e abra o arquivo *SBEventGridIntegration.sln*.
3. Na janela Gerenciador de Soluções, expanda o projeto **MessageSender** e selecione **Program.cs**.
4. Substitua `<SERVICE BUS NAMESPACE - CONNECTION STRING>` pela cadeia de conexão do namespace do Barramento de Serviço e `<TOPIC NAME>` pelo nome do tópico. 

    ```csharp
    const string ServiceBusConnectionString = "<SERVICE BUS NAMESPACE - CONNECTION STRING>";
    const string TopicName = "<TOPIC NAME>";
    ```
5. Crie e execute o programa para enviar cinco mensagens de teste (`const int numberOfMessages = 5;`) para o tópico do Barramento de Serviço. 

    :::image type="content" source="./media/service-bus-event-grid-prerequisites/console-app-output.png" alt-text="Saída do aplicativo de console":::
