---
title: Otimizar o Apache HBase com o Apache Ambari no Azure HDInsight
description: Use a interface do usuário da Web do Apache amAmbari para configurar e otimizar o Apache HBase.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 60c9916bc7d7b3b380a332f41924ee744002fd66
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428193"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Otimizar o Apache HBase com o Apache Ambari no Azure HDInsight

O Apache Ambari é uma interface da Web para gerenciar e monitorar clusters HDInsight. Para obter uma introdução à interface do usuário da Web do amAmbari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

A configuração do Apache HBase é modificada na guia **configurações do HBase** . As seções a seguir descrevem algumas das definições de configuração importantes que afetam o desempenho do HBase.

## <a name="set-hbase_heapsize"></a>Definir HBASE_HEAPSIZE

> [!NOTE]
> Este artigo contém referências ao termo *mestre*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.

O tamanho do heap do HBase especifica a quantidade máxima do heap a ser usada, em megabytes, por *região* e por servidores *mestre*. O valor padrão é 1,000 MB. Esse valor deve ser ajustado para a carga de trabalho do cluster.

1. Para modificar, navegue até o painel **HBase-env Avançado** na guia **Configurações** do HBase e, em seguida, localize a configuração `HBASE_HEAPSIZE`.

1. Altere o valor padrão para 5.000 MB.

    !["Apache Ambari HBase Memory heapsize"](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho com uso intenso de leitura

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com uso intenso de leitura.

### <a name="block-cache-size"></a>Tamanho do cache do bloco

O cache do bloco é o cache de leitura. Seu tamanho é controlado pelo parâmetro `hfile.block.cache.size`. O valor padrão é 0.4, que é 40 por cento da memória total do servidor da região. Quanto maior o tamanho do cache do bloco, mais rápidas serão as leituras aleatórias.

1. Para modificar esse parâmetro, navegue até a guia **Configurações** na guia **Configurações** do HBase e, em seguida, localize **% do RegionServer Alocada para Buffers de Leitura**.

    ![Tamanho do cache do bloco de memória do Apache HBase](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Para alterar o valor, selecione o ícone **Editar**.

### <a name="memstore-size"></a>Tamanho do Memstore

Todas as edições são armazenadas no buffer de memória, chamado de *Memstore*. Esse buffer aumenta a quantidade total de dados que podem ser gravados em disco em uma única operação. Ele também acelera o acesso às edições recentes. O tamanho do Memstore é definido pelos dois parâmetros a seguir:

* `hbase.regionserver.global.memstore.UpperLimit`: define o percentual máximo do servidor de região que o Memstore combinado pode usar.

* `hbase.regionserver.global.memstore.LowerLimit`: define o percentual mínimo do servidor de região que o Memstore combinado pode usar.

Para otimizar leituras aleatórias, você pode reduzir os limites superior e inferior do Memstore.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas buscadas ao fazer a varredura do disco

A configuração `hbase.client.scanner.caching` define o número de linhas lidas do disco quando o método `next` é chamado em um scanner.  O valor padrão é 100. Quanto maior o número, menos chamadas remotas serão feitas do cliente para o servidor de região, resultando em verificações mais rápidas. No entanto, essa configuração também aumentará a pressão de memória no cliente.

![Número de linhas do Apache HBase buscadas](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não defina o valor de modo que o tempo entre a invocação do método seguinte em um scanner seja maior que o tempo limite do scanner. A duração do tempo limite do scanner é definida pela propriedade `hbase.regionserver.lease.period`.

## <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho com uso intenso de gravação

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com uso intenso de gravação.

### <a name="maximum-region-file-size"></a>Tamanho máximo do arquivo de região

O HBase armazena dados em um formato de arquivo interno, chamado *HFile*. A propriedade `hbase.hregion.max.filesize` define o tamanho de um único HFile para uma região.  Uma região será dividida em duas regiões se a soma de todos os HFiles nela for maior do que essa configuração.

![' O tamanho máximo do Apache HBase HRegion '](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Quanto maior o tamanho do arquivo de região, menor o número de divisões. É possível aumentar o tamanho do arquivo para determinar um valor que resulta no desempenho máximo de gravação.

### <a name="avoid-update-blocking"></a>Evitar o bloqueio de atualização

* A propriedade `hbase.hregion.memstore.flush.size` define o tamanho no qual o Memstore é liberado para o disco. O tamanho padrão é 128 MB.

* O multiplicador de bloco de região do HBase é definido por `hbase.hregion.memstore.block.multiplier` . O valor padrão é 4. O máximo permitido é 8.

* O HBase bloqueará atualizações se o Memstore tiver (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes.

    Com os valores padrão de tamanho de liberação e multiplicador de bloco, as atualizações serão bloqueadas quando o Memstore tiver 128 * 4 = 512 MB. Para reduzir o número de blocos de atualização, aumente o valor de `hbase.hregion.memstore.block.multiplier`.

![Multiplicador de bloco de região do Apache HBase](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definir tamanho do Memstore

O tamanho do Memstore é definido pelos parâmetros `hbase.regionserver.global.memstore.upperLimit` e `hbase.regionserver.global.memstore.lowerLimit`. Definir esses valores como iguais uns aos outros reduz pausas durante as gravações (também causando liberações mais frequentes) e resulta em um melhor desempenho de gravação.

## <a name="set-memstore-local-allocation-buffer"></a>Definir o buffer de alocação local do Memstore

O uso do buffer de alocação local do Memstore é determinado pela propriedade `hbase.hregion.memstore.mslab.enabled`. Quando habilitado (true), essa configuração impede a fragmentação de heap durante uma operação de gravação pesada. O valor padrão é true.

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight com a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar clusters](./hdinsight-changing-configs-via-ambari.md)
* [Otimizar o Apache Hive](./optimize-hive-ambari.md)
* [Otimizar o Apache Pig](./optimize-pig-ambari.md)
