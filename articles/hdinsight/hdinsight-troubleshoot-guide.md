---
title: "Guias de solução de problemas do Azure HDInsight | Microsoft Docs"
description: "Solucionar problemas com cargas de trabalho do Hadoop usando o Azure HDInsight. A documentação passo a passo mostra como usar o HDInsight para resolver problemas comuns com Hive, Spark, YARN, HBase, HDFS e Storm."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.openlocfilehash: f246c6d1355220314a2f56aa91634625ec352c69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Solucionar problemas usando o Azure HDInsight

| Carga de trabalho do Apache | Principais perguntas |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Solucionar problemas do HBase](hdinsight-troubleshoot-hbase.md)|<br>[Como fazer para executar relatórios de comando hbck com várias regiões não atribuídas](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Como fazer para corrigir problemas de tempo limite usando comandos hbck para atribuições de região](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Como fazer para forçar a desabilitação do modo de segurança do HDFS em um cluster](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Como fazer para corrigir problemas de conectividade de JDBC ou SQLLine com o Apache Phoenix](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[O que faz com o servidor mestre falhar ao iniciar](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[O que causa uma falha de reinicialização em um servidor de região](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Solucionar problemas do HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Como fazer para acessar um HDFS local de dentro de um cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Como fazer para forçar a desabilitação do modo de segurança do HDFS em um cluster](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Solucionar problemas do HBase](hdinsight-troubleshoot-hive.md)|<br>[Como fazer para exportar um metastore do Hive e importá-lo para outro cluster](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Como fazer para localizar logs do Hive em um cluster](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Como fazer para inicializar o shell do Hive com configurações específicas em um cluster](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Como fazer para analisar dados de DAG do Tez em um caminho crítico do cluster](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Como fazer para baixar dados de DAG do Tez de um cluster](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Solucionar problemas do Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Como fazer para configurar um aplicativo Spark usando Ambari nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark usando um bloco de anotações do Jupyter nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark usando Livy nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Como fazer para configurar um aplicativo Spark usando spark-sumbit nos clusters](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[O que causa uma exceção OutOfMemoryError de um aplicativo Spark](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Solucionar problemas do Storm](hdinsight-troubleshoot-STORM.md)|<br>[Como fazer para acessar a interface do usuário do Storm em um cluster](hdinsight-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Como fazer para transferir informações de ponto de verificação de spout no hub de eventos Storm de uma topologia para outra](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Como fazer para localizar binários do Storm em um cluster](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Como fazer para determinar a topologia de implantação de um cluster do Storm](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Como fazer para localizar binários de spout do hub de eventos Storm para desenvolvimento](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Como fazer para localizar arquivos de configuração do Storm Log4J nos clusters](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Solucionar problemas do YARN](hdinsight-troubleshoot-YARN.md)|<br>[Como fazer para criar uma nova fila do YARN em um cluster](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Como fazer para baixar logs do YARN de um cluster](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Recursos de solução de problemas do HDInsight

| Para obter informações sobre | Consulte estes artigos |
| --- | --- |
| HDInsight no Linux e otimização | - [Informações sobre o uso do HDInsight no Linux](hdinsight-hadoop-linux-information.md)<br>- [Solução de problemas de desempenho e memória do Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Desempenho de consulta de Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Logs e despejos de memória | - [Acesso aos logs de aplicativo Hadoop YARN no Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Habilitar despejos de heap para serviços Hadoop no Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analisar logs do HDInsight](hdinsight-debug-jobs.md)|
| Errors | - [Entenda e resolva erros do WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Configurações de Hive para corrigir o erro OutofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Ferramentas | - [Usar Exibições do Ambari para depurar trabalhos do Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Otimizar consultas de Hive](hdinsight-hadoop-optimize-hive-query.md) |
