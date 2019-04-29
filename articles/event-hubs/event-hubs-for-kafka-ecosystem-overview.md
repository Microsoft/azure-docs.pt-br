---
title: Usar um hub de eventos do aplicativo Apache Kafka – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre a compatibilidade do Apache Kafka com os Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821711"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usar Hubs de Eventos do Azure de aplicativos Apache Kafka
Os Hubs de Eventos fornecem um ponto de extremidade Kafka que pode ser usado por seus aplicativos baseados em Kafka existentes como uma alternativa para executar seu próprio cluster Kafka. Os Hubs de Eventos dão suporte ao [protocolo 1.0 e posterior do Apache Kafka](https://kafka.apache.org/documentation/) e funciona com aplicativos existentes do Kafka, incluindo o MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>O que os Hubs de eventos para o Kafka oferece?

Os Hubs de eventos para o recurso de Kafka fornece um cabeçalho de protocolo na parte superior de Hubs de eventos do Azure que é binário compatível com versões do Kafka 1.0 e posterior para tanto leitura e gravação para os tópicos do Kafka. Você pode começar a usar o ponto de extremidade do Kafka dos aplicativos sem alteração de código, mas com uma alteração mínima de configuração. Você atualiza a cadeia de conexão nas configurações para apontar para o ponto de extremidade do Kafka exposto pelo hub de eventos, em vez de apontar para o cluster do Kafka. Em seguida, você poderá começar a transmitir eventos dos aplicativos que usam o protocolo Kafka nos Hubs de Eventos. Essa integração também dá suporte a estruturas como [Kafka conectar](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), que está atualmente em visualização. 

Conceitualmente, Kafka e Hubs de Eventos são quase idênticos: ambos são logs particionados criados para a transmissão de dados. A tabela a seguir mapeia conceitos entre Kafka e Hubs de Eventos.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapeamento conceitual de Kafka e Hub de Eventos

| Conceito de Kafka | Conceito de Hubs de Eventos|
| --- | --- |
| HDInsight | Namespace |
| Tópico | Hub de evento |
| Partition | Partition|
| Grupo de Consumidores | Grupo de Consumidores |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Principais diferenças entre Kafka e Hubs de Eventos

Embora o [Kafka Apache](https://kafka.apache.org/) seja um software, que pode ser executado sempre que você quiser, os Hubs de Eventos são um serviço de nuvem semelhante ao Armazenamento de Blobs do Azure. Não há nenhum servidor ou rede para gerenciar e nenhum agente para configurar. Crie um namespace, que é um FQDN no qual seus tópicos residem e crie Hubs de Eventos ou tópicos dentro desse namespace. Para obter mais informações sobre namespaces e Hubs de Eventos, consulte [Recursos de Hubs de Eventos](event-hubs-features.md#namespace). Como um serviço de nuvem, os Hubs de Eventos usam um único endereço IP virtual estável como o ponto de extremidade, para que os clientes não precisem saber sobre os agentes ou computadores em um cluster. 

A escala em Hubs de Eventos é controlada pelo número de unidades de produtividade que você adquirir, com cada unidade de produtividade lhe dando direito a 1 MB por segundo ou 1000 eventos por segundo de entrada. Por padrão, os Hubs de Eventos ampliam as unidades de taxa de transferência quando você atinge seu limite com o recurso [Inflação Automática](event-hubs-auto-inflate.md); esse recurso também funciona com o recurso Hubs de Eventos para Kafka. 

### <a name="security-and-authentication"></a>Segurança e autenticação

Os Hubs de Eventos do Azure requerem SSL ou TLS para toda a comunicação e usam Assinaturas de Acesso Compartilhado (SAS) para autenticação. Esse requisito também é verdadeiro para um ponto de extremidade Kafka em Hubs de Eventos. Para compatibilidade com Kafka, os Hubs de Eventos usam SASL PLAIN para autenticação e SASL SSL para segurança de transporte. Para obter mais informações sobre a segurança em Hubs de Eventos, consulte [Autenticação e segurança de Hubs de Eventos](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Outros recursos de Hubs de Eventos disponíveis para Kafka

O recurso Hub de eventos para Kafka permite que você escreva com um protocolo e leia com outro, para que seus produtores atuais do Kafka possam continuar publicando via Kafka e você pode adicionar leitores a Hubs de eventos, como o Azure Stream Analytics ou o Azure Functions. Além disso, os recursos do Event Hubs, como [Capture](event-hubs-capture-overview.md) e [Geo Disaster-Recovery](event-hubs-geo-dr.md), também funcionam com o recurso Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Recursos que ainda não são suportados 

Aqui está a lista de recursos de Kafka que ainda não têm suporte:

*   Produtor idempotente
*   Transação
*   Compactação
*   Retenção baseada em tamanho
*   Compactação do log
*   Adicionando partições a um tópico existente
*   Suporte a API de Kafta HTTP
*   Kafka Streams

## <a name="next-steps"></a>Próximas etapas

Este artigo forneceu uma introdução aos Hubs de Eventos para Kafka. Para saber mais, consulte os seguintes links:

- [Como criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md)
- [Transmitir para Hubs de Eventos a partir de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Espelhar um agente do Kafka em um hub de eventos habilitado para Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos habilitado para Kafka](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos habilitado para Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos habilitado para Kafka](event-hubs-kafka-connect-tutorial.md)
- [Conectar o Akka Streams a um hub de eventos habilitado para Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


