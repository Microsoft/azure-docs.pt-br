---
title: "Visão geral das camadas de preço do Sistema de Mensagens Premium e Standard do Barramento de Serviço do Azure | Microsoft Docs"
description: "Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .net
author: djrosanova
manager: timlt
editor: 
ms.assetid: e211774d-821c-4d79-8563-57472d746c58
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: darosa;sethm
ms.openlocfilehash: a0c169e0580468e83a07c077f8c60e83d3fb52f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Camadas de sistema de mensagens Premium e Standard do Barramento de Serviço

O Sistema de Mensagens do Barramento de Serviço, que inclui entidades como filas e tópicos, combina recursos corporativos de mensagens com rica semântica de assinatura para publicação na escala de nuvem. O Sistema de Mensagens do Barramento de Serviço é usado como o backbone de comunicação para muitas soluções de nuvem sofisticadas.

A camada *Premium* do Sistema de Mensagens do Barramento de Serviço atende às solicitações comuns dos clientes em torno da escala, do desempenho e da disponibilidade para aplicativos de missão crítica. Embora os conjuntos de recursos sejam quase idênticos, essas duas camadas do Sistema de Mensagens do Barramento de Serviço foram desenvolvidas para atender a diferentes casos de uso.

Algumas diferenças de alto nível são destacadas na tabela a seguir.

| Premium | Standard |
| --- | --- |
| Alta taxa de transferência |Taxa de transferência variável |
| Desempenho previsível |Latência variável |
| Preço fixo |Preço pré-pago variável |
| Capacidade de escalar a carga de trabalho verticalmente |N/D |
| Até 1 MB de tamanho de mensagem |Até 256 KB de tamanho de mensagem |

O **Sistema de Mensagens Premium do Barramento de Serviço** fornece isolamento de recursos no nível de CPU e memória, de modo que a carga de trabalho do cliente seja executada isoladamente. Esse contêiner de recurso é chamado de *unidade do sistema de mensagens*. Cada namespace premium é alocado para pelo menos uma unidade do sistema de mensagens. Você pode adquirir 1, 2 ou 4 unidades do sistema de mensagens para cada namespace Premium do Barramento de serviço. Uma única carga de trabalho ou entidade pode abranger várias unidades do sistema de mensagens, e o número de unidades do sistema de mensagens pode ser alterado à vontade, embora a cobrança seja feita por taxas diárias ou de 24 horas. O resultado é um desempenho previsível e repetível para sua solução baseada no Barramento de Serviço.

Esse desempenho não é apenas o mais previsível e disponível, mas também o mais rápido. O Sistema de Mensagens Premium do Barramento de Serviço se baseia no mecanismo de armazenamento introduzido nos [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Com o Sistema de Mensagens Premium, o desempenho de pico é muito mais rápido do que com a camada Standard.

## <a name="premium-messaging-technical-differences"></a>Diferenças técnicas do sistema de mensagens Premium

As seções a seguir discutem algumas diferenças entre as camadas dos sistemas de mensagens Premium e Standard.

### <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados

Filas e tópicos particionados têm suporte no Sistema de Mensagens Premium; na verdade essas entidades são particionadas sempre (e não podem ser desabilitadas). No entanto, as filas e tópicos particionados Premium não funcionam da mesma forma na camada Standard do Sistema de Mensagens do Barramento de Serviço. O sistema de mensagens Premium não usa o SQL como um armazenamento de dados e não tem mais a possível competição por recurso associada a uma plataforma compartilhada. Consequentemente, o particionamento não é necessário para melhorar o desempenho. Além disso, a contagem de partições foi alterada, de 16 partições no Sistema de Mensagens Standard para duas partições no Premium. Ter duas partições garante disponibilidade, além de ser um número mais apropriado para o ambiente de tempo de execução Premium. 

Com as mensagens Premium, quando você especifica o tamanho de uma entidade com [MaxSizeInMegabytes](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxsizeinmegabytes#Microsoft_ServiceBus_Messaging_QueueDescription_MaxSizeInMegabytes), o tamanho é dividido igualmente entre as duas partições, ao contrário de [Entidades particionadas padrão](service-bus-partitioning.md#standard) em que o tamanho total é 16 vezes o tamanho especificado. 

Para saber mais sobre o particionamento, confira as [Filas e tópicos particionados](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades expressas

Como o sistema de mensagens Premium é executado em um ambiente de tempo de execução totalmente isolado, não há suporte para as entidades expressas em namespaces Premium. Para saber mais sobre o recurso expresso, consulte a propriedade [QueueDescription.EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress).

Se você tiver código em execução no sistema de mensagens padrão e deseja portá-lo para a camada Premium, verifique se a propriedade [EnableExpress](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enableexpress#Microsoft_ServiceBus_Messaging_QueueDescription_EnableExpress) está definida como **false** (o valor padrão).

## <a name="get-started-with-premium-messaging"></a>Introdução ao sistema de mensagens Premium

A introdução ao Sistema de Mensagens Premium é simples e o processo é semelhante ao do Sistema de Mensagens Padrão. Comece [criando um namespace](service-bus-create-namespace-portal.md). Verifique se você selecionou **Premium** em **Tipo de preços**.

![criar-premium-namespace][create-premium-namespace]

Você também pode criar um [Namespace Premium usando modelos do Azure Resource Manager](https://azure.microsoft.com/en-us/resources/templates/101-servicebus-pn-ar/).


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Sistema de Mensagens do Barramento de Serviço, confira os tópicos a seguir.

* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (postagem de blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introdução ao Sistema de Mensagens Premium do Barramento de Serviço do Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Visão geral do Sistema de Mensagens do Barramento de Serviço](service-bus-messaging-overview.md)
* [Como usar filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
