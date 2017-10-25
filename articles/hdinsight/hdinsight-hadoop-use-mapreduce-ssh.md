---
title: "MapReduce e conexão SSH com o Hadoop no HDInsight – Azure | Microsoft Docs"
description: Aprenda a usar o SSH para executar trabalhos MapReduce usando o Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: eaf6278f97cd5ddd7e049ff4745181f39d7949a0
ms.contentlocale: pt-br
ms.lasthandoff: 08/01/2017

---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usar o MapReduce com Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce de uma conexão SSH (Secure Shell) para o HDInsight.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte [Dicas do HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Pré-requisitos

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Um cliente SSH. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a id="ssh"></a>Conexão com o SSH

Conecte-se ao cluster usando o SSH. Por exemplo, o comando exibido a seguir realiza a conexão a um cluster chamado **myhdinsight**:

```bash
ssh admin@myhdinsight-ssh.azurehdinsight.net
```

**Se você usa uma chave de certificado para autenticação de SSH**, talvez seja necessário especificar o local da chave privada no sistema cliente, por exemplo:

```bash
ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net
```

**Se você usa uma senha para autenticação SSH**, você precisa fornecer a senha quando solicitado.

Para saber mais sobre como usar o SSH com HDInsight, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="hadoop"></a>Usar comandos Hadoop

1. Uma vez conectado ao cluster HDInsight, use o comando a seguir para iniciar um trabalho MapReduce:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Esse comando inicia a classe `wordcount`, que está contida no arquivo `hadoop-mapreduce-examples.jar`. Ele usa o documento `/example/data/gutenberg/davinci.txt` como entrada e a saída é armazenada em `/example/data/WordCountOutput`.

    > [!NOTE]
    > Para saber mais sobre esse trabalho MapReduce e os dados de exemplo, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. O trabalho emite detalhes conforme é processado, retornando informações semelhantes ao texto a seguir quando é concluído:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída:

    ```bash
    hdfs dfs -ls /example/data/WordCountOutput
    ```

    Esse comando exibe dois arquivos, `_SUCCESS` e `part-r-00000`. O arquivo `part-r-00000` contém a saída para esse trabalho.

    > [!NOTE]
    > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####** . Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

4. Para exibir a saída, use o comando a seguir:

    ```bash
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```

    Esse comando exibirá uma lista de palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt** e o número de vezes que cada palavra ocorreu. O texto a seguir é um exemplo dos dados contidos no arquivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo

Como você pode ver, os comandos Hadoop fornecem uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e para então exibir a saída do trabalho.

## <a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

