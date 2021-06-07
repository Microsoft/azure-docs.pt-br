---
title: Solucionar problemas do YARN usando o Azure HDInsight
description: Obtenha respostas para perguntas comuns sobre como trabalhar com o Apache Hadoop YARN e o Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 0cd2571276992812327e286ba9b935fcbf6fbbaf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871802"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Solucione problemas de YARN do Apache Hadoop usando o Azure HDInsight

Saiba mais sobre os principais problemas e suas soluções ao trabalhar com cargas de Apache Hadoop YARN no Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Como fazer para criar uma nova fila do YARN em um cluster?

### <a name="resolution-steps"></a>Etapas de resolução

Use as etapas a seguir por meio do Ambari para criar uma nova fila do YARN e equilibrar a alocação de capacidade entre todas as filas.

Neste exemplo, duas filas existentes (**padrão** e **thriftsvr**) são alteradas de 50% da capacidade para 25% da capacidade, o que oferece a nova capacidade de fila de 50% (spark).

| Fila | Capacity | Capacidade máxima |
| --- | --- | --- |
| padrão | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Selecione o ícone **Exibições do Ambari** e então selecione o padrão de grade. Em seguida, selecione **Gerenciador de Filas do YARN**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png" alt-text="Gerenciador de filas do YARN do Apache Ambari" border="false":::
2. Selecione a fila **padrão**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png" alt-text="Apache Ambari YARN selecionar fila padrão" border="false":::
3. Para a fila **padrão** , altere a **capacidade** de 50% para 25%. Para a fila **thriftsvr**, altere a **capacidade** para 25%.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png" alt-text="Alterar a capacidade para 25% para as filas padrão e thriftsvr" border="false":::
4. Para criar uma nova fila, selecione **Adicionar Fila**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png" alt-text="Adicionar fila do painel do Apache Ambari YARN" border="false":::

5. Dê um nome à nova fila.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png" alt-text="Fila de nome do painel do Ambari YARN do Apache" border="false":::  

6. Deixe os valores de **capacidade** em 50% e então selecione o botão **Ações**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png" alt-text="Ação de seleção do Apache Ambari YARN" border="false":::  
7. Escolha **Salvar e Atualizar Filas**.

    :::image type="content" source="media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png" alt-text="Selecionar salvar e atualizar filas" border="false":::  

Essas alterações estarão visíveis imediatamente na interface do usuário do Agendador do YARN.

### <a name="additional-reading"></a>Leituras adicionais

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Como fazer para baixar logs do YARN de um cluster?

### <a name="resolution-steps"></a>Etapas de resolução

1. Conecte-se ao cluster HDInsight com um cliente SSH (Secure Shell). Para saber mais, veja [Leituras adicionais](#additional-reading-2).

1. Para listar todas as IDs de aplicativo dos aplicativos YARN em execução no momento, execute este comando:

    ```apache
    yarn top
    ```

    As IDs são listadas na coluna **APPLICATIONID**. Você pode baixar os logs da coluna **APPLICATIONID**.

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Para baixar os logs de contêineres do YARN para todos os mestres de aplicativo, use este comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    Este comando cria um arquivo de log chamado amlogs.txt.

1. Para baixar os logs do contêiner do YARN somente para o aplicativo mestre mais recente, use este comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    Este comando cria um arquivo de log chamado latestamlogs.txt.

1. Para baixar os logs do contêiner do YARN para os dois primeiros mestres de aplicativo, use este comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    Este comando cria um arquivo de log chamado first2amlogs.txt.

1. Para baixar todos os logs do contêiner do YARN, use este comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    Este comando cria um arquivo de log chamado logs.txt.

1. Para baixar o log do contêiner YARN para um contêiner específico, use o seguinte comando:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    Este comando cria um arquivo de log chamado containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Leituras adicionais

- [Conectar ao HDInsight (Apache Hadoop) usando SSH](./hdinsight-hadoop-linux-use-ssh-unix.md)
- [Apache Hadoop conceitos e aplicativos do YARN](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]