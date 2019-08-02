---
title: Solucionar problemas do Hive usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Hive e o Azure HDInsight.
keywords: Azure HDInsight, Hive, perguntas frequentes, guia de solução de problemas, perguntas comuns
ms.service: hdinsight
author: dharmeshkakadia
ms.author: dkakadia
ms.topic: conceptual
ms.date: 11/2/2017
ms.openlocfilehash: 91e6803e0a1302a33a3bf176ad84d0b0e0c8c5b6
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875936"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Solucionar problemas do Apache Hive usando o Azure HDInsight

Saiba mais sobre as principais perguntas e suas resoluções ao trabalhar com cargas de Apache Hive no Apache Ambari.


## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Como fazer para exportar um metastore do Hive e importá-lo em outro cluster?


### <a name="resolution-steps"></a>Etapas de resolução

1. Conecte-se ao cluster HDInsight com um cliente SSH (Secure Shell). Para saber mais, veja [Leituras adicionais](#additional-reading-end).

2. Execute o seguinte comando no cluster do HDInsight do qual você deseja exportar o metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Este comando gera um arquivo chamado allatables.sql.

3. Copie o arquivo alltables.sql para o novo cluster do HDInsight e execute o seguinte comando:

   ```apache
   hive -f alltables.sql
   ```

O código nas etapas de resolução supõe que os caminhos de dados no novo cluster sejam iguais aos caminhos de dados no cluster antigo. Se os caminhos de dados forem diferentes, você poderá editar manualmente o arquivo alltables.sql gerado para refletir as alterações.

### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Como fazer para localizar logs do Hive em um cluster?

### <a name="resolution-steps"></a>Etapas de resolução

1. Conectar-se ao cluster HDInsight usando SSH. Para saber mais, veja **Leituras adicionais**.

2. Para exibir logs de cliente do Hive, use o seguinte comando:

   ```apache
   /tmp/<username>/hive.log 
   ```

3. Para exibir logs de metastore do Hive, use o seguinte comando:

   ```apache
   /var/log/hive/hivemetastore.log 
   ```

4. Para exibir logs do Hiveserver, use o seguinte comando:

   ```apache
   /var/log/hive/hiveserver2.log 
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Como fazer para inicializar o shell do Hive com configurações específicas em um cluster?

### <a name="resolution-steps"></a>Etapas de resolução

1. Especifique um par chave-valor de configuração quando iniciar o shell do Hive. Para obter mais informações, consulte [Leituras adicionais](#additional-reading-end).

   ```apache
   hive -hiveconf a=b 
   ```

2. Para listar todas as configurações efetivas no shell de Hive, use o seguinte comando:

   ```apache
   hive> set;
   ```

   Por exemplo, use o seguinte comando para iniciar o shell do Hive com o registro em log de depuração habilitado no console:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console 
   ```

### <a name="additional-reading"></a>Leitura adicional

- [Propriedades de configuração do Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Como fazer para analisar dados de DAG do Apache Tez em um caminho crítico do cluster?


### <a name="resolution-steps"></a>Etapas de resolução
 
1. Para analisar um DAG (Grafo Direcionado Acíclico) do Apache Tez em um grafo crítico do cluster, conecte-se ao cluster do HDInsight usando SSH. Para saber mais, veja [Leituras adicionais](#additional-reading-end).

2. No prompt de comando, execute o comando a seguir:
   
   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para listar outros analisadores que podem ser usados para analisar o DAG do Tez, use o seguinte comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Você precisa fornecer um programa de exemplo como o primeiro argumento.

   Nomes de programa válidos incluem:
    - **ContainerReuseAnalyzer**: imprimir detalhes de reutilização do contêiner em um DAG
    - **CriticalPath**: Localizar o caminho crítico de um DAG
    - **LocalityAnalyzer**: imprimir detalhes de localidade em um DAG
    - **ShuffleTimeAnalyzer**: analisar os detalhes de tempo de ordem aleatória em um DAG
    - **SkewAnalyzer**: analisar os detalhes de distorção em um DAG
    - **SlowNodeAnalyzer**: detalhes do nó de impressão em um DAG
    - **SlowTaskIdentifier**: imprimir detalhes de tarefa lenta em um DAG
    - **SlowestVertexAnalyzer**: imprimir detalhes do vértice mais lentos em um DAG
    - **SpillAnalyzer**: detalhes de despejo de impressão em um DAG
    - **TaskConcurrencyAnalyzer**: imprimir os detalhes de simultaneidade de tarefa em um DAG
    - **VertexLevelCriticalPathAnalyzer**: Localizar o caminho crítico no nível do vértice em um DAG


### <a name="additional-reading"></a>Leitura adicional

- [Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Como fazer para baixar dados de DAG do Tez de um cluster?


#### <a name="resolution-steps"></a>Etapas de resolução

Há duas maneiras de coletar os dados de DAG do Tez:

- Na linha de comando:
 
    Conectar-se ao cluster HDInsight usando SSH. No prompt de comando, execute o comando a seguir:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
  ```

- Use o modo de exibição Tez do Ambari:
   
  1. Vá para o Ambari. 
  2. Vá para o modo de exibição do Tez (sob o ícone de blocos no canto superior direito). 
  3. Selecione o DAG que você deseja exibir.
  4. Selecione **Baixar dados**.

### <a name="additional-reading-end"></a>Leitura adicional

[Conectar-se a um cluster HDInsight usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md)


### <a name="see-also"></a>Consulte também
[Solucionar problemas usando o Azure HDInsight](hdinsight-troubleshoot-guide.md)




