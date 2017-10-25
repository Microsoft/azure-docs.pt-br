---
title: "MapReduce e área de trabalho remota com o Hadoop no HDInsight – Azure | Microsoft Docs"
description: Saiba como usar o Remote Desktop para se conectar ao Hadoop no HDInsight e executar trabalhos MapReduce.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: b56674857b013f9bb3d4dd4b6e97b34e0a97b1b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Usar o MapReduce no Hadoop no HDInsight com Remote Desktop
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, você aprenderá como se conectar a um Hadoop no cluster HDInsight usando a Área de Trabalho Remota e executar trabalhos MapReduce usando o comando Hadoop.

> [!IMPORTANT]
> A Área de Trabalho Remota está disponível somente em clusters HDInsight baseados no Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para HDInsight 3.4 ou superior, confira [Usar o MapReduce com SSH](hdinsight-hadoop-use-mapreduce-ssh.md) para obter informações sobre como se conectar ao cluster HDInsight e executar trabalhos do MapReduce.

## <a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster HDInsight baseado em Windows (Hadoop no HDInsight)
* Um computador cliente executando o Windows 7, Windows 8 ou Windows 10

## <a id="connect"></a>Conectar com a área de trabalho remota
Habilite a área de trabalho remota para o cluster HDInsight e conecte-se a ele seguindo as instruções em [Conectar aos clusters HDInsight usando o RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="hadoop"></a>Usar o comando Hadoop
Quando conectado à área de trabalho para o cluster HDInsight, use as etapas a seguir para executar um trabalho MapReduce usando o comando Hadoop:

1. Na área de trabalho do HDInsight, inicie a **Linha de Comando do Hadoop**. Isso abre um novo prompt de comando no diretório **c:\apps\dist\hadoop-&lt;número de versão>**.

   > [!NOTE]
   > O número de versão muda à medida que o Hadoop é atualizado. A variável de ambiente **HADOOP_HOME** pode ser usada para localizar o caminho. Por exemplo, `cd %HADOOP_HOME%` altera os diretórios para o diretório do Hadoop, sem a necessidade de saber o número de versão.
   >
   >
2. Para usar o comando **Hadoop** para executar um trabalho MapReduce de exemplo, use o seguinte comando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar** no diretório atual. Como entrada, ele usa o documento **wasb://example/data/gutenberg/davinci.txt** e a saída é armazenada **wasb:///example/data/WordCountOutput**.

   > [!NOTE]
   > Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte <a href="hdinsight-use-mapreduce.md">Usar o MapReduce no HDInsight Hadoop</a>.
   >
   >
3. O trabalho emite detalhes conforme é processado, retornando informações semelhantes às seguintes quando é concluído:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Depois do trabalho estar concluído, use o seguinte comando para listar os arquivos de saída armazenados em **wasb://example/data/WordCountOutput**:

        hadoop fs -ls wasb:///example/data/WordCountOutput

    Isso deve exibir dois arquivos, **_SUCCESS** e **part-r-00000**. O arquivo **part-r-00000** contém a saída para esse trabalho.

   > [!NOTE]
   > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####** . Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.
   >
   >
5. Para exibir a saída, use o comando a seguir:

        hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

    Isso exibirá uma lista de palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt**, juntamente com o número de vezes que cada palavra ocorreu. A seguir, um exemplo dos dados que estarão contidos no arquivo:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a id="summary"></a>Resumo
Como você pode ver, o comando Hadoop fornece uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e exibir a saída do trabalho.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
