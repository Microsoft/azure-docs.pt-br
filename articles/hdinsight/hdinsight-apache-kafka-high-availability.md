---
title: Alta disponibilidade com o Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Saiba como garantir a alta disponibilidade com o Apache Kafka no Azure HDInsight. Saiba como reequilibrar réplicas da partição no Kafka para que eles fiquem em domínios de falha diferentes dentro da região do Azure que contém o HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 3edec2e68356604562af2219ccd498732c564ec5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight"></a>Alta disponibilidade de seus dados com o Apache Kafka (versão prévia) no HDInsight

Saiba como configurar réplicas da partição para tópicos Kafka a fim de tirar proveito da configuração de rack do hardware subjacente. Essa configuração garante a disponibilidade dos dados armazenados no Apache Kafka no HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Domínios de falha e atualização com Kafka

Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. As máquinas virtuais e os discos gerenciados que implementam os nós em um cluster HDInsight são distribuídos entre esses domínios de falha. Essa arquitetura limita o possível impacto de falhas físicas de hardware.

Cada região do Azure tem um número específico de domínios de falha. Para obter uma lista de domínios e a quantidade de domínios de falha que eles contêm, confira a documentação [conjuntos de disponibilidade](../virtual-machines/linux/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> O Kafka não está ciente dos domínios de falha. Quando você cria um tópico no Kafka, ele pode armazenar todas as réplicas da partição no mesmo domínio de falha. Para resolver esse problema, fornecemos a [ferramenta de rebalanceamento de partição do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quando rebalancear réplicas da partição

Para garantir a mais alta disponibilidade de seus dados do Kafka, você deve rebalancear as réplicas de partição do tópico nas seguintes situações:

* Quando um novo tópico ou uma partição é criado

* Quando você expande um cluster

## <a name="replication-factor"></a>Fator de replicação

> [!IMPORTANT]
> É recomendável usar uma região do Azure que contenha três domínios de falha e um fator de replicação de 3.

Se você precisa usar uma região que contém apenas dois domínios de falha, use um fator de replicação de 4 para distribuir as réplicas uniformemente entre os dois domínios de falha.

Para obter um exemplo como criar tópicos e configurar o fator de replicação, consulte o documento [Introdução ao Kafka no HDInsight](hdinsight-apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Como rebalancear réplicas da partição

Use a [ferramenta de rebalanceamento de partição do Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) para rebalancear tópicos selecionados. Essa ferramenta deve ser executada em uma sessão SSH para o nó principal do cluster Kafka.

Para saber mais sobre como se conectar ao HDInsight usando SSH, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Próximas etapas

* [Escalabilidade do Kafka no HDInsight](hdinsight-apache-kafka-scalability.md)
* [Espelhamento com o Kafka no HDInsight](hdinsight-apache-kafka-mirroring.md)