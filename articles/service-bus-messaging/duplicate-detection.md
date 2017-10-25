---
title: "Detecção de mensagens duplicadas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Detectar mensagens duplicadas do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: sethm
ms.openlocfilehash: 470246469297d5fa95eba2b147d5304e74c0003f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="duplicate-detection"></a>Detecção de duplicidade

Se um aplicativo encontrar um erro fatal imediatamente após enviar uma mensagem e a instância do aplicativo reiniciada, portanto erroneamente acreditar que a entrega da mensagem anterior não ocorreu, um envio subsequente faz com que a mesma mensagem apareça no sistema duas vezes.

Também é possível ocorrer um erro no nível do cliente ou da rede um momento antes e para uma mensagem enviada ser confirmada na fila, com a confirmação não retornando ao cliente com êxito. Este cenário deixa o cliente em dúvida quanto ao resultado da operação de envio.

A detecção de duplicidades tira a dúvida dessas situações permitindo que o remetente reenvie a mesma mensagem e que a fila ou o tópico descarte quaisquer cópias duplicadas.

Habilitar detecção de duplicidades ajuda a controlar a *MessageId* controlada pelo aplicativo de todas as mensagens enviadas para uma fila ou tópico durante um intervalo especificado. Se qualquer nova mensagem for enviada contendo uma *MessageId* que já foi registrada durante o intervalo, a mensagem será relatada como aceita (a operação de envio é bem-sucedida), mas a mensagem recém-enviada é instantaneamente ignorada e descartada. Nenhuma outra parte da mensagem além de *MessageId* é considerada.

O controle do aplicativo do identificador é essencial, pois apenas isso permite que o aplicativo ligue a *MessageId* a um contexto de processo de negócios do qual ele pode ser reconstruído de forma previsível em caso de falha.

Para um processo de negócios em que várias mensagens são enviadas no decorrer de tratamento de algum contexto de aplicativo, a *MessageId* pode ser uma composição do identificador de contexto do nível do aplicativo, como um número de ordem de compra e o assunto da mensagem, por exemplo, **12345.2017/payment**.

A *MessageId* sempre pode ser algum GUID, mas a ancoragem o identificador para o processo de negócios produz a repetição previsível, o que é desejado para aproveitar o recurso de detecção de duplicidades com eficiência.

## <a name="enable-duplicate-detection"></a>Habilitar detecção de duplicidade

No portal, o recurso é ativado durante a criação de entidade com a caixa de seleção **Habilitar detecção de duplicidade**, que está desativado por padrão. A configuração para criar novos tópicos é equivalente.

![][1]

Programaticamente, você pode definir o sinalizador com a propriedade [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) na API do .NET de estrutura completa. Com a API do Azure Resource Manager, o valor é definido com a propriedade [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

O histórico de tempo de detecção de duplicidades tem como padrão 30 segundos para filas e tópicos, com um valor máximo de 40 minutos. Você pode alterar essa configuração na janela de propriedades de fila e tópico no Portal do Azure.

![][2]

Programaticamente, você pode configurar o tamanho da janela de detecção de duplicidades durante o qual as IDs de mensagem são mantidas, usando a propriedade [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) com a API do .NET Framework completa. Com a API do Azure Resource Manager, o valor é definido com a propriedade [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

Observe que habilitar a detecção de duplicidades e o tamanho da janela afeta diretamente a taxa de transferência da fila (e do tópico), uma vez que todas as IDs de mensagem registradas devem corresponder ao identificador de mensagem recém-enviado.

Manter a janela pequena significa que menos IDs de mensagem devem ser retidos e correspondidos e a taxa de transferência é menos afetada. Para entidades de alta taxa de transferência que exigem a detecção de duplicidades, você deve manter a janela o menor possível.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as mensagens do Barramento de Serviço, consulte os tópicos a seguir:

* [Conceitos fundamentais do barramento de serviço](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e assinaturas do Barramento de Serviço](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como usar tópicos e assinaturas do Barramento de Serviço](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png