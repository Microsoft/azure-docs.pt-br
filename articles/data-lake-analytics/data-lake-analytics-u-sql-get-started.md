---
title: Introdução à linguagem U-SQL no Azure Data Lake Analytics
description: Conheça os fundamentos da linguagem U-SQL no Azure Data Lake Analytics.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 2a138801ba13c6008880e3d24c89d1c23323b853
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626226"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Introdução à U-SQL no Azure Data Lake Analytics
U-SQL é uma linguagem que combina o SQL declarativo com o C# imperativo para permitir que você processe dados em qualquer escala. Por meio da capacidade de consulta distribuída escalonável do U-SQL, você pode analisar, de modo eficiente, dados entre repositórios relacionais como o Banco de Dados SQL do Azure. Com o U-SQL, você pode processar dados não estruturados aplicando o esquema na leitura e inserindo lógica personalizada e UDFs. Além disso, o U-SQL inclui extensibilidade que lhe dá controle refinado sobre como executar em escala. 

## <a name="learning-resources"></a>Recursos de aprendizagem

* O [Tutorial do U-SQL](https://aka.ms/usqltutorial) fornece um passo a passo guiado sobre a maior parte da linguagem U-SQL. A leitura deste documento é recomendada para todos os desenvolvedores que desejam aprender sobre U-SQL.
* Para obter informações detalhadas sobre a **sintaxe da linguagem U-SQL**, consulte a [U-SQL Language Reference](https://docs.microsoft.com/u-sql/) (Referência da linguagem U-SQL).
* Para entender a **filosofia de design do U-SQL**, confira a postagem do blog do Visual Studio [Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/) (Apresentando o U-SQL – Uma linguagem que torna fácil o processamento de Big Data).

## <a name="prerequisites"></a>Pré-requisitos

Antes de percorrer os exemplos do U-SQL neste documento, leia e conclua [Tutorial: Desenvolver scripts U-SQL usando ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). Esse tutorial explica a mecânica do uso do U-SQL com as Ferramentas do Azure Data Lake para Visual Studio.

## <a name="your-first-u-sql-script"></a>Seu primeiro script U-SQL

O script U-SQL a seguir é simples e nos permite explorar muitos aspectos da linguagem U-SQL.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Esse script não possui nenhuma etapa de transformação. Ele faz a leitura do arquivo de origem chamado `SearchLog.tsv`, o esquematiza e grava o conjunto de linhas de volta em um arquivo chamado SearchLog-first-u-sql.csv.

Observe o ponto de interrogação ao lado do tipo de dados no campo `Duration`. Isso significa que o campo `Duration` pode ser nulo.

### <a name="key-concepts"></a>Principais conceitos
* **Variáveis de conjunto de linhas**: Cada expressão de consulta que produz um conjunto de linhas pode ser atribuído a uma variável. O U-SQL segue o padrão de nomenclatura de variável do T-SQL (`@searchlog`, por exemplo) no script.
* A palavra-chave **EXTRACT** lê dados de um arquivo e define o esquema na leitura. `Extractors.Tsv` é um extrator U-SQL interno para arquivos de valores separados por tabulação. Você pode desenvolver extratores personalizados.
* **OUTPUT** grava dados de um conjunto de linhas em um arquivo. `Outputters.Csv()` é um outputter U-SQL interno para criar um arquivo de valores separados por vírgulas. Você pode desenvolver outputters personalizados.

### <a name="file-paths"></a>Caminhos de arquivo

As instruções EXTRACT e OUTPUT usam caminhos de arquivo. Os caminhos de arquivo podem ser absolutos ou relativos:

O caminho de arquivo absoluto a seguir se refere a um arquivo em um Data Lake Store chamado `mystore`:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

O caminho de arquivo a seguir começa com `"/"`. Ele se refere a um arquivo na conta padrão do Data Lake Store:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Usar variáveis escalares

Você pode usar variáveis escalares também para facilitar a manutenção do seu script. O script U-SQL anterior também pode ser escrito da seguinte forma:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformar conjuntos de linhas

Use **SELECT** para transformar conjuntos de linhas:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

A cláusula WHERE usa uma [expressão booliana C#](/dotnet/csharp/language-reference/operators/index). Você pode usar a linguagem de expressão do C# para fazer suas próprias expressões e funções. Você pode até mesmo executar uma filtragem mais complexa combinando-os com associações lógicas (ANDs) e desassociações (ORs).

O script a seguir usa o método DateTime.Parse() e um conjunto.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >A segunda consulta está operando no resultado do primeiro conjunto de linhas, que cria uma composição dos dois filtros. Também é possível reutilizar um nome de variável; o escopo dos nomes é definido lexicalmente.

## <a name="aggregate-rowsets"></a>Agregar conjuntos de linhas
O U-SQL fornece a você as conhecidas funções ORDER BY, GROUP BY e agregações.

A consulta a seguir localiza a duração total por região e então exibe as cinco maiores durações, na ordem.

Os conjuntos de linhas do U-SQL não mantêm a ordem para a consulta seguinte. Portanto, para solicitar uma saída, você precisa adicionar ORDER BY à instrução OUTPUT:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

A cláusula ORDER BY do U-SQL requer o uso da cláusula FETCH em uma expressão SELECT.

A cláusula HAVING do U-SQL pode ser usada para restringir a saída aos grupos que satisfazem a condição de HAVING:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Para cenários de agregação avançados, consulte a documentação de referência do U-SQL sobre as [funções de agregação, análise e referência](/u-sql/built-in-functions)

## <a name="next-steps"></a>Próximas etapas
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolvimento de scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
