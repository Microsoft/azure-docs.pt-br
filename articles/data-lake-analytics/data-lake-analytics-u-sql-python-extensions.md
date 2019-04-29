---
title: Estender scripts U-SQL com Python no Azure Data Lake Analytics
description: Saiba como executar código Python em scripts U-SQL usando o Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813409"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Estender scripts U-SQL com código Python no Azure Data Lake Analytics

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que as extensões de Python estejam instaladas na sua conta do Azure Data Lake Analytics.

* Acesse a sua conta do Data Lake Analytics no portal do Azure
* No menu à esquerda, em **INTRODUÇÃO** clique em **Scripts de Exemplo**
* Selecione **Instalar Extensões U-SQL** e clique em **OK**

## <a name="overview"></a>Visão geral 

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
* [Usar as Ferramentas do Azure Data Lake para Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
