---
title: "Desenvolver trabalhos MapReduce de streaming do Python com o HDInsight – Azure | Microsoft Docs"
description: Saiba como usar o Python no streaming para trabalhos de MapReduce. O Hadoop fornece uma API de streaming para MapReduce para escrever em linguagens diferentes do Java.
services: hdinsight
keyword: mapreduce python,python map reduce,python mapreduce
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.openlocfilehash: b86605c49291a99f49c4b2841d46324cfd0db56d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="develop-python-streaming-mapreduce-programs-for-hdinsight"></a>Desenvolver programas MapReduce de streaming do Python para o HDInsight

Saiba como usar o Python no streaming para operações do MapReduce. O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapeamento e redução em outras linguagens além do Java. As etapas neste documento implementam os componentes Map e Reduce em Python.

## <a name="prerequisites"></a>Pré-requisitos

* Um Hadoop baseado em Linux no cluster HDInsight

  > [!IMPORTANT]
  > As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Um editor de texto

  > [!IMPORTANT]
  > O editor de texto deve usar LF com fim de linha. Usar CRLF como terminação de linha causará erros ao executar o trabalho MapReduce em clusters HDInsight baseados em Linux.

* Os comandos `ssh` e `scp` ou o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-3.8.0)

## <a name="word-count"></a>Contagem de palavras

Este exemplo é uma contagem básica de palavras implementada em um Python usando um mapeador e um redutor. O mapeador divide frases em palavras individuais e o redutor agrega as palavras e contagens para produzir a saída.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduz as fases.

![ilustração do processo do mapreduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="streaming-mapreduce"></a>Transmissão do MapReduce

O Hadoop permite que você especifique um arquivo que contém o mapa e a lógica de redução usada por um trabalho. Os requisitos específicos para o mapa e lógica de redução são:

* **Entrada**- os componentes mapa e reduzir devem ler dados de entrada de STDIN.
* **Saída**- os componentes mapa e reduzir devem gravar os dados de saída para STDOUT.
* **Formato de dados**- os dados consumidos e produzidos devem ser um par chave/valor, separado por um caractere de tabulação.

O Python pode facilmente atender a esses requisitos usando o módulo `sys` para ler do STDIN e usando `print` para imprimir para o STDOUT. O restante é simplesmente formatar os dados com um caractere de tabulação (`\t`) entre a chave e o valor.

## <a name="create-the-mapper-and-reducer"></a>Criar o mapeador e redutor

1. Crie um arquivo chamado `mapper.py` e use o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # Use the sys module
   import sys

   # 'file' in this case is STDIN
   def read_input(file):
       # Split each line into words
       for line in file:
           yield line.split()

   def main(separator='\t'):
       # Read the data using read_input
       data = read_input(sys.stdin)
       # Process each word returned from read_input
       for words in data:
           # Process each word
           for word in words:
               # Write to STDOUT
               print '%s%s%d' % (word, separator, 1)

   if __name__ == "__main__":
       main()
   ```

2. Crie um arquivo chamado **reducer.py** e use o seguinte código como o conteúdo:

   ```python
   #!/usr/bin/env python

   # import modules
   from itertools import groupby
   from operator import itemgetter
   import sys

   # 'file' in this case is STDIN
   def read_mapper_output(file, separator='\t'):
       # Go through each line
       for line in file:
           # Strip out the separator character
           yield line.rstrip().split(separator, 1)

   def main(separator='\t'):
       # Read the data using read_mapper_output
       data = read_mapper_output(sys.stdin, separator=separator)
       # Group words and counts into 'group'
       #   Since MapReduce is a distributed process, each word
       #   may have multiple counts. 'group' will have all counts
       #   which can be retrieved using the word as the key.
       for current_word, group in groupby(data, itemgetter(0)):
           try:
               # For each word, pull the count(s) for the word
               #   from 'group' and create a total count
               total_count = sum(int(count) for current_word, count in group)
               # Write to stdout
               print "%s%s%d" % (current_word, separator, total_count)
           except ValueError:
               # Count was not a number, so do nothing
               pass

   if __name__ == "__main__":
       main()
   ```

## <a name="run-using-powershell"></a>Executar usando PowerShell

Para assegurar que os arquivos tenham as terminações de linha corretas, use o seguinte script do PowerShell:

[!code-powershell[main](../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=138-140)]

Use o seguinte script do PowerShell para carregar os arquivos, execute o trabalho e exiba a saída:

[!code-powershell[main](../../powershell_scripts/hdinsight/streaming-python/streaming-python.ps1?range=5-134)]

## <a name="run-from-an-ssh-session"></a>Executar de uma sessão SSH

1. No ambiente de desenvolvimento, no mesmo diretório dos arquivos `mapper.py` e `reducer.py`, use o comando a seguir:

    ```bash
    scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:
    ```

    Substitua `username` pelo nome de usuário do SSH do cluster e substitua `clustername` pelo nome do cluster.

    Esse comando copia os arquivos do sistema local para o nó principal.

    > [!NOTE]
    > Se você tiver usado uma senha para proteger sua conta SSH, será solicitado que você forneça essa senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo: `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

2. Conecte-se ao cluster usando o SSH:

    ```bash
    ssh username@clustername-ssh.azurehdinsight.net`
    ```

    Para obter mais informações a respeito, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Para assegurar que mapper.py e reducer.py têm as terminações de linha corretas, use os seguintes comandos:

    ```bash
    perl -pi -e 's/\r\n/\n/g' mapper.py
    perl -pi -e 's/\r\n/\n/g' reducer.py
    ```

4. Use o seguinte comando para iniciar o trabalho MapReduce.

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
    ```

    Esse comando tem as seguintes partes:

   * **hadoop-streaming.jar**: usado ao executar operações de transmissão do MapReduce. Faz interfaces do Hadoop com o código externo do MapReduce fornecido por você.

   * **-files**: adiciona os arquivos especificados ao trabalho MapReduce.

   * **-mapper**: informa o Hadoop qual arquivo usar como mapeador.

   * **-reducer**: informa o Hadoop qual arquivo usar como redutor.

   * **-input**: o arquivo de entrada por meio do qual devemos contar palavras.

   * **-output**: o diretório no qual a saída é gravada.

    Conforme o trabalho MapReduce é realizado, o processo é exibido como percentuais.

        15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%


5. Para exibir a saída, use o comando a seguir:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Esse comando exibe uma lista de palavras e o número de ocorrências da palavra.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
