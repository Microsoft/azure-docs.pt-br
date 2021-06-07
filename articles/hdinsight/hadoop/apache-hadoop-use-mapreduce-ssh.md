---
title: MapReduce e conexão SSH com Apache Hadoop-Azure HDInsight
description: Aprenda a usar o SSH para executar tarefas MapReduce usando o Apache Hadoop no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/10/2020
ms.openlocfilehash: 991b91d1feee185d17bbf01266e0392f347e1a66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939637"
---
# <a name="use-mapreduce-with-apache-hadoop-on-hdinsight-with-ssh"></a>Use o MapReduce com o Apache Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce de uma conexão SSH (Secure Shell) para o HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Apache Hadoop baseados em Linux, mas for novo no HDInsight, consulte [dicas do HDInsight baseadas em Linux](../hdinsight-hadoop-linux-information.md).

## <a name="prerequisites"></a>Pré-requisitos

Um cluster do Apache Hadoop no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="use-hadoop-commands"></a>Usar comandos Hadoop

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Uma vez conectado ao cluster HDInsight, use o comando a seguir para iniciar um trabalho MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Esse comando inicia a classe `wordcount`, que está contida no arquivo `hadoop-mapreduce-examples.jar`. Ele usa o documento `/example/data/gutenberg/davinci.txt` como entrada e a saída é armazenada em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para saber mais sobre esse trabalho MapReduce e os dados de exemplo, confira [Usar o MapReduce no Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

    O trabalho emite detalhes conforme é processado, retornando informações semelhantes ao texto a seguir quando é concluído:

    ```output
    File Input Format Counters
    Bytes Read=1395666
    File Output Format Counters
    Bytes Written=337623
    ```

1. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Esse comando exibe dois arquivos, `_SUCCESS` e `part-r-00000`. O arquivo `part-r-00000` contém a saída para esse trabalho.

    > [!NOTE]  
    > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####** . Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

1. Para exibir a saída, use o comando a seguir:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Esse comando exibirá uma lista de palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt** e o número de vezes que cada palavra ocorreu. O texto a seguir é um exemplo dos dados contidos no arquivo:

    ```output
    wreathed        3
    wreathing       1
    wreaths         1
    wrecked         3
    wrenching       1
    wretched        6
    wriggling       1
    ```

## <a name="next-steps"></a>Próximas etapas

Como você pode ver, os comandos Hadoop fornecem uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e para então exibir a saída do trabalho. Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)
* [Usar Apache Hive com Apache Hadoop no HDInsight](hdinsight-use-hive.md)
