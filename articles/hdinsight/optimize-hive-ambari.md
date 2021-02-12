---
title: Otimizar Apache Hive com o Apache Ambari no Azure HDInsight
description: Use a interface do usuário da Web do Apache amAmbari para configurar e otimizar Apache Hive.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 349f58720e6fff52191dfff65108cd1320e41eed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939247"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Otimizar Apache Hive com o Apache Ambari no Azure HDInsight

O Apache Ambari é uma interface da Web para gerenciar e monitorar clusters HDInsight. Para obter uma introdução à interface do usuário da Web do amAmbari, consulte [gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

As seções a seguir descrevem opções de configuração para otimizar o desempenho geral do Apache Hive.

1. Para modificar parâmetros de configuração do Hive, selecione **Hive** na barra lateral Serviços.
1. Navegue até a guia **Configurações**.

## <a name="set-the-hive-execution-engine"></a>Definir o mecanismo de execução do Hive

O hive fornece dois mecanismos de execução: Apache Hadoop MapReduce e o Apache TEZ. O Tez é mais rápido que o MapReduce. Clusters Linux do HDInsight têm o Tez como mecanismo de execução padrão. Para alterar o mecanismo de execução:

1. Na guia **Configurações** do Hive, digite **mecanismo de execução** na caixa de filtro.

    ![Mecanismo de execução de pesquisa do Apache Ambari](./media/optimize-hive-ambari/ambari-search-execution.png)

1. O valor padrão da propriedade **Otimização** é **Tez**.

    ![Otimização-mecanismo de Apache Tez](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Ajustar mapeadores

O Hadoop tenta dividir (*mapear*) arquivos únicos em vários arquivos e processar os arquivos resultantes em paralelo. O número de mapeadores depende do número de divisões. Os dois parâmetros de configuração a seguir definem o número de divisões para o mecanismo de execução Tez:

* `tez.grouping.min-size`: limite inferior do tamanho de uma divisão agrupada, com um valor padrão de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: limite superior do tamanho de uma divisão agrupada, com um valor padrão de 1 GB (1.073.741.824 bytes).

Como diretriz de desempenho, diminua esses dois parâmetros para melhorar a latência, aumentar para obter mais produtividade.

Por exemplo, para definir quatro tarefas de mapeador com o tamanho de dados de 128 MB, você definiria os dois parâmetros como 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros de limite, navegue até a guia **Configurações** do serviço Tez. Expanda o painel **Geral** e localize os parâmetros `tez.grouping.max-size` e `tez.grouping.min-size`.

1. Defina os dois parâmetros como **33,554,432** bytes (32 MB).

    ![Tamanhos de agrupamento do Apache Ambari tez](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Essas alterações afetam todos os trabalhos do Tez no servidor. Para obter o resultado ideal, escolha valores de parâmetro apropriados.

## <a name="tune-reducers"></a>Ajustar redutores

Apache ORC e Snappy ambos oferecem alto desempenho. Entretanto, o Hive pode ter poucos redutores por padrão, o que causa gargalos.

Por exemplo, digamos que você tenha um tamanho de dados de entrada igual a 50 GB. Esses dados em formato ORC com compactação do Snappy terão 1 GB. O Hive estima o número de redutores necessários como: (número de entrada de bytes para mapeadores / `hive.exec.reducers.bytes.per.reducer`).

Com as configurações padrão, este exemplo é de quatro redutores.

O parâmetro `hive.exec.reducers.bytes.per.reducer` especifica o número de bytes processados por redutor. O valor padrão é 64 MB. Diminuir esse valor aumenta o paralelismo e pode melhorar o desempenho. Diminui-lo demais também pode produzir muitos redutores, o que pode prejudicar o desempenho. Este parâmetro é baseado em seus requisitos de dados específicos, nas configurações de compactação e em outros fatores ambientais.

1. Para modificá-lo, navegue até a guia **Configurações** do Hive e localize o parâmetro **Dados por Redutor** na página Configurações.

    ![Dados do Apache Ambari por redutor](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Selecione **Editar** para modificar o valor para 128 MB (134.217.728 bytes) e, em seguida, pressione **Enter** para salvar.

    ![Dados de Ambari por redutor-editado](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, há oito redutores (1024/128).

1. Atribuir um valor incorreto para o parâmetro **Dados por Redutor** pode resultar em um grande número de redutores, afetando negativamente o desempenho da consulta. Para limitar o número máximo de redutores, defina um valor apropriado para `hive.exec.reducers.max`. O valor padrão é 1009.

## <a name="enable-parallel-execution"></a>Habilitar execução paralela

Uma consulta do Hive é executada em uma ou mais etapas. Se as etapas independentes puderem ser executadas em paralelo, o desempenho da consulta aumentará.

1. Para habilitar a execução de consultas em paralelo, navegue até a guia **Configurações** do Hive e pesquise pela propriedade `hive.exec.parallel`. O valor padrão é false. Altere o valor para true e pressione **Enter** para salvá-lo.

1. Para limitar o número de trabalhos a serem executados em paralelo, modifique a `hive.exec.parallel.thread.number` propriedade. O valor padrão é 8.

    ![Exibição paralela Apache Hive exec](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Habilitar vetorização

O Hive processa dados linha por linha. A vetorização instrui o Hive a processar dados em blocos de 1.024 linhas em vez de uma linha por vez. A vetorização é aplicável somente ao formato de arquivo ORC.

1. Para habilitar uma execução de consulta vetorizada, navegue até a guia **Configurações** do Hive e pesquise pelo parâmetro `hive.vectorized.execution.enabled`. O valor padrão é true para o Hive 0.13.0 ou posterior.

1. Para habilitar a execução vetorizada para o lado de redução da consulta, defina o parâmetro `hive.vectorized.execution.reduce.enabled` como true. O valor padrão é false.

    ![Apache Hive execução vetorizada](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Habilitar CBO (otimização baseada em custo)

Por padrão, o Hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A otimização baseada em custo (CBO) avalia vários planos para executar uma consulta. E atribui um custo a cada plano e, em seguida, determina o plano mais barato para executar uma consulta.

Para habilitar o CBO, navegue para configurações de configuração do **Hive**  >    >   e localize **habilitar otimizador baseado em custo** e, em seguida, alterne o botão de alternância para **ativado**.

![Otimizador baseado em custo do HDInsight](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho de consulta do Hive quando a CBO está habilitada:

* `hive.compute.query.using.stats`

    Quando definido como true, o Hive usa estatísticas armazenadas em seu metastore para responder consultas simples como `count(*)`.

    ![Apache Hive consulta de computação usando estatísticas](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Estatísticas de coluna são criadas quando a CBO está habilitada. O Hive usa estatísticas de coluna, que são armazenadas em um metastore, para otimizar consultas. Obter estatísticas de coluna para cada coluna leva mais tempo quando o número de colunas é alto. Quando definida como false, essa configuração desabilita a busca de estatísticas de coluna do metastore.

    ![Estatísticas de Apache Hive definir estatística de coluna](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Estatísticas de partição básicas, como número de linhas, tamanho de dados e tamanho do arquivo, são armazenadas no metastore. Se definido como true, as estatísticas de partição são buscadas do metastore. Quando for falso, o tamanho do arquivo será obtido do sistema de arquivos. E o número de linhas é buscado a partir do esquema de linha.

    ![Estatísticas de partição, conjunto de estatísticas do Hive](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Habilitar compactação intermediária

Tarefas de mapeamento criam arquivos intermediários que são usados pelas tarefas do redutor. A compactação intermediária reduz o tamanho do arquivo intermediário.

Trabalhos do Hadoop normalmente têm um gargalo de E/S. Compactar os dados pode acelerar a E/S e a transferência de rede em geral.

Os tipos de compactação disponíveis são:

| Formatar | Ferramenta | Algoritmo | Extensão do arquivo | Divisível? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | Não |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Sim |
| LZO | `Lzop` | LZO | `.lzo` | Sim, se indexado |
| Snappy | N/D | Snappy | Snappy | Não |

Como regra geral, ter o método de compactação divisões é importante; caso contrário, poucos Mapeadores serão criados. Se os dados de entrada forem texto, `bzip2` será a melhor opção. Para o formato ORC, o Snappy é a opção de compactação mais rápida.

1. Para habilitar a compactação intermediária, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.exec.compress.intermediate` como verdadeiro. O valor padrão é false.

    ![' Hive exec compactar intermediário '](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para compactar arquivos intermediários, escolha um codec de compactação com menor custo de CPU, mesmo se o codec não tiver uma alta saída de compactação.

1. Para definir o codec de compactação intermediário, adicione a propriedade personalizada `mapred.map.output.compression.codec` ao arquivo `hive-site.xml` ou `mapred-site.xml`.

1. Para adicionar uma configuração personalizada:

    a. Navegue até configurações do **Hive**  >    >  **avançado**  >  **personalizado Hive-site**.

    b. Selecione **Adicionar Propriedade...** na parte inferior do painel Hive-site personalizado.

    c. Na janela Adicionar Propriedade, digite `mapred.map.output.compression.codec` como a chave e `org.apache.hadoop.io.compress.SnappyCodec` como o valor.

    d. Selecione **Adicionar**.

    ![' Apache Hive adicionar propriedade personalizada '](./media/optimize-hive-ambari/hive-custom-property.png)

    Essa configuração compactará o arquivo intermediário usando a compactação de instantâneo. Após ser adicionada, a propriedade aparecerá no painel hive-site personalizado.

    > [!NOTE]  
    > Este procedimento modifica o arquivo `$HADOOP_HOME/conf/hive-site.xml`.

## <a name="compress-final-output"></a>Compactar a saída final

A saída final do Hive também pode ser compactada.

1. Para compactar a saída final do Hive, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.exec.compress.output` como verdadeiro. O valor padrão é false.

1. Para escolher o codec de compactação de saída, adicione a propriedade personalizada `mapred.output.compression.codec` ao painel hive-site personalizado, conforme descrito na etapa 3 da seção anterior.

    ![Apache Hive propriedade personalizada ADD2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Habilitar a execução especulativa

A execução especulativa inicia um determinado número de tarefas duplicadas para detectar e negar listar o rastreador de tarefas de execução lenta. Ao melhorar a execução geral do trabalho, otimizando os resultados de tarefas individuais.

A execução especulativa não deve ser ativada para tarefas MapReduce de execução longa com grandes quantidades de entrada.

* Para habilitar a execução especulativa, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.mapred.reduce.tasks.speculative.execution` como verdadeiro. O valor padrão é false.

    ![' Hive mapred reduzir a execução especular tarefas '](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Ajustar partições dinâmicas

O hive permite a criação de partições dinâmicas ao inserir registros em uma tabela, sem predefinir todas as partições. Essa capacidade é um recurso poderoso. Embora isso possa resultar na criação de um grande número de partições. E um grande número de arquivos para cada partição.

1. Para o Hive fazer partições dinâmicas, o valor do parâmetro `hive.exec.dynamic.partition` deve ser true (o padrão).

1. Altere o modo de partição dinâmica para *estrito*. No modo estrito, pelo menos uma partição deve ser estática. Essa configuração impede consultas sem o filtro de partição na cláusula WHERE, ou seja, *estrita* impede consultas que verificam todas as partições. Navegue até a guia **Configurações** do Hive e, em seguida, defina `hive.exec.dynamic.partition.mode` como **estrito**. O valor padrão é **não estrito**.

1. Para limitar o número de partições dinâmicas a serem criadas, modifique o parâmetro `hive.exec.max.dynamic.partitions`. O valor padrão é 5.000.

1. Para limitar o número total de partições dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode`. O valor padrão é 2.000.

## <a name="enable-local-mode"></a>Habilitar modo local

O modo local permite que o hive realize todas as tarefas de um trabalho em um único computador. Ou, às vezes, em um único processo. Essa configuração melhora o desempenho da consulta se os dados de entrada forem pequenos. E a sobrecarga de iniciar tarefas para consultas consome um percentual significativo da execução de consulta geral.

Para habilitar o modo local, adicione o parâmetro `hive.exec.mode.local.auto` ao painel hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression).

![Modo Apache Hive exec local automático](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Definir MultiGROUP BY para MapReduce único

Quando esta propriedade é definida como true, uma consulta MultiGROUP BY com chaves de agrupamento comuns gera um único trabalho de MapReduce.  

Para habilitar esse comportamento, adicione o parâmetro `hive.multigroupby.singlereducer` ao painel hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression).

![Definir MultiGROUP BY para MapReduce único do Hive](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Otimizações adicionais do Hive

As seções a seguir descrevem otimizações adicionais relacionadas ao Hive que você pode definir.

### <a name="join-optimizations"></a>Otimizações de junção

O tipo de junção padrão no Hive é a *junção em ordem aleatória*. No Hive, mapeadores especiais leem a entrada e emitem um par de chave/valor de junção para um arquivo intermediário. O Hadoop classifica e mescla esses pares em um estágio de ordem aleatória. Esse estágio de ordem aleatória é caro. Selecionar a junção correta com base em seus dados pode melhorar significativamente o desempenho.

| Tipo de junção | Quando | Como | Configurações do Hive | Comentários |
| --- | --- | --- | --- | --- |
| Junção em ordem aleatória | <ul><li>Opção padrão</li><li>Sempre funciona</li></ul> | <ul><li>Lê de parte de uma das tabelas</li><li>Particiona em buckets e classifica de acordo com a chave de junção</li><li>Envia um bucket para cada redução</li><li>A junção é feita no lado de redução</li></ul> | Nenhuma configuração significativa do Hive é necessária | Sempre funciona |
| Junção de mapa | <ul><li>Uma tabela pode caber na memória</li></ul> | <ul><li>Lê a tabela pequena na tabela de hash de memória</li><li>Transmite parte do arquivo grande</li><li>Junta cada registro da tabela de hash</li><li>As junções são feitas apenas pelo mapeador</li></ul> | `hive.auto.confvert.join=true` | Rápido, mas limitado |
| Classificar bucket de mesclagem | Se as duas tabelas forem: <ul><li>Classificadas da mesma forma</li><li>Particionadas em bucket da mesma forma</li><li>União na coluna classificada/particionada em bucket</li></ul> | Cada processo: <ul><li>Lê um bucket de cada tabela</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Eficiente |

### <a name="execution-engine-optimizations"></a>Otimizações do mecanismo de execução

Recomendações adicionais para otimizar o mecanismo de execução do Hive:

| Configuração | Recomendadas | Padrão do HDInsight |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = mais seguro, mais lento; false = mais rápido | false |
| `tez.am.resource.memory.mb` | limite superior de 4 GB para a maioria dos | Ajustado automaticamente |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight com a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar as consultas do Apache Hive no Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md)
* [Otimizar clusters](./optimize-hive-ambari.md)
* [Otimizar o Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar o Apache Pig](./optimize-pig-ambari.md)
