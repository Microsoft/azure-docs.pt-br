---
title: Otimizar configurações de cluster com o Apache Ambari - Azure HDInsight
description: Use a interface da Web do Apache Ambari para configurar e otimizar clusters do HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: f0db36fa380d0d1bb7f2b581c4bf8fa1abfaadaf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60698491"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Use o Apache Ambari para otimizar as configurações de cluster do HDInsight

O HDInsight fornece clusters [Apache Hadoop](https://hadoop.apache.org/) para aplicativos de processamento de dados de larga escala. Gerenciar, monitorar e otimizar esses clusters complexos com vários nós pode ser desafiador. [Apache Ambari](https://ambari.apache.org/) é uma interface da Web voltada para gerenciar e monitorar clusters Linux do HDInsight.  Para clusters do Windows, use a [API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

Para ver uma introdução ao uso da interface do usuário da Web do Ambari, consulte [Gerenciar clusters HDInsight usando a interface de usuário do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)

Faça logon no Ambari em `https://CLUSTERNAME.azurehdidnsight.net` usando suas credenciais do cluster. A tela inicial exibe um painel de visão geral.

![Painel do Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-dashboard.png)

A interface do usuário da Web do Ambari pode ser usada para gerenciar hosts, serviços, alertas, configurações e modos de exibição. O Ambari não pode ser usado para criar um cluster HDInsight, atualizar serviços, gerenciar pilhas e versões, descomissionar ou recomissionar hosts ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerenciar a configuração do cluster

As configurações ajudam a ajustar a um serviço específico. Para modificar as definições de configuração de um serviço, selecione o serviço na barra lateral **Serviços** (à esquerda) e, em seguida, navegue até a guia **Configurações** na página de detalhes do serviço.

![Barra lateral de serviços](./media/hdinsight-changing-configs-via-ambari/services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Modificar o tamanho do heap do NameNode Java

O tamanho do heap do NameNode Java depende de muitos fatores, como a carga no cluster, o número de arquivos e os números de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam exigir mais ou menos memória. 

Para modificar o tamanho do heap do NameNode Java:

1. Selecione **HDFS** na barra lateral Serviços e navegue até a guia **Configurações**.

    ![Configuração do HDFS](./media/hdinsight-changing-configs-via-ambari/hdfs-config.png)

1. Localize a configuração **Tamanho do heap do NameNode Java**. Também é possível usar a caixa de texto **filtro** para digitar e localizar uma configuração específica. Selecione o ícone de **caneta** ao lado do nome da configuração.

    ![Tamanho do heap do NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size.png)

1. Digite o novo valor na caixa de texto e pressione **Enter** para salvar a alteração.

    ![Editar o tamanho do heap do NameNode Java](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit.png)

1. O tamanho de heap do NameNode Java é alterado para 1 GB de 2 GB.

    ![Tamanho do heap do NameNode Java editado](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Salve as alterações clicando no botão verde **Salvar** na parte superior da tela de configuração.

    ![Salvar alterações](./media/hdinsight-changing-configs-via-ambari/save-changes.png)

## <a name="apache-hive-optimization"></a>Otimização do Apache Hive

As seções a seguir descrevem opções de configuração para otimizar o desempenho geral do Apache Hive.

1. Para modificar parâmetros de configuração do Hive, selecione **Hive** na barra lateral Serviços.
1. Navegue até a guia **Configurações**.

### <a name="set-the-hive-execution-engine"></a>Definir o mecanismo de execução do Hive

O Hive fornece dois mecanismos de execução: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) e [Apache TEZ](https://tez.apache.org/). O Tez é mais rápido que o MapReduce. Clusters Linux do HDInsight têm o Tez como mecanismo de execução padrão. Para alterar o mecanismo de execução:

1. Na guia **Configurações** do Hive, digite **mecanismo de execução** na caixa de filtro.

    ![Mecanismo de execução de pesquisa](./media/hdinsight-changing-configs-via-ambari/search-execution.png)

1. O valor padrão da propriedade **Otimização** é **Tez**.

    ![Otimização – Tez](./media/hdinsight-changing-configs-via-ambari/optimization-tez.png)

### <a name="tune-mappers"></a>Ajustar mapeadores

O Hadoop tenta dividir (*mapear*) arquivos únicos em vários arquivos e processar os arquivos resultantes em paralelo. O número de mapeadores depende do número de divisões. Os dois parâmetros de configuração a seguir definem o número de divisões para o mecanismo de execução Tez:

* `tez.grouping.min-size`: Limite inferior do tamanho de uma divisão agrupada, com um valor padrão de 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: Limite superior do tamanho de uma divisão agrupada, com um valor padrão de 1 GB (1.073.741.824 bytes).

Como regra de desempenho geral, diminua esses dois parâmetros para melhorar a latência e aumente para aumentar a taxa de transferência.

Por exemplo, para definir quatro tarefas de mapeador com o tamanho de dados de 128 MB, você definiria os dois parâmetros como 32 MB cada (33.554.432 bytes).

1. Para modificar os parâmetros de limite, navegue até a guia **Configurações** do serviço Tez. Expanda o painel **Geral** e localize os parâmetros `tez.grouping.max-size` e `tez.grouping.min-size`.

1. Defina os dois parâmetros como **33,554,432** bytes (32 MB).

    ![Tamanhos de agrupamento do Tez](./media/hdinsight-changing-configs-via-ambari/tez-grouping-size.png)
 
Essas alterações afetam todos os trabalhos do Tez no servidor. Para obter o resultado ideal, escolha valores de parâmetro apropriados.

### <a name="tune-reducers"></a>Ajustar redutores

[Apache ORC](https://orc.apache.org/) e [Snappy](https://google.github.io/snappy/) ambos oferecem alto desempenho. Entretanto, o Hive pode ter poucos redutores por padrão, o que causa gargalos.

Por exemplo, digamos que você tenha um tamanho de dados de entrada igual a 50 GB. Esses dados em formato ORC com compactação do Snappy terão 1 GB. O Hive estima o número de redutores necessários como: (número de entrada de bytes para mapeadores / `hive.exec.reducers.bytes.per.reducer`).

Com as configurações padrão, este exemplo terá 4 redutores.

O parâmetro `hive.exec.reducers.bytes.per.reducer` especifica o número de bytes processados por redutor. O valor padrão é 64 MB. Diminuir esse valor aumenta o paralelismo e pode melhorar o desempenho. Diminui-lo demais também pode produzir muitos redutores, o que pode prejudicar o desempenho. Este parâmetro é baseado em seus requisitos de dados específicos, nas configurações de compactação e em outros fatores ambientais.

1. Para modificá-lo, navegue até a guia **Configurações** do Hive e localize o parâmetro **Dados por Redutor** na página Configurações.

    ![Dados por Redutor](./media/hdinsight-changing-configs-via-ambari/data-per-reducer.png)
 
1. Selecione **Editar** para modificar o valor para 128 MB (134.217.728 bytes) e, em seguida, pressione **Enter** para salvar.

    ![Dados por Redutor – editado](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Dado um tamanho de entrada de 1.024 MB, com 128 MB de dados por redutor, há 8 redutores (1024/128).

1. Atribuir um valor incorreto para o parâmetro **Dados por Redutor** pode resultar em um grande número de redutores, afetando negativamente o desempenho da consulta. Para limitar o número máximo de redutores, defina um valor apropriado para `hive.exec.reducers.max`. O valor padrão é 1009.

### <a name="enable-parallel-execution"></a>Habilitar execução paralela

Uma consulta do Hive é executada em uma ou mais etapas. Se as etapas independentes puderem ser executadas em paralelo, o desempenho da consulta aumentará.

1.  Para habilitar a execução de consultas em paralelo, navegue até a guia **Configurações** do Hive e pesquise pela propriedade `hive.exec.parallel`. O valor padrão é falso. Altere o valor para true e pressione **Enter** para salvá-lo.
 
1.  Para limitar o número de trabalhos a serem executados em paralelo, modifique o `hive.exec.parallel.thread.number` propriedade. O valor padrão é 8.

    ![Execução do Hive em paralelo](./media/hdinsight-changing-configs-via-ambari/hive-exec-parallel.png)


### <a name="enable-vectorization"></a>Habilitar vetorização

O Hive processa dados linha por linha. A vetorização instrui o Hive a processar dados em blocos de 1.024 linhas em vez de uma linha por vez. A vetorização é aplicável somente ao formato de arquivo ORC.

1. Para habilitar uma execução de consulta vetorizada, navegue até a guia **Configurações** do Hive e pesquise pelo parâmetro `hive.vectorized.execution.enabled`. O valor padrão é true para o Hive 0.13.0 ou posterior.
 
1. Para habilitar a execução vetorizada para o lado de redução da consulta, defina o parâmetro `hive.vectorized.execution.reduce.enabled` como true. O valor padrão é falso.

    ![Execução vetorizada no Hive](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Habilitar CBO (otimização baseada em custo)

Por padrão, o Hive segue um conjunto de regras para encontrar um plano de execução de consulta ideal. A CBO (otimização baseada em custo) avalia vários planos para executar uma consulta, atribui um custo a cada plano e determina o plano mais barato para executar uma consulta.

Para habilitar a CBO, navegue até a guia **Configurações** do Hive, pesquise por `parameter hive.cbo.enable` e, em seguida, mude o botão de alternância para **Ativado**.

![Configuração de CBO](./media/hdinsight-changing-configs-via-ambari/cbo.png)

Os seguintes parâmetros de configuração adicionais aumentam o desempenho de consulta do Hive quando a CBO está habilitada:

* `hive.compute.query.using.stats`

    Quando definido como true, o Hive usa estatísticas armazenadas em seu metastore para responder consultas simples como `count(*)`.

    ![Estatísticas de CBO](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Estatísticas de coluna são criadas quando a CBO está habilitada. O Hive usa estatísticas de coluna, que são armazenadas em um metastore, para otimizar consultas. Obter estatísticas de coluna para cada coluna leva mais tempo quando o número de colunas é alto. Quando definida como false, essa configuração desabilita a busca de estatísticas de coluna do metastore.

    ![Estatísticas de coluna, conjunto de estatísticas do Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Estatísticas de partição básicas, como número de linhas, tamanho de dados e tamanho do arquivo, são armazenadas no metastore. Quando definido como true, as estatísticas de partição são buscadas do metastore. Quando definido como false, o tamanho do arquivo é buscado do sistema de arquivos e o número de linhas é buscado do esquema de linha.

    ![Estatísticas de partição, conjunto de estatísticas do Hive](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Habilitar compactação intermediária

Tarefas de mapeamento criam arquivos intermediários que são usados pelas tarefas do redutor. A compactação intermediária reduz o tamanho do arquivo intermediário.

Trabalhos do Hadoop normalmente têm um gargalo de E/S. Compactar os dados pode acelerar a E/S e a transferência de rede em geral.

Os tipos de compactação disponíveis são:

| Formatar | Ferramenta | Algoritmo | Extensão de arquivo | Divisível? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | .gz | Não  |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Sim |
| LZO | Lzop | LZO | .lzo | Sim, se indexado |
| Snappy | N/D | Snappy | Snappy | Não  |

Como regra geral, é importante que o método de compactação seja divisível. Caso contrário, poucos mapeadores serão criados. Se os dados de entrada forem texto, `bzip2` será a melhor opção. Para o formato ORC, o Snappy é a opção de compactação mais rápida.

1. Para habilitar a compactação intermediária, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.exec.compress.intermediate` como verdadeiro. O valor padrão é falso.

    ![Compactação intermediária de execução do Hive](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Para compactar arquivos intermediários, escolha um codec de compactação com menor custo de CPU, mesmo se o codec não tiver uma alta saída de compactação.

1. Para definir o codec de compactação intermediário, adicione a propriedade personalizada `mapred.map.output.compression.codec` ao arquivo `hive-site.xml` ou `mapred-site.xml`.

1. Para adicionar uma configuração personalizada:

     a. Navegue até a guia **Configurações** do Hive e selecione a guia **Avançado**.

    b. Na guia **Avançado**, localize e expanda o painel **hive-site personalizado**.

    c. Clique no link **Adicionar Propriedade** na parte inferior do painel hive-site personalizado.

    d. Na janela Adicionar Propriedade, digite `mapred.map.output.compression.codec` como a chave e `org.apache.hadoop.io.compress.SnappyCodec` como o valor.

    e. Clique em **Adicionar**.

    ![Propriedade personalizada do Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Isso compactará o arquivo intermediário usando a compactação do Snappy. Após ser adicionada, a propriedade aparecerá no painel hive-site personalizado.

    > [!NOTE]  
    > Este procedimento modifica o arquivo `$HADOOP_HOME/conf/hive-site.xml`.

### <a name="compress-final-output"></a>Compactar a saída final

A saída final do Hive também pode ser compactada.

1. Para compactar a saída final do Hive, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.exec.compress.output` como verdadeiro. O valor padrão é falso.

1. Para escolher o codec de compactação de saída, adicione a propriedade personalizada `mapred.output.compression.codec` ao painel hive-site personalizado, conforme descrito na etapa 3 da seção anterior.

    ![Propriedade personalizada do Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Habilitar a execução especulativa

A execução especulativa inicializa um determinado número de tarefas duplicadas para detectar e colocar na lista negra o monitor de tarefas de execução lenta, melhorando a execução geral do trabalho ao otimizar os resultados das tarefas individuais.

A execução especulativa não deve ser ativada para tarefas MapReduce de execução longa com grandes quantidades de entrada.

* Para habilitar a execução especulativa, navegue até a guia **Configurações** do Hive e, em seguida, defina o parâmetro `hive.mapred.reduce.tasks.speculative.execution` como verdadeiro. O valor padrão é falso.

    ![Execução especulativa de tarefas de redução mapred do Hive](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Ajustar partições dinâmicas

O Hive permite criar partições dinâmicas ao inserir registros em uma tabela sem predefinir cada uma das partições. Esse é um recurso poderoso, embora possa resultar na criação de um grande número de partições e de um grande número de arquivos para cada partição.

1. Para o Hive fazer partições dinâmicas, o valor do parâmetro `hive.exec.dynamic.partition` deve ser true (o padrão).

1. Altere o modo de partição dinâmica para *estrito*. No modo estrito, pelo menos uma partição deve ser estática. Isso impede consultas sem o filtro de partição na cláusula WHERE, ou seja, *estrito* impede consultas que examinam todas as partições. Navegue até a guia **Configurações** do Hive e, em seguida, defina `hive.exec.dynamic.partition.mode` como **estrito**. O valor padrão é **não estrito**.
 
1. Para limitar o número de partições dinâmicas a serem criadas, modifique o parâmetro `hive.exec.max.dynamic.partitions`. O valor padrão é 5.000.
 
1. Para limitar o número total de partições dinâmicas por nó, modifique `hive.exec.max.dynamic.partitions.pernode`. O valor padrão é 2.000.

### <a name="enable-local-mode"></a>Habilitar modo local

O modo local permite que o Hive execute todas as tarefas de um trabalho em um único computador ou, às vezes, em um único processo. Isso melhorará o desempenho de consulta se os dados de entrada forem pequenos e a sobrecarga da inicialização de tarefas para consultas consumir um percentual significativo da execução de consulta geral.

Para habilitar o modo local, adicione o parâmetro `hive.exec.mode.local.auto` ao painel hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression).

![Modo de execução local automático do Hive](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Definir MultiGROUP BY para MapReduce único

Quando esta propriedade é definida como true, uma consulta MultiGROUP BY com chaves de agrupamento comuns gera um único trabalho de MapReduce.  

Para habilitar esse comportamento, adicione o parâmetro `hive.multigroupby.singlereducer` ao painel hive-site personalizado, conforme explicado na etapa 3 da seção [Habilitar compactação intermediária](#enable-intermediate-compression).

![Definir MultiGROUP BY para MapReduce único do Hive](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Otimizações adicionais do Hive

As seções a seguir descrevem otimizações adicionais relacionadas ao Hive que você pode definir.

#### <a name="join-optimizations"></a>Otimizações de junção

O tipo de junção padrão no Hive é a *junção em ordem aleatória*. No Hive, mapeadores especiais leem a entrada e emitem um par de chave/valor de junção para um arquivo intermediário. O Hadoop classifica e mescla esses pares em um estágio de ordem aleatória. Esse estágio de ordem aleatória é caro. Selecionar a junção correta com base em seus dados pode melhorar significativamente o desempenho.

| Tipo de junção | Quando | Como | Configurações do Hive | Comentários |
| -- | -- | -- | -- | -- |
| Junção em ordem aleatória | <ul><li>Opção padrão</li><li>Sempre funciona</li></ul> | <ul><li>Lê de parte de uma das tabelas</li><li>Particiona em buckets e classifica de acordo com a chave de junção</li><li>Envia um bucket para cada redução</li><li>A junção é feita no lado de redução</li></ul> | Nenhuma configuração significativa do Hive é necessária | Sempre funciona |
| Junção de mapa | <ul><li>Uma tabela pode caber na memória</li></ul> | <ul><li>Lê a tabela pequena na tabela de hash de memória</li><li>Transmite parte do arquivo grande</li><li>Junta cada registro da tabela de hash</li><li>As junções são feitas apenas pelo mapeador</li></ul> | `hive.auto.confvert.join=true` | Muito rápido, mas limitado |
| Classificar bucket de mesclagem | Se as duas tabelas forem: <ul><li>Classificadas da mesma forma</li><li>Particionadas em bucket da mesma forma</li><li>União na coluna classificada/particionada em bucket</li></ul> | Cada processo: <ul><li>Lê um bucket de cada tabela</li><li>Processa a linha com o valor mais baixo</li></ul> | `hive.auto.convert.sortmerge.join=true` | Muito eficiente |

#### <a name="execution-engine-optimizations"></a>Otimizações do mecanismo de execução

Recomendações adicionais para otimizar o mecanismo de execução do Hive:

| Configuração | Recomendadas | Padrão do HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | True = mais seguro, mais lento; false = mais rápido | falso |
| `tez.am.resource.memory.mb` | Limite superior de 4 GB para a maioria | Ajustado automaticamente |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20000 |

## <a name="apache-pig-optimization"></a>Otimização de Apache Pig

Propriedades de [Apache Pig](https://pig.apache.org/) podem ser modificadas da interface do usuário da Web do Ambari para ajustar consultas de Pig. Modificar propriedades de Pig do Ambari modifica diretamente as propriedades de Pig no arquivo `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Para modificar propriedades de Pig, navegue até a guia **Configurações** de Pig e, em seguida, expanda o painel **Propriedades avançadas de Pig**.

1. Localize, remova as marcas de comentário e altere o valor da propriedade que deseja modificar.

1. Selecione **salvar** no lado superior direito da janela para salvar o novo valor. Algumas propriedades podem exigir uma reinicialização do serviço.

    ![Propriedades avançadas de Pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)
 
> [!NOTE]  
> As configurações de nível de sessão substituem os valores de propriedade no arquivo `pig.properties`.

### <a name="tune-execution-engine"></a>Ajustar mecanismo de execução

Dois mecanismos de execução estão disponíveis para executar scripts de Pig: MapReduce e Tez. O Tez é um mecanismo otimizado e muito mais rápido que o MapReduce.

1. Para modificar o mecanismo de execução, no painel **Propriedades avançadas de Pig**, localize a propriedade `exectype`.

1. O valor padrão é **MapReduce**. Altere-o para **Tez**.


### <a name="enable-local-mode"></a>Habilitar modo local

De forma semelhante ao Hive, o modo local é usado para acelerar trabalhos com quantidades de dados relativamente menores.

1. Para habilitar o modo local, defina `pig.auto.local.enabled` como **true**. O valor padrão é falso.

1. Trabalhos com um tamanho de dados de entrada menor que o valor da propriedade `pig.auto.local.input.maxbytes` são considerados trabalhos pequenos. O valor padrão é 1 GB.


### <a name="copy-user-jar-cache"></a>Copiar o cache de jar do usuário

O Pig copia os arquivos JAR exigidos pelo UDFs para um cache distribuído para torná-los disponíveis para nós de tarefa. Esses jars não mudam com frequência. Se habilitada, a configuração `pig.user.cache.enabled` permite que os jars sejam colocados em um cache para reutilizá-los para trabalhos executados pelo mesmo usuário. Isso resulta em uma pequena melhoria no desempenho do trabalho.

1. Para habilitar, defina `pig.user.cache.enabled` como true. O padrão é false.

1. Para definir o caminho base dos jars armazenado em cache, defina `pig.user.cache.location` como o caminho base. O padrão é `/tmp`.


### <a name="optimize-performance-with-memory-settings"></a>Otimizar o desempenho com as configurações de memória

As seguintes configurações de memória podem ajudar a otimizar o desempenho de script do Pig.

* `pig.cachedbag.memusage`: A quantidade de memória alocada a um recipiente. Um recipiente é um conjunto de tuplas. Uma tupla é um conjunto ordenado de campos e um campo é uma parte dos dados. Se estiverem além da memória alocada, os dados em um recipiente serão despejados para o disco. O valor padrão é 0.2, que representa 20 por cento da memória disponível. Essa memória é compartilhada entre todos os recipientes em um aplicativo.

* `pig.spill.size.threshold`: Recipientes maiores que esse limite de tamanho de despejo (em bytes) são despejados para o disco. O valor padrão é 5 MB.


### <a name="compress-temporary-files"></a>Compactar arquivos temporários

O Pig gera arquivos temporários durante a execução do trabalho. Compactar os arquivos temporários resulta em uma melhoria do desempenho ao ler ou gravar arquivos no disco. As configurações a seguir podem ser usadas para compactar arquivos temporários.

* `pig.tmpfilecompression`: Quando for true, habilitará a compactação de arquivos temporários. O valor padrão é falso.

* `pig.tmpfilecompression.codec`: O codec de compactação a ser usado para compactar os arquivos temporários. Os codecs de compactação recomendados são o [LZO](https://www.oberhumer.com/opensource/lzo/) e o Snappy para menor utilização da CPU.

### <a name="enable-split-combining"></a>Habilitar combinação de divisão

Quando habilitada, pequenos arquivos são combinados para que haja menos tarefas de mapeamento. Isso aumenta a eficiência de trabalhos com muitos arquivos pequenos. Para habilitar, defina `pig.noSplitCombination` como true. O valor padrão é falso.


### <a name="tune-mappers"></a>Ajustar mapeadores

O número de mapeadores é controlado modificando a propriedade `pig.maxCombinedSplitSize`. Isso especifica o tamanho dos dados a serem processados por uma única tarefa de mapeamento. O valor padrão é o tamanho do bloco padrão do sistema de arquivos. Aumentar esse valor resulta na redução do número de tarefas do mapeador.


### <a name="tune-reducers"></a>Ajustar redutores

O número de redutores é calculado com base no parâmetro `pig.exec.reducers.bytes.per.reducer`. O parâmetro especifica o número de bytes processados por redutor e por padrão é 1 GB. Para limitar o número máximo de redutores, defina a propriedade `pig.exec.reducers.max`, que por padrão é 999.


## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Otimização do Apache HBase com a interface do usuário da Web do Ambari

A configuração do [Apache HBase](https://hbase.apache.org/) é modificada na guia **HBase Configs**. As seções a seguir descrevem algumas das definições de configuração importantes que afetam o desempenho do HBase.

### <a name="set-hbaseheapsize"></a>Definir HBASE_HEAPSIZE

O tamanho do heap do HBase especifica a quantidade máxima do heap a ser usada, em megabytes, por *região* e por servidores *mestre*. O valor padrão é 1,000 MB. Isso deve ser ajustado de acordo com a carga de trabalho do cluster.

1. Para modificar, navegue até o painel **HBase-env Avançado** na guia **Configurações** do HBase e, em seguida, localize a configuração `HBASE_HEAPSIZE`.

1. Altere o valor padrão para 5.000 MB.

    ![HBASE_HEAPSIZE](./media/hdinsight-changing-configs-via-ambari/hbase-heapsize.png)


### <a name="optimize-read-heavy-workloads"></a>Otimizar cargas de trabalho com uso intenso de leitura

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com uso intenso de leitura.

#### <a name="block-cache-size"></a>Tamanho do cache do bloco

O cache do bloco é o cache de leitura. Seu tamanho é controlado pelo parâmetro `hfile.block.cache.size`. O valor padrão é 0.4, que é 40 por cento da memória total do servidor da região. Quanto maior o tamanho do cache do bloco, mais rápidas serão as leituras aleatórias.

1. Para modificar esse parâmetro, navegue até a guia **Configurações** na guia **Configurações** do HBase e, em seguida, localize **% do RegionServer Alocada para Buffers de Leitura**.

    ![Tamanho do cache do bloco do HBase](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)
 
1. Para alterar o valor, selecione o ícone **Editar**.


#### <a name="memstore-size"></a>Tamanho do Memstore

Todas as edições são armazenadas no buffer de memória, chamado de *Memstore*. Isso aumenta a quantidade total de dados que podem ser gravados em disco em uma única operação e acelera o acesso posterior às edições recentes. O tamanho do Memstore é definido pelos dois parâmetros a seguir:

* `hbase.regionserver.global.memstore.UpperLimit`: Define o percentual máximo do servidor de região que o Memstore combinado pode usar.

* `hbase.regionserver.global.memstore.LowerLimit`: Define o percentual mínimo do servidor de região que o Memstore combinado pode usar.

Para otimizar leituras aleatórias, você pode reduzir os limites superior e inferior do Memstore.


#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Número de linhas buscadas ao fazer a varredura do disco

A configuração `hbase.client.scanner.caching` define o número de linhas lidas do disco quando o método `next` é chamado em um scanner.  O valor padrão é 100. Quanto maior o número, menos chamadas remotas serão feitas do cliente para o servidor de região, resultando em verificações mais rápidas. No entanto, isso também aumentará a pressão de memória no cliente.

![Número de linhas buscadas no HBase](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Não defina o valor de modo que o tempo entre a invocação do método seguinte em um scanner seja maior que o tempo limite do scanner. A duração do tempo limite do scanner é definida pela propriedade `hbase.regionserver.lease.period`.


### <a name="optimize-write-heavy-workloads"></a>Otimizar cargas de trabalho com uso intenso de gravação

As configurações a seguir são importantes para melhorar o desempenho de cargas de trabalho com uso intenso de gravação.


#### <a name="maximum-region-file-size"></a>Tamanho máximo do arquivo de região

O HBase armazena dados em um formato de arquivo interno, chamado *HFile*. A propriedade `hbase.hregion.max.filesize` define o tamanho de um único HFile para uma região.  Uma região será dividida em duas regiões se a soma de todos os HFiles nela for maior do que essa configuração.
 
![Tamanho máximo de HRegion do HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Quanto maior o tamanho do arquivo de região, menor o número de divisões. É possível aumentar o tamanho do arquivo para determinar um valor que resulta no desempenho máximo de gravação.


#### <a name="avoid-update-blocking"></a>Evitar o bloqueio de atualização

* A propriedade `hbase.hregion.memstore.flush.size` define o tamanho no qual o Memstore é liberado para o disco. O tamanho padrão é 128 MB.

* O multiplicador de bloco de região do Hbase é definido por `hbase.hregion.memstore.block.multiplier`. O valor padrão é 4. O máximo permitido é 8.

* O HBase bloqueará atualizações se o Memstore tiver (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes.

    Com os valores padrão de tamanho de liberação e multiplicador de bloco, as atualizações serão bloqueadas quando o Memstore tiver 128 * 4 = 512 MB. Para reduzir o número de blocos de atualização, aumente o valor de `hbase.hregion.memstore.block.multiplier`.

![Multiplicador de bloco de região do HBase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)


### <a name="define-memstore-size"></a>Definir tamanho do Memstore

O tamanho do Memstore é definido pelos parâmetros `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit`. Definir esses valores como iguais uns aos outros reduz pausas durante as gravações (também causando liberações mais frequentes) e resulta em um melhor desempenho de gravação.


### <a name="set-memstore-local-allocation-buffer"></a>Definir o buffer de alocação local do Memstore

O uso do buffer de alocação local do Memstore é determinado pela propriedade `hbase.hregion.memstore.mslab.enabled`. Quando habilitado (true), isso evita a fragmentação do heap durante a operação com uso intenso de gravação. O valor padrão é true.
 
![hbase.hregion.memstore.mslab.enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)


## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight com a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
