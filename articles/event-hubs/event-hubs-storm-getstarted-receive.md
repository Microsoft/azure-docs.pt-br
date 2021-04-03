---
title: 'Guia de início rápido: receber eventos usando o Apache Storm – Hubs de Eventos do Azure'
description: 'Guia de início rápido: este artigo fornecerá informações sobre como receber eventos dos Hubs de Eventos do Azure usando o Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 099a3ff1ad57616de0d7929d787b82e9d55c9e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87087570"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Guia de início rápido: receber eventos dos Hubs de Eventos usando o Apache Storm

[Apache Storm](https://storm.incubator.apache.org) é um sistema de computação distribuído em tempo real que simplifica o processamento confiável de fluxos de dados ilimitados. Esta seção mostra como usar um spout do Storm de Hubs de Eventos do Azure para receber eventos de Hubs de Eventos. Usando o Apache Storm, você pode dividir eventos em vários processos hospedados em nós diferentes. A integração de Hubs de Eventos com o Storm simplifica o consumo de eventos pela verificação de forma transparente de seu progresso usando a instalação de Zookeeper do Storm, gerenciando pontos de verificação persistentes e recebimentos paralelos de Hubs de Eventos.

Para obter mais informações sobre padrões de recebimento de Hubs de Eventos, consulte [Visão geral de hubs de eventos][Event Hubs overview].

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar com o guia de início rápido, **crie um namespace dos Hubs de Eventos e um hub de eventos**. Use o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de Eventos e obter as credenciais de gerenciamento que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [nesse artigo](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Criar o projeto e adicionar o código

Este tutorial usa uma instalação do [HDInsight Storm][HDInsight Storm] , que acompanha o spout de Hubs de Eventos já disponível.

1. Siga o procedimento [HDInsight Storm - Introdução](../hdinsight/storm/apache-storm-overview.md) para criar um novo cluster HDInsight e conectá-lo por meio da Área de Trabalho Remota.
2. Copie o arquivo `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` para seu ambiente de desenvolvimento local. Ele contém o events-storm-spout.
3. Use o seguinte comando para instalar o pacote no armazenamento Maven local. Isso permite que você adicione-o como uma referência no projeto Storm em uma etapa posterior.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. No Eclipse, crie um novo projeto Maven (clique em **Arquivo**, **Novo** e **Projeto**).
   
    ![Arquivo -> Novo -> Projeto][12]
5. Selecione **Uso do local de workspace padrão** e clique em **Avançar**
6. Selecione o arquétipo **maven-archetype-quickstart**, depois clique em **Avançar**
7. Insira um **GroupId** e **ArtifactId** e clique em **Concluir**
8. Em **pom.xml**, adicione as seguintes dependências ao nó `<dependency>`.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. Na pasta **src**, crie um arquivo chamado **Config.properties** e copie o seguinte conteúdo, substituindo os valores `receive rule key` e `event hub name`:

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    O valor para **eventhub.receiver.credits** determina quantos eventos são divididos em lotes antes de liberá-los para o pipeline do Storm. Para simplificar, este exemplo define esse valor como 10. Em produção, ele normalmente deve ser definido para valores mais altos; por exemplo, 1024.
10. Crie uma nova classe chamada **LoggerBolt** com o seguinte código:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Este bolt do Storm registra o conteúdo dos eventos recebidos. Isso pode ser estendido facilmente para armazenar tuplas em um serviço de armazenamento. O [HDInsight Storm com exemplo de Hub de Eventos] usa essa mesma abordagem para armazenar dados no Armazenamento do Microsoft Azure e Power BI.
11. Crie uma classe chamada **LogTopology** com o código a seguir:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Essa classe cria um novo spout de Hubs de Eventos, usando as propriedades no arquivo de configuração para instanciá-lo. É importante observar que esse exemplo cria tantas tarefas spouts quanto o número de partições no hub de eventos, para usar o paralelismo máximo permitido por esse hub de eventos.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão geral de Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm com exemplo de Hub de Eventos]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
