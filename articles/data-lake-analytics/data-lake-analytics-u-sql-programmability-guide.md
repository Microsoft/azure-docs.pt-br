---
title: Guia de programação de U-SQL para o Azure Data Lake
description: Saiba mais sobre a visão geral do U-SQL e a programação de UDF no Azure Data Lake Analytics para permitir que você crie um bom script USQL.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96510855"
---
# <a name="u-sql-programmability-guide-overview"></a>Visão geral do guia de programação do U-SQL

O U-SQL é uma linguagem de consulta projetada para o tipo big data de cargas de trabalho. Um dos recursos exclusivos do U-SQL é a combinação da linguagem declarativa semelhante ao SQL com a extensibilidade e a programação fornecidas por C#. Neste guia, nosso foco é a extensibilidade e a programação da linguagem U-SQL habilitadas por C#.

## <a name="requirements"></a>Requisitos

Baixe e instale as [Ferramentas do Azure Data Lake para Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Introdução ao U-SQL  

Examine o script U-SQL a seguir:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Esse script define dois conjuntos de linhas: `@a` e `@results`. O conjunto de linhas `@results` é definido de `@a`.

## <a name="c-types-and-expressions-in-u-sql-script"></a>Tipos e expressões de C# no script U-SQL

Uma expressão de U-SQL é uma expressão de C# combinada com operações lógicas U-SQL como `AND`, `OR` e `NOT`. Expressões U-SQL podem ser usadas com SELECT, EXTRACT, WHERE, HAVING, GROUP BY e DECLARE. Por exemplo, o script a seguir analisa uma cadeia de caracteres de um valor DateTime.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

O snippet a seguir analisa uma cadeia de caracteres de um valor DateTime em uma instrução DECLARE.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>Usar expressões C# para conversões de tipo de dados

O exemplo a seguir demonstra como você pode fazer uma conversão de dados de data e hora usando expressões C#. Nesse cenário específico, os dados de datetime da cadeia de caracteres são convertidos em datetime padrão com a notação de hora 00:00:00 meia-noite.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>Usar expressões C# para a data de hoje

Para efetuar pull da data de hoje, podemos usar a seguinte expressão C#: `DateTime.Now.ToString("M/d/yyyy")`

Aqui está um exemplo de como usar essa expressão em um script:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>Usando assemblies .NET

O modelo de extensibilidade do U-SQL depende em grande parte da capacidade de adicionar código personalizado de assemblies .NET. 

### <a name="register-a-net-assembly"></a>Registrar um assembly .NET

Use a instrução `CREATE ASSEMBLY` para colocar um assembly do .NET em um banco de dados U-SQL. Posteriormente, os scripts U-SQL poderão usar esses assemblies usando a instrução `REFERENCE ASSEMBLY`. 

O código a seguir mostra como registrar um assembly:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

O código a seguir mostra como referenciar um assembly:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Consulte as [instruções de registro do assembly](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog), que abrangem este tópico mais detalhadamente.


### <a name="use-assembly-versioning"></a>Usar o controle de versão do assembly
Atualmente, o U-SQL usa o .NET Framework versão 4.7.2. Portanto, faça com que seus próprios assemblies sejam compatíveis com essa versão do runtime.

Conforme mencionado anteriormente, o U-SQL executa código em um formato de 64 bits (x64). Portanto, faça com que seu código seja compilado para execução em x64. Caso contrário, você obterá o erro de formato incorreto mostrado anteriormente.

Cada arquivo de recurso e DLL de assembly carregado, como um runtime diferente, um assembly nativo ou um arquivo config, pode ter no máximo 400 MB. O tamanho total dos recursos implantados, seja por meio de DEPLOY RESOURCE ou por meio de referências a assemblies e a seus arquivos adicionais, não pode exceder 3 GB.

Por fim, observe que cada banco de dados U-SQL pode conter apenas uma versão de qualquer determinado assembly. Por exemplo, se você precisar da versão 7 e da versão 8 da biblioteca NewtonSoft Json.NET, será necessário registrá-las em dois bancos de dados diferentes. Além disso, cada script pode fazer referência apenas a uma versão da DLL de um determinado assembly. Nesse sentido, o U-SQL segue a semântica de controle de versão e gerenciamento do assembly de C#.

## <a name="use-user-defined-functions-udf"></a>Funções definidas pelo usuário: UDF
As funções definidas pelo usuário ou UDF do U-SQL são rotinas de programação que aceitam parâmetros, executam uma ação (mo um cálculo complexo)e retornam o resultado dessa ação como um valor. O valor de retorno da UDF pode ser apenas um único escalar. A UDF do U-SQL pode ser chamado no script base U-SQL como qualquer outra função escalar C#.

Recomendamos que você inicialize as funções do U-SQL definidas pelo usuário como **públicas** e **estáticas**.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Primeiro, vamos ver um exemplo simples de como criar uma UDF.

Nesse cenário de caso de uso, precisamos determinar o período fiscal, inclusive o trimestre fiscal e o mês fiscal do primeiro logon para o usuário específico. O primeiro mês fiscal do ano em nosso cenário é junho.

Para calcular o período fiscal, introduzimos a seguinte função C#:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Ela simplesmente calcula o mês e o trimestre fiscal e retorna um valor de cadeia de caracteres. No caso de junho, o primeiro mês do primeiro trimestre fiscal, usamos "Q1:P1". No caso de julho, usamos "Q1:P2" e assim por diante.

Essa é uma função C# regular que vamos usar em nosso projeto U-SQL.

Veja como é a seção code-behind nesse cenário:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Agora, vamos chamar essa função do script base U-SQL. Para fazer isso, precisamos fornecer um nome totalmente qualificado para a função, incluindo o namespace, que nesse caso é NameSpace.Class.Function(parameter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Veja abaixo o script base U-SQL real:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Veja abaixo o arquivo de saída da execução do script:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Esse exemplo demonstra um uso simples de UDF embutida no U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Manter o estado entre invocações da UDF
Os objetos de programação C# do U-SQL podem ser mais sofisticados ao utilizar interatividade por meio de variáveis globais de code-behind. Vejamos o cenário de caso de uso comercial a seguir.

Em grandes organizações, os usuários podem alternar entre diversos aplicativos internos. Eles podem incluir o Microsoft Dynamics CRM, Power BI e assim por diante. O cliente pode querer aplicar uma análise de telemetria de como os usuários alternam entre diferentes aplicativos, quais são as tendências de uso e assim por diante. O objetivo da empresa é otimizar o uso de aplicativos. Ela também pode querer combinar diferentes aplicativos ou rotinas específicas de logon.

Para atingir essa meta, precisamos determinar as IDs de uma sessão e o tempo de latência desde a última sessão ocorrida.

Precisamos encontrar um logon anterior e atribuí-lo a todas as sessões que estão sendo geradas para o mesmo aplicativo. Primeiro desafio: o script base U-SQL não nos permite aplicar cálculos sobre colunas já calculadas com a função LAG. Segundo desafio: temos que manter a sessão específica para todas as sessões no mesmo período.

Para resolver esse problema, use uma variável global dentro da seção code-behind: `static public string globalSession;`.

Essa variável global é aplicada a todo o conjunto de linhas durante a execução do nosso script.

Veja abaixo a seção code-behind do nosso programa U-SQL:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

Este exemplo mostra a variável global `static public string globalSession;` usada dentro da função `getStampUserSession` sendo reinicializada sempre que o parâmetro Session é alterado.

O script base U-SQL é o seguinte:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

A função `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` é chamada aqui durante o segundo cálculo do conjunto de linhas da memória. Ele passa a coluna `UserSessionTimestamp` e retorna o valor até que `UserSessionTimestamp` seja alterado.

O arquivo de saída é o seguinte:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

Esse exemplo demonstra um cenário de caso de uso mais complicado em que usamos uma variável global dentro da seção code-behind aplicada a todo um conjunto de linhas da memória.

## <a name="next-steps"></a>Próximas etapas
* [Guia de programação do U-SQL-UDT e UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Guia de programação do U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)