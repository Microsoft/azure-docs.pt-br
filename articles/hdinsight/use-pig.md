---
title: Usar o Apache Pig
titleSuffix: Azure HDInsight
description: Aprenda a usar o Pig com o Apache Hadoop no HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 7b74a41f7d6b636dddce0388d5ee0e0a12658d52
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944615"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Use o Apache Pig com o Apache Hadoop no HDInsight

Saiba como usar [Apache Pig](https://pig.apache.org/) com o HDInsight.

O Apache Pig é uma plataforma para criar programas para o Apache Hadoop usando uma linguagem procedural conhecida como *Pig Latin*. O Pig é uma alternativa ao Java para a criação de soluções de *MapReduce* e está incluído no Azure HDInsight. Use a tabela a seguir para descobrir várias maneiras pelas quais o Pig pode ser usado com o HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Por que usar o Apache Pig

Um dos desafios do processamento de dados usando o MapReduce no Hadoop é implementar a lógica de processamento usando somente um mapa e uma função de redução. Em processamentos complexos, é comum você precisar dividir o processamento em várias operações de MapReduce e depois encadeá-las para atingir o resultado desejado.

O Pig permite que você defina o processamento como uma série de transformações pelas quais os dados fluem para produzir a saída desejada.

A linguagem do Pig Latin permite descrever o fluxo de dados a partir de dados brutos, por meio de uma ou mais transformações, para produzir a saída desejada. Os programas em Pig Latin seguem este padrão geral:

* **Carregar**: ler dados a serem manipulados do sistema de arquivos.

* **Transformação**: manipule os dados.

* **Despejar ou armazenar**: dados de saída na tela ou armazená-los para processamento.

### <a name="user-defined-functions"></a>Funções definidas pelo usuário

O Pig Latin também oferece suporte a UDF (funções definidas pelo usuário), que permite que você invoque componentes externos que implementam a lógica que é difícil de modelar no Pig Latin.

Para saber mais sobre o Pig Latin, confira o [Manual de referência do Pig Latin 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) e o [Manual de referência do Pig Latin 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html).

## <a name="example-data"></a><a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster. O exemplo do Pig neste documento usa o arquivo *log4j* de `/example/data/sample.log`.

Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade, por exemplo:

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

No exemplo anterior, o nível de log é ERRO.

> [!NOTE]  
> Você também pode gerar um arquivo log4j usando a ferramenta de log [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) e carregá-lo para o blob. Consulte [Carregar dados para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para saber mais sobre como o armazenamento de blob do Azure é usado com o HDInsight, confira [Usar armazenamento de blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a name="example-job"></a><a id="job"></a>Exemplo de trabalho

O trabalho do Pig Latin a seguir carrega o arquivo `sample.log` do armazenamento padrão para o cluster HDInsight. Em seguida, ele executa uma série de transformações que resultam na contagem de quantas vezes cada nível de log ocorreu nos dados de entrada. Os resultados são gravados em STDOUT.

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

A imagem a seguir mostra um resumo do que cada transformação faz aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Executar o trabalho do Pig Latin

O HDInsight pode executar trabalhos de Pig Latin usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

## <a name="pig-and-sql-server-integration-services"></a>Pig e SQL Server Integration Services

Também é possível usar o SSIS (SQL Server Integration Services) para executar um trabalho do Pig. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Pig no HDInsight.

* [Tarefa de Pig de HDInsight do Azure][pigtask]

* [Gerenciador de Conexões da Assinatura do Azure][connectionmanager]

Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].

## <a name="next-steps"></a><a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar o Pig com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight](hdinsight-upload-data.md)
* [Usar o Apache Hive com o HDInsight](./hadoop/hdinsight-use-hive.md)
* [Usar o Apache Sqoop com HDInsight](./hadoop/hdinsight-use-sqoop.md)
* [Usar trabalhos do MapReduce com o HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: /sql/integration-services/control-flow/azure-hdinsight-pig-task?viewFallbackFrom=sql-server-2014
[connectionmanager]: /sql/integration-services/connection-manager/azure-subscription-connection-manager?viewFallbackFrom=sql-server-2014
[ssispack]: /sql/integration-services/azure-feature-pack-for-integration-services-ssis?viewFallbackFrom=sql-server-2014
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif