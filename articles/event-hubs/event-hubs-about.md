---
title: O que é Hub de Eventos do Azure? – um serviço de ingestão de Big Data | Microsoft Docs
description: Saiba mais sobre Hubs de Eventos do Azure, um serviço de streaming de Big Data que ingere milhões de eventos por segundo.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 242f2fa9885f3f85439caddd061f650baafb8df4
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314408"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Hubs de Eventos do Azure – uma plataforma de streaming de Big Data e um serviço de ingestão de eventos
Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos. Ele pode receber e processar milhões de eventos por segundo. Os dados enviados para um hub de eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento.

Os seguintes cenários estão alguns dos cenários em que você pode usar os Hubs de Eventos:

- Detecção de anomalias (fraude/exceções)
- Registro em log do aplicativo
- Pipelines de análise, como a sequência de cliques
- Criação de painéis em tempo real
- Arquivamento de dados
- Processamento de transação
- Processamento de telemetria do usuário
- Streaming de telemetria do dispositivo

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Por que usar Hubs de Eventos?

Dados são valiosos apenas quando há uma maneira fácil de processar e obter análises em tempo hábil das fontes de dados. Os Hubs de Eventos fornecem uma plataforma de processamento de fluxo distribuído com baixa latência e integração perfeita, com serviços de análise e dados dentro e fora do Azure para criar seu pipeline completo de Big Data.

Os Hubs de Evento representam a "porta da frente" de um pipeline de evento, geralmente chamado de *ingestor de eventos* em arquiteturas da solução. Um ingestor de eventos é um componente ou serviço que fica entre os editores de eventos e consumidores de eventos para desacoplar a produção de uma transmissão de eventos do consumo desses eventos. Os Hubs de Eventos fornecem uma plataforma unificada de streaming com o buffer de retenção de tempo, desacoplando os produtores do evento dos consumidores do evento.

As seções abaixo descrevem os principais recursos do serviço Hubs de Eventos do Azure:

## <a name="fully-managed-paas"></a>PaaS totalmente gerenciado

Os Hubs de Eventos são um PaaS (Plataforma como Serviço) totalmente gerenciado com pouca configuração ou sobrecarga de gerenciamento, para que você se concentre em suas soluções de negócios. [Os Hubs de Eventos para ecossistemas do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) proporcionam a experiência de PaaS Kafka sem a necessidade de gerenciar, configurar ou executar seus clusters.

## <a name="support-for-real-time-and-batch-processing"></a>Suporte a processamento em lotes e em tempo real

Ingerir, armazenar em buffer, armazenar e processar seu fluxo em tempo real para obter análises acionáveis. Os Hubs de Eventos usam um [modelo de consumidor particionado](event-hubs-scalability.md#partitions), permitindo que vários aplicativos processem o fluxo simultaneamente e permitindo que você controle a velocidade de processamento.

[Capture](event-hubs-capture-overview.md) seus dados em tempo quase real em um [armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/) ou no [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) para retenção de longo prazo ou processamento em microlotes. Obtenha esse comportamento no mesmo fluxo que você usa para obter a análise em tempo real. A configuração da captura de dados de evento é rápida. Não há custos administrativos para executá-la e ela é dimensionada automaticamente com as [unidades de produtividade](event-hubs-scalability.md#throughput-units) dos Hubs de Eventos. Os Hubs de Eventos permitem que você se concentre no processamento de dados, em vez de na captura de dados.

Os Hubs de Eventos do Azure também se integram com o [Azure Functions](/azure/azure-functions/) para uma arquitetura sem servidor.

## <a name="scalable"></a>Escalonável

Com os Hubs de eventos, você pode iniciar com fluxos de dados em megabytes e aumentar para gigabytes ou terabytes. O recurso [Expandir automaticamente](event-hubs-auto-inflate.md) é uma das muitas opções disponíveis para aumentar o número de unidades de produtividade para atender às suas necessidades de uso.

## <a name="rich-ecosystem"></a>Ecossistema avançado

[Os Hubs de Eventos para ecossistemas do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) permitem que clientes e aplicativos do [Apache Kafka (1.0 e posterior)](https://kafka.apache.org/) se comuniquem com os Hubs de Eventos. Você não precisa definir, configurar nem gerenciar seus próprios clusters Kafka.

Com uma enorme variedade disponível em várias [linguagens (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs), você pode iniciar facilmente o processamento dos seus fluxos nos Hubs de Eventos. Todos as linguagens com suporte do cliente fornecem integração de baixo nível. O ecossistema também fornece integração perfeita com serviços do Azure, como o Azure Stream Analytics e o Azure Functions, permitindo que você crie arquiteturas sem servidor.

## <a name="key-architecture-components"></a>Principais componentes da arquitetura
Os Hubs de Eventos contêm os seguintes [componentes principais](event-hubs-features.md):

- **Produtores de eventos**: uma entidade que envia dados para um hub de eventos. Os editores de eventos podem publicar eventos usando HTTPS ou AMQP 1.0 ou Apache Kafka (1.0 e acima)
- **Partições**: cada consumidor lê somente um subconjunto específico ou partição do fluxo de mensagens.
- **Grupos de consumidores**: uma exibição (estado, posição ou deslocamento) de todo um hub de eventos. Os grupos de consumidores permitem que vários aplicativos de consumo tenham uma exibição separada do fluxo de eventos. Eles leem o fluxo de forma de maneira independente em seu próprio ritmo e com seus próprios deslocamentos.
- **Unidades de produtividade**: unidades de capacidade pré-adquiridas que controlam a capacidade de produtividade dos Hubs de Eventos.
- **Receptores de eventos**: qualquer entidade que leia dados de eventos de um hub de eventos. Todos os consumidores dos Hubs de Eventos se conectam por meio da sessão do AMQP 1.0. O serviço de Hubs de Eventos oferece eventos por meio de uma sessão conforme eles ficam disponíveis. Todos os consumidores do Kafka se conectam por meio do protocolo Kafka 1.0 e posterior.

A figura a seguir mostra a arquitetura de processamento de fluxo dos Hubs de Eventos:

![Hubs de Eventos](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Próximas etapas

Para começar a usar os Hubs de Eventos, consulte os tutoriais **Enviar e receber eventos**:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (somente enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (somente receber)](event-hubs-storm-getstarted-receive.md)


Para saber mais sobre os Hubs de Eventos, consulte os artigos a seguir:

- [Visão geral dos recursos de Hubs de Eventos](event-hubs-features.md)
- [Perguntas frequentes](event-hubs-faq.md).


