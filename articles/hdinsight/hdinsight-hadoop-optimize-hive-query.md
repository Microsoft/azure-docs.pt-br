---
title: Otimizar consultas do Hive no Azure HDInsight | Microsoft Docs
description: Este artigo descreve como otimizar suas consultas do Apache Hive para Hadoop no HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.openlocfilehash: 218085d8d3969218be1a0557fdc477c730879cbe
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543700"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Otimizar as consultas do Apache Hive no Azure HDInsight

No Azure HDInsight, há vários tipos de cluster e tecnologias que podem executar consultas do Apache Hive. Ao criar o cluster do HDInsight, escolha o tipo de cluster apropriado para ajudar a otimizar o desempenho de suas necessidades de carga de trabalho.

Por exemplo, escolha **consulta interativa** tipo otimizar para consultas ad hoc e interativas de cluster. Escolha o tipo de cluster Apache **Hadoop** para otimizar para consultas do Hive usadas como um processo em lote. Os tipos de cluster **Spark** e **HBase** também podem executar consultas Hive. Para saber mais sobre como executar consultas Hive em vários tipos de cluster do HDInsight, confira [O que é o Apache Hive e HiveQL no Azure HDInsight?](hadoop/hdinsight-use-hive.md).

Os clusters HDInsight do tipo de cluster Hadoop não são otimizados para desempenho, por padrão. Este artigo descreve alguns dos métodos de otimização de desempenho do Hive mais comuns que você pode aplicar às suas consultas.

## <a name="scale-out-worker-nodes"></a>Escalar nós de trabalho horizontalmente

O aumento do número de nós de trabalho em um cluster HDInsight permite que o trabalho aproveite mais mapeadores e redutores para execução paralela. Há duas maneiras de aumentar a escalabilidade horizontal no HDInsight:

* No momento da criação de um cluster, você pode especificar o número de nós de trabalho usando o portal do Azure, o Azure PowerShell ou a interface de linha de comando.  Para saber mais, veja [Criar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md). A seguinte captura de tela mostra a configuração de nó de trabalho no Portal do Azure:
  
    ![scaleout_1][image-hdi-optimize-hive-scaleout_1]
    
* Após a criação, você também pode editar o número de nós de trabalho para escalar horizontalmente ainda mais um cluster sem recriar um:

    ![scaleout_1][image-hdi-optimize-hive-scaleout_2]

Para saber mais sobre dimensionamento do HDInsight, consulte [Dimensionar clusters HDInsight](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Usar Apache Tez, em vez de MapReduce

[Apache Tez](https://tez.apache.org/) é um mecanismo de execução alternativo ao mecanismo MapReduce. Clusters HDInsight baseados em Linux têm o Tez habilitado por padrão.

![tez_1][image-hdi-optimize-hive-tez_1]

O Tez é mais rápido porque:

* **Execute o DAG (grafo direcionado acíclico) como um trabalho único no mecanismo MapReduce**. O DAG requer que cada conjunto de mapeadores seja seguido por um conjunto de redutores. Isso faz com que vários trabalhos do MapReduce sejam gerados para cada consulta do Hive. O Tez não tem essa restrição e pode processar DAG complexo como um único trabalho, minimizando a sobrecarga de inicialização de trabalho.
* **Evita gravações desnecessárias**. Vários trabalhos são usados para processar a mesma consulta Hive no mecanismo MapReduce. A saída de cada trabalho do MapReduce é gravada no HDFS para dados intermediários. Como o Tez minimiza o número de trabalhos para cada consulta do Hive, ele pode evitar gravação desnecessária.
* **Minimiza atrasos de inicialização**. O Tez é mais capaz de minimizar o atraso de inicialização, reduzindo o número de mapeadores de que precisa para ser iniciado, além de aumentar a otimização de maneira geral.
* **Reutiliza contêineres**. Sempre que possível, o Tez é capaz de reutilizar contêineres para garantir que a latência devido à inicialização de contêineres seja reduzida.
* **Técnicas de otimização contínua**. Tradicionalmente, a otimização ocorria durante a fase de compilação. No entanto, há disponibilidade de mais informações sobre as entradas que permitem maior otimização durante o tempo de execução. O Tez usa técnicas de otimização contínua que permitem otimizar ainda mais o plano mais adiante na fase de tempo de execução.

Para obter mais informações sobre esses conceitos, consulte [Apache TEZ](https://tez.apache.org/).

Você pode fazer qualquer consulta do Hive habilitada pelo Tez prefixando a consulta com o seguinte conjunto de comandos:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Particionamento do Hive

As operações de E/S são o principal gargalo de desempenho para executar consultas do Hive. O desempenho pode ser melhorado se a quantidade de dados que precisam ser lidos puder ser reduzida. Por padrão, consultas do Hive examinam tabelas inteiras do Hive. Porém, para consultas que só precisam verificar uma pequena quantidade de dados (por exemplo, consultas com filtragem), esse comportamento cria uma sobrecarga desnecessária. O particionamento do Hive permite que as consultas do Hive acessem somente a quantidade necessária de dados nas tabelas do Hive.

O particionamento do Hive é implementado pela reorganização dos dados brutos em novos diretórios. Cada partição tem seu próprio diretório de arquivos. O particionamento é definido pelo usuário. O diagrama a seguir ilustra o particionamento de uma tabela do Hive pela coluna *Ano*. Um novo diretório é criado para cada ano.

![Particionamento do Hive][image-hdi-optimize-hive-partitioning_1]

Algumas considerações sobre particionamento:

* **Não particione pouco demais** – O particionamento em colunas com apenas alguns valores pode causar poucas partições. Por exemplo, o particionamento por gênero só cria duas partições (“masculino” e “feminino”), reduzindo a latência no máximo pela metade.
* **Não particione muito demais** – No outro extremo, criar uma partição em uma coluna com um valor exclusivo (por exemplo, userid) causa várias partições. Partição demais causa muita carga sobre namenode do cluster porque ele precisa manipular o grande número de diretórios.
* **Evite distorção de dados** -Escolha com bom senso sua chave de particionamento para que todas as partições tenham o mesmo tamanho. Por exemplo, o particionamento na coluna *Estado* pode afetar a distribuição de dados. Como o estado da Califórnia tem uma população quase 30 vezes maior que a de Vermont, o tamanho da partição é potencialmente distorcido e o desempenho pode variar muito.

Para criar uma tabela de partição, use a cláusula *Particionado por* :

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Depois de criar a tabela particionada, você pode criar particionamento estático ou dinâmico.

* **Particionamento estático** significa que você já fragmentou os dados nos diretórios apropriados. Com partições estáticas, você adiciona partições Hive manualmente com base no local do diretório. O snippet de código a seguir é um exemplo.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Particionamento dinâmico** significa que você deseja que o Hive crie partições automaticamente para você. Como você já criou a tabela de partição a partir da tabela de preparo, só precisa inserir dados na tabela particionada:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Para obter mais informações, consulte [Partitioned Tables](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables) (Tabelas particionadas).

## <a name="use-the-orcfile-format"></a>Use o formato ORCFile
O Hive dá suporte a vários formatos de arquivo. Por exemplo:

* **Texto**: é o formato de arquivo padrão e funciona com a maioria dos cenários.
* **Avro**: funciona bem para cenários de interoperabilidade.
* **ORC/Parquet**: mais adequado para desempenho.

O formato ORC é uma maneira altamente eficiente para armazenar dados do Hive. Comparado a outros formatos, o ORC tem as seguintes vantagens:

* suporte para tipos complexos, incluindo a data e hora e tipos complexos e semiestruturados.
* até 70% de compactação.
* indexa a cada 10.000 linhas, o que permite ignorar linhas de índices.
* uma redução significativa no tempo de execução.

Para habilitar o formato ORC, primeiro você deve criar uma tabela com a cláusula *Armazenado como ORC*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Em seguida, insira dados na tabela ORC a partir da tabela de preparo. Por exemplo:

   ```hive
   INSERT INTO TABLE lineitem_orc
   SELECT L_ORDERKEY as L_ORDERKEY, 
          L_PARTKEY as L_PARTKEY , 
          L_SUPPKEY as L_SUPPKEY,
          L_LINENUMBER as L_LINENUMBER,
          L_QUANTITY as L_QUANTITY, 
          L_EXTENDEDPRICE as L_EXTENDEDPRICE,
          L_DISCOUNT as L_DISCOUNT,
          L_TAX as L_TAX,
          L_RETURNFLAG as L_RETURNFLAG,
          L_LINESTATUS as L_LINESTATUS,
          L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;
   ```
   
Você pode ler mais sobre o formato ORC no [manual da linguagem Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vetorização

A vetorização permite que o Hive processe um lote de 1.024 linhas juntas em vez de processar uma linha por vez. Isso significa que operações simples são concluídas mais rapidamente porque menos código interno precisa ser executado.

Para habilitar a vetorização, prefixe sua consulta do Hive com a seguinte configuração:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Para obter mais informações, consulte [Execução de consultas vetorizadas](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Outros métodos de otimização
Há mais métodos de otimização que você pode considerar, por exemplo:

* **Bucketing do Hive:** uma técnica que permite clusterizar ou segmentar grandes conjuntos de dados para otimizar o desempenho da consulta.
* **Otimização de junção:** otimização do planejamento da execução de consultas do Hive para melhorar a eficiência de junções e reduzir a necessidade de dicas de usuário. Para obter mais informações, consulte [Otimização de junção](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
* **Aumentar redutores**.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a vários métodos comuns de otimização de consultas do Hive. Para saber mais, consulte os seguintes artigos:

* [Usar o Apache Hive no HDInsight](hadoop/hdinsight-use-hive.md)
* [Analisar dados de atraso de voo usando a consulta interativa no HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Analise os dados do Twitter usando o Apache Hive no HDInsight](hdinsight-analyze-twitter-data-linux.md)

[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query/partitioning_1.png
