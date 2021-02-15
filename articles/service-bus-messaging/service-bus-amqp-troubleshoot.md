---
title: Solucionar problemas de erros do AMQP no barramento de serviço do Azure | Microsoft Docs
description: Fornece uma lista de erros do AMQP que você pode receber ao usar o barramento de serviço do Azure e causar esses erros.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064342"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros de AMQP no barramento de serviço do Azure
Este artigo fornece alguns dos erros que você recebe ao usar o AMQP com o barramento de serviço do Azure. Eles são todos os comportamentos padrão do serviço. Você pode evitá-los fazendo chamadas de envio/recebimento na conexão/link, que recria automaticamente a conexão/link.

## <a name="link-is-closed"></a>O link está fechado 
Você verá o erro a seguir quando a conexão AMQP e o link estiverem ativos, mas nenhuma chamada (por exemplo, enviar ou receber) for feita usando o link por 10 minutos. Portanto, o link é fechado. A conexão ainda está aberta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A conexão está fechada
Você verá o erro a seguir na conexão AMQP quando todos os links na conexão tiverem sido fechados porque não havia atividade (ociosa) e um novo link não foi criado em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>O link não foi criado 
Você vê esse erro quando uma nova conexão AMQP é criada, mas um link não é criado dentro de 1 minuto da criação da conexão AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o AMQP e o Barramento de Serviço, visite os seguintes links:

* [Visão geral do Barramento de Serviço para AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Barramento de Serviço para Windows Server]

[Visão geral do Barramento de Serviço para AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Barramento de Serviço para Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)