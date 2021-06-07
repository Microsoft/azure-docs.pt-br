---
title: Compreenda os conceitos de código Apache Spark para Azure Data Lake Analytics desenvolvedores U-SQL.
description: Este artigo descreve Apache Spark conceitos para ajudar os desenvolvedores de U-SQL a entender os conceitos de código do Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: 4faa48a9edb5ea157fde67e4a4f3008864342075
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639762"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Entender o código Apache Spark para desenvolvedores do U-SQL

Esta seção fornece diretrizes de alto nível sobre como transformar scripts U-SQL para Apache Spark.

- Ele começa com uma [comparação dos paradigmas de processamento de dois idiomas](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Fornece dicas sobre como:
   - [Transformar scripts](#transform-u-sql-scripts) , incluindo [expressões de conjunto de linhas](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) do U-SQL
   - [Código do .NET](#transform-net-code)
   - [Tipos de dados](#transform-typed-values)
   - [Objetos de catálogo](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Entenda a linguagem e os paradigmas de processamento do U-SQL e do Spark

Antes de começar a migrar os scripts de U-SQL do Azure Data Lake Analytics para o Spark, é útil entender a linguagem geral e as filosofias de processamento dos dois sistemas.

O U-SQL é uma linguagem de consulta declarativa semelhante a SQL que usa um paradigma de fluxo de dados e permite que você incorpore e escale horizontalmente o código do usuário escrito em .NET (por exemplo, C#), Python e R. As extensões de usuário podem implementar expressões simples ou funções definidas pelo usuário, mas também podem fornecer ao usuário a capacidade de implementar, portanto, chamados de operadores definidos pelo usuário que implementam operadores personalizados para executar transformações de nível de conjunto de linhas, extrações e gravação de saída.

O Spark é uma estrutura de expansão que oferece várias associações de linguagem em escala, Java, Python, .NET, etc., onde você escreve principalmente seu código em uma dessas linguagens, cria abstrações de dados chamadas RDD (conjuntos de dados distribuídos resilientes), dataframes e DataSets e usa uma DSL (linguagem específica de domínio) semelhante à LINQ para transformá-los. Ele também fornece SparkSQL como um subidioma declarativo nas abstrações de dataframe e DataSet. A DSL fornece duas categorias de operações, transformações e ações. Aplicar transformações às abstrações de dados não executará a transformação, mas, em vez disso, criará o plano de execução que será enviado para avaliação com uma ação (por exemplo, gravando o resultado em uma tabela ou arquivo temporário ou imprimindo o resultado).

Assim, ao converter um script U-SQL em um programa Spark, você terá que decidir qual idioma você deseja usar para, pelo menos, gerar a abstração de quadro de dados (que é a abstração de dados usada com mais frequência) e se você deseja gravar as transformações declarativas de Dataflow usando DSL ou SparkSQL. Em alguns casos mais complexos, talvez seja necessário dividir o script U-SQL em uma sequência do Spark e outras etapas implementadas com o lote do Azure ou Azure Functions.

Além disso, o Azure Data Lake Analytics oferece U-SQL em um ambiente de serviço de trabalho sem servidor, enquanto o Azure Databricks e o Azure HDInsight oferecem o Spark na forma de um serviço de cluster. Ao transformar seu aplicativo, você terá que levar em conta as implicações do agora criando, dimensionando, dimensionando e descomissionando os clusters.

## <a name="transform-u-sql-scripts"></a>Transformar scripts U-SQL

Os scripts U-SQL seguem o seguinte padrão de processamento:

1. Os dados são lidos a partir de arquivos não estruturados, usando a `EXTRACT` instrução, um local ou uma especificação de conjunto de arquivos e o extrator definido pelo usuário e o esquema desejado ou de tabelas U-SQL (tabelas gerenciadas ou externas). Ele é representado como um conjunto de linhas.
2. Os conjuntos de linhas são transformados em várias instruções U-SQL que aplicam expressões U-SQL aos conjuntos de linhas e produzem novos conjuntos de linhas.
3. Por fim, os conjuntos de linhas resultantes são enviados para ambos os arquivos usando a `OUTPUT` instrução que especifica os locais e um outgerador interno ou definido pelo usuário, ou em uma tabela U-SQL.

O script é avaliado lentamente, o que significa que cada etapa de extração e transformação é composta em uma árvore de expressão e avaliada globalmente (o fluxo de os).

Os programas do Spark são semelhantes, pois você usaria conectores do Spark para ler os dados e criar os quadros, e depois aplicar as transformações nos quadros de molduras usando o LINQ-like DSL ou SparkSQL e, em seguida, gravar o resultado em arquivos, tabelas do Spark temporárias, alguns tipos de linguagem de programação ou o console.

## <a name="transform-net-code"></a>Transformar código .NET

A linguagem de expressão do U-SQL é C# e oferece várias maneiras de escalar horizontalmente o código .NET personalizado.

Como o Spark atualmente não dá suporte nativo à execução de código .NET, você precisará reescrever suas expressões em uma expressão de Spark, escala, Java ou Python equivalente ou encontrar uma maneira de chamar seu código .NET. Se o seu script usar bibliotecas .NET, você terá as seguintes opções:

- Traduza seu código .NET em escala ou Python.
- Divida seu script U-SQL em várias etapas, em que você usa processos de lote do Azure para aplicar as transformações do .NET (se você puder obter uma escala aceitável)
- Use uma associação de linguagem .NET disponível em código aberto chamado Moebius. Este projeto não está em um estado com suporte.

Em qualquer caso, se você tiver uma grande quantidade de lógica do .NET em seus scripts U-SQL, entre em contato conosco por meio de seu representante de conta da Microsoft para obter mais diretrizes.

Os detalhes a seguir são para os diferentes casos de usos do .NET e do C# em scripts U-SQL.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Transformar expressões em C# do U-SQL em linha escalar

A linguagem de expressão do U-SQL é C#. Muitas das expressões do U-SQL em linha escalar são implementadas nativamente para melhorar o desempenho, enquanto expressões mais complexas podem ser executadas por meio da chamada ao .NET Framework.

O Spark tem sua própria linguagem de expressão escalar (como parte da DSL ou em SparkSQL) e permite chamar funções definidas pelo usuário escritas em seu idioma de hospedagem.

Se você tiver expressões escalares no U-SQL, primeiro você deve encontrar a expressão escalar do Spark mais apropriada compreendida nativamente para obter o máximo de desempenho e, em seguida, mapear as outras expressões para uma função definida pelo usuário do idioma de hospedagem do Spark de sua escolha.

Lembre-se de que .NET e C# têm semânticas de tipo diferentes das linguagens de hospedagem do Spark e a DSL do Spark. Consulte [abaixo](#transform-typed-values) para obter mais detalhes sobre as diferenças do sistema de tipos.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Transformar funções .NET escalares definidas pelo usuário e agregadores definidos pelo usuário

O U-SQL fornece maneiras de chamar funções .NET escalares arbitrárias e chamar agregadores definidos pelo usuário escritos em .NET.

O Spark também oferece suporte para funções definidas pelo usuário e agregadores definidos pelo usuário escritos na maioria de suas linguagens de hospedagem que podem ser chamadas a partir de DSL e SparkSQL do Spark.

### <a name="transform-user-defined-operators-udos"></a>Transformar operadores definidos pelo usuário (UDOs)

O U-SQL fornece várias categorias de operadores definidos pelo usuário (UDOs), como extratores, outmakers, redutores, processadores, aplicadores e combinadores que podem ser escritos em .NET (e-em algumas extensões Python e R).

O Spark não oferece o mesmo modelo de extensibilidade para operadores, mas tem recursos equivalentes para alguns.

O Spark equivalente a extratores e outistores são conectores do Spark. Para muitos extratores U-SQL, você pode encontrar um conector equivalente na Comunidade do Spark. Para outros, você precisará escrever um conector personalizado. Se o extrator U-SQL for complexo e fizer uso de várias bibliotecas .NET, poderá ser preferível criar um conector em escalabilidade que usa a interoperabilidade para chamar a biblioteca .NET que faz o processamento real dos dados. Nesse caso, você precisará implantar o tempo de execução do .NET Core no cluster do Spark e verificar se as bibliotecas .NET referenciadas estão .NET Standard compatíveis com 2,0.

Os outros tipos de UDOs do U-SQL precisarão ser reescritos usando funções e agregadores definidos pelo usuário e a expressão DLS ou SparkSQL do Spark semanticamente adequada. Por exemplo, um processador pode ser mapeado para uma seleção de uma variedade de invocações UDF, empacotada como uma função que usa um dataframe como um argumento e retorna um dataframe.

### <a name="transform-u-sqls-optional-libraries"></a>Transformar bibliotecas opcionais do U-SQL

O U-SQL fornece um conjunto de bibliotecas opcionais e de demonstração que oferecem suporte a [Python](data-lake-analytics-u-sql-python-extensions.md), [R](data-lake-analytics-u-sql-r-extensions.md), [JSON, XML, Avro](https://github.com/Azure/usql/tree/master/Examples/DataFormats)e a alguns [recursos de serviços cognitivas](data-lake-analytics-u-sql-cognitive.md).

O Spark oferece sua própria integração Python e R, pySpark e Sparkr, respectivamente, e fornece conectores para ler e gravar JSON, XML e AVRO.

Se você precisar transformar um script referenciando as bibliotecas de serviços cognitivas, recomendamos entrar em contato conosco por meio de seu representante de conta da Microsoft.

## <a name="transform-typed-values"></a>Transformar valores digitados

Como o sistema de tipos do U-SQL é baseado no sistema de tipos .NET e o Spark tem seu próprio sistema de tipos, que é afetado pela Associação de idioma do host, você precisará certificar-se de que os tipos nos quais está operando estão próximos e para certos tipos, os intervalos de tipo, precisão e/ou escala podem ser um pouco diferentes. Além disso, o U-SQL e o Spark tratam `null` valores de forma diferente.

### <a name="data-types"></a>Tipos de dados

A tabela a seguir fornece os tipos equivalentes em Spark, escala e PySpark para os tipos de U-SQL fornecidos.

| U-SQL | Spark |  Scala | PySpark |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Para obter mais informações, consulte:

- [org. Apache. Spark. Sql. Types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Tipos de SQL e de quadros de datapara Spark](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)
- [Tipos de valor escalares](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark. Sql. Types](https://spark.apache.org/docs/2.3.1/api/python/_modules/pyspark/sql/types.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Tratamento de NULL

No Spark, tipos por padrão permitem valores nulos no U-SQL, você marca explicitamente escalar, não objeto como anulável. Embora o Spark permita que você defina uma coluna como não anulável, ela não impedirá a restrição e [poderá levar a um resultado incorreto](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836).

No Spark, NULL indica que o valor é desconhecido. Um valor nulo do Spark é diferente de qualquer valor, incluindo o próprio. As comparações entre dois valores nulos do Spark ou entre um valor nulo e qualquer outro valor retornam Unknown, pois o valor de cada NULL é desconhecido.  

Esse comportamento é diferente do U-SQL, que segue a semântica do C#, em que `null` é diferente de qualquer valor, mas é igual a si mesmo.  

Portanto, uma `SELECT` instrução SparkSQL que usa `WHERE column_name = NULL` retorna zero linhas mesmo se houver valores nulos no `column_name` , enquanto no U-SQL, ele retornará as linhas em que `column_name` está definido como `null` . Da mesma forma, uma `SELECT` instrução Spark que usa `WHERE column_name != NULL` retorna zero linhas mesmo que haja valores não nulos no `column_name` , enquanto no U-SQL, ele retornaria as linhas que têm não nulo. Portanto, se você quiser a semântica de verificação nula do U-SQL, deverá usar [IsNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) e [IsNotNull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) , respectivamente (ou seus equivalentes de DSL).

## <a name="transform-u-sql-catalog-objects"></a>Transformar objetos do catálogo U-SQL

Uma grande diferença é que os scripts U-SQL podem fazer uso de seus objetos de catálogo, muitos dos quais não têm nenhum equivalente direto do Spark.

O Spark fornece suporte para os conceitos de meta Store do hive, principalmente bancos de dados e tabelas, para que você possa mapear bancos de dados e esquemas do U-SQL para bancos de dados Hive e tabelas U-SQL para tabelas do Spark (consulte [movendo dados armazenados em tabelas u-SQL](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)), mas não tem suporte para exibições, TVFs (funções com valor de tabela), procedimentos armazenados, assemblies u-SQL, fontes de dados externas etc.

Os objetos de código do U-SQL, como modos de exibição, TVFs, procedimentos armazenados e assemblies, podem ser modelados por meio de funções de código e bibliotecas no Spark e referenciados usando os mecanismos de abstração de procedimento e função do idioma host (por exemplo, por meio da importação de módulos Python ou referência a funções escalares).

Se o catálogo do U-SQL tiver sido usado para compartilhar dados e objetos de código entre projetos e equipes, os mecanismos equivalentes para compartilhamento precisarão ser usados (por exemplo, Maven para compartilhar objetos de código).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>Transformar expressões de conjunto de linhas U-SQL e expressões escalares baseadas em SQL

A linguagem principal do U-SQL está transformando conjuntos de linhas e baseia-se no SQL. Veja a seguir uma lista não exaustiva das expressões de conjunto de linhas mais comuns oferecidas no U-SQL:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+ Agregações +`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN`expressões
- `CROSS`/`OUTER``APPLY`expressões
- `PIVOT`/`UNPIVOT` expressões
- `VALUES` Construtor de conjunto de linhas

- Definir expressões `UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Além disso, o U-SQL fornece uma variedade de expressões escalares baseadas em SQL, como

- `OVER` expressões de janela
- uma variedade de agregadores internos e funções de classificação ( `SUM` `FIRST` etc.)
- Algumas das expressões escalares do SQL mais conhecidas: `CASE` , `LIKE` , ( `NOT` ) `IN` , `AND` , `OR` etc.

O Spark oferece expressões equivalentes em seu formato DSL e SparkSQL para a maioria dessas expressões. Algumas das expressões sem suporte nativo no Spark precisarão ser reescritas usando uma combinação das expressões nativas do Spark e padrões semanticamente equivalentes. Por exemplo, `OUTER UNION` terá que ser traduzido para a combinação equivalente de projeções e uniões.

Devido à manipulação diferente de valores nulos, uma junção U-SQL sempre corresponderá a uma linha se ambas as colunas comparadas contiverem um valor nulo, enquanto uma junção no Spark não corresponderá a essas colunas, a menos que sejam adicionadas verificações nulas explícitas.

## <a name="transform-other-u-sql-concepts"></a>Transformar outros conceitos do U-SQL

O U-SQL também oferece uma variedade de outros recursos e conceitos, como consultas federadas em SQL Server bancos de dados, parâmetros, escalares e variáveis de expressão lambda, variáveis de sistema, `OPTION` dicas.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Consultas federadas em bancos de dados SQL Server/tabelas externas

O U-SQL fornece a fonte de dados e as tabelas externas, bem como consultas diretas no Azure SQL Database. Embora o Spark não ofereça as mesmas abstrações de objeto, ele fornece o [conector do Spark para o banco de dados SQL do Azure](../azure-sql/database/spark-connector.md) que pode ser usado para consultar bancos de dados SQL.

### <a name="u-sql-parameters-and-variables"></a>Parâmetros e variáveis do U-SQL

Parâmetros e variáveis de usuário têm conceitos equivalentes no Spark e em seus idiomas de hospedagem.

Por exemplo, em escala, você pode definir uma variável com a `var` palavra-chave:

```
var x = 2 * 3;
println(x)
```

As variáveis de sistema do U-SQL (variáveis que começam com `@@` ) podem ser divididas em duas categorias:

- Variáveis de sistema configuráveis que podem ser definidas para valores específicos para afetar o comportamento dos scripts
- Variáveis de sistema informativas que consultam informações de nível de trabalho e de sistema

A maioria das variáveis de sistema configurável não tem equivalente direto no Spark. Algumas das variáveis do sistema informativo podem ser modeladas passando as informações como argumentos durante a execução do trabalho, outras podem ter uma função equivalente no idioma de hospedagem do Spark.

### <a name="u-sql-hints"></a>Dicas de U-SQL

O U-SQL oferece várias maneiras sintáticas de fornecer dicas para o otimizador de consulta e o mecanismo de execução:  

- Definindo uma variável de sistema U-SQL
- uma `OPTION` cláusula associada à expressão de conjunto de linhas para fornecer uma dica de dados ou de plano
- uma dica de junção na sintaxe da expressão de junção (por exemplo, `BROADCASTLEFT` )

O otimizador de consulta baseado em custo do Spark tem seus próprios recursos para fornecer dicas e ajustar o desempenho da consulta. Consulte a documentação correspondente.

## <a name="next-steps"></a>Próximas etapas

- [Entender os formatos de dados do Spark para desenvolvedores do U-SQL](understand-spark-data-formats.md)
- [.NET para Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Transformar dados usando a atividade do Spark no Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Transformar dados usando a atividade do Hive do Hadoop no Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [O que é o Apache Spark no Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
