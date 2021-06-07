---
title: Alta disponibilidade com o Apache Kafka - Azure HDInsight
description: Saiba como garantir a alta disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que eles fiquem em domínios de falha diferentes dentro da região do Azure que contém o HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e3f31bc1876a0c1f90ad4882ff782be25b4b6ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98932748"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Alta disponibilidade de seus dados com o Apache Kafka no HDInsight

Saiba como configurar réplicas da partição para tópicos Apache Kafka a fim de tirar proveito da configuração de rack do hardware subjacente. Essa configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Domínios de falha e atualização com Apache Kafka

Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. As máquinas virtuais e os discos gerenciados que implementam os nós em um cluster HDInsight são distribuídos entre esses domínios de falha. Essa arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e a quantidade de domínios de falha que eles contêm, confira a documentação [conjuntos de disponibilidade](../../virtual-machines/availability.md#availability-sets).

> [!IMPORTANT]  
> O Kafka não está ciente dos domínios de falha. Quando você cria um tópico no Kafka, ele pode armazenar todas as réplicas da partição no mesmo domínio de falha. Para resolver esse problema, o HDInsight fornece a [ferramenta de rebalanceamento de partição do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quando rebalancear réplicas da partição

Para garantir a mais alta disponibilidade de seus dados do Kafka, você deve rebalancear as réplicas de partição do tópico nas seguintes situações:

* Quando um novo tópico ou uma partição é criado

* Quando você expande um cluster

## <a name="replication-factor"></a>Fator de replicação

> [!IMPORTANT]  
> É recomendável usar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se você precisa usar uma região que contém apenas dois domínios de falha, use um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

Para obter um exemplo como criar tópicos e configurar o fator de replicação, consulte o documento [Introdução ao Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Como rebalancear réplicas da partição

Use a [ferramenta de rebalanceamento de partição do Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para rebalancear tópicos selecionados. Essa ferramenta deve ser executada em uma sessão SSH para o nó principal do cluster Kafka.

Para saber mais sobre como se conectar ao HDInsight usando SSH, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Próximas etapas

* [Escalabilidade do Apache Kafka no HDInsight](apache-kafka-scalability.md)
* [Espelhamento com o Apache Kafka no HDInsight](apache-kafka-mirroring.md)