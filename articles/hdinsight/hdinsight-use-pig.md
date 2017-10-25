---
title: Usar o Pig do Hadoop no HDInsight | Microsoft Docs
description: Aprenda a usar o Pig com Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: acfeb52b-4b81-4a7d-af77-3e9908407404
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/15/2017
ms.author: larryfr
ms.openlocfilehash: 474f901ffdaf1ed372ace19076ef723b8b10cb9a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-pig-with-hadoop-on-hdinsight"></a>Usar o Pig com Hadoop no HDInsight

Saiba como usar [Apache Pig](http://pig.apache.org/) com o HDInsight...

O Pig é uma plataforma para criar programas para o Hadoop usando uma linguagem de procedimento conhecida como *Pig Latin*. O Pig é uma alternativa ao Java para a criação de soluções de *MapReduce* e está incluído no Azure HDInsight. Use a tabela a seguir para descobrir várias maneiras pelas quais o Pig pode ser usado com o HDInsight:

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](hdinsight-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (por enquanto) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Área de Trabalho Remota](hdinsight-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="why"></a>Por que usar o Pig

Um dos desafios do processamento de dados usando o MapReduce no Hadoop é implementar a lógica de processamento usando somente um mapa e uma função de redução. Em processamentos complexos, é comum você precisar dividir o processamento em várias operações de MapReduce e depois encadeá-las para atingir o resultado desejado.

O Pig permite que você defina o processamento como uma série de transformações pelas quais os dados fluem para produzir a saída desejada.

A linguagem do Pig Latin permite descrever o fluxo de dados a partir de dados brutos, por meio de uma ou mais transformações, para produzir a saída desejada. Os programas em Pig Latin seguem este padrão geral:

* **Carga**: ler dados a serem manipulados no sistema de arquivos

* **Transformação**: manipular os dados

* **Despejo ou armazenamento**: a saída dos dados na tela ou armazenamento para processamento

### <a name="user-defined-functions"></a>Funções definidas pelo usuário

O Pig Latin também oferece suporte a UDF (funções definidas pelo usuário), que permite que você invoque componentes externos que implementam a lógica que é difícil de modelar no Pig Latin.

Para saber mais sobre o Pig Latin, confira o [Manual de referência do Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e o [Manual de referência do Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obter um exemplo de uso de UDF com o Hive, confira os seguinte documentos:

* [DataFu de uso com Pig no HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) -DataFu é uma coleção de útil UDFs mantida pela Apache
* [Use o Python com o Pig e o Hive no HDInsight](hdinsight-python.md)
* [Use o C# com o Hive e com o Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster. O exemplo do Pig neste documento usa o arquivo *log4j* de `/example/data/sample.log`.

Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é ERRO.

> [!NOTE]
> Você também pode gerar um arquivo log4j usando a ferramenta de log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregá-lo para o blob. Consulte [Carregar dados para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para saber mais sobre como o armazenamento de blob do Azure é usado com o HDInsight, confira [Usar armazenamento de blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Exemplo de trabalho

O trabalho do Pig Latin a seguir carrega o arquivo `sample.log` do armazenamento padrão para o cluster HDInsight. Em seguida, ele executa uma série de transformações que resultam na contagem de quantas vezes cada nível de log ocorreu nos dados de entrada. Os resultados são gravados em STDOUT.

    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

A imagem a seguir mostra um resumo do que cada transformação faz aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a id="run"></a>Executar o trabalho do Pig Latin

O HDInsight pode executar trabalhos de Pig Latin usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... com esse **sistema operacional de cluster** | ... deste **cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](hdinsight-hadoop-use-pig-ssh.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |&nbsp; |✔ |Linux ou Windows |Windows (por enquanto) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Área de Trabalho Remota](hdinsight-hadoop-use-pig-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="pig-and-sql-server-integration-services"></a>Pig e SQL Server Integration Services

Também é possível usar o SSIS (SQL Server Integration Services) para executar um trabalho do Pig. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Pig no HDInsight.

* [Tarefa do Pig do Azure HDInsight][pigtask]

* [Gerenciador de Conexões de Assinatura do Azure][connectionmanager]

Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Próximas etapas
Agora que você aprendeu a usar o Pig com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Use o Sqoop com o HDInsight](hdinsight-use-sqoop.md)
* [Usar o Oozie com o HDInsight](hdinsight-use-oozie.md)
* [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx


[hdinsight-upload-data]: hdinsight-upload-data.md

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
