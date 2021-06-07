---
title: Otimizar o Apache Pig com o Apache Ambari no Azure HDInsight
description: Use a interface do usuário da Web do Apache amAmbari para configurar e otimizar o Apache Pig.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 43159360342063af1682a984db037ae286721411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871156"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Otimizar o Apache Pig com o Apache Ambari no Azure HDInsight

O Apache Ambari é uma interface da Web para gerenciar e monitorar clusters HDInsight. Para obter uma introdução à interface do usuário da Web do amAmbari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Propriedades de Apache Pig podem ser modificadas da interface do usuário da Web do Ambari para ajustar consultas de Pig. Modificar propriedades de Pig do Ambari modifica diretamente as propriedades de Pig no arquivo `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Para modificar propriedades de Pig, navegue até a guia **Configurações** de Pig e, em seguida, expanda o painel **Propriedades avançadas de Pig**.

1. Localize, remova as marcas de comentário e altere o valor da propriedade que deseja modificar.

1. Selecione **salvar** no lado superior direito da janela para salvar o novo valor. Algumas propriedades podem exigir uma reinicialização do serviço.

    :::image type="content" source="./media/optimize-pig-ambari/advanced-pig-properties.png" alt-text="Propriedades avançadas do Apache Pig" border="true":::

> [!NOTE]  
> As configurações de nível de sessão substituem os valores de propriedade no arquivo `pig.properties`.

## <a name="tune-execution-engine"></a>Ajustar mecanismo de execução

Dois mecanismos de execução estão disponíveis para executar scripts de Pig: MapReduce e Tez. O Tez é um mecanismo otimizado e muito mais rápido que o MapReduce.

1. Para modificar o mecanismo de execução, no painel **Propriedades avançadas de Pig**, localize a propriedade `exectype`.

1. O valor padrão é **MapReduce**. Altere-o para **Tez**.

## <a name="enable-local-mode"></a>Habilitar modo local

De forma semelhante ao Hive, o modo local é usado para acelerar trabalhos com quantidades de dados relativamente menores.

1. Para habilitar o modo local, defina `pig.auto.local.enabled` como **true**. O valor padrão é false.

1. Trabalhos com um tamanho de dados de entrada menor que o valor da propriedade `pig.auto.local.input.maxbytes` são considerados trabalhos pequenos. O valor padrão é 1 GB.

## <a name="copy-user-jar-cache"></a>Copiar o cache de jar do usuário

O Pig copia os arquivos JAR exigidos pelo UDFs para um cache distribuído para torná-los disponíveis para nós de tarefa. Esses jars não são alterados com frequência. Se habilitada, a configuração `pig.user.cache.enabled` permite que os jars sejam colocados em um cache para reutilizá-los para trabalhos executados pelo mesmo usuário. Essa configuração resulta em um aumento secundário no desempenho do trabalho.

1. Para habilitar, defina `pig.user.cache.enabled` como true. O padrão é falso.

1. Para definir o caminho base dos jars armazenado em cache, defina `pig.user.cache.location` como o caminho base. O padrão é `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com as configurações de memória

As seguintes configurações de memória podem ajudar a otimizar o desempenho de script do Pig.

* `pig.cachedbag.memusage`: A quantidade de memória fornecida a um recipiente. Um recipiente é um conjunto de tuplas. Uma tupla é um conjunto ordenado de campos e um campo é uma parte dos dados. Se os dados em um recipiente estiverem além da memória especificada, eles serão despejados no disco. O valor padrão é 0.2, que representa 20 por cento da memória disponível. Essa memória é compartilhada entre todos os recipientes em um aplicativo.

* `pig.spill.size.threshold`: recipientes maiores que esse limite de tamanho de despejo (em bytes) são despejados para o disco. O valor padrão é 5 MB.

## <a name="compress-temporary-files"></a>Compactar arquivos temporários

O Pig gera arquivos temporários durante a execução do trabalho. Compactar os arquivos temporários resulta em uma melhoria do desempenho ao ler ou gravar arquivos no disco. As configurações a seguir podem ser usadas para compactar arquivos temporários.

* `pig.tmpfilecompression`: quando for true, habilitará a compactação de arquivos temporários. O valor padrão é false.

* `pig.tmpfilecompression.codec`: o codec de compactação a ser usado para compactar os arquivos temporários. Os codecs de compactação recomendados são LZO e encaixados para menor uso da CPU.

## <a name="enable-split-combining"></a>Habilitar combinação de divisão

Quando habilitada, pequenos arquivos são combinados para que haja menos tarefas de mapeamento. Essa configuração melhora a eficiência dos trabalhos com muitos arquivos pequenos. Para habilitar, defina `pig.noSplitCombination` como true. O valor padrão é false.

## <a name="tune-mappers"></a>Ajustar mapeadores

O número de mapeadores é controlado modificando a propriedade `pig.maxCombinedSplitSize`. Essa propriedade especifica o tamanho dos dados a serem processados por uma única tarefa de mapa. O valor padrão é o tamanho do bloco padrão do sistema de arquivos. Aumentar esse valor resulta em um número menor de tarefas de mapeador.

## <a name="tune-reducers"></a>Ajustar redutores

O número de redutores é calculado com base no parâmetro `pig.exec.reducers.bytes.per.reducer`. O parâmetro especifica o número de bytes processados por redutor e por padrão é 1 GB. Para limitar o número máximo de redutores, defina a `pig.exec.reducers.max` propriedade, por padrão, 999.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight com a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar clusters](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar o Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar o Apache Hive](./optimize-hive-ambari.md)
