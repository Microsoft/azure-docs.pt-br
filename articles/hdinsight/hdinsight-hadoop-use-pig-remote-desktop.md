---
title: "Usar o Pig do Hadoop com área de trabalho remota no HDInsight – Azure | Microsoft Docs"
description: "Aprenda a usar o comando Pig para executar instruções Pig Latin por meio de uma conexão de área de trabalho remota a um cluster Hadoop em HDInsight baseado em Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 5e8d4fbd8afc54c8bbc1a9a71c66d7022a7d5986
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Executar trabalhos do Pig por meio de uma conexão de área de trabalho remota
[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento fornece uma explicação passo a passo para usar o comando Pig para executar instruções Pig Latin por meio de uma conexão de área de trabalho remota a um cluster HDInsight baseado em Windows. O Pig Latin permite que você crie aplicativos MapReduce descrevendo as transformações de dados, em vez das funções mapear e reduzir.

> [!IMPORTANT]
> A Área de Trabalho Remota está disponível somente em clusters HDInsight que usam o Windows como o sistema operacional. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> No caso do HDInsight 3.4 ou superior, confira [Usar Pig com o HDInsight e SSH](hdinsight-hadoop-use-pig-ssh.md) para obter informações sobre como executar interativamente trabalhos do Pig diretamente no cluster de uma linha de comando.

## <a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster HDInsight baseado em Windows (Hadoop no HDInsight)
* Um computador cliente executando o Windows 7, Windows 8 ou Windows 10

## <a id="connect"></a>Conectar com a área de trabalho remota
Habilite a área de trabalho remota para o cluster HDInsight e conecte-se a ele seguindo as instruções em [Conectar aos clusters HDInsight usando o RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Usar o comando Pig
1. Após ter estabelecido uma conexão de área de trabalho remota, inicie a **Linha de Comando do Hadoop** usando o ícone correspondente na área de trabalho.
2. Use o seguinte para iniciar o comando do Pig:

        %pig_home%\bin\pig

    Você verá um prompt `grunt>` .
3. Insira a instrução a seguir:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Esse comando carrega o conteúdo do arquivo sample.log no arquivo LOGS. Você pode exibir o conteúdo do arquivo usando o seguinte comando:

        DUMP LOGS;
4. Transforme os dados aplicando uma expressão regular para extrair apenas o nível de registro em log de cada registro:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Você pode usar **DUMP** para exibir os dados após a transformação. Nesse caso, `DUMP LEVELS;`.
5. Continue a aplicar transformações usando as instruções a seguir. Use `DUMP` para exibir o resultado da transformação após cada etapa.

    <table>
    <tr>
    <th>Instrução</th><th>O que faz</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Remove as linhas que contêm um valor nulo para o nível de log e armazena os resultados em FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Agrupa as linhas pelo nível de log e armazena os resultados em GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Cria um novo conjunto de dados que contém cada valor de nível de log exclusivo e quantas vezes ele ocorre. Isso é armazenado em FREQUENCIES</td>
    </tr>
    <tr>
    <td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordena os níveis de log por contagem (decrescente) e armazena em RESULT</td>
    </tr>
    </table>
6. Você também pode salvar os resultados de uma transformação usando a instrução `STORE`. Por exemplo, o comando a seguir salva o `RESULT` no diretório **/example/data/pigout** , no contêiner de armazenamento padrão para seu cluster:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Os dados são armazenados no diretório especificado nos arquivos chamados **part-nnnnn**. Se o diretório já existir, você receberá uma mensagem de erro.
   >
   >
7. Para sair do prompt do assistente, insira a instrução a seguir.

        QUIT;

### <a name="pig-latin-batch-files"></a>Arquivos de lote do Pig Latin
Você também pode usar o comando Pig para executar o Pig Latin contido em um arquivo.

1. Depois de sair do prompt do grunt, abra o **Bloco de Notas** e crie um novo arquivo chamado **pigbatch.pig** no diretório **%PIG_HOME%**.
2. Digite ou cole as seguintes linhas no arquivo **pigbatch.pig** e salve-o quando terminar:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Use o demonstrado a seguir para executar o arquivo **pigbatch.pig** utilizando o comando pig.

        pig %PIG_HOME%\pigbatch.pig

    Quando o trabalho em lotes for concluído, você verá a seguinte saída, que deverá ser igual àquela de quando você usou `DUMP RESULT;` nas etapas anteriores:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Resumo
Como você pode ver, o comando Pig permite executar operações do MapReduce, ou usar trabalhos do Pig Latin armazenados em arquivos em lotes.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre o Pig no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
