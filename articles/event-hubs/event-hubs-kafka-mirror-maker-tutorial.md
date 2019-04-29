---
title: Usar o MirrorMaker do Apache Kafka - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka MirrorMaker para espelhar um cluster do Kafka no AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: a7271eb6b8cbc8a117b5a8e75edfe02985ec3452
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821521"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Use Kafka MirrorMaker com Hubs de Eventos para o Apache Kafka

Este tutorial mostra como espelhar um agente do Kafka em um hub de eventos ativado pelo Kafka usando o Kafka MirrorMaker.

   ![MirrorMaker Kafka com Hubs de Eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar o Kafka MirrorMaker
> * Executar o Kafka MirrorMaker

## <a name="introduction"></a>Introdução
Uma consideração importante para aplicativos de escala de nuvem modernos é a capacidade de atualizar, melhorar e alterar a infraestrutura sem interromper o serviço. Este tutorial mostra como um hub de eventos habilitado para Kafka e como o Kafka MirrorMaker pode integrar um pipeline Kafka existente no Azure "espelhando" o fluxo de entrada do Kafka no serviço Hubs de Eventos. 

Um ponto de extremidade Kafka dos Hubs de Eventos do Azure permite que você se conecte aos Hubs de Eventos do Azure usando o protocolo Kafka (ou seja, clientes Kafka). Ao fazer alterações mínimas em um aplicativo do Kafka, você pode se conectar aos Hubs de Eventos do Azure e aproveitar os benefícios do ecossistema do Azure. Os Hubs de Eventos habilitados para Kafka atualmente oferecem suporte às versões de Kafka 1.0 e posteriores.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7 +](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](https://maven.apache.org/download.cgi) e [instalar](https://maven.apache.org/install.html) um armazenamento binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [Criando um Hub de Eventos habilitado para Kafka](event-hubs-create.md) para obter instruções sobre como obter um ponto de extremidade Kafka nos Hubs de Eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de Hubs de Eventos habilitada para Kafka, clone o repositório dos Hubs de Eventos do Azure para Kafka e navegue até a subpasta `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster Kafka

Use o [guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as configurações desejadas (ou use um cluster Kafka existente).

## <a name="configure-kafka-mirrormaker"></a>Configurar o Kafka MirrorMaker

O Kafka MirrorMaker permite o "espelhamento" de um fluxo. Dada a origem e destino dos clusters Kafka, o MirrorMaker garante que todas as mensagens enviadas para o cluster de origem são recebidas por clusters de origem e de destino. Este exemplo mostra como espelhar um cluster Kafka de origem com um hub de eventos habilitado para Kafka de destino. Esse cenário pode ser usado para enviar dados de um pipeline Kafka existente para Hubs de Eventos sem interromper o fluxo de dados. 

Para obter mais informações sobre o Kafka MirrorMaker, consulte o [guia de Espelhamento do Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Para configurar o Kafka MirrorMaker, dê a ele um cluster Kafka como consumidor/origem e um hub de eventos habilitado para Kafka como produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o arquivo de configuração do consumidor `source-kafka.config`, que informa ao MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora, atualize o arquivo de configuração do produtor `mirror-eventhub.config`, que manda o MirrorMaker enviar os dados duplicados (ou "espelhados") para o serviço de Hubs de Eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de extremidade Kafka dos Hubs de Eventos. O serviço dos Hubs de Eventos requer uma comunicação segura (SASL), que é obtida com a definição das três últimas propriedades na configuração a seguir: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Executar o Kafka MirrorMaker

Execute o script do Kafka MirrorMaker a partir do diretório raiz do Kafka usando os arquivos de configuração atualizados recentemente. Certifique-se de copiar os arquivos de configuração para o diretório raiz do Kafka, ou de atualizar seus caminhos no comando a seguir.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar que eventos estão atingindo o hub de eventos habilitado para Kafka, confira as estatísticas de entrada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), ou execute um consumidor no hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados para a fonte de cluster Kafka são recebidos pelo cluster Kafka e o serviço do hub de eventos habilitado para Kafka espelhado. Usando o MirrorMaker e um ponto de extremidade Kafka nos Hubs de Eventos, você pode migrar um pipeline Kafka existente para o serviço de Hubs de Eventos do Azure gerenciado sem alterar o cluster existente ou interromper qualquer fluxo de dados em andamento.

## <a name="samples"></a>Exemplos
Consulte os seguintes exemplos no GitHub:

- [Código de exemplo para este tutorial no GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure evento Hubs Kafka MirrorMaker em execução em uma instância de contêiner do Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar um cluster Kafka
> * Configurar o Kafka MirrorMaker
> * Executar o Kafka MirrorMaker

Para saber mais sobre os Hubs de Eventos e Hubs de Eventos para o Kafka, consulte o tópico a seguir:  

- [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
- [Hubs de Eventos do Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Como criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md)
- [Transmitir para Hubs de Eventos a partir de seus aplicativos Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Conectar o Apache Spark a um hub de eventos habilitado para Kafka](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos habilitado para Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrar o Kafka Connect a um hub de eventos habilitado para Kafka](event-hubs-kafka-connect-tutorial.md)
- [Conectar o Akka Streams a um hub de eventos habilitado para Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
