---
title: Exemplo de topologias Apache Storm no Microsoft Azure HDInsight
description: Uma lista das topologias de exemplo do Storm criadas e testadas com o Apache Storm no HDInsight, incluindo topologias básicas C# e Java e trabalho com os Hubs de Eventos.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: a535510ce56147f708f1cd8219cf898e6e0df36b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483785"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exemplo de topologias e componentes do Apache Storm para o Apache Storm no HDInsight

A seguir está uma lista de exemplos criados e mantidos pela Microsoft para uso com [Apache Storm](https://storm.apache.org/) no HDInsight. Esses exemplos abrangem uma variedade de tópicos, desde a criação de topologias C# e Java básicas até o trabalho com os serviços do Azure, como Hubs de Eventos, Banco de Dados Cosmos, Banco de Dados SQL, [Apache HBase](https://hbase.apache.org/) no HDInsight e Armazenamento do Azure. Alguns exemplos também demonstram como trabalhar com tecnologias não Azure ou até mesmo com tecnologias não Microsoft, como o SignalR e Socket.IO.

| DESCRIÇÃO | Demonstra | Estrutura/linguagem |
|:--- |:--- |:--- |
| [Gravar no Azure Data Lake Storage do Apache Storm](apache-storm-write-data-lake-store.md) |Gravando no Azure Data Lake Storage |Java |
| [Origem do Bolt e Spout do Hub de Eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Fonte para o Bolt e Spout do Hub de Eventos |Java |
| [Desenvolver topologias baseadas em Java para Apache Storm no HDInsight][5797064f] |Maven |Java |
| [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio][16fce2d1] |Ferramentas do HDInsight para Visual Studio |C#, Java |
| [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (C#)][844d1d81] |Hubs de Eventos |C# e Java |
| [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/) |Hubs de Eventos |Java |
| [Processar dados de sensor de veículo nos Hubs de Eventos usando o Apache Storm no HDInsight][246ee964] |Hubs de Eventos, Cosmos DB, WASB (Azure Storage Blob) |C#, Java |
| [Extração, transformação e carregamento (ETL) de Hubs de eventos do Azure para o Apache HBase, usando o Apache Storm no HDInsight][b4b68194] |Hubs de Evento, HBase |C# |
| [Modelo C# projeto de topologia do Storm para trabalhar com os serviços do Azure do Apache Storm no HDInsight][ce0c02a2] |Hubs de Eventos, Cosmos DB, Banco de Dados SQL, HBase, SignalR |C#, Java |
| [Parâmetros de comparação de escalabilidade para leitura dos Hubs de eventos do Azure usando o Apache Storm no HDInsight][d6c540e3] |Taxa de transferência de mensagem, Hubs de Evento, Banco de Dados SQL |C#, Java |
| [Usar o Python com o Apache Storm no HDInsight](apache-storm-develop-python-topology.md) |Componentes do Python com uma topologia Flux |Python |
| [Use o Apache Kafka com o Apache Storm no HDInsight](../hdinsight-apache-storm-with-kafka.md) | Leitura e gravação em Apache Storm para o Apache Kafka | Java |

> [!WARNING]  
> Os exemplos em C# nesta lista foram originalmente criados e testados com HDInsight baseados em Windows e podem não funcionar corretamente com clusters HDInsight baseados em Linux. Os clusters baseados em Linux usam Mono para executar o código .NET e poderão ter problemas de compatibilidade com as estruturas e os pacotes usados no exemplo.
>
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou posterior.

### <a name="next-steps"></a>Próximas etapas

* [Criar e monitorar uma topologia Apache Storm no HDInsight do Azure](./apache-storm-quickstart.md)
* [Saiba como implantar e gerenciar topologias Apache Storm com o Apache Storm no HDInsight][6eb0d3b8]


[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Saiba como implantar e gerenciar topologias usando o Apache Storm Dashboard e a interface do usuário do Storm baseados na Web ou as ferramentas do HDInsight para Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Saiba como criar topologias Storm do C# usando as Ferramentas do HDInsight para Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Saiba como criar topologias Storm em Java, usando o Maven, criando uma topologia básica de contagem de palavras."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Saiba como ler e gravar dados dos Hubs de Eventos do Azure com o Storm no HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Saiba como usar uma topologia Storm para ler mensagens dos Hubs de Eventos do Azure, ler documentos do Azure Cosmos DB para referência a dados e salvar os dados no Armazenamento do Azure."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Várias topologias para demonstrar a produtividade ao ler com base nos Hubs de Eventos do Azure e armazenar no Banco de Dados SQL usando o Apache Storm no HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Saiba como ler dados dos Hubs de Eventos do Azure, agregar e transformar os dados e armazená-los no HBase no HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este projeto contém modelos de spouts, bolts e topologias para interagir com vários serviços do Azure como Hubs de Eventos, Cosmos DB e Banco de Dados SQL."

