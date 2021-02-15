---
title: UDFs (funções definidas pelo usuário) no Azure Cosmos DB
description: Saiba mais sobre as funções definidas pelo usuário no Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546328"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>UDFs (funções definidas pelo usuário) no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A API do SQL fornece suporte para UDFs (funções definidas pelo usuário). Com UDFs escalares, você pode passar zero ou muitos argumentos e retornar um único resultado de argumento. A API verifica cada argumento para valores JSON válidos.  

## <a name="udf-use-cases"></a>Casos de uso de UDF

A API estende a sintaxe SQL para dar suporte à lógica de aplicativo personalizada usando UDFs. Você pode registrar UDFs com a API do SQL e referenciá-los em consultas SQL. Ao contrário de procedimentos armazenados e gatilhos, as UDFs são somente leitura.

Usando UDFs, você pode estender a linguagem de consulta de Azure Cosmos DB. UDFs são uma ótima maneira de expressar uma lógica de negócios complexa na projeção de uma consulta.

No entanto, recomendamos evitar UDFs quando:

- Uma função equivalente do [sistema](sql-query-system-functions.md) já existe no Azure Cosmos DB. As funções do sistema sempre usarão menos RU que o UDF equivalente.
- O UDF é o único filtro na `WHERE` cláusula da consulta. Os UDF não utilizam o índice, portanto, avaliar o UDF exigirá o carregamento de documentos. A combinação de predicados de filtro adicionais que usam o índice, em combinação com um UDF, na `WHERE` cláusula reduzirá o número de documentos processados pelo UDF.

Se você precisar usar o mesmo UDF várias vezes em uma consulta, deverá referenciar o UDF em uma [subconsulta](sql-query-subquery.md#evaluate-once-and-reference-many-times), permitindo que você use uma expressão de junção para avaliar o UDF uma vez, mas faça referência a ele muitas vezes.

## <a name="examples"></a>Exemplos

O exemplo a seguir registra um UDF em um contêiner de item no banco de dados Cosmos. O exemplo cria um UDF cujo nome é `REGEX_MATCH` . Ele aceita dois valores de cadeia de caracteres JSON `input` e `pattern` verifica se o primeiro corresponde ao padrão especificado no segundo usando a função do JavaScript `string.match()` .

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Agora, use essa UDF em uma projeção de consulta. Você deve qualificar UDFs com o prefixo que diferencia maiúsculas de minúsculas `udf.` ao chamá-los de dentro de consultas.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Os resultados são:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Você pode usar o UDF qualificado com o `udf.` prefixo dentro de um filtro, como no exemplo a seguir:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Os resultados são:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

Em essência, as UDFs são expressões escalares válidas que você pode usar em projeções e filtros.

Para expandir o poder das UDFs, observe outro exemplo com a lógica condicional:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

O exemplo a seguir exercita o UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Os resultados são:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Se as propriedades referenciadas pelos parâmetros UDF não estiverem disponíveis no valor JSON, o parâmetro será considerado como indefinido e a invocação de UDF será ignorada. Da mesma forma, se o resultado do UDF for indefinido, ele não será incluído no resultado.

Como mostram os exemplos anteriores, as UDFs integram o poder da linguagem JavaScript com a API do SQL. As UDFs fornecem uma rica interface programável para realizar um procedimento complexo, lógica condicional, com a ajuda de recursos internos de tempo de execução do JavaScript. A API do SQL fornece os argumentos para as UDFs de cada item de origem no estágio atual de cláusula WHERE ou SELECT do processamento. O resultado é incorporado diretamente no pipeline de execução geral. Em resumo, as UDFs são ótimas ferramentas para fazer uma lógica de negócios complexa como parte das consultas.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [Agregações](sql-query-aggregate-functions.md)