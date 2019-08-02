---
title: Streaming em escala no Azure HDInsight
description: Como usar fluxo de dados com Clusters HDInsight escalonáveis.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 97bc2e585ccce250dd83dc855c3eda5f958186a7
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484068"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Streaming em escala no HDInsight

Soluções de big data em tempo real atuam em dados que estão em movimento. Normalmente, esses dados são mais valiosos em seu tempo de chegada. Se o fluxo de dados de entrada tornar-se maior do que pode ser tratado no momento, talvez seja necessário restringir os recursos. Alternativamente, um Cluster HDInsight pode escalar verticalmente para atender sua solução de streaming, adicionando nós sob demanda.


Em um aplicativo de streaming, uma ou mais fontes de dados geram eventos (às vezes em milhões por segundo) que precisam ser ingeridos rapidamente sem remover quaisquer informações úteis. Os eventos de entrada são tratados com *buffer do fluxo*, também chamado de *enfileiramento de eventos*, por um serviço como [Apache Kafka](kafka/apache-kafka-introduction.md) ou [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/). Após coletar os eventos, será possível analisar os dados usando um sistema de análise em tempo real dentro da camada do *processamento do fluxo* como [Apache Storm](storm/apache-storm-overview.md) ou [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Os dados processados podem ser armazenados em sistemas de armazenamento em longo prazo, como o [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) e exibido em tempo real em um painel do business intelligence, como [Power BI](https://powerbi.microsoft.com), Tableau ou uma página da Web personalizada.


![Padrões de Streaming do HDInsight](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

O Apache Kafka fornece um serviço de enfileiramento de mensagens de baixa latência e com alta taxa de transferência e agora faz parte do conjunto Apache do OSS (Open Source Software). O Kafka usa um modelo de mensagens de publicação e assinatura e armazena fluxos de dados particionados com segurança em um cluster distribuído e replicado. O Kafka escala linearmente à medida que aumenta a taxa de transferência.

Para obter mais informações, consulte [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

O Apache Storm é um sistema de computação de fonte aberta distribuído, tolerante a falhas que é otimizado para processar fluxos de dados em tempo real com o Hadoop. A unidade central de dados para um evento de entrada é um Tuple, que é um conjunto imutável de pares chave-valor. Uma sequência ilimitada desse Tuples forma um Stream, que é proveniente de um Spout. O Spout encapsula uma fonte de dados de streaming (como Kafka) e emite Tuples. Uma topologia de tempestade é uma sequência de transformações nesses streams.

Para obter mais informações, consulte [O que é Apache Storm no Microsoft Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming  é uma extensão para Spark, que permite reutilizar o mesmo código usado para o processamento em lotes. É possível combinar consultas em lotes e interativas na mesma aplicação. Ao contrário do Storm, o Spark Streaming fornece uma semântica de processamento exatamente um estado. Quando usado em combinação com [Kafka Direct API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), o que garante que todos os dados do Kafka sejam recebidos pelo Spark Streaming exatamente uma vez, é possível obter exatamente garantias ponta a ponta. Um dos pontos fortes do Spark Streaming é a capacidade de tolerância a falhas, recuperando os nós com falhas rapidamente quando vários nós estão sendo usados dentro do cluster.

Para obter mais informações, consulte [O que é Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Dimensionamento de um cluster

Embora você possa especificar o número de nós no cluster durante a criação, convém aumentar ou reduzir o cluster de acordo com a carga de trabalho. Todos os Clusters HDInsight permitem que você [altere o número de nós no cluster](hdinsight-administer-use-portal-linux.md#scale-clusters). Os clusters do Spark podem ser removidos sem perda de dados, pois todos os dados ficam no Armazenamento do Microsoft Azure ou no Data Lake Storage.

Há vantagens para as tecnologias de dissociação. Por exemplo, o Kafka é uma tecnologia de buffer de evento e por isso é muito intensivo em E/S e não necessita de muita capacidade de processamento. Em comparação, os processadores de fluxo como o Spark Streaming são computação intensiva, exigindo VMs mais avançadas. Ao ter essas tecnologias separadas em clusters diferentes, você poderá dimensionar independentemente enquanto utiliza as VMs da melhor forma.

### <a name="scale-the-stream-buffering-layer"></a>Dimensionar a camada de buffer de fluxo

As tecnologias de buffer de fluxo dos Hubs de Eventos e Kafka usam partições e os consumidores fazem as leituras a partir dessas partições. Dimensionar a taxa de transferência de entrada exige escalar o número de partições e adicionar partições fornece paralelismo crescente. Em Hubs de Eventos, a contagem de partições não poderá ser alterada após a implantação e por isso é importante iniciar com a escala de destino em mente. Com o Kafka, é possível [adicionar partições](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), mesmo quando o Kafka estiver processando dados. O Kafka fornece uma ferramenta para reatribuir partições, `kafka-reassign-partitions.sh`. O HDInsight fornece uma [ferramenta de rebalanceamento de réplica de partição](https://github.com/hdinsight/hdinsight-kafka-tools),  `rebalance_rackaware.py`. Essa ferramenta de rebalanceamento chama a ferramenta `kafka-reassign-partitions.sh` de forma que cada réplica esteja em um domínio de falha separado e o domínio de atualização, com reconhecimento de rack do Kafka e aumentando a tolerância a falhas.

### <a name="scale-the-stream-processing-layer"></a>Dimensionar a camada de processamento de fluxo

Tanto o Apache Storm quanto o Spark Streaming suportam os nós de trabalho para seus clusters, mesmo quando os dados estão sendo processados.

Para aproveitar os novos nós adicionados através do dimensionamento do Storm, será necessário reequilibrar as topologias do Storm iniciadas antes do aumento do tamanho do cluster. Esse rebalanceamento pode ser feito usando a interface do usuário da Web Storm ou a CLI. Para obter mais informações, consulte [Documentação do Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

O Apache Spark usa três parâmetros fundamentais para configurar o ambiente, dependendo dos requisitos do aplicativo: `spark.executor.instances`, `spark.executor.cores`, e `spark.executor.memory`. Um *executor* é um processo lançado para um aplicativo Spark. Um executor é executado no nó de trabalho e é responsável pela execução das tarefas do aplicativo. O número padrão de executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Esses números são armazenados no arquivo `spark-defaults.conf`em cada nó de cabeçalho do cluster.

Esses três parâmetros podem ser configurados no nível do cluster para todos os aplicativos executados no cluster e também podem ser especificados para cada aplicativo individual. Para obter mais informações, consulte [Gerenciando recursos para clusters Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar e monitorar uma topologia Apache Storm no HDInsight do Azure](storm/apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](storm/apache-storm-example-topology.md)
* [Introdução ao Apache Spark no HDInsight](spark/apache-spark-overview.md)
* [Iniciar com o Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
