---
title: Trabalhar com tipos de dados SQL e R e objetos
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Saiba como trabalhar com tipos de dados e objetos de dados no R com o banco de dados do SQL Azure usando serviços de Machine Learning (versão prévia), incluindo problemas comuns que você pode encontrar.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ms.openlocfilehash: 069a2a5b3b26bf517b57034f05ab7080ab392319
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702513"
---
# <a name="work-with-r-and-sql-data-in-azure-sql-database-machine-learning-services-preview"></a>Trabalhar com dados de R e SQL no banco de dados para SQL Azure Machine Learning Services (versão prévia)

Este artigo discute alguns dos problemas comuns que você pode encontrar ao mover dados entre R e o banco de dados SQL [serviços de Machine Learning (com R) no banco de dados SQL](sql-database-machine-learning-services-overview.md). A experiência obtida por meio deste exercício fornece essenciais em segundo plano ao trabalhar com dados em seu próprio script.

Problemas comuns que você pode encontrar incluem:

- Às vezes, os tipos de dados não correspondem
- Conversões implícitas podem ocorrer
- Operações cast e convert, às vezes, são necessárias
- R e SQL usam objetos de dados diferentes

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

- Para executar o código de exemplo nestes exercícios, primeiro você precisa ter um Banco de dados SQL do Azure com os Serviços do Machine Learning (com R) habilitados. Durante a versão prévia pública, a Microsoft realizará sua integração e habilitará o aprendizado de máquina para seu banco de dados novo ou existente. Siga as etapas em [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).

- Verifique se você instalou a versão mais recente do SSMS ([SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)). Você pode executar scripts R usando outras ferramentas de consulta ou gerenciamento de banco de dados, mas neste início rápido, você usará o SSMS.

## <a name="working-with-a-data-frame"></a>Trabalhando com um quadro de dados

Quando o script retorna resultados de R para SQL, ele deve retornar os dados como uma **Frame**. Qualquer outro tipo de objeto gerado no seu script - se de que ser uma lista, fator, vetor ou dados binários - deve ser convertido em um quadro de dados se você desejar enviá-lo como parte dos resultados do procedimento armazenado. Felizmente, há várias funções de R para dar suporte à conversão de outros objetos em um quadro de dados. Você pode até mesmo serializar um modelo binário e retorná-lo em um quadro de dados, que será feito neste artigo.

Primeiro, vamos experimentar alguns objetos básicos de R - vetores, matrizes e listas - e ver como a conversão em um quadro de dados muda a saída passada para o SQL.

Compare esses dois scripts de "Hello World" em R. Os scripts parecem quase idênticos, mas o primeiro retorna uma única coluna de três valores, enquanto o segundo retorna três colunas com um único valor cada.

**Exemplo 1**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
'
    , @input_data_1 = N'';
```

**Exemplo 2**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N' OutputDataSet<- data.frame(c("hello"), " ", c("world"));'
    , @input_data_1 = N'';
```

Por que os resultados são tão diferentes?

A resposta geralmente pode ser encontrada usando o R `str()` comando. Adicione a função `str(object_name)` em qualquer lugar no seu script R para ter os dados de esquema do objeto R especificado retornado como uma mensagem informativa. Você pode exibir as mensagens na **mensagens** guia no SSMS.

Para descobrir por que o exemplo 1 e 2 do exemplo tem resultados tão diferentes, insira a linha `str(OutputDataSet)` no final o `@script` definição de variável em cada instrução, como este:

**Exemplo 1 com a função str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
mytextvariable <- c("hello", " ", "world");
OutputDataSet <- as.data.frame(mytextvariable);
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

**Exemplo 2 com a função str adicionado**

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(c("hello"), " ", c("world"));
str(OutputDataSet);
'
    , @input_data_1 = N'  ';
```

Agora, examine o texto em **mensagens** para ver por que a saída é diferente.

**Resultados – exemplo 1**

```text
STDOUT message(s) from external script:
'data.frame':   3 obs. of  1 variable:
$ mytextvariable: Factor w/ 3 levels " ","hello","world": 2 1 3
```

**Resultados – exemplo 2**

```text
STDOUT message(s) from external script:
'data.frame':   1 obs. of  3 variables:
$ c..hello..: Factor w/ 1 level "hello": 1
$ X...      : Factor w/ 1 level " ": 1
$ c..world..: Factor w/ 1 level "world": 1
```

Como você pode ver, uma pequena alteração na sintaxe de R teve um grande efeito no esquema dos resultados. Para todos os detalhes, as diferenças nos tipos de dados de R são explicadas em detalhes na *estruturas de dados* seção ["Advanced R", de Hadley Wickham](http://adv-r.had.co.nz).

Por enquanto, apenas esteja ciente de que você precisa verificar os resultados esperados ao moldar objetos R em quadros de dados.

> [!TIP]
> Você também pode usar funções de identidade de R, como `is.matrix`, `is.vector`, para retornar informações sobre a estrutura de dados internos.

## <a name="implicit-conversion-of-data-objects"></a>Conversão implícita de objetos de dados

Cada objeto de dados R tem suas próprias regras para como os valores são manipulados quando combinado com outros objetos de dados se os objetos de dados têm o mesmo número de dimensões ou se qualquer objeto de dados contém tipos de dados heterogêneos.

Por exemplo, suponha que você deseja executar uma multiplicação de matriz usando R. Você deseja multiplicar uma matrix de coluna única com três valores de uma matriz com quatro valores e espera uma matriz de 4 x 3 como resultado.

Primeiro, crie uma pequena tabela de dados de teste.

```sql
CREATE TABLE RTestData (col1 INT NOT NULL)

INSERT INTO RTestData
VALUES (1);

INSERT INTO RTestData
VALUES (10);

INSERT INTO RTestData
VALUES (100);
GO
```

Agora execute o script a seguir.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:15);
OutputDataSet <- as.data.frame(x %*% y);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col1] INT
            , [Col2] INT
            , [Col3] INT
            , Col4 INT
            ));
```

Nos bastidores, a coluna de três valores é convertida em uma matriz de coluna única. Como uma matriz é apenas um caso especial de uma matriz de R, a matriz `y` é implicitamente moldada em uma matriz de coluna única para adequar os dois argumentos.

**Resultados**

|Col1|Col2|Col3|Col4|
|---|---|---|---|
|12|13|14|15|
|120|130|140|150|
|1.200|1300|1.400|1500|

No entanto, observe o que acontece quando você alterar o tamanho da matriz `y`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
x <- as.matrix(InputDataSet);
y <- array(12:14);
OutputDataSet <- as.data.frame(y %*% x);
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS(([Col1] INT));
```

Agora o R retorna um único valor como resultado.

**Resultados**
    
|Col1|
|---|
|1542|

Por quê? Nesse caso, porque os dois argumentos podem ser tratados como vetores de mesmo tamanho, R retorna o produto interno como uma matriz.  Isso é o comportamento esperado de acordo com as regras de álgebra linear. No entanto, ele pode causar problemas se seu aplicativo downstream espera que o esquema de saída nunca mude!

## <a name="merge-or-multiply-columns-of-different-length"></a>Mesclar ou multiplicar colunas de comprimento diferente

O R fornece grande flexibilidade para trabalhar com vetores de tamanhos diferentes e combinar essas estruturas semelhantes a colunas em quadros de dados. Listas de vetores podem parecer com uma tabela, mas eles não seguem as regras que regem as tabelas de banco de dados.

Por exemplo, o script a seguir define uma matriz numérica de tamanho 6 e os armazena na variável do R `df1`. A matriz numérica é combinada, em seguida, com os inteiros da tabela RTestData (criado acima) que contém três (3) valores, para criar um novo quadro de dados, `df2`.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
df1 <- as.data.frame( array(1:6) );
df2 <- as.data.frame( c( InputDataSet , df1 ));
OutputDataSet <- df2;
'
    , @input_data_1 = N' SELECT [Col1]  from RTestData;'
WITH RESULT SETS((
            [Col2] INT NOT NULL
            , [Col3] INT NOT NULL
            ));
```

Para preencher o quadro de dados, R repete os elementos recuperados do RTestData quantas vezes forem necessárias para corresponder ao número de elementos na matriz `df1`.

**Resultados**
    
|*Col2*|*Col3*|
|----|----|
|1|1|
|10|2|
|100|3|
|1|4|
|10|5|
|100|6|

Lembre-se de que um quadro de dados somente se parece com uma tabela, mas é, na verdade, uma lista de vetores.

## <a name="cast-or-convert-sql-data"></a>Cast ou convert de dados do SQL

R e SQL não usam os mesmos tipos de dados, portanto, quando você executa uma consulta em SQL para obter dados e, em seguida, passá-lo para o tempo de execução de R, algum tipo de conversão implícita geralmente ocorre. Outro conjunto de conversões ocorre quando você retorna dados de R para SQL.

- SQL envia os dados da consulta para o processo do R e o converte em uma representação interna para maior eficiência.
- O tempo de execução do R carrega os dados em uma variável de Frame e executa suas próprias operações nos dados.
- O mecanismo de banco de dados retorna os dados para o SQL usando uma conexão interna protegida e apresenta os dados em termos de tipos de dados SQL.
- Obter os dados conectando-se ao SQL usando uma biblioteca de cliente ou da rede que pode emitir consultas SQL e lidar com conjuntos de dados tabulares. Esse aplicativo cliente pode afetar os dados de outras maneiras.

Para ver como isso funciona, executar uma consulta como esta na [AdventureWorksDW](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks) depósito de dados. Essa exibição retorna dados de vendas usados na criação de previsões.

```sql
USE AdventureWorksDW
GO

SELECT ReportingDate
    , CAST(ModelRegion AS VARCHAR(50)) AS ProductSeries
    , Amount
FROM [AdventureWorksDW].[dbo].[vTimeSeries]
WHERE [ModelRegion] = 'M200 Europe'
ORDER BY ReportingDate ASC
```

> [!NOTE]
> Você pode usar qualquer versão do AdventureWorks, ou criar uma consulta diferente usando um banco de dados de sua preferência. O ponto é tentar manipular alguns dados que contém o texto, data e hora e valores numéricos.

Agora, tente usar essa consulta como entrada para o procedimento armazenado.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
str(InputDataSet);
OutputDataSet <- InputDataSet;
'
    , @input_data_1 = N'
           SELECT ReportingDate
         , CAST(ModelRegion as varchar(50)) as ProductSeries
         , Amount
           FROM [AdventureWorksDW].[dbo].[vTimeSeries]
           WHERE [ModelRegion] = ''M200 Europe''
           ORDER BY ReportingDate ASC ;'
WITH RESULT SETS undefined;
```

Se você receber um erro, você provavelmente precisará fazer algumas modificações no texto da consulta. Por exemplo, o predicado de cadeia de caracteres na cláusula WHERE deve estar entre dois conjuntos de aspas simples.

Depois de obter a consulta funcionar, examine os resultados do `str` função para ver como R trata os dados de entrada.

**Resultados**

```text
STDOUT message(s) from external script: 'data.frame':    37 obs. of  3 variables:
STDOUT message(s) from external script: $ ReportingDate: POSIXct, format: "2010-12-24 23:00:00" "2010-12-24 23:00:00"
STDOUT message(s) from external script: $ ProductSeries: Factor w/ 1 levels "M200 Europe",..: 1 1 1 1 1 1 1 1 1 1
STDOUT message(s) from external script: $ Amount       : num  3400 16925 20350 16950 16950
```

- A coluna datetime foi processada usando o tipo de dados R **POSIXct**.
- A coluna de texto "ProductSeries" foi identificado como um **fator**, que significa que uma variável categórica. Valores de cadeia de caracteres são tratadas como fatores por padrão. Se você passar uma cadeia de caracteres para R, ele é convertido em um inteiro para uso interno e, em seguida, mapeado para a cadeia de caracteres na saída.

## <a name="summary"></a>Resumo

Até mesmo para essas breves exemplos, você pode ver a necessidade de verificar os efeitos da conversão de dados ao passar SQL consultas como entrada. Porque não há suporte para alguns tipos de dados SQL, R, considere essas formas de evitar erros:

- Teste os dados com antecedência e verifique se a colunas ou valores em seu esquema que pode ser um problema quando passados para o código R.
- Especificar as colunas na fonte de dados de entrada individualmente, em vez de usar `SELECT *`e saber como cada coluna será tratada.
- Execute conversões explícitas conforme necessário durante a preparação dos dados de entrada, para evitar surpresas.
- Evite colunas de passagem de dados (como GUIDS ou rowguids) que causam erros e não são úteis para modelagem.

Para obter mais informações sobre tipos de dados de R compatíveis e sem suporte, consulte [tipos de dados e bibliotecas de R](/sql/advanced-analytics/r/r-libraries-and-data-types.md).
