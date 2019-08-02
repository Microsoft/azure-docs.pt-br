---
title: Funções definidas pelo usuário (UDFs) no Azure Cosmos DB
description: Saiba mais sobre funções definidas pelo usuário no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342923"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Funções definidas pelo usuário (UDFs) no Azure Cosmos DB

A API do SQL fornece suporte para funções definidas pelo usuário (UDFs). Com UDFs escalares, você pode passar zero ou muitos argumentos e retornar um resultado único argumento. A API verifica cada argumento para valores JSON legais.  

A API estende a sintaxe SQL para dar suporte à lógica de aplicativo personalizado usando UDFs. Você pode registrar as UDFs com a API do SQL e referenciá-los em consultas SQL. Na verdade, as UDFs são projetadas de maneira especial para serem chamadas por consultas. Como resultado, as UDFs não tem acesso ao objeto de contexto, como outros tipos de JavaScript, como procedimentos armazenados e gatilhos. Consultas são somente leitura e podem executar em réplicas primárias ou secundárias. UDFs, ao contrário de outros tipos de JavaScript, são projetados para funcionar em réplicas secundárias.

O exemplo a seguir registra um UDF em um contêiner de itens no banco de dados do Cosmos DB. O exemplo cria um UDF cujo nome é `REGEX_MATCH`. Ele aceita dois valores de cadeia de caracteres JSON, `input` e `pattern`, e verifica se as correspondências primeiro o padrão especificado no segundo uso de JavaScript `string.match()` função.

## <a name="examples"></a>Exemplos

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

Agora, use esta UDF em uma projeção de consulta. Você deve qualificar UDFs com o prefixo que diferencia maiusculas de minúsculas `udf.` ao chamá-las de dentro de consultas.

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

Você pode usar a UDF qualificada com o `udf.` prefixo dentro de um filtro, como no exemplo a seguir:

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

Para expandir o poder das UDFs, ver outro exemplo com lógica condicional:

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

O exemplo a seguir aplica a UDF:

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

Se as propriedades referidas aos UDFs parâmetros não estão disponíveis no valor JSON, o parâmetro é considerado indefinido e a invocação das UDFs é ignorada. Da mesma forma, se o resultado das UDFs for indefinido, ele não está incluído no resultado.

Como mostram os exemplos anteriores, as UDFs integram o poder da linguagem JavaScript com a API do SQL. UDFs fornecem uma rica interface programável para complexas realizar procedimentos condicional lógicas com a Ajuda de recursos internos de tempo de execução do JavaScript. A API do SQL fornece os argumentos às UDFs para cada item de origem na cláusula SELECT ou onde atual estágio do processamento. O resultado é incorporado perfeitamente no pipeline de execução geral. Em resumo, as UDFs são ótimas ferramentas para realizar a lógica de negócios complexos como parte das consultas.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções do sistema](sql-query-system-functions.md)
- [agregações](sql-query-aggregates.md)