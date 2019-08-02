---
title: Uma introdução ao Apache Kafka no HDInsight – Azure
description: 'Saiba mais sobre o Apache Kafka no HDInsight: O que é, o que ele faz e onde encontrar exemplos e obter informações introdutórias.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/13/2019
ms.openlocfilehash: e500f08de414a5907a0f1aeeca90d8c2090adcdd
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137068"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>O que é o Apache Kafka no Azure HDInsight

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de streaming distribuída de software livre que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real. O Kafka também fornece funcionalidade de agente de mensagem semelhante a uma fila de mensagens, onde você pode publicar e assinar os fluxos de dados nomeados. 

Estas são as características específicas do Kafka no HDInsight:

* Ele é um serviço gerenciado que fornece um processo de configuração simplificado. O resultado é uma configuração testada e com suporte da Microsoft.

* A Microsoft fornece um SLA (Contrato de Nível de Serviço) de 99,9% de tempo de atividade no Kafka. Para saber mais, veja o documento [Informações de SLA para o HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Ele usa o Azure Managed Disks como repositório de backup para Kafka. O Managed Disks pode fornecer até 16 TB de armazenamento por agente do Kafka. Para saber mais sobre como configurar discos gerenciados com o Kafka no HDInsight, confira [Aumentar a escalabilidade do Apache Kafka no HDInsight](apache-kafka-scalability.md).

    Para saber mais sobre discos gerenciados, veja [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* O Kafka foi criado com uma exibição única e bidimensional de um rack. O Azure separa um rack em duas dimensões: UD (domínios de atualização) e FD (domínios de falha). A Microsoft fornece ferramentas que redistribuem partições e réplicas do Kafka entre UDs e FDs. 

    Para saber mais, confira [Alta disponibilidade com Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* O HDInsight permite que você altere o número de nós de trabalho (que hospedam o agente do Kafka) após a criação do cluster. O dimensionamento pode ser executado a partir do portal do Azure, do Azure PowerShell e de outras interfaces de gerenciamento do Azure. Para Kafka você deve redistribuir as réplicas de partição após as operações de dimensionamento. Redistribuir as partições permite ao Kafka aproveitar o novo número de nós de trabalho.

    Para saber mais, confira [Alta disponibilidade com Apache Kafka no HDInsight](apache-kafka-high-availability.md).

* Os logs do Azure Monitor podem ser usados para monitorar o Kafka no HDInsight. Os logs do Azure Monitor expõem as informações do nível da máquina virtual, como o disco e as métricas NIC, além das métricas JMX do Kafka.

    Para saber mais, confira [Analisar logs do Apache Kafka no HDInsight](apache-kafka-log-analytics-operations-management.md).

### <a name="apache-kafka-on-hdinsight-architecture"></a>Arquitetura do Apache Kafka no HDInsight

O diagrama a seguir mostra uma configuração típica de Kafka que usa grupos de consumidores, particionamento e a replicação oferece leitura paralela de eventos com tolerância a falhas:

![Diagrama de configuração do cluster Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

O Apache ZooKeeper gerencia o estado do cluster Kafka. O Zookeeper foi criado para executar transações simultâneas, resilientes e de baixa latência. 

O Kafka armazena registros (dados) em **tópicos**. Os registros são produzidos por **produtores** e consumidos por **consumidores**. Os produtores enviam registros aos **agentes** do Kafka. Cada nó de trabalho no cluster HDInsight é um agente do Kafka. 

Registros de partição de tópicos entre agentes. Ao consumir registros, você pode usar um consumidor por partição a fim de alcançar o processamento paralelo dos dados.

A replicação é utilizada para duplicar partições entre os nós, protegendo contra falhas de nó (agente). Uma partição marcada com um *(L)* no diagrama é o líder da partição específica. O produtor tráfego é roteado para o preenchimento de cada nó, usando o estado gerenciado pelo ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Por que usar o Apache Kafka no HDInsight?

Veja a seguir tarefas e padrões comuns que podem ser executadas usando Kafka no HDInsight:

* **Replicação de dados do Apache Kafka**: O Kafka fornece o utilitário MirrorMaker, que replica os dados entre os clusters do Kafka.

    Para saber mais sobre como usar o MirrorMaker, confira [Replicar tópicos do Apache Kafka com o Apache Kafka no HDInsight](apache-kafka-mirroring.md).

* **Padrão de mensagens de publicação/assinatura**: O Kafka fornece uma API de Produtor para publicação de registros em um tópico do Kafka. A API de Consumidor é usada na assinatura de um tópico.

    Para saber mais, confira [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Processamento de fluxo**: O Kafka é frequentemente usado com o Apache Storm ou o Spark para processamento de fluxo em tempo real. O Kafka 0.10.0.0 (HDInsight versão 3.5 e 3.6) introduziu uma API de streaming que permite que você crie soluções de transmissão sem a necessidade do Storm ou do Spark.

    Para saber mais, confira [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Escala horizontal**: O Kafka particiona fluxos entre os nós no cluster HDInsight. Os processos do Consumidor podem ser associados a partições individuais para fornecer balanceamento de carga ao consumir registros.

    Para saber mais, confira [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

* **Entrega em ordem**: Dentro de cada partição, os registros são armazenados no fluxo na ordem em que foram recebidos. Ao associar um processo do consumidor por partição, você pode garantir que os registros sejam processados na ordem.

    Para saber mais, confira [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Casos de uso

* **Sistema de mensagens**: Como ele dá suporte ao padrão de mensagem de publicação/assinatura, o Kafka geralmente é usado como um agente de mensagem.

* **Acompanhamento de atividades**: Como o Kafka fornece o log em ordem de registros, ele pode ser usado para acompanhar e recriar atividades. Por exemplo, as ações do usuário em um site ou em um aplicativo.

* **Agregação**: Usando o processamento de fluxo, você pode agregar informações de fluxos diferentes para combinar e centralizar as informações em dados operacionais.

* **Transformação**: Usando o processamento de fluxo, você pode combinar e enriquecer dados de vários tópicos de entrada em um ou mais tópicos de saída.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para aprender a usar o Apache Kafka no HDInsight:

* [Início Rápido: Criar o Apache Kafka no HDInsight](apache-kafka-get-started.md)

* [Tutorial: Usar o Apache Spark com o Apache Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Tutorial: Usar o Apache Storm com o Apache Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
