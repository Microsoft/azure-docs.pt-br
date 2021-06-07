---
title: Guia do desenvolvedor de Apache Kafka para hubs de eventos
description: Este artigo fornece links para artigos que descrevem como integrar seus aplicativos Kafka com os hubs de eventos do Azure.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061726"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure
Este artigo fornece links para artigos que descrevem como integrar seus aplicativos de Apache Kafka com os hubs de eventos do Azure. 

## <a name="overview"></a>Visão geral
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Kafka existentes como uma alternativa para executar seu próprio cluster Kafka. Os hubs de eventos funcionam com muitos dos seus aplicativos Kafka existentes. Para obter mais informações, consulte [hubs de eventos para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Inícios rápidos
Você pode encontrar os guias de início rápido no GitHub e neste conjunto de conteúdo que ajuda você a se acelerar rapidamente nos hubs de eventos para Kafka.

### <a name="quickstarts-in-github"></a>Guias de início rápido no GitHub
Consulte os seguintes guias de início rápido no repositório **Azure-Event-hubs-for-Kafka** : 

| Idioma/estrutura do cliente | Descrição | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escritos em C# usando o .NET Core 2,0.</p><p>Este exemplo baseia-se no [cliente .net Apache Kafka do](https://github.com/confluentinc/confluent-kafka-dotnet)Kafka, modificado para uso com os hubs de eventos para o am.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escritos em Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor gravados no nó.</p><p>Este exemplo usa a biblioteca [node-rdkafka](https://github.com/Blizzard/node-rdkafka) . </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escritos em Python.</p><p>Este exemplo baseia-se no [cliente Python de Apache Kafka do](https://github.com/confluentinc/confluent-kafka-python)Kafka, modificado para uso com os hubs de eventos para o am.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escritos em go.</p><p>Este exemplo é baseado no [cliente do Golang do Apache Kafka do](https://github.com/confluentinc/confluent-kafka-go)Kafka, modificado para uso com os hubs de eventos para.</p>| 
| [Sarama Kafka go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escrito em go usando a biblioteca de [cliente Sarama Kafka](https://github.com/Shopify/sarama) . |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Este guia de início rápido mostrará como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando a CLI que vem agrupada com a distribuição Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat é um consumidor de linha de comando não JVM e produtor baseado em librdkafka, popular devido à sua velocidade e pequena superfície. Este guia de início rápido contém uma configuração de exemplo e vários comandos kafkacat de exemplo simples. | 
 
### <a name="quickstarts-in-docs"></a>Guias de início rápido no DOCS
Consulte o guia de início rápido: [streaming de dados com hubs de eventos usando o protocolo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) neste conjunto de conteúdo, que fornece instruções passo a passo sobre como transmitir para os hubs de eventos. Você aprenderá a usar seus produtores e os consumidores para se comunicar com os Hubs de Eventos com apenas uma alteração de configuração nos seus aplicativos. 


## <a name="tutorials"></a>Tutoriais 

### <a name="tutorials-in-github"></a>TUTORIAIS no GitHub
Consulte os seguintes tutoriais no GitHub:

| Tutorial | Descrição | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Este tutorial mostra como conectar fluxos de Akka a hubs de eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Há dois tutoriais separados usando linguagens de programação **Java** e **escalares** . | 
| [Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Este documento explicará a integração do Kafka Connect com os hubs de eventos do Azure e a implantação de conectores filestreamname e FileStreamSink básicos. Embora esses conectores não sejam destinados ao uso em produção, eles demonstram um cenário de conexão Kafka de ponta a ponta em que os hubs de eventos do Azure se disfarçam como um agente Kafka.| 
| [Super pulsação](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Este documento explicará a integração do filesuper e dos hubs de eventos por meio da saída de Kafka do filesuperation. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Este tutorial mostrará como conectar o Apache Flink a hubs de eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Este documento orientará você na integração de hubs de eventos e fluentes usando o `out_kafka` plug-in de saída para o fluentd. |
| [Interoperabilidade](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Este tutorial mostra como trocar eventos entre consumidores e produtores usando protocolos diferentes. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Este tutorial explicará a integração do Logstash com os hubs de eventos habilitados para Kafka usando Logstash Kafka plug-ins de entrada/saída. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Este tutorial mostra como um hub de eventos e Kafka MirrorMaker podem integrar um pipeline Kafka existente ao Azure espelhando o fluxo de entrada Kafka no serviço de hubs de eventos. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Este tutorial mostrará como conectar o Apache NiFi a um namespace de hubs de eventos. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Os guias de início rápido mostram como criar e se conectar a um ponto de extremidade Kafka dos hubs de eventos usando um produtor de exemplo e um consumidor escritos em linguagens de programação Go e Java. |
| [Registro de esquema do influente](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Este tutorial explicará a integração do registro de esquema e dos hubs de eventos para Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Este tutorial mostrará como conectar seu aplicativo Spark a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters Kafka. | 

### <a name="tutorials-in-docs"></a>TUTORIAIS no DOCS
Além disso, consulte o tutorial: [processar Apache Kafka para eventos de hubs de eventos usando o Stream Analytics](event-hubs-kafka-stream-analytics.md) neste conjunto de conteúdo, que mostra como transmitir dados para os hubs de eventos e processá-los com Azure Stream Analytics.

## <a name="how-to-guides"></a>Guias de instruções
Consulte os seguintes guias de instruções em nossa documentação:

| Artigo | Descrição | 
| ------- | ----------- | 
| [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md) | Mostra como espelhar um agente do Kafka em um hub de eventos usando o Kafka MirrorMaker. |
| [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md) | Orienta você pela conexão do aplicativo Spark aos hubs de eventos para streaming em tempo real. |
| [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md) | Mostra como conectar o Apache Flink a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. |
| [Integrar o Apache Kafka conectar-se a um hub de eventos (visualização)](event-hubs-kafka-connect-tutorial.md) | Orienta você pela integração do Kafka Connect a um hub de eventos e à implantação de conectores do filestreamname e do FileStreamSink básicos. |
| [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md) | Mostra como conectar fluxos do Akka a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. |
| [Usar o iniciador do Spring boot para Apache Kafka com os hubs de eventos do Azure](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Demonstra como configurar um fichário de fluxo de nuvem Spring baseado em Java criado com o inicializador Spring boot para usar Apache Kafka com os hubs de eventos do Azure. |

## <a name="next-steps"></a>Próximas etapas
Examine os exemplos no repositório GitHub [Azure-Event-hubs-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) em pastas de início rápido e tutoriais.

Além disso, consulte os seguintes artigos:

- [Guia de solução de problemas Apache Kafka para hubs de eventos](apache-kafka-troubleshooting-guide.md)
- [Perguntas frequentes-hubs de eventos para Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Guia de migração de Apache Kafka para hubs de eventos](apache-kafka-migration-guide.md)



