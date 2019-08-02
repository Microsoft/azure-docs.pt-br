---
title: O que é o Apache Storm – Azure HDInsight
description: O Apache Storm permite que você processe fluxos de dados em tempo real. O Azure HDInsight permite que você crie clusters Storm em nuvem do Azure facilmente. Com o Visual Studio, você pode criar soluções Storm usando C# e depois implantar seus clusters Storm do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: casos de uso do apache storm, cluster storm, o que é o apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 00c536120ed9507d5ecc6fe930429d12514945b3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483829"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](https://storm.apache.org/) é um sistema de computação distribuída, tolerante a falhas e de software livre. Você pode usar o Storm para processar fluxos de dados em tempo real com o [Apache Hadoop](https://hadoop.apache.org/). As soluções do Storm também podem oferecer um processamento de dados garantido, com a capacidade de reproduzir dados que não tenham sido processados com sucesso da primeira vez.

## <a name="why-use-apache-storm-on-hdinsight"></a>Por que usar o Apache Storm no HDInsight?

O Storm no HDInsight oferece os seguintes recursos:

* __99% Contrato de Nível de Serviço (SLA) no tempo de atividade do Storm__: Para saber mais, veja o documento [Informações de SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Dá suporte à personalização fácil executando scripts em relação a um cluster Storm durante ou após a criação. Para obter mais informações, confira [Personalizar clusters HDInsight usando a ação de script](../hdinsight-hadoop-customize-cluster-linux.md).

* **Crie soluções em vários idiomas**: Você pode escrever componentes do Storm na linguagem de sua escolha, como Java, C# e Python.

    * Integra o Visual Studio ao HDInsight para desenvolvimento, gerenciamento e monitoramento de topologias em C#. Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Dá suporte à interface Java Trident. Você pode criar topologias Storm que dão suporte ao processamento de mensagens exatamente uma vez, à persistência de armazenamento de dados transacional e a um conjunto de operações de análise de fluxo comuns.

* **Escala dinâmica**: Você pode adicionar ou remover nós de trabalho sem afetar a execução de topologias Storm.

    * Você deve desativar e reativar topologias em execução para aproveitar novos nós adicionados por meio de operações de dimensionamento.

* **Criar pipelines de streaming usando vários serviços do Azure**: O Storm no HDInsight integra-se a outros serviços do Azure, como Hubs de Eventos, Banco de Dados SQL, Armazenamento do Azure e Azure Data Lake Storage.

    Para ver um exemplo de solução que se integrar aos serviços do Azure, consulte [Processar eventos dos Hubs de Eventos com Apache Storm no HDInsight](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

Para obter uma lista de empresas que estão usando o Apache Storm em suas soluções de análise em tempo real, consulte [Empresas que estão usando o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a usar o Storm, consulte [Criar e monitorar uma topologia Apache Storm no do Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Como funciona o Apache Storm

O Storm processa topologias em vez dos trabalhos do [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) com que você pode estar familiarizado. As topologias do Storm são compostas por vários componentes organizados em um DAG (grafo direcionado acíclico). Fluxo de dados entre os componentes no grafo. Cada componente consome um ou mais fluxos de dados e, opcionalmente, pode emitir um ou mais fluxos. O diagrama a seguir ilustra como os dados fluem entre componentes em uma topologia de contagem de palavras básica:

![Exemplo de como os componentes são organizados em uma topologia do Storm](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes Spout trazem dados para uma topologia. Eles emitem um ou mais fluxos na topologia.

* Os componentes Bolt consomem fluxos emitidos de spouts ou de outros bolts. Os bolts podem opcionalmente emitir fluxos na topologia. Os bolts também são responsáveis por gravar dados em armazenamentos ou serviço externos, como HDFS, Kafka ou HBase.

## <a name="reliability"></a>Confiabilidade

O Apache Storm sempre faz com que cada mensagem de entrada seja sempre totalmente processada, mesmo quando a análise de dados é difundida por centenas de nós.

O nó Nimbus fornece uma funcionalidade semelhante ao Apache Hadoop JobTracker e atribui tarefas a outros nós em um cluster por meio do [Apache ZooKeeper](https://zookeeper.apache.org/). Os Nós do Zookeeper fazem a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo Supervisor nos nós de trabalho. Quando um nó de processamento falha, o nó Nimbus é informado e atribui a tarefa e os dados associados a outro nó.

A configuração padrão dos clusters Apache Storm é ter apenas um nó Nimbus. O Storm no HDInsight oferece dois nós Nimbus. Se o nó primário falhar, o cluster Storm alternará para o nó secundário enquanto o nó primário é recuperado. O diagrama a seguir ilustra a configuração de fluxo de tarefa para o Storm no HDInsight:

![Diagrama do Nimbus, Zookeeper e Supervisor](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>Fácil de criar

Você pode provisionar um novo cluster Storm no HDInsight em minutos. Para obter mais informações sobre como criar um cluster Storm, consulte [Criar clusters do Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Fácil de uso

* __Conectividade Secure Shell (SSH)__ : Você pode acessar os nós principais do cluster Storm pela Internet usando o SSH. Você pode executar comandos diretamente no cluster usando SSH.

  Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade da Web__: Todos os clusters HDInsight fornecem a interface da Web do Ambari. Você pode facilmente monitorar, configurar e gerenciar serviços no cluster usando a interface da Web do Ambari. Os clusters do Storm também fornecem a interface do usuário do Storm. Você pode monitorar e gerenciar topologias Storm em execução no navegador usando a interface do usuário do Storm.

  Para obter mais informações, confira os documentos [Administrar o HDInsight usando a interface do usuário do Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e [Monitorar e gerenciar usando a interface do usuário da interface do usuário do Apache Storm](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI Clássica do Azure__: o PowerShell e a CLI clássica fornecem utilitários de linha de comando que você pode usar no sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: As Ferramentas do Azure Data Lake para Visual Studio incluem modelos de projeto para criar topologias Storm em C# usando a estrutura SCP.NET. O Data Lake Tools também fornece ferramentas para implantar, monitorar e gerenciar soluções com o Storm no HDInsight.

  Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração a outros serviços do Azure

* __Azure Data Lake Storage__: Para obter um exemplo de como usar o Data Lake Store com um cluster Storm, confira [Usar o Azure Data Lake Store com Apache Storm no HDInsight](apache-storm-write-data-lake-store.md).

* __Hubs de Evento__: Para obter um exemplo de como usar os Hubs de Eventos com um cluster Storm, veja os seguintes exemplos:

    * [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Banco de Dados SQL__, __Cosmos DB__, __Hubs de eventos__, e __HBase__: Exemplos de modelo são incluídos nas Ferramentas Data Lake para Visual Studio. Para saber mais,confira [Desenvolver uma topologia em C# para Apache Storm no HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Suporte

O Storm no HDInsight é fornecido com suporte contínuo completo de nível empresarial. O Storm no HDInsight também tem um SLA de 99,9%. Isso significa a Microsoft garante que um cluster Storm tenha conectividade externa pelo menos 99,9% do tempo.

Para saber mais, confira o [Suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Casos de uso do Apache Storm

Abaixo estão alguns cenários comuns em que você pode usar o Storm no HDInsight:

* Internet das coisas (IoT)
* Detecção de fraude
* Análise das redes sociais
* Extração, transformação e carregamento (ETL)
* Monitoramento de rede
* Search
* Mobile Engagement

Para obter informações sobre cenários do mundo real, consulte o [Como as empresas estão usando o documento Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Desenvolvimento

Os desenvolvedores do .NET podem projetar e implementar topologias em C# usando Data Lake Tools para Visual Studio. Você também pode criar topologias híbridas que usam componentes Java e C#.

Para obter mais informações, consulte [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Você também pode desenvolver soluções Java usando o IDE de sua escolha. Para obter mais informações, consulte [desenvolver topologias Java para Apache Storm no HDInsight](apache-storm-develop-java-topology.md).

Também é possível usar Python para desenvolver componentes do Storm. Para obter mais informações, consulte [Desenvolver topologias do Apache Storm usando Python no HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões de desenvolvimento comuns

### <a name="guaranteed-message-processing"></a>Processamento de mensagem garantido

O Apache Storm pode oferecer diferentes níveis de processamento de mensagem garantido. Por exemplo, um aplicativo básico Storm pode garantir um processamento pelo menos uma vez e o [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte [Garantias do processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de leitura de uma tupla de entrada, emitindo zero ou mais tuplas, e depois confirmando a tupla de entrada imediatamente no final do método de execução é comum. O Storm oferece a interface [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar esse padrão.

### <a name="joins"></a>Junções

O modo como os fluxos de dados são unidos varia entre aplicativos. Por exemplo, você pode juntar cada tupla de vários fluxos em um novo fluxo ou pode juntar somente lotes de tuplas para uma janela específica. De qualquer forma, a junção pode ser realizada usando [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). O agrupamento de campo é uma maneira de definir como as tuplas são roteadas para os bolts.

No exemplo de Java a seguir, fieldsGrouping é usado para rotear tuplas provenientes dos componentes "1", "2" e "3" para o bolt MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Lotes

O Apache Storm fornece um mecanismo de cronometragem interno conhecido como "tupla em escala". Você pode definir quantas vezes uma tupla em escala é emitida em sua topologia.

Para obter um exemplo de como usar uma tupla de escala de um componente em C#, confira [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

O cache na memória geralmente é usado como um mecanismo para acelerar o processamento, uma vez que mantém os ativos usados com mais frequência na memória. Como uma topologia é distribuída entre vários nós, e entre vários processos dentro de cada nó, considere o uso de [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Use `fieldsGrouping` para garantir que as tuplas que contêm os campos que são usados para pesquisa em cache sejam sempre roteadas para o mesmo processo. Essa funcionalidade de agrupamento evita a duplicação de entradas de cache entre os processos.

### <a name="stream-top-n"></a>Fluxo "N Superior"

Quando sua topologia depende do cálculo de um valor de N principal, calcule o valor N principal paralelamente. Em seguida, mescle o resultado desses cálculos em um valor global. Essa operação pode ser feita usando [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) para rotear por campo no processamento paralelo. Em seguida, você pode rotear para um bolt que determina o valor N principal globalmente.

Para obter um exemplo do cálculo de um valor de N principal, confira o exemplo [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registro em log

O Storm usa o [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) para registrar informações em log. Por padrão, uma grande quantidade de dados é registrada e pode ser difícil classificar as informações. Você pode incluir um arquivo de configuração de log como parte de sua topologia do Storm para controlar o comportamento de log.

Para uma topologia de exemplo que demonstra como configurar o log, veja o exemplo [WordCount baseado em Java](apache-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as soluções de análise em tempo real com o Apache Storm no HDInsight:

* [Criar e monitorar uma topologia Apache Storm no Azure HDInsight](apache-storm-quickstart.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)
