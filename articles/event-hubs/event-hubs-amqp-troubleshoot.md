---
title: Solucionar problemas de erros do AMQP nos hubs de eventos do Azure | Microsoft Docs
description: Fornece uma lista de erros do AMQP que você pode receber ao usar os hubs de eventos do Azure e causar esses erros.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466058"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Erros de AMQP nos hubs de eventos do Azure
Este artigo fornece alguns dos erros que você recebe ao usar o AMQP com os hubs de eventos do Azure. Eles são todos os comportamentos padrão do serviço. Você pode evitá-los fazendo chamadas de envio/recebimento na conexão/link, que recria automaticamente a conexão/link.

## <a name="link-is-closed"></a>O link está fechado 
Você verá o erro a seguir quando a conexão AMQP e o link estiverem ativos, mas nenhuma chamada (por exemplo, enviar ou receber) for feita usando o link por 30 minutos. Portanto, o link é fechado. A conexão ainda está aberta.

"AMQP: link: Detach-FORCED: o link ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 ' é forçado a ser desanexado pelo agente devido a erros ocorridos no Publicador (link164614). Desanexar origem: AmqpMessagePublisher. IdleTimerExpired: tempo limite de ociosidade: 00:30:00. TrackingID: 00000000000000000000000000000000000000_G2_B3, SystemTracker: MyNamespace: tópico: mytópico, timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>A conexão está fechada
Você verá o erro a seguir na conexão AMQP quando todos os links na conexão tiverem sido fechados porque não havia atividade (ociosa) e um novo link não tiver sido criado em 5 minutos.

"Erro {Condition = AMQP: Connection: forced, Description = ' a conexão estava inativa por mais do que os 300000 milissegundos permitidos e é fechada pelo contêiner ' LinkTracker '. TrackingID: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, carimbo de data/hora: 2019-03-06T17:32:00 ', info = NULL} "

## <a name="link-isnt-created"></a>O link não foi criado 
Você vê esse erro quando uma nova conexão AMQP é criada, mas um link não é criado dentro de 1 minuto da criação da conexão AMQP.

"Erro {Condition = AMQP: Connection: forced, Description = ' a conexão estava inativa por mais do que os 60000 milissegundos permitidos e é fechada pelo contêiner ' LinkTracker '. TrackingID: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, carimbo de data/hora: 2019-03-06T18:41:51 ', info = NULL} "

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o AMQP, consulte [Guia do protocolo AMQP 1,0](../service-bus-messaging/service-bus-amqp-protocol-guide.md).
