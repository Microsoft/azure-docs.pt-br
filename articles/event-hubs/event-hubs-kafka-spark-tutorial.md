---
title: Conecte-se com seu aplicativo Apache Spark – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Apache Spark com Hubs de Eventos do Azure para Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913731"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Conectar seu aplicativo Apache Spark com os hubs de eventos do Azure
Este tutorial orienta você pela conexão do aplicativo Spark aos hubs de eventos para streaming em tempo real. Essa integração permite o streaming sem a necessidade de alterar seus clientes de protocolo ou executar seus próprios clusters Kafka ou Zookeeper. Este tutorial requer o Apache Spark v 2.4 + e Apache Kafka v 2.0 +.

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o Spark
> * Ler dos Hubs de Eventos para Kafka
> * Gravar nos Hubs de Eventos para o Kafka

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial, verifique se você possui:
-   Assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> O adaptador Spark-Kafka foi atualizado para dar suporte ao Kafka v2.0 a partir do Spark v2.4. Em versões anteriores do Spark, o adaptador dava suporte ao Kafka v0.10 e versões posteriores, mas dependia especificamente das APIs do Kafka v0.10. Como os Hubs de Eventos para Kafka não dão suporte ao Kafka v0.10, os adaptadores Spark-Kafka de versões do Spark anteriores à v2.4 não têm suporte pelos Hubs de Eventos para ecossistemas do Kafka.


## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Obtenha a cadeia de conexão dos Hubs de Eventos e o FQDN (nome de domínio totalmente qualificado) para uso posterior. Para obter instruções, confira [Obter uma cadeia de conexão dos Hubs de Eventos](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo
Clone o repositório dos Hubs de Eventos do Azure e navegue até a subpasta `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Ler dos Hubs de Eventos para Kafka
Com algumas alterações de configuração, você pode começar a ler dos Hubs de Eventos para Kafka. Atualize **BOOTSTRAP_SERVERS** e **EH_SASL** com detalhes de seu namespace e você poderá iniciar o streaming com os Hubs de Eventos como faria com o Kafka. Para ver o código de exemplo completo, consulte o arquivo sparkConsumer.scala no GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Gravar nos Hubs de Eventos para o Kafka
Você também pode gravar nos Hubs de Eventos da mesma forma que gravaria no Kafka. Não se esqueça de atualizar sua configuração para alterar **BOOTSTRAP_SERVERS** e **EH_SASL** com informações de seu namespace dos Hubs de Eventos.  Para ver o código de exemplo completo, consulte o arquivo sparkProducer.scala no GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, confira os artigos a seguir:  

- [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)

