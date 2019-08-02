---
title: Usar um Shell Interativo do Spark no Azure HDInsight
description: Um Shell interativo do Spark fornece um processo de leitura/execução/impressão para executar um comando do Spark de cada vez e ver os resultados.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.openlocfilehash: 7aac2812787a7c14d99377754a4f85e699ef3f09
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441884"
---
# <a name="run-apache-spark-from-the-spark-shell"></a>Execute o Apache Spark no Shell Spark

Interativo [Apache Spark](https://spark.apache.org/) Shell fornece um ambiente de REPL (read-executar-print loop) para executar um comando Spark por vez e ver os resultados. Esse processo é útil para desenvolvimento e depuração. O Spark fornece um shell para cada um dos idiomas com suporte: Scala, Python e R.

## <a name="get-to-an-apache-spark-shell-with-ssh"></a>Obter um Shell do Apache Spark com o SSH

Acesse um Shell do Apache Spark no HDInsight conectando-se ao nó principal primário do cluster usando SSH:

     ssh <sshusername>@<clustername>-ssh.azurehdinsight.net

É possível obter o comando completo do SSH do cluster no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até o painel para acessar o cluster HDInsight Spark.
3. Selecione o SSH (Secure Shell).

    ![Painel do HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-blade.png)

4. Copie o comando SSH exibido e execute-o no seu terminal.

    ![Painel do SSH no HDInsight no portal do Azure](./media/apache-spark-shell/hdinsight-spark-ssh-blade.png)

Para saber mais sobre como usar o SSH para se conectar ao HDInsight, confira [Usar o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-an-apache-spark-shell"></a>Executar um Shell do Apache Spark

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
| --jars JAR_LIST | Lista separada por vírgulas de jars locais a ser incluída em classpaths de driver e executor. No HDInsight, essa lista é composta de caminhos para o sistema de arquivos padrão no Armazenamento do Microsoft Azure ou no Data Lake Storage. | `--jars /path/to/examples.jar` |
| --packages MAVEN_COORDS | Lista separada por vírgulas de coordenadas maven de jars para incluir nos classpaths de driver e executor. Pesquisa o repositório local maven e, em seguida, o maven central e todos os repositórios remotos adicionais especificados com `--repositories`. O formato das coordenadas é *groupId*:*artifactId*:*version*. | `--packages "com.microsoft.azure:azure-eventhubs:0.14.0"`|
| --py-files LIST | Apenas para Python, uma lista separada por vírgulas de arquivos .zip, .egg ou .py a ser colocada no PYTHONPATH. | `--pyfiles "samples.py"` |

## <a name="next-steps"></a>Próximas etapas

- Ver [Introdução ao Apache Spark no Azure HDInsight](apache-spark-overview.md) para uma visão geral.
- Consulte [Criar um cluster Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md) para trabalhar com clusters Spark e SparkSQL.
- Ver [o que é o Streaming estruturado do Apache Spark?](apache-spark-streaming-overview.md) para gravar aplicativos que processam dados de streaming com o Spark.
