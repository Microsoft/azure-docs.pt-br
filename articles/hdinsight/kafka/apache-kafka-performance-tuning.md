---
title: Otimização de desempenho para clusters do Apache Kafka HDInsight
description: Fornece uma visão geral das técnicas para otimizar as cargas de trabalho do Apache Kafka no Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935879"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Otimização de desempenho para clusters do Apache Kafka HDInsight

Este artigo fornece algumas sugestões para otimizar o desempenho de suas cargas de trabalho de Apache Kafka no HDInsight. O foco é ajustar a configuração do produtor e do agente. Há diferentes maneiras de medir o desempenho, e as otimizações que você aplica dependem de suas necessidades de negócios.

## <a name="architecture-overview"></a>Visão geral da arquitetura

Os tópicos do Kafka são usados para organizar registros. Os registros são produzidos por produtores e consumidos por consumidores. Os produtores enviam registros para os agentes Kafka, que armazenam os dados. Cada nó de trabalho no cluster HDInsight é um agente do Kafka.

Registros de partição de tópicos entre agentes. Ao consumir registros, você pode usar um consumidor por partição a fim de alcançar o processamento paralelo dos dados.

A replicação é usada para duplicar partições entre nós. Isso protege contra interrupções do nó (agente). Uma única partição entre o grupo de réplicas é designada como líder de partição. O produtor tráfego é roteado para o preenchimento de cada nó, usando o estado gerenciado pelo ZooKeeper.

## <a name="identify-your-scenario"></a>Identificar seu cenário

Apache Kafka desempenho tem dois aspectos principais – taxa de transferência e latência. Produtividade é a taxa máxima na qual os dados podem ser processados. A taxa de transferência mais alta geralmente é melhor. Latência é o tempo que leva para que os dados sejam armazenados ou recuperados. A latência mais baixa geralmente é melhor. Encontrar o equilíbrio certo entre a taxa de transferência, a latência e o custo da infraestrutura do aplicativo pode ser desafiador. Seus requisitos de desempenho provavelmente corresponderão a uma das três situações comuns a seguir, dependendo se você precisa de alta taxa de transferência, baixa latência ou ambos:

* Alta taxa de transferência, baixa latência. Esse cenário requer alta taxa de transferência e baixa latência (aproximadamente 100 milissegundos). Um exemplo desse tipo de aplicativo é o monitoramento de disponibilidade do serviço.
* Alta taxa de transferência, alta latência. Esse cenário requer alta taxa de transferência (~ 1,5 GBps), mas pode tolerar latência mais alta (< 250 MS). Um exemplo desse tipo de aplicativo é a ingestão de dados de telemetria para processos quase em tempo real, como aplicativos de detecção de intrusão e segurança.
* Baixa taxa de transferência, baixa latência. Esse cenário requer baixa latência (< 10 ms) para processamento em tempo real, mas pode tolerar uma taxa de transferência mais baixa. Um exemplo desse tipo de aplicativo são verificações de ortografia e gramática online.

## <a name="producer-configurations"></a>Configurações de produtor

As seções a seguir destacarão algumas das propriedades de configuração mais importantes para otimizar o desempenho dos produtores do Kafka. Para obter uma explicação detalhada de todas as propriedades de configuração, consulte [Apache Kafka documentação sobre configurações de produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Tamanho do lote

Apache Kafka produtores montam grupos de mensagens (chamados de lotes) que são enviados como uma unidade a ser armazenada em uma única partição de armazenamento. Tamanho do lote significa o número de bytes que devem estar presentes antes que esse grupo seja transmitido. O aumento do `batch.size` parâmetro pode aumentar a taxa de transferência, pois reduz a sobrecarga de processamento das solicitações de rede e e/s. Sob carga leve, o tamanho do lote aumentado pode aumentar a latência de envio do Kafka, pois o produtor espera que um lote esteja pronto. Sob carga pesada, é recomendável aumentar o tamanho do lote para melhorar a taxa de transferência e a latência.

### <a name="producer-required-acknowledgments"></a>Confirmações necessárias do produtor

A configuração necessária do produtor `acks` determina o número de confirmações exigidas pelo líder da partição antes que uma solicitação de gravação seja considerada concluída. Essa configuração afeta a confiabilidade dos dados e usa os valores de `0` , `1` ou `-1` . O valor de `-1` significa que uma confirmação deve ser recebida de todas as réplicas antes de a gravação ser concluída. A configuração `acks = -1` fornece garantias mais fortes contra perda de dados, mas também resulta em latência mais alta e taxa de transferência mais baixa. Se os requisitos do aplicativo exigirem maior taxa de transferência, tente definir `acks = 0` ou `acks = 1` . Tenha em mente que a não confirmação de todas as réplicas pode reduzir a confiabilidade dos dados.

### <a name="compression"></a>Compactação

Um produtor do Kafka pode ser configurado para compactar mensagens antes de enviá-las aos agentes. A `compression.type` configuração especifica o codec de compactação a ser usado. Os codecs de compactação com suporte são "gzip," "encaixado" e "lz4". A compactação é benéfica e deve ser considerada se houver uma limitação na capacidade do disco.

Entre os dois codecs de compactação comumente usados `gzip` e o `snappy` `gzip` tem uma taxa maior de compactação, o que resulta em menor uso do disco no custo de maior carga da CPU. O `snappy` codec fornece menos compactação com menos sobrecarga de CPU. Você pode decidir qual codec usar com base nas limitações de disco do agente ou da CPU do produtor. `gzip` pode compactar dados a uma taxa cinco vezes maior que `snappy` .

Usar a compactação de dados aumentará o número de registros que podem ser armazenados em um disco. Ele também pode aumentar a sobrecarga de CPU em casos em que há uma incompatibilidade entre os formatos de compactação usados pelo produtor e o agente. como os dados devem ser compactados antes do envio e depois descompactados antes do processamento.

## <a name="broker-settings"></a>Configurações do agente

As seções a seguir destacarão algumas das configurações mais importantes para otimizar o desempenho de seus agentes Kafka. Para obter uma explicação detalhada de todas as configurações do agente, consulte a [documentação do Apache Kafka sobre configurações do produtor](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Número de discos

Os discos de armazenamento têm IOPS limitado (operações de entrada/saída por segundo) e bytes de leitura/gravação por segundo. Ao criar novas partições, o Kafka armazena cada nova partição no disco com menos partições existentes para equilibrá-las nos discos disponíveis. Apesar da estratégia de armazenamento, ao processar centenas de réplicas de partição em cada disco, o Kafka pode facilmente saturar a taxa de transferência de disco disponível. A compensação aqui é entre a taxa de transferência e o custo. Se seu aplicativo exigir maior taxa de transferência, crie um cluster com mais discos gerenciados por agente. Atualmente, o HDInsight não dá suporte à adição de discos gerenciados a um cluster em execução. Para obter mais informações sobre como configurar o número de discos gerenciados, consulte [Configurar o armazenamento e a escalabilidade para Apache Kafka no HDInsight](apache-kafka-scalability.md). Entenda as implicações de custo do aumento do espaço de armazenamento para os nós no cluster.

### <a name="number-of-topics-and-partitions"></a>Número de tópicos e partições

Kafka produtores escrevem para tópicos. Consumidores de Kafka lidos a partir de tópicos. Um tópico é associado a um log, que é uma estrutura de dados em disco. O Kafka acrescenta registros de um produtor ao final de um log de tópico. Um log de tópico consiste em várias partições que são distribuídas por vários arquivos. Esses arquivos são, por sua vez, espalhados por vários nós de cluster Kafka. Os consumidores lêem os tópicos do Kafka em sua cadência e podem escolher sua posição (deslocamento) no log do tópico.

Cada partição Kafka é um arquivo de log no sistema e threads de produtor podem gravar em vários logs simultaneamente. Da mesma forma, como cada thread de consumidor lê mensagens de uma partição, o consumo de várias partições também é tratado em paralelo.

Aumentar a densidade da partição (o número de partições por agente) adiciona uma sobrecarga relacionada às operações de metadados e a solicitação/resposta de partição entre o líder de partição e seus seguidores. Mesmo na ausência de fluxo de dados, as réplicas de partição ainda buscam dados de líderes, o que resulta em processamento extra para solicitações de envio e recebimento pela rede.

Para Apache Kafka clusters 1,1 e acima no HDInsight, recomendamos que você tenha um máximo de 1000 partições por agente, incluindo réplicas. O aumento do número de partições por agente diminui a taxa de transferência e também pode causar indisponibilidade de tópico. Para obter mais informações sobre o suporte de partição do Kafka, consulte [a postagem oficial do blog de Apache Kafka no aumento no número de partições com suporte na versão 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Para obter detalhes sobre como modificar tópicos, consulte [Apache Kafka: modificando tópicos](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Número de réplicas

O fator de replicação mais alto resulta em solicitações adicionais entre o líder de partição e os seguidores. Consequentemente, um fator de replicação maior consome mais disco e CPU para lidar com solicitações adicionais, aumentando a latência de gravação e diminuindo a taxa de transferência.

Recomendamos que você use a replicação pelo menos 3x para Kafka no Azure HDInsight. A maioria das regiões do Azure tem três domínios de falha, mas em regiões com apenas dois domínios de falha, os usuários devem usar 4x replicação.

Para obter mais informações sobre replicação, consulte [Apache Kafka: Replication](https://kafka.apache.org/documentation/#replication) e [Apache Kafka: aumentando o fator de replicação](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Próximas etapas

* [Processando trilhões de eventos por dia com o Apache Kafka no Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
