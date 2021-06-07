---
title: Usar o MirrorMaker do Apache Kafka - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka MirrorMaker para espelhar um cluster do Kafka no AzureEvent Hubs.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97860998"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Usar Apache Kafka MirrorMaker com hubs de eventos

Este tutorial mostra como espelhar um agente do Kafka em um hub de eventos do Azure usando o Kafka MirrorMaker. Se você estiver hospedando Apache Kafka em kubernetes usando o operador CNCF Strimzi, poderá consultar o tutorial nesta [postagem de blog](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) para saber como configurar o Kafka com o Strimzi e o Mirror Maker 2. 

   ![MirrorMaker Kafka com Hubs de Eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)

> [!NOTE]
> Este artigo contém referências ao termo *lista de permissões*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar o Kafka MirrorMaker
> * Executar o Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Este tutorial mostra como um hub de eventos e Kafka MirrorMaker podem integrar um pipeline Kafka existente ao Azure por meio do "espelhamento" do fluxo de entrada Kafka no serviço de hubs de eventos, que permite a integração de fluxos de Apache Kafka usando vários [padrões de Federação](event-hubs-federation-overview.md). 

Um ponto de extremidade Kafka dos Hubs de Eventos do Azure permite que você se conecte aos Hubs de Eventos do Azure usando o protocolo Kafka (ou seja, clientes Kafka). Ao fazer alterações mínimas em um aplicativo do Kafka, você pode se conectar aos Hubs de Eventos do Azure e aproveitar os benefícios do ecossistema do Azure. Atualmente, os hubs de eventos dão suporte ao protocolo de Apache Kafka versões 1,0 e posteriores.

Você pode usar a MirrorMaker 1 do Apache Kafka de uma unidirecional de Apache Kafka para os hubs de eventos. MirrorMaker 2 pode ser usado em ambas as direções, mas as [ `MirrorCheckpointConnector` e `MirrorHeartbeatConnector` que são configuráveis no MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) devem ser configuradas para apontar para o agente de Apache Kafka e não para os hubs de eventos. Este tutorial mostra a configuração de MirrorMaker 1.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

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

É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de hubs de eventos, clone os hubs de eventos do Azure para o repositório Kafka e navegue até a `mirror-maker` subpasta:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster Kafka

Use o [guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as configurações desejadas (ou use um cluster Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar o Kafka MirrorMaker

O Kafka MirrorMaker permite o "espelhamento" de um fluxo. Dada a origem e destino dos clusters Kafka, o MirrorMaker garante que todas as mensagens enviadas para o cluster de origem são recebidas por clusters de origem e de destino. Este exemplo mostra como espelhar um cluster Kafka de origem com um hub de eventos de destino. Esse cenário pode ser usado para enviar dados de um pipeline Kafka existente para Hubs de Eventos sem interromper o fluxo de dados. 

Para obter mais informações sobre o Kafka MirrorMaker, consulte o [guia de Espelhamento do Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dê a ele um cluster Kafka como seu consumidor/origem e um hub de eventos como seu produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o arquivo de configuração do consumidor `source-kafka.config`, que informa ao MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora, atualize o arquivo de configuração do produtor `mirror-eventhub.config`, que manda o MirrorMaker enviar os dados duplicados (ou "espelhados") para o serviço de Hubs de Eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de extremidade Kafka dos Hubs de Eventos. O serviço dos Hubs de Eventos requer uma comunicação segura (SASL), que é obtida com a definição das três últimas propriedades na configuração a seguir: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Executar o Kafka MirrorMaker

Execute o script do Kafka MirrorMaker a partir do diretório raiz do Kafka usando os arquivos de configuração atualizados recentemente. Certifique-se de copiar os arquivos de configuração para o diretório raiz do Kafka, ou de atualizar seus caminhos no comando a seguir.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar se os eventos estão atingindo o Hub de eventos, consulte as estatísticas de entrada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/)ou execute um consumidor no Hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados ao cluster Kafka de origem são recebidos pelo cluster Kafka e pelo hub de eventos espelhado. Usando o MirrorMaker e um ponto de extremidade Kafka nos Hubs de Eventos, você pode migrar um pipeline Kafka existente para o serviço de Hubs de Eventos do Azure gerenciado sem alterar o cluster existente ou interromper qualquer fluxo de dados em andamento.

## <a name="samples"></a>Exemplos
Confira as seguintes amostras no GitHub:

- [Código de exemplo para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Hubs de eventos do Azure Kafka MirrorMaker em execução em uma instância de contêiner do Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os hubs de eventos para Kafka, consulte os seguintes artigos:  

- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos](event-hubs-kafka-connect-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)