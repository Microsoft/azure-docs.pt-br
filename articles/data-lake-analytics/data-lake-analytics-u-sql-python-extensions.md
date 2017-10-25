---
title: Estender scripts U-SQL com Python no Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como executar o código Python em Scripts U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/20/2017
ms.author: saveenr
ms.openlocfilehash: d18ef1f747aee2fa01cef9891432d0461031ee4c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-extending-u-sql-with-python"></a>Tutorial: introdução à extensão do U-SQL com o Python

As extensões do Python para o U-SQL permitem aos desenvolvedores a realização de execução massivamente paralela do código Python. O exemplo a seguir ilustra as etapas básicas:

* Use a instrução `REFERENCE ASSEMBLY` para habilitar as extensões Python para o Script U-SQL
* Usando a operação `REDUCE` para particionar os dados de entrada em uma chave
* As extensões do Python para U-SQL incluem um redutor interno (`Extension.Python.Reducer`) que executa o código Python em cada vértice atribuído ao redutor
* O script U-SQL contém o código Python inserido, que tem uma função chamada `usqlml_main` que aceita um DataFrame pandas como entrada e retorna um DataFrame pandas como saída.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Como o Python se integra com o U-SQL

### <a name="datatypes"></a>Tipos de dados

* As cadeias de caracteres e colunas numéricas do U-SQL são convertidas como estão entre Pandas e U-SQL
* Os Nulos do U-SQL são convertidos de/para valores Pandas `NA`

### <a name="schemas"></a>Esquemas

* Os vetores de índice no Pandas não têm suporte no U-SQL. Todos os quadros de dados de entrada na função Python sempre têm um índice numérico de 64 bits de 0 até o número de linhas menos 1. 
* Conjuntos de dados U-SQL não podem ter nomes de coluna duplicados
* Nomes de coluna de conjuntos de dados U-SQL que não são cadeias de caracteres. 

### <a name="python-versions"></a>Versões do Python
Há suporte somente para o Python 3.5.1 (compilado para Windows). 

### <a name="standard-python-modules"></a>Módulos de Python Standard
Todos os módulos de Python padrão estão incluídos.

### <a name="additional-python-modules"></a>Módulos de Python adicionais
Além das bibliotecas Python padrão, várias bibliotecas Python comumente usadas estão incluídas:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Mensagens de Exceção
Atualmente, uma exceção no código Python aparece como falha genérica de vértice. No futuro, as mensagens de erro do trabalho U-SQL exibirão a mensagem de exceção do Python.

### <a name="input-and-output-size-limitations"></a>Limitações de tamanho de Entrada e Saída
Cada vértice tem uma quantidade limitada de memória atribuída a ele. Atualmente, esse limite é de 6 GB para um AU. Como os DataFrames de entrada e de saída devem existir na memória no código Python, o tamanho total para a entrada e saída não pode exceder os 6 GB.

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)

