---
title: Estudo de caso da arquitetura de solução de alta disponibilidade do Azure HDInsight
description: Este artigo é um estudo de caso fictício de uma possível arquitetura de solução de alta disponibilidade do Azure HDInsight.
keywords: alta disponibilidade hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99508584"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Estudo de caso da arquitetura de solução de alta disponibilidade do Azure HDInsight

Os mecanismos de replicação do Azure HDInsight podem ser integrados em uma arquitetura de solução altamente disponível. Neste artigo, um estudo de caso fictício para o comércio da Contoso é usado para explicar possíveis abordagens de recuperação de desastres de alta disponibilidade, considerações de custo e seus designs correspondentes.

As recomendações de recuperação de desastre de alta disponibilidade podem ter muitas permutações e combinações. Essas soluções devem ser acessadas após a desliberação dos prós e contras de cada opção. Este artigo aborda apenas uma solução possível.

## <a name="customer-architecture"></a>Arquitetura do cliente

A imagem a seguir ilustra a arquitetura primária de varejo da contoso. A arquitetura consiste em uma carga de trabalho de streaming, carga de trabalho em lote, camada de serviço, camada de consumo, camada de armazenamento e controle de versão.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Arquitetura de varejo da contoso":::

### <a name="streaming-workload"></a>Carga de trabalho de streaming

Dispositivos e sensores produzem dados para o HDInsight Kafka, que constitui a estrutura de mensagens. HDInsight An as leituras de consumidor do Spark dos tópicos do Kafka. O Spark transforma as mensagens de entrada e grava-as em um cluster HDInsight HBase na camada de serviço.

### <a name="batch-workload"></a>Carga de trabalho do lote

HDInsight An cluster Hadoop que executa o hive e o MapReduce ingeri dados de sistemas transacionais locais. Os dados brutos transformados por Hive e MapReduce são armazenados em tabelas Hive em uma partição lógica do data Lake apoiada por Azure Data Lake Storage Gen2. Os dados armazenados em tabelas do hive também são disponibilizados para o Spark SQL, que faz transformações em lote antes de armazenar os dados organizados no HBase para servir.

### <a name="serving-layer"></a>Camada de serviço

HDInsight An cluster HBase com Apache Phoenix é usado para servir dados para aplicativos Web e painéis de visualização. HDInsight An cluster LLAP é usado para atender aos requisitos de relatórios internos.

### <a name="consumption-layer"></a>Camada de consumo

Uma camada de gerenciamento de API e aplicativos de API do Azure de volta uma página da Web voltada para o público. Os requisitos de relatórios internos são atendidos por Power BI.

### <a name="storage-layer"></a>Camada de armazenamento

O Azure Data Lake Storage Gen2 particionado logicamente é usado como um data Lake corporativo. Os metastores do HDInsight são apoiados pelo banco de BD SQL do Azure.

### <a name="version-control-system"></a>Sistema de controle de versão

Um sistema de controle de versão integrado a um Azure Pipelines e hospedado fora do Azure.

## <a name="customer-business-continuity-requirements"></a>Requisitos de continuidade dos negócios do cliente

É importante determinar a funcionalidade de negócios mínima que você precisará se houver um desastre.

### <a name="contoso-retails-business-continuity-requirements"></a>Requisitos de continuidade dos negócios de varejo da contoso

* Devemos ser protegidos contra uma falha regional ou um problema de integridade do serviço regional.
* Meus clientes nunca devem ver um erro 404. O conteúdo público sempre deve ser atendido. (RTO = 0)  
* Para a maior parte do ano, podemos mostrar o conteúdo público que está obsoleto em 5 horas. (RPO = 5 horas)
* Durante a época de Natal, nosso conteúdo público voltado deve estar sempre atualizado. (RPO = 0)
* Meus requisitos internos de relatório não são considerados críticos para a continuidade dos negócios.
* Otimize os custos de continuidade dos negócios.

## <a name="proposed-solution"></a>Solução proposta

A imagem a seguir mostra a arquitetura de recuperação de desastre de alta disponibilidade de varejo da contoso.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Solução da contoso":::

O **Kafka** usa a replicação [ativa – passiva](hdinsight-business-continuity-architecture.md#apache-kafka) para os tópicos Kafka de espelhamento da região primária para a região secundária. Uma alternativa à replicação Kafka pode ser produzida para Kafka em ambas as regiões.

O **Hive e o Spark** usam modelos [de replicação secundária ativos primários sob demanda](hdinsight-business-continuity-architecture.md#apache-spark) durante horários normais. O processo de replicação do hive é executado periodicamente e acompanha o hive do Azure SQL metastore e a replicação da conta de armazenamento do hive. A conta de armazenamento do Spark é replicada periodicamente usando o ADF DistCP. A natureza transitória desses clusters ajuda a otimizar os custos. As replicações são agendadas a cada 4 horas para chegar a um RPO que está bem dentro do requisito de cinco horas.

A replicação do **HBase** usa o modelo [líder – de acompanhamento](hdinsight-business-continuity-architecture.md#apache-hbase) durante os horários normais para garantir que os dados sejam sempre atendidos, independentemente da região, e o RPO seja muito baixo.

Se houver uma falha regional na região primária, a página da Web e o conteúdo de back-end serão servidos da região secundária por 5 horas com certo grau de desatualização. Se o painel de integridade do serviço do Azure não indicar uma recuperação ETA na janela de cinco horas, o varejo da Contoso criará a camada de transformação do hive e do Spark na região secundária e, em seguida, apontará todas as fontes de dados upstream para a região secundária. Tornar a região secundária gravável causaria um processo de failback que envolve a replicação de volta para o primário.

Durante uma temporada de compras de pico, todo o pipeline secundário estará sempre ativo e em execução. Produtores de Kafka produzem para as duas regiões e a replicação do HBase seria alterada de Leader-Follower para Leader-Leader para garantir que o conteúdo público voltado esteja sempre atualizado.

Nenhuma solução de failover precisa ser projetada para relatórios internos, pois não é essencial para a continuidade dos negócios.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os itens discutidos neste artigo, consulte:

* [Continuidade de negócios do Azure HDInsight](./hdinsight-business-continuity.md)
* [Arquiteturas de continuidade de negócios do Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
