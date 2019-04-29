---
title: Corrigir um erro Hive sem memória no Azure HDInsight
description: Corrija um erro Hive sem memória no HDInsight. O cenário de cliente é uma consulta em várias tabelas grandes.
keywords: erro de memória insuficiente, OOM, configurações do Hive
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2e7328b95aecc8e644d7b9e2ec407a62551fff79
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764196"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Consertar um erro de memória insuficiente do Apache Hive no Azure HDInsight

Saiba como consertar um erro OOM (memória insuficiente) do Apache Hive sem ao processar tabelas grandes definindo as configurações de memória do Hive.

## <a name="run-apache-hive-query-against-large-tables"></a>Executar a consulta do Apache Hive em tabelas grandes

Um cliente executou uma consulta do Hive:

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Algumas nuances desta consulta:

* T1 é um alias para uma grande tabela, TABLE1, com vários tipos de coluna STRING.
* Outras tabelas não são tão grandes, mas têm muitas colunas.
* Todas as tabelas estão associadas umas às outras, em alguns casos com várias colunas em TABLE 1 e em outras.

A consulta do Hive demorou 26 minutos para ser concluída em um cluster HDInsight A3 de 24 nós. O cliente percebeu as seguintes mensagens de aviso:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Usando o mecanismo de execução Apache Tez. A mesma consulta foi executada por 15 minutos e depois lançou o seguinte erro:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

O erro permanece ao usar uma máquina virtual maior (por exemplo, D12).


## <a name="debug-the-out-of-memory-error"></a>Depurar o erro de memória insuficiente

Nosso suporte e as equipes de engenharia juntos descobriram que um dos problemas que causou o erro de memória insuficiente era um [problema conhecido descrito no Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesn't take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

O **hive.auto.convert.join.noconditionaltask** no arquivo hive-site.xml estava definido como **true**:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

É provável que junção de mapa tenha sido a causa do erro de falta de memória do Java Heap Space. Conforme explicado na postagem no blog [Configurações de memória Yarn do Hadoop no HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), quando o mecanismo de execução Tez é usado, o espaço heap usado realmente pertence ao contêiner Tez. Confira a imagem a seguir que descreve a memória do contêiner Tez.

![Diagrama de memória do contêiner Tez: Erro de memória insuficiente do Hive](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Como sugere a postagem no blog, as duas configurações de memória a seguir definem a memória de contêiner para o heap: **hive.tez.container.size** e **hive.tez.java.opts**. Em nossa experiência, a exceção de falta de memória não significa que o tamanho do contêiner seja muito pequeno. Isso significa que o tamanho do heap de Java (hive.tez.java.opts) é muito pequeno. Portanto, sempre que você vir falta de memória, poderá tentar aumentar **hive.tez.java.opts**. Se necessário, pode ser que você precise aumentar **hive.tez.container.size**. A configuração **java.opts** deve ser aproximadamente 80% do **container.size**.

> [!NOTE]  
> A configuração **hive.tez.java.opts** deve ser menor que **hive.tez.container.size**.
> 
> 

Como uma máquina D12 tem 28 GB de memória, decidimos usar um tamanho de contêiner de 10 GB (10240 MB) e atribuir 80% para java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Com as novas configurações, a consulta foi executada com êxito em menos de dez minutos.

## <a name="next-steps"></a>Próximos passos

O recebimento de um erro de memória insuficiente não significa necessariamente que o tamanho do contêiner é muito pequeno. Em vez disso, você deve definir as configurações de memória para que o tamanho do heap seja aumentado para pelo menos 80% do tamanho da memória do contêiner. Para otimizar consultas do Hive, veja [Otimizar consultas do Apache Hive para Apache Hadoop no HDInsight](hdinsight-hadoop-optimize-hive-query.md).
