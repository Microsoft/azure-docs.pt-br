---
title: Date_Bucket (Transact-SQL)-Azure SQL Edge
description: Saiba mais sobre como usar Date_Bucket no Azure SQL Edge
keywords: Date_Bucket, SQL no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073512"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (T-SQL)

Essa função retorna o valor DateTime correspondente ao início de cada bucket DateTime, do carimbo de data/hora definido pelo `origin` parâmetro ou o valor de origem padrão de `1900-01-01 00:00:00.000` se o parâmetro Origin não for especificado. 

Confira [Tipos e funções de data e hora do &#40;T-SQL&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) para ter uma visão geral de todos os tipos e funções de data e hora do T-SQL.

[Convenções de sintaxe Transact-SQL](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Sintaxe

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argumentos

*datePart*

A parte da *data* que é usada com o parâmetro 'number'. Ex.: ano, mês, minuto, segundo etc.

> [!NOTE]
> `DATE_BUCKET` não aceita os equivalentes de variável definidos pelo usuário para os argumentos *datePart*.
  
|*datePart*|Abreviações|  
|---|---|
|**day**|**dd**, **d**|  
|**week**|**wk**, **ww**| 
|**month**|**mm**, **m**|
|**quarter**|**qq**, **q**|  
|**year**|**yy**, **yyyy**|  
|**hour**|**hh**|  
|**minute**|**mi**, **n**|  
|**second**|**ss**, **s**|  
|**millisecond**|**ms**|  

*number*

O número inteiro que decide a largura do bucket combinado com o argumento *datePart*. Isso representa a largura dos buckets de datePart da hora de origem. **`This argument cannot be a negative integer value`**. 

*date*

Uma expressão que pode resolver um dos seguintes valores:

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

Para *date*, `DATE_BUCKET` aceitará uma expressão de coluna, expressão ou variável definida pelo usuário se elas resolverem para qualquer um dos tipos de dados mencionados acima.

**Ter** 

Uma expressão opcional que pode ser resolvida para um dos seguintes valores:

+ **date**
+ **datetime**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

O tipo de dados para `Origin` deve corresponder ao tipo de dados do `Date` parâmetro. 

`DATE_BUCKET` usa um valor de data de origem padrão de, `1900-01-01 00:00:00.000` ou seja, 12:00 am na segunda-feira, 1 1900 de Janeiro, se nenhum valor de origem for especificado para a função.

## <a name="return-type"></a>Tipo de retorno

O tipo de dados do valor retornado para esse método é dinâmico. O tipo de retorno depende do argumento fornecido para `date`. Se um tipo de dados de entrada válido for fornecido para `date`, `DATE_BUCKET` retornará o mesmo tipo de dados. `DATE_BUCKET` gera um erro se um literal de cadeia de caracteres é especificado para o parâmetro `date`.

## <a name="return-values"></a>Valores de retorno

### <a name="understanding-the-output-from-date_bucket"></a>Entendendo a saída de `DATE_BUCKET`

`Date_Bucket` retorna o valor de data ou hora mais recente, correspondente aos parâmetros datePart e number. Por exemplo, nas expressões abaixo, `Date_Bucket` retornará o valor de saída de `2020-04-13 00:00:00.0000000`, pois a saída é calculada com base em buckets de uma semana a partir da hora de origem padrão de `1900-01-01 00:00:00.000`. O valor de `2020-04-13 00:00:00.0000000` é 6276 semanas do valor de origem de `1900-01-01 00:00:00.000`. 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Para todas as expressões abaixo, o mesmo valor de saída de `2020-04-13 00:00:00.0000000` será retornado. Isso ocorre porque `2020-04-13 00:00:00.0000000` é 6276 semanas da data de origem e 6276 é divisível por 2, 3, 4 e 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

A saída para a expressão abaixo é `2020-04-06 00:00:00.0000000` , que é de 6275 semanas a partir da hora de origem padrão `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

A saída para a expressão abaixo é `2020-06-09 00:00:00.0000000` , que é de 75 semanas a partir do tempo de origem especificado `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>Argumento datepart

**dayofyear**, **day** e **weekday** retornam o mesmo valor. Cada *datepart* retorna o mesmo valor das abreviações dela.
  
## <a name="number-argument"></a>Argumento number

O argumento *number* não pode exceder o intervalo de valores **int** positivos. Nas instruções a seguir, o argumento *number* excede o intervalo de **int** em 1. A instrução a seguir retorna esta mensagem de erro: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Se um valor negativo do número for passado para a função `Date_Bucket`, será retornado o erro a seguir. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>Argumento date  

`DATE_BUCKET` retorna o valor base correspondente ao tipo de dados do argumento `date`. No exemplo a seguir, um valor de saída com datatype datetime2 é retornado. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>Argumento de origem  

O tipo de dados dos `origin` `date` argumentos e em deve ser o mesmo. Se tipos de dados diferentes forem usados, um erro será gerado.

## <a name="remarks"></a>Comentários

Use `DATE_BUCKET` nas seguintes cláusulas:

+ GROUP BY
+ HAVING
+ ORDER BY
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>Exemplos

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>a. Calculando Date_Bucket com uma largura de bucket de 1 da hora de origem

Cada uma dessas instruções incrementa *date_bucket* com uma largura de Bucket de 1 da hora de origem:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Este é o conjunto de resultados.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Usando expressões como argumentos para os parâmetros de número e data

Estes exemplos usam diferentes tipos de expressões como argumentos para os parâmetros *number* e *date*. Esses exemplos são criados usando o banco de dados ' AdventureWorksDW2017 '.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Especificando variáveis definidas pelo usuário como número e data  

Este exemplo especifica variáveis definidas pelo usuário como argumentos para *número* e *data*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Este é o conjunto de resultados.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Especificando uma coluna como data

No exemplo a seguir, estamos calculando a soma de OrderQuantity e a soma de UnitPrice agrupados em buckets de datas semanais.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Este é o conjunto de resultados.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Especificando função de sistema escalar como data

Este exemplo especifica `SYSDATETIME` para *data*. O valor exato retornado depende do dia e da hora da execução da instrução:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Este é o conjunto de resultados.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Especificando subconsultas e funções escalares como número e data

Este exemplo usa subconsultas escalares, `MAX(OrderDate)`, como argumentos para *número* e *data*. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` funciona como um argumento artificial do parâmetro de número que mostra como selecionar um argumento *number* em uma lista de valores.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Especificando expressões numéricas e funções de sistema escalares como número e data

Este exemplo usa uma expressão numérica ((10/2)) e funções escalares do sistema (SYSDATETIME) como argumentos para número e data.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Especificando uma função de janela de agregação como número

Este exemplo usa uma função de janela de agregação como um argumento para *number*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Usando um valor de origem não padrão

Este exemplo usa um valor de orgin não padrão para gerar os buckets de data. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Confira também

[CAST e CONVERT &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
