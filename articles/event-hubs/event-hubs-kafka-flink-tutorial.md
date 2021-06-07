---
title: Usar o Apache Flink para o Apache Kafka – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como conectar o Apache Flink a um hub de eventos do Azure
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369498"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Usar o Apache Flink com Hubs de Eventos do Azure para o Apache Kafka
Este tutorial mostra como conectar o Apache Flink a um hub de eventos sem alterar seus clientes de protocolo ou executar seus próprios clusters. Para obter mais informações sobre o suporte de hubs de eventos para o protocolo de consumidor Apache Kafka, consulte [hubs de eventos para Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Executar o produtor Flink 
> * Executar o consumidor Flink

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário atender aos seguintes pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um arquivo binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace dos hubs de eventos

É necessário um namespace dos Hubs de Eventos para enviar ou receber de qualquer serviço de Hubs de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem a cadeia de conexão dos hubs de eventos, clone os hubs de eventos do Azure para o repositório Kafka e navegue até a `flink` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Executar o produtor Flink

Seguindo o exemplo do produtor do Flink, envie mensagens para o serviço do Hubs de Eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="producerconfig"></a>producer.config

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `producer/src/main/resources/producer.config` para direcionar o produtor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Executar o produtor na linha de comando

Para executar o produtor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Agora, o produtor começará a enviar eventos para o Hub de eventos no tópico `test` e imprimirá os eventos para stdout.

## <a name="run-flink-consumer"></a>Executar o consumidor Flink

Usando o exemplo de consumidor fornecido, receba mensagens do hub de eventos. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="consumerconfig"></a>consumer.config

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `consumer/src/main/resources/consumer.config` para direcionar o consumidor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor na linha de comando

Para executar o consumidor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Se o Hub de eventos tiver eventos (por exemplo, se o seu produtor também estiver em execução), o consumidor agora começará a receber eventos do tópico `test` .

Confira [Guia de conector Kafka do Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) para saber informações mais detalhadas sobre como conectar o Flink ao Kafka.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os hubs de eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)