---
title: "O que é o Apache Hive e HiveQL – Azure HDInsight | Microsoft Docs"
description: "O Apache Hive é um sistema de data warehouse para Hadoop. Você pode consultar dados armazenados no Hive usando o HiveQL, que é semelhante ao Transact-SQL. Neste documento, você aprenderá a usar o Hive e HiveQL com o Azure HDInsight."
keywords: "hiveql,o que é o hive,hadoop hiveql,como usar o hive,aprender hive,o que é o hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 90422088897fd5aa10ebd6e76910c74ad5718b07
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?

O [Apache Hive](http://hive.apache.org/) é um sistema de data warehouse para Hadoop. O Hive permite o resumo de dados, consultas e análise de dados. Consultas de hive são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL.

O Hive permite que você projete estrutura em grandes volumes de dados sem estrutura. Depois de definir a estrutura, você pode usar o HiveQL para consultar os dados sem conhecimento de Java ou do MapReduce.

O HDInsight fornece vários tipos de cluster, que são ajustados para cargas de trabalho específicas. Os seguintes tipos de cluster geralmente são usados para consultas de Hive:

* __Consulta Interativa__: um cluster Hadoop que fornece funcionalidade [processamento analítico de baixa latência (LLAP)](https://cwiki.apache.org/confluence/display/Hive/LLAP) para melhorar os tempos de resposta de consultas interativas. Para obter mais informações, confira o documento [Introdução à Consulta Interativa no HDInsight](hdinsight-hadoop-use-interactive-hive.md).

* __Hadoop__: um cluster Hadoop que está ajustado para cargas de trabalho de processamento em lotes. Para obter mais informações, confira o documento [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

* __Spark__: o Apache Spark tem funcionalidade interna para trabalhar com o Hive. Para obter mais informações, confira o documento [Introdução ao Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

* __HBase__: o HiveQL pode ser usado para consultar dados armazenados no HBase. Para obter mais informações, confira o documento [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md).

## <a name="how-to-use-hive"></a>Como usar o Hive

Use a tabela a seguir para aprender a usar o Hive com HDInsight:

| **Use esse método** se você quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Exibição de Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualquer um (baseado em navegador) |
| [Cliente de Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows* |Linux, Unix, Mac OS X ou Windows |
| [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows* |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows* |Windows |

> [!IMPORTANT]
> \*O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Se você estiver usando um cluster do HDInsight baseado em Windows, você pode usar o [console de consulta](hdinsight-hadoop-use-hive-query-console.md) do seu navegador ou a [área de trabalho remota](hdinsight-hadoop-use-hive-remote-desktop.md) para executar consultas de Hive.

## <a name="hiveql-language-reference"></a>Referência da linguagem HiveQL

A referência da linguagem HiveQL está disponível no [manual de linguagem (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive e estrutura de dados

O Hive sabe como trabalhar com dados estruturados e semi-estruturados. Por exemplo, arquivos de texto onde os campos são delimitados por caracteres específicos. A seguinte instrução do HiveQL cria uma tabela com dados delimitados por espaços:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

O Hive também dá suporte a **serializador/desserializadores (SerDe)** personalizados para dados complexos ou com estrutura irregular. Para saber mais, consulte o documento [Como usar SerDe JSON personalizado com o HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Para obter mais informações sobre formatos de arquivo suportados pelo Hive, confira o [manual de linguagem (https://cwiki.apache.org/confluence/display/Hive/LanguageManual)](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

## <a name="hive-internal-tables-vs-external-tables"></a>Tabelas internas Hive vs. tabelas externas

Há dois tipos de tabelas que você pode criar com o Hive:

* __Interna__: os dados são armazenados no data warehouse do Hive. O data warehouse está localizado no `/hive/warehouse/` no armazenamento padrão para o cluster.

    Use tabelas internas quando:

    * Os dados são temporários.
    * Você quer que o Hive gerencie o ciclo de vida da tabela e dos dados.

* __Externa__: os dados são armazenados fora do data warehouse. Os dados podem ser armazenados em qualquer armazenamento acessível pelo cluster.

    Use tabelas externas quando:

    * Os dados também são usados fora do Hive. Por exemplo, os arquivos de dados são atualizados por outro processo (que não bloqueia os arquivos.)
    * Os dados devem permanecer no local anterior, mesmo depois de descartar a tabela.
    * Você precisa de um local personalizado, como uma conta de armazenamento não padrão.
    * Um programa que não seja o hive gerencia o formato de dados, local etc.

Para obter mais informações, confira a publicação do blog [Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

## <a name="user-defined-functions-udf"></a>UDF (Funções definidas pelo usuário)

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL. Para obter um exemplo de uso de UDFs com o Hive, confira os seguinte documentos:

* [Como usar uma função definida pelo usuário do Java com o Hive](hdinsight-hadoop-hive-java-udf.md)

* [Como usar uma função definida pelo usuário do Python com Hive e Pig](hdinsight-python.md)

* [Como usar uma função definidas pelo usuário do C# com Hive e Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar uma função definida pelo usuário personalizado de Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Um exemplo de função definida pelo usuário do Hive para converter formatos de data/hora para carimbo de data/hora do Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a id="data"></a>Dados de exemplo

O Hive no HDInsight vem pré-carregado com uma tabela interna chamada `hivesampletable`. O HDInsight também fornece conjuntos de dados de exemplo que podem ser usados com o Hive. Esses conjuntos de dados são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster.

## <a id="job"></a>Exemplo de consulta do Hive

As seguintes instruções HiveQL projetam colunas para o arquivo `/example/data/sample.log`:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as instruções HiveQL executam as seguintes ações:

* `set hive.execution.engine=tez;`: Define o mecanismo de execução para usar o Tez. Usar o Tez no lugar do MapReduce pode fornecer um aumento no desempenho da consulta. Para saber mais sobre o Tez, consulte a seção [Usar o Apache Tez para desempenho aprimorado](#usetez) .

    > [!NOTE]
    > Essa instrução só é necessária ao usar um cluster HDInsight baseado em Windows. O Tez é o mecanismo padrão de execução para HDInsight baseados em Linux.

* `DROP TABLE`: se a tabela já existir, exclua-a.

* `CREATE EXTERNAL TABLE`: cria uma nova tabela **externa** no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local e no formato originais.

* `ROW FORMAT`: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

* `STORED AS TEXTFILE LOCATION`: informa ao Hive o local em que os dados são armazenados (o diretório `example/data`) e se estão armazenados como texto. Os dados podem estar em um arquivo ou distribuídos em vários arquivos dentro do diretório.

* `SELECT`: seleciona uma contagem de todas as linhas nas quais a coluna **t4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, já que há três linhas que contêm esse valor.

* `INPUT__FILE__NAME LIKE '%.log'`- O Hive tenta aplicar o esquema a todos os arquivos no diretório. Nesse caso, o diretório contém arquivos que não correspondem ao esquema. Para evitar dados incorretos nos resultados, essa instrução informa ao Hive que devemos retornar apenas dados de arquivos que terminam em .log.

> [!NOTE]
> As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
>
> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

Para criar uma tabela **interna** em vez de externa, use o HiveQL a seguir:

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

As instruções executam as seguintes ações:

* `CREATE TABLE IF NOT EXISTS`: se a tabela não existir, crie uma. Uma vez que a palavra-chave **EXTERNA** não é usada, essa instrução cria uma tabela interna. Uma tabela interna é armazenada no data warehouse do Hive e é totalmente gerenciada pelo Hive.

* `STORED AS ORC`: armazena os dados no formato Colunar de linha otimizado (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

* `INSERT OVERWRITE ... SELECT`: seleciona as linhas na tabela **log4jLogs** que contém **[ERROR]** e insere os dados na tabela **errorLogs**.

> [!NOTE]
> Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

## <a name="improve-hive-query-performance"></a>Como melhorar o desempenho de consulta de Hive

### <a id="usetez"></a>Apache Tez

[Apache Tez](http://tez.apache.org) é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. O Tez está habilitado por padrão para clusters HDInsight baseados em Linux.

> [!NOTE]
> Atualmente o Tez está desativado por padrão para clusters baseados no Windows HDInsight e deve ser habilitado. Para aproveitar o Tez, o seguinte valor deve ser definido para uma consulta Hive:
>
> `set hive.execution.engine=tez;`
>
> O Tez é o mecanismo padrão para clusters HDInsight baseados em Linux.

Os [documentos de design do Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contêm vários detalhes das escolhas de implantação e configurações de ajuste.

Para ajudar na depuração de trabalhos executados usando Tez, o HDInsight fornece as seguintes interfaces de usuário na Web que permitem a exibição de detalhes de trabalhos do Tez:

* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](hdinsight-debug-ambari-tez-view.md)

* [Usar a interface de usuário do Tez no HDInsight baseado em Windows](hdinsight-debug-tez-ui.md)

### <a name="low-latency-analytical-processing-llap"></a>Processamento analítico de baixa latência (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (também conhecido como vida longa e processo) é um novo recurso no Hive 2.0 que permite armazenar as consultas em cache na memória. O LLAP acelera as consultas de Hive em até [26 vezes mais rápido do que o Hive 1.x em alguns casos](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

O HDInsight fornece LLAP no tipo de cluster da Consulta Interativa. Para obter mais informações, confira o documento [Introdução à Consulta Interativa](hdinsight-hadoop-use-interactive-hive.md).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Trabalhos do Hive e SQL Server Integration Services

Também é possível usar o SSIS (SQL Server Integration Services) para executar um trabalho do Hive. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Hive no HDInsight.

* [Tarefa do Hive do Azure HDInsight][hivetask]

* [Gerenciador de Conexões de Assinatura do Azure][connectionmanager]

Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos do MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

