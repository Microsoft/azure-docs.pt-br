---
title: Desempenho de Apache Spark-cache de e/s do Azure HDInsight (versão prévia)
description: Saiba mais sobre o Cache de E/S do Azure HDInsight e sobre como usá-lo para melhorar o desempenho do Apache Spark.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 32f0756e59f81ad2c5d155581feed8c4fef09808
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940129"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Melhorar o desempenho de cargas de trabalho de Apache Spark usando o cache de e/s do Azure HDInsight

O Cache de E/S é um serviço de cache de dados para o Azure HDInsight que melhora o desempenho de trabalhos do Apache Spark. O cache de E/S também funciona com [Apache TEZ](https://tez.apache.org/) e [Apache Hive](https://hive.apache.org/) cargas de trabalho, que podem ser executadas nos clusters [Apache Spark](https://spark.apache.org/). O Cache de E/S usa um componente de cache de software livre chamado RubiX. O RubiX é um cache de disco local para uso com mecanismos de análise de big data que acessam dados de sistemas de armazenamento de nuvem. O RubiX é exclusivo entre os sistemas de cache porque usa unidades de estado sólido (SSDs) em vez de reservar memória operacional para fins de cache. O serviço de Cache de E/S é iniciado e gerencia Servidores de Metadados do RubiX nó de trabalho do cluster. Ele também configura todos os serviços do cluster para uso transparente do cache do RubiX.

A maioria dos SSDs fornece mais de 1 GB por segundo de largura de banda. Essa largura de banda, complementada pelo cache de arquivo na memória do sistema operacional, fornece largura de banda suficiente para carregar os mecanismos de processamento de computação de big data, como o Apache Spark. A memória operacional fica disponível para o Apache Spark processar tarefas altamente dependentes da memória, como as ordens aleatórias. Ter o uso exclusivo da memória operacional permite que o Apache Spark atinja o uso ideal de recursos.  

> [!Note]  
> Atualmente, o Cache de E/S usa o RubiX como um componente de cache, mas isso pode mudar em futuras versões do serviço. Use interfaces de Cache de E/S e não obtenha qualquer dependência diretamente na implementação do RubiX.
>O cache de e/s só tem suporte no armazenamento de BLOBs do Azure no momento.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Benefícios do Cache de E/S do Azure HDInsight

Usar o Cache de E/S fornece um aumento de desempenho para trabalhos que leem dados do Armazenamento de Blobs do Azure.

Você não precisa fazer nenhuma alteração em seus trabalhos do Spark para ver os aumentos de desempenho ao usar o Cache de E/S. Quando o Cache de E/S é desabilitado, esse código Spark leria os dados remotamente do Armazenamento de Blobs do Azure: `spark.read.load('wasbs:///myfolder/data.parquet').count()`. Quando o Cache de E/S é ativado, a mesma linha de código causa uma leitura em cache por meio do Cache de E/S. Nas leituras a seguir, os dados são lidos localmente do SSD. Os nós de trabalho no cluster HDInsight são equipados com unidades SSD anexadas localmente e dedicadas. O Cache de E/S do HDInsight usa esse SSDs locais para armazenar em cache, o que fornece um nível mais baixo de latência e maximiza a largura de banda.

## <a name="getting-started"></a>Introdução

O Cache de E/S do Azure HDInsight está desativado por padrão na versão prévia. O Cache de E/S está disponível em clusters Spark do Azure HDInsight 3.6+, que executam o Apache Spark 2.3.  Para ativar o cache de e/s no HDInsight 4,0, execute as seguintes etapas:

1. Em um navegador da Web, navegue até `https://CLUSTERNAME.azurehdinsight.net`, em que `CLUSTERNAME` é o nome do cluster.

1. Selecione o serviço **Cache de E/S** à esquerda.

1. Selecione **ações** (**ações de serviço** no HDI 3,6) e **ative**.

    ![Habilitando o serviço de cache de e/s no Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "Habilitando o serviço de cache de e/s no Ambari")

1. Confirme a reinicialização de todos os serviços afetados no cluster.

> [!NOTE]  
> Mesmo que a barra de progresso mostre ativado, o Cache de E/S, na verdade, não estará habilitado até que você reinicie os outros serviços afetados.

## <a name="troubleshooting"></a>Solução de problemas
  
Você pode receber erros de espaço em disco ao executar trabalhos do Spark depois de habilitar o Cache de E/S. Esses erros acontecem porque o Spark também usa o armazenamento de disco local para armazenar dados durante operações de embaralhamento. O Spark poderá ocupar todo o espaço do SSD, já que o Cache de E/S está habilitado e o espaço para armazenamento do Spark é reduzido. A quantidade de espaço padrão usado pelo Cache de E/S é de metade do espaço total do SSD. O uso de espaço em disco para o Cache de E/S é configurável no Ambari. Se você receber erros de espaço em disco, reduza a quantidade de espaço de SSD usada pelo Cache de E/S e reinicie o serviço. Para alterar o espaço definido para o Cache de E/S, execute as seguintes etapas:

1. No Apache Ambari, selecione o serviço **HDFS** à esquerda.

1. Selecione as guias **Configurações** e **Avançado**.

    ![Editar configuração avançada do HDFS](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "Editar configuração avançada do HDFS")

1. Role para baixo e expanda a área **Site principal personalizado**.

1. Localize a propriedade **hadoop.cache.data.fullness.percentage**.

1. Altere o valor na caixa.

    ![Editar porcentagem de total do cache de e/s](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Editar porcentagem de total do cache de e/s")

1. Selecione **Salvar** no canto superior direito.

1. Selecione **reiniciar**  >  **reiniciar todos os afetados**.

    ![O Apache Ambari reinicia todos os afetados](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Reiniciar todos os afetados")

1. Selecione **confirmar reiniciar tudo**.

Se isso não funcionar, desabilite o cache de e/s.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre o Cache de E/S, incluindo os benchmarks de desempenho nesta postagem no blog: [Trabalhos do Apache Spark ficam até nove vezes mais rápidos com o Cache de E/S do HDInsight](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
