---
title: Referência da linguagem SQL de aceleração de consulta
titleSuffix: Azure Storage
description: Saiba como usar a sintaxe SQL de aceleração de consulta.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 2eda67e377a3b61e696e732b916d788c00a18eae
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908771"
---
# <a name="query-acceleration-sql-language-reference"></a>Referência da linguagem SQL de aceleração de consulta

A aceleração de consulta dá suporte a uma linguagem do tipo ANSI SQL para expressar consultas no conteúdo do blob.  O dialeto SQL de aceleração de consulta é um subconjunto do ANSI SQL, com um conjunto limitado de tipos de dados com suporte, operadores etc., mas também se expande no SQL ANSI para dar suporte a consultas em formatos de dados semiestruturados hierárquicos, como JSON. 

## <a name="select-syntax"></a>SELECIONAR sintaxe

A única instrução SQL com suporte da aceleração de consulta é a instrução SELECT. Este exemplo retorna todas as linhas para as quais a expressão retorna true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Para dados formatados em CSV, a *tabela* deve ser `BlobStorage` .  Isso significa que a consulta será executada em qualquer blob especificado na chamada REST.
Para dados formatados em JSON, a *tabela* é um "descritor de tabela".   Consulte a seção [descritores de tabela](#table-descriptors) deste artigo.

No exemplo a seguir, para cada linha para a qual a *expressão* WHERE retorna true, essa instrução retornará uma nova linha que é feita da avaliação de cada uma das expressões de projeção.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir retorna uma computação de agregação (por exemplo: o valor médio de uma determinada coluna) sobre cada uma das linhas para as quais a *expressão* retorna true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

O exemplo a seguir retorna deslocamentos adequados para a divisão de um blob formatado em CSV.  Consulte a seção [Sys. Split](#sys-split) deste artigo.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Tipos de dados

|Tipo de dados|Descrição|
|---------|-------------------------------------------|
|INT      |Inteiro com sinal de 64 bits.                     |
|FLOAT    |ponto flutuante de 64 bits ("precisão dupla").|
|STRING   |Cadeia de caracteres Unicode de comprimento variável.            |
|timestamp|Um ponto no tempo.                           |
|BOOLEAN  |True ou false.                             |

Ao ler valores de dados formatados em CSV, todos os valores são lidos como cadeias de caracteres.  Valores de cadeia de caracteres podem ser convertidos em outros tipos usando expressões de conversão.  Os valores podem ser convertidos implicitamente em outros tipos, dependendo do contexto. para obter mais informações, consulte [precedência de tipo de dados (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>Expressões

### <a name="referencing-fields"></a>Fazendo referência a campos

Para dados formatados em JSON ou dados formatados em CSV com uma linha de cabeçalho, os campos podem ser referenciados por nome.  Os nomes de campo podem ser colocados entre aspas ou sem aspas. Os nomes de campo entre aspas são colocados entre caracteres de aspas duplas ("), podem conter espaços e diferenciam maiúsculas de minúsculas.  Os nomes de campos sem aspas não diferenciam maiúsculas de minúsculas e não podem conter caracteres especiais.

Em dados formatados em CSV, os campos também podem ser referenciados por ordinal, prefixado com um caractere de sublinhado (_).  Por exemplo, o primeiro campo pode ser referenciado como _1 ou o décimo de campo pode ser referenciado como _11.  A referência de campos por ordinal é útil para dados formatados em CSV que não contêm uma linha de cabeçalho; nesse caso, a única maneira de fazer referência a um determinado campo é por ordinal.

### <a name="operators"></a>Operadores

Há suporte para os seguintes operadores SQL padrão:

|Operador|Descrição|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |Compara a igualdade de duas expressões (operador de comparação).|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Testa se uma expressão não é igual a outra expressão (um operador de comparação).|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Compara duas expressões para que não sejam iguais a (um operador de comparação).|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |Compara duas expressões para menores que (um operador de comparação).|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Compara duas expressões para menores ou iguais (um operador de comparação).|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |Compara duas expressões para maior que (um operador de comparação). |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Compara duas expressões por maior ou igual a (um operador de comparação).|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |Soma dois números. Este operador aritmético de adição também pode adicionar um número, em dias, a uma data.|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |Subtrai dois números (um operador de subtração aritmético). |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |Divide um número por outro (um operador de divisão aritmética).|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |Multiplica duas expressões (um operador de multiplicação aritmética).|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |Retorna o resto de um número dividido por outro.|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Executa uma operação lógica AND bit a bit entre dois valores inteiros.|
|[OR](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Executa uma operação OR lógica em bits entre dois valores inteiros especificados como convertido em expressões binárias dentro de instruções Transact-SQL.|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |Nega uma entrada booliana.|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |Converte uma expressão de um tipo de dados em outro.|
|[BETWEEN](/sql/t-sql/language-elements/between-transact-sql)    |Especifica um intervalo a ser testado.|
|[IN](/sql/t-sql/language-elements/in-transact-sql)    |Determina se um valor especificado corresponde a qualquer valor em uma subconsulta ou uma lista.|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |Retorna um valor nulo se as duas expressões especificadas forem iguais.|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |Avalia os argumentos na ordem e retorna o valor atual da primeira expressão que inicialmente não avalia como NULL.|

Se os tipos de dados à esquerda e à direita de um operador forem diferentes, a conversão automática será executada de acordo com as regras especificadas aqui: [precedência de tipo de dados (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

A linguagem SQL de aceleração de consulta dá suporte apenas a um subconjunto muito pequeno dos tipos de dados discutidos neste artigo.  Consulte a seção [tipos de dados](#data-types) deste artigo.

### <a name="casts"></a>Conversões

A linguagem SQL de aceleração de consulta dá suporte ao operador CAST, de acordo com as regras aqui: [conversão de tipo de dados (mecanismo de banco de dados)](/sql/t-sql/data-types/data-type-conversion-database-engine).  

A linguagem SQL de aceleração de consulta dá suporte apenas a um pequeno subconjunto dos tipos de dados discutidos neste artigo.  Consulte a seção [tipos de dados](#data-types) deste artigo.

### <a name="string-functions"></a>Funções de cadeia de caracteres

A linguagem SQL de aceleração de consulta dá suporte às seguintes funções de cadeia de caracteres SQL padrão:

|Função|Descrição|
|--|--|
|CHAR_LENGTH    | Retorna o comprimento em caracteres da expressão de cadeia de caracteres, se a expressão de cadeia de caracteres for de um tipo de dados de caractere; caso contrário, retorna o comprimento em bytes da expressão de cadeia de caracteres (o menor inteiro não menor que o número de bits dividido por 8). (Essa função é a mesma que a função CHARACTER_LENGTH.)|
|CHARACTER_LENGTH    |Retorna o comprimento em caracteres da expressão de cadeia de caracteres, se a expressão de cadeia de caracteres for de um tipo de dados de caractere; caso contrário, retorna o comprimento em bytes da expressão de cadeia de caracteres (o menor inteiro não menor que o número de bits dividido por 8). (Essa função é a mesma que a função CHAR_LENGTH|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |Retorna uma expressão de caractere depois de converter para minúsculas os dados de caracteres em maiúsculas.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |Retorna uma expressão de caractere com dados de caractere em minúsculas convertidos em maiúsculas.|
|[SUBSTRING](/sql/t-sql/functions/substring-transact-sql)    |Retorna parte de uma expressão de caractere, binária, texto ou imagem no SQL Server.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |Remove o caractere de espaço (32) ou outros caracteres especificados do início e do fim de uma cadeia de caracteres.|
|LEADING    |Description|
|TRAILING    |Description|

Aqui estão alguns exemplos:

|Função|Exemplo|Resultado|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Funções de data

Há suporte para as seguintes funções de data SQL padrão:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Atualmente, convertemos todos os [formatos de data de IS08601 padrão](https://www.w3.org/TR/NOTE-datetime). 

#### <a name="date_add-function"></a>Função DATE_ADD

A linguagem SQL de aceleração de consulta dá suporte a ano, mês, dia, hora, minuto, segundo para a ``DATE_ADD`` função.

Exemplos:

' ' SQL DATE_ADD (datepart, quantidade, carimbo de data/hora) DATE_ADD (' minuto ', 1, conversão (' 2017-01-02T03:04:05.006 Z ' como carimbo de data/hora)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRAIR função

Para EXTRAção diferente da parte de data com suporte para a ``DATE_ADD`` função, a linguagem SQL de aceleração de consulta dá suporte a timezone_hour e timezone_minute como parte de data.

Exemplos:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>Função TO_STRING

Exemplos:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

Esta tabela descreve as cadeias de caracteres que você pode usar para especificar o formato de saída da ``TO_STRING`` função.

|Cadeia de formato    |Saída                               |
|-----------------|-------------------------------------|
|aa               |Ano no formato de 2 dígitos – 1999 como ' 99 '|
|s                |Ano no formato de 4 dígitos               |
|yyyy             |Ano no formato de 4 dígitos               |
|M                |Mês do ano – 1                    |
|MM               |Zero preenchido por mês – 01               |
|MMM              |Abbr. mês do ano – JAN            |
|MMMM             |Mês inteiro – maio                      |
|d                |Dia do mês (1-31)                  |
|dd               |Zero dia preenchido do mês (01-31)     |
|um                |AM ou PM                             |
|h                |Hora do dia (1-12)                   |
|hh               |Zero horas preenchidas dia OD (01-12)     |
|H                |Hora do dia (0-23)                   |
|HH               |Zero hora do dia preenchida (00-23)      |
|m                |Minuto de hora (0-59)                |
|mm               |Zero de hora preenchida (00-59)           |
|s                |Segundo de minutos (0-59)             |
|ss               |Nenhum segundos preenchido (00-59)          |
|S                |Fração de segundos (0,1-0,9)        |
|SS               |Fração de segundos (0,01-0,99)      |
|SSS              |Fração de segundos (0,001-0,999)    |
|X                |Deslocamento em horas                      |
|XX ou XXXX       |Deslocamento em horas e minutos (+ 0430)  |
|XXX ou XXXXX     |Deslocamento em horas e minutos (-07:00) |
|x                |Deslocamento em horas (7)                  |
|XX ou xxxx       |Deslocamento em hora e minuto (+ 0530)    |
|Xxx ou xxxxx     |Deslocamento em hora e minuto (+ 05:30)   |

#### <a name="to_timestamp-function"></a>Função TO_TIMESTAMP

Há suporte apenas para formatos IS08601.

Exemplos:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Você também pode usar a ``UTCNOW`` função para obter a hora do sistema.


## <a name="aggregate-expressions"></a>Expressões de agregação

Uma instrução SELECT pode conter uma ou mais expressões de projeção ou uma única expressão de agregação.  Há suporte para as seguintes expressões de agregação:

|Expression|Descrição|
|--|--|
|[CONTAGEM ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Retorna o número de registros que correspondem à expressão de predicado.|
|[CONTAGEM (expressão)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Retorna o número de registros para os quais a expressão é não nula.|
|[MÉDIA (expressão)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Retorna a média dos valores não nulos da expressão.|
|[MIN (expressão)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Retorna o valor não nulo mínimo da expressão.|
|[MAX (expressão](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Retorna o valor não nulo máximo da expressão.|
|[SUM (expressão)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Retorna a soma de todos os valores não nulos da expressão.|

### <a name="missing"></a>AUSÊNCIA

O ``IS MISSING`` operador é o único não padrão com suporte da linguagem SQL de aceleração de consulta.  Para dados JSON, se um campo estiver ausente de um registro de entrada específico, o campo de expressão ``IS MISSING`` será avaliado como o valor booliano true.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Descritores de tabela

Para dados CSV, o nome da tabela é sempre `BlobStorage` .  Por exemplo:

```sql
SELECT * FROM BlobStorage
```

Para dados JSON, há opções adicionais disponíveis:

```sql
SELECT * FROM BlobStorage[*].path
```

Isso permite consultas sobre subconjuntos de dados JSON.

Para consultas JSON, você pode mencionar o caminho em parte da cláusula FROM. Esses caminhos ajudarão a analisar o subconjunto de dados JSON. Esses caminhos podem fazer referência a valores de objeto e matriz JSON.

Vamos usar um exemplo para entender isso mais detalhadamente.

Estes são os nossos dados de exemplo:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Você pode estar interessado apenas no `warehouses` objeto JSON dos dados acima. O `warehouses` objeto é um tipo de matriz JSON, portanto, você pode mencionar isso na cláusula FROM. A consulta de exemplo pode ser semelhante a esta.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

A consulta obtém todos os campos, mas seleciona apenas a latitude.

Se você quisesse acessar apenas o `dimensions` valor do objeto JSON, poderia usar referir-se a esse objeto em sua consulta. Por exemplo:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Isso também limita o acesso a membros do `dimensions` objeto. Se você quiser acessar outros membros de campos JSON e valores internos de objetos JSON, poderá usar uma consulta como mostrado no exemplo a seguir:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage e BlobStorage [ \* ] fazem referência a todo o objeto. No entanto, se você tiver um caminho na cláusula FROM, precisará usar BlobStorage [ \* ]. Path

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. Split

Essa é uma forma especial da instrução SELECT, que está disponível somente para dados formatados em CSV.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Use essa instrução nos casos em que você deseja baixar e, em seguida, processar registros de dados CSV em lotes. Dessa forma, você pode processar registros em paralelo em vez de ter que baixar todos os registros de uma só vez. Essa instrução não retorna registros do arquivo CSV. Em vez disso, ele retorna uma coleção de tamanhos de lote. Você pode usar cada tamanho de lote para recuperar um lote de registros de dados. 

Use o parâmetro *split_size* para especificar o número de bytes que você deseja que cada lote contenha. Por exemplo, se você quiser processar apenas 10 MB de dados por vez, a instrução será parecida com esta: `SELECT sys.split(10485760)FROM BlobStorage` porque 10 MB é igual a 10.485.760 bytes. Cada lote conterá tantos registros quantos couberem nesses 10 MB. 

Na maioria dos casos, o tamanho de cada lote será um pouco maior do que o número especificado. Isso ocorre porque um lote não pode conter um registro parcial. Se o último registro em um lote iniciar antes do fim do limite, o lote será maior para que possa conter o registro completo. O tamanho do último lote provavelmente será menor do que o tamanho especificado.

>[!NOTE]
> O split_size deve ser pelo menos 10 MB (10485760).

## <a name="see-also"></a>Veja também

- [Aceleração de consulta Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Filtrar dados usando a aceleração de consulta Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md)