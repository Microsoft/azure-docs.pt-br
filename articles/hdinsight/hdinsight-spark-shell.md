---
title: Usar um Shell Interativo do Spark no Azure HDInsight | Microsoft Docs
description: "Um Shell interativo do Spark fornece um processo de leitura/execução/impressão para executar um comando do Spark de cada vez e ver os resultados."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: maxluk
ms.openlocfilehash: 48c6a24f6c820eaf09da6fa3a0a496ddbef1e7a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-spark-from-the-spark-shell"></a>Execute o Spark no Shell do Spark

Um Shell interativo do Spark fornece um ambiente REPL (loop de leitura/execução/impressão) para executar um comando do Spark de cada vez e ver os resultados. Esse processo é útil para desenvolvimento e depuração. O Spark fornece um shell para cada um dos idiomas com suporte: Scala, Python e R.

## <a name="get-to-a-spark-shell-with-ssh"></a>Acessar um shell do Spark com o SSH

Acesse um shell do Spark no HDInsight ao se conectar ao nó principal do cluster usando o SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

É possível obter o comando completo do SSH do cluster no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até o painel para acessar o cluster HDInsight Spark.
3. Selecione o SSH (Secure Shell).

    ![Painel do HDInsight no portal do Azure](./media/hdinsight-spark-shell/hdinsight-spark-blade.png)

4. Copie o comando SSH exibido e execute-o no seu terminal.

    ![Painel do SSH no HDInsight no portal do Azure](./media/hdinsight-spark-shell/hdinsight-spark-ssh-blade.png)

Para saber mais sobre como usar o SSH para se conectar ao HDInsight, confira [Usar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-a-spark-shell"></a>Executar um shell do Spark

O Spark oferece shells para as linguagens Scala (spark-shell), Python (pyspark) e R (sparkR). Na sessão do SSH no nó principal do cluster HDInsight, insira um dos comandos a seguir:

    ./bin/spark-shell
    ./bin/pyspark
    ./bin/sparkR

Agora, é possível inserir comandos do Spark na linguagem correta.

## <a name="sparksession-and-sparkcontext-instances"></a>Instâncias SparkSession e SparkContext

Por padrão, ao executar o shell do Spark, as instâncias SparkSession e SparkContext são automaticamente instanciadas.

Para acessar a instância de SparkSession, digite `spark`. Para acessar a instância de SparkContext, digite `sc`.

## <a name="important-shell-parameters"></a>Parâmetros de shell importantes

O comando shell do Spark (`spark-shell`, `pyspark` ou `sparkR`) dá suporte a vários parâmetros de linha de comando. Para ver uma lista completa de parâmetros, inicie o shell do Spark com a opção `--help`. Observe que alguns desses parâmetros podem se aplicar somente a `spark-submit`, que encapsula o shell do Spark.

| switch | description | exemplo |
| --- | --- | --- |
| --master MASTER_URL | Especifica a URL mestre. No HDInsight, esse valor é sempre `yarn`. | `--master yarn`|
| --jars JAR_LIST | Lista separada por vírgulas de jars locais a ser incluída em classpaths de driver e executor. No HDInsight, essa lista é composta de caminhos para o sistema de arquivos padrão no Armazenamento do Microsoft Azure ou no Data Lake Store. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Lista separada por vírgulas de coordenadas maven de jars para incluir nos classpaths de driver e executor. Pesquisa o repositório local maven e, em seguida, o maven central e todos os repositórios remotos adicionais especificados com `--repositories`. O formato das coordenadas é *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Apenas para Python, uma lista separada por vírgulas de arquivos .zip, .egg ou .py a ser colocada no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Próximas etapas

- Consulte [Introdução ao Spark no Azure HDInsight](hdinsight-apache-spark-overview.md) para obter uma visão geral.
- Consulte [Criar um cluster Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Consulte [O que é o Streaming Estruturado do Spark?](hdinsight-spark-streaming-overview.md) para gravar aplicativos que processam dados de streaming com o Spark.

