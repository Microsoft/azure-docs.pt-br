---
title: Perguntas frequentes-hubs de eventos do Azure para Apache Kafka
description: Este artigo responde a perguntas frequentes sobre o suporte dos hubs de eventos do Azure para clientes Apache Kafka não cobertos em outro lugar.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: dc6a12b2098a1fdf33adda92b4347f91ab4e5489
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828108"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Perguntas frequentes-hubs de eventos para Apache Kafka 
Este artigo fornece respostas para algumas das perguntas frequentes sobre a migração para os hubs de eventos para Apache Kafka.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Os hubs de eventos do Azure são executados no Apache Kafka?

Não. Os hubs de eventos do Azure são um agente de várias camadas nativo de nuvem com suporte para vários protocolos desenvolvidos e mantidos pela Microsoft e não usam nenhum código de Apache Kafka. Um dos protocolos com suporte é o Protocolo RPC Kafka para as APIs de consumidor e produtor do cliente Kafka. Os hubs de eventos funcionam com muitos dos seus aplicativos Kafka existentes. Para obter mais informações, consulte [hubs de eventos para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). Como os conceitos de Apache Kafka e hubs de eventos do Azure são muito semelhantes (mas não idênticos), podemos oferecer a confiabilidade incomparável dos hubs de eventos do Azure aos clientes com investimentos Apache Kafkas existentes. 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Grupo de consumidores dos hubs de eventos versus grupo de consumidores Kafka
Qual é a diferença entre um grupo de consumidores de Hub de eventos e um grupo de consumidores Kafka nos hubs de eventos? Os grupos de consumidores Kafka nos hubs de eventos são totalmente distintos dos grupos de consumidores dos hubs de eventos padrão.

**Grupos de consumidores dos hubs de eventos**

- Eles são gerenciados com operações CRUD (criar, recuperar, atualizar e excluir) por meio de modelos de portal, SDK ou Azure Resource Manager. Os grupos de consumidores dos hubs de eventos não podem ser criados.
- Elas são entidades filhas de um hub de eventos. Isso significa que o mesmo nome de grupo de consumidores pode ser reutilizado entre os hubs de eventos no mesmo namespace porque eles são entidades separadas.
- Eles não são usados para armazenar deslocamentos. O consumo de AMQP orquestrado é feito usando o armazenamento de deslocamento externo, por exemplo, o armazenamento do Azure.

**Grupos de consumidores do Kafka**

- Eles são criados.  Os grupos de Kafka podem ser gerenciados por meio das APIs do grupo de consumidores do Kafka.
- Eles podem armazenar deslocamentos no serviço de hubs de eventos.
- Eles são usados como chaves no que é efetivamente um armazenamento de chave-valor de deslocamento. Para um par exclusivo de `group.id` e `topic-partition` , armazenamos um deslocamento no armazenamento do Azure (replicação em 3x). Os usuários dos hubs de eventos não incorrem em custos de armazenamento extra do armazenamento de deslocamentos Kafka. Os deslocamentos são manipulable por meio das APIs do grupo de consumidores do Kafka, mas as *contas* de armazenamento de deslocamento não são diretamente visíveis ou manipulable para usuários do hub de eventos.  
- Eles abrangem um namespace. Usar o mesmo nome de grupo Kafka para vários aplicativos em vários tópicos significa que todos os aplicativos e seus clientes Kafka serão rebalanceados sempre que um único aplicativo precisar de rebalanceamento.  Escolha os nomes de grupo com sabedoria.
- Eles são totalmente distintos dos grupos de consumidores dos hubs de eventos. Você **não** precisa usar ' $default ', nem precisa se preocupar com os clientes Kafka que interferem nas cargas de trabalho do AMQP.
- Eles não são visíveis na portal do Azure. As informações do grupo de consumidores estão acessíveis por meio de APIs do Kafka.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Guia do desenvolvedor de Apache Kafka para hubs de eventos](apache-kafka-developer-guide.md)
- [Guia de migração de Apache Kafka para hubs de eventos](apache-kafka-migration-guide.md)
- [Guia de solução de problemas Apache Kafka para hubs de eventos](apache-kafka-troubleshooting-guide.md)
- [Configurações recomendadas](apache-kafka-configurations.md)

