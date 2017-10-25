---
title: "O que é o Apache Storm - Azure HDInsight | Microsoft Docs"
description: "O Apache Storm permite que você processe fluxos de dados em tempo real. O Azure HDInsight permite que você crie clusters Storm em nuvem do Azure facilmente. Com o Visual Studio, você pode criar soluções Storm usando C# e depois implantar seus clusters Storm do HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: "casos de uso do apache storm, cluster storm, o que é o apache storm"
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/03/2017
ms.author: larryfr
ms.openlocfilehash: 073672f1223313938baedee027072cb96062294b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>O que é o Apache Storm no Azure HDInsight?

O [Apache Storm](http://storm.apache.org/) é um sistema de computação distribuída, tolerante a falhas e de software livre. Você pode usar o Storm para processar streams de dados em tempo real com o Hadoop. As soluções do Storm também podem oferecer um processamento de dados garantido, com a capacidade de reproduzir dados que não tenham sido processados com sucesso da primeira vez.

O Storm no HDInsight oferece os seguintes benefícios:

* É executado como um serviço gerenciado com um SLA de 99,9% de tempo de atividade.

* Dá suporte à personalização fácil executando scripts em relação a um cluster Storm durante ou após a criação. Para obter mais informações, confira [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

* Usa vários idiomas. Você pode escrever componentes do Storm na linguagem de sua escolha, como Java, C# e Python.

    * Integra o Visual Studio ao HDInsight para desenvolvimento, gerenciamento e monitoramento de topologias em C#. Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Dá suporte à interface Java Trident. Você pode criar topologias Storm que dão suporte ao processamento de mensagens exatamente uma vez, à persistência de armazenamento de dados transacional e a um conjunto de operações de análise de fluxo comuns.

*  Dimensione clusters Storm facilmente. Você pode adicionar ou remover nós de trabalho sem afetar a execução de topologias Storm.

* Integra-se com os seguintes serviços do Azure:

    * Hubs de eventos do Azure

    * Rede Virtual do Azure

    * Banco de Dados SQL do Azure

    * Armazenamento do Azure

    * Azure Cosmos DB

* Combina com segurança os recursos de vários clusters HDInsight usando a Rede Virtual. Você pode criar pipelines analíticos que usam clusters Storm, Kafka, Spark, HBase ou Hadoop.

Para obter uma lista de empresas que estão usando o Apache Storm em suas soluções de análise em tempo real, consulte [Empresas que estão usando o Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para começar a usar o Storm, consulte [Introdução ao uso do Storm no HDInsight][gettingstarted].

## <a name="how-does-storm-work"></a>Como funciona o Storm

O Storm processa topologias em vez dos trabalhos do MapReduce com que você pode estar familiarizado. As topologias do Storm são compostas por vários componentes organizados em um DAG (gráfico acíclico dirigido). Fluxo de dados entre os componentes no gráfico. Cada componente consome um ou mais fluxos de dados e, opcionalmente, pode emitir um ou mais fluxos. O diagrama a seguir ilustra como os dados fluem entre componentes em uma topologia de contagem de palavras básica:

![Exemplo de como os componentes são organizados em uma topologia do Storm](./media/hdinsight-storm-overview/example-apache-storm-topology-diagram.png)

* Os componentes Spout trazem dados para uma topologia. Eles emitem um ou mais fluxos na topologia.

* Os componentes Bolt consomem fluxos emitidos de spouts ou de outros bolts. Os bolts podem opcionalmente emitir fluxos na topologia. Os bolts também são responsáveis por gravar dados em armazenamentos ou serviço externos, como HDFS, Kafka ou HBase.

## <a name="ease-of-creation"></a>Fácil de criar

Você pode provisionar um novo cluster Storm no HDInsight em minutos. Para saber mais sobre como criar um cluster Storm, confira [Introdução ao Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

## <a name="ease-of-use"></a>Fácil de uso

* __Conectividade SSH (Secure Shell)__: você pode acessar os nós principais do cluster Storm pela Internet usando o SSH. Você pode executar comandos diretamente no cluster usando SSH.

  Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Conectividade da Web__: todos os clusters HDInsight fornecem a interface da Web do Ambari. Você pode facilmente monitorar, configurar e gerenciar serviços no cluster usando a interface da Web do Ambari. Os clusters do Storm também fornecem a interface do usuário do Storm. Você pode monitorar e gerenciar topologias Storm em execução no navegador usando a interface do usuário do Storm.

  Para obter mais informações, confira os documentos [Administrar o HDInsight usando a interface do usuário do Ambari](hdinsight-hadoop-manage-ambari.md) e [Monitorar e gerenciar usando a interface do usuário do Storm](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell e CLI do Azure__: o PowerShell e a CLI fornecem utilitários de linha de comando que você pode usar no sistema cliente para trabalhar com o HDInsight e outros serviços do Azure.

* __Integração do Visual Studio__: o Ferramentas do Azure Data Lake para Visual Studio inclui modelos de projeto para criar topologias Storm em C# usando a estrutura SCP.Net. O Data Lake Tools também fornece ferramentas para implantar, monitorar e gerenciar soluções com o Storm no HDInsight.

  Para obter mais informações, confira [Desenvolver topologias Storm C# com as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integração a outros serviços do Azure

* __Azure Data Lake Store__: para obter um exemplo de como usar o Data Lake Store com um cluster Storm, confira [Usar o Azure Data Lake Store com Apache Storm no HDInsight](hdinsight-storm-write-data-lake-store.md).

* __Hubs de Eventos__: para obter um exemplo de como usar os Hubs de Eventos com um cluster Storm, confira os seguintes documentos:

    * [Desenvolver uma topologia baseada em Java para Storm no HDInsight](hdinsight-storm-develop-java-topology.md)

    * [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __Banco de dados SQL__, __Cosmos DB__, __Hubs de Eventos__ e __HBase__: exemplos de modelo são incluídos nas Ferramentas Data Lake para Visual Studio. Para saber mais,confira [Desenvolver uma topologia em C# para Storm no HDInsight](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Confiabilidade

O Apache Storm sempre faz com que cada mensagem de entrada seja sempre totalmente processada, mesmo quando a análise de dados é difundida por centenas de nós.

O nó Nimbus fornece uma funcionalidade semelhante ao Hadoop JobTracker e atribui tarefas a outros nós em um cluster por meio do Zookeeper. Os Nós do Zookeeper fazem a coordenação de um cluster e facilitam a comunicação entre o Nimbus e o processo Supervisor nos nós de trabalho. Quando um nó de processamento falha, o nó Nimbus é informado e atribui a tarefa e os dados associados a outro nó.

A configuração padrão dos clusters Apache Storm é ter apenas um nó Nimbus. O Storm no HDInsight oferece dois nós Nimbus. Se o nó primário falhar, o cluster Storm alternará para o nó secundário enquanto o nó primário é recuperado. O diagrama a seguir ilustra a configuração de fluxo de tarefa para o Storm no HDInsight:

![Diagrama do Nimbus, Zookeeper e Supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Escala

Os clusters HDInsight podem ser dimensionados dinamicamente com a adição ou remoção de nós de trabalho. Essa operação pode ser executada durante o processamento de dados.

> [!IMPORTANT]
> Para tirar proveito dos novos nós adicionados por meio do dimensionamento, você precisa balancear novamente as topologias do Storm iniciadas antes do tamanho do cluster ser aumentado.

## <a name="support"></a>Suporte

O Storm no HDInsight é fornecido com suporte contínuo completo de nível empresarial. O Storm no HDInsight também tem um SLA de 99,9%. Isso significa que podemos garantir que um cluster Storm tenha conectividade externa pelo menos 99,9% do tempo.

Para saber mais, confira o [Suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Casos de uso do Apache Storm

Abaixo estão alguns cenários comuns em que você pode usar o Storm no HDInsight:

* Internet das coisas (IoT)
* Detecção de fraude
* Análise das redes sociais
* Extração, transformação e carregamento (ETL)
* Monitoramento de rede
* Pesquisar
* Mobile Engagement

Para saber mais sobre cenários reais, confira o documento [Como as empresas estão usando o Storm](https://storm.apache.org/documentation/Powered-By.html).

## <a name="development"></a>Desenvolvimento

Os desenvolvedores do .NET podem projetar e implementar topologias em C# usando Data Lake Tools para Visual Studio. Você também pode criar topologias híbridas que usam componentes Java e C#.

Para obter mais informações, consulte [Desenvolver topologias C# para o Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Você também pode desenvolver soluções Java usando o IDE de sua escolha. Para saber mais, confira [Desenvolver topologias em Java para o Storm no HDInsight](hdinsight-storm-develop-java-topology.md).

Também é possível usar Python para desenvolver componentes do Storm. Para saber mais, confira [Desenvolver topologias Storm usando Python no HDInsight](hdinsight-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Padrões de desenvolvimento comuns

### <a name="guaranteed-message-processing"></a>Processamento de mensagem garantido

O Apache Storm pode oferecer diferentes níveis de processamento de mensagem garantido. Por exemplo, um aplicativo básico Storm pode garantir um processamento pelo menos uma vez e o Trident pode garantir o processamento exatamente uma vez.

Para obter mais informações, consulte [Garantias do processamento de dados](https://storm.apache.org/about/guarantees-data-processing.html) em apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

O padrão de leitura de uma tupla de entrada, emitindo zero ou mais tuplas, e depois confirmando a tupla de entrada imediatamente no final do método de execução é comum. O Storm oferece a interface [IBasicBolt](https://storm.apache.org/releases/1.0.3/javadocs/org/apache/storm/topology/IBasicBolt.html) para automatizar esse padrão.

### <a name="joins"></a>Junções

O modo como os fluxos de dados são unidos varia entre aplicativos. Por exemplo, você pode juntar cada tupla de vários fluxos em um novo fluxo ou pode juntar somente lotes de tuplas para uma janela específica. De qualquer forma, a junção pode ser realizada usando [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). O agrupamento de campo é uma maneira de definir como as tuplas são roteadas para os bolts.

No exemplo de Java a seguir, fieldsGrouping é usado para rotear tuplas provenientes dos componentes "1", "2" e "3" para o bolt MyJoiner:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Lotes

O Apache Storm fornece um mecanismo de cronometragem interno conhecido como "tupla em escala". Você pode definir quantas vezes uma tupla em escala é emitida em sua topologia.

Para obter um exemplo de como usar uma tupla de escala de um componente em C#, confira [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

O cache na memória geralmente é usado como um mecanismo para acelerar o processamento, uma vez que mantém os ativos usados com mais frequência na memória. Como uma topologia é distribuída entre vários nós, e entre vários processos dentro de cada nó, considere o uso de [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Use `fieldsGrouping` para garantir que as tuplas que contêm os campos que são usados para pesquisa em cache sejam sempre roteadas para o mesmo processo. Essa funcionalidade de agrupamento evita a duplicação de entradas de cache entre os processos.

### <a name="stream-top-n"></a>Fluxo "N Superior"

Quando sua topologia depende do cálculo de um valor de N principal, calcule o valor N principal paralelamente. Em seguida, mescle o resultado desses cálculos em um valor global. Essa operação pode ser feita usando [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) para rotear por campo no processamento paralelo. Em seguida, você pode rotear para um bolt que determina o valor N principal globalmente.

Para obter um exemplo do cálculo de um valor de N principal, confira o exemplo [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Registro em log

O Storm usa o Apache Log4j para registrar informações em log. Por padrão, uma grande quantidade de dados é registrada e pode ser difícil classificar as informações. Você pode incluir um arquivo de configuração de log como parte de sua topologia do Storm para controlar o comportamento de log.

Para uma topologia de exemplo que demonstra como configurar o log, veja o exemplo [WordCount baseado em Java](hdinsight-storm-develop-java-topology.md) para Storm no HDInsight.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as soluções de análise em tempo real com o Storm no HDInsight:

* [Introdução ao Apache Storm no HDInsight][gettingstarted]
* [Topologias de exemplo para Apache Storm no HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md
