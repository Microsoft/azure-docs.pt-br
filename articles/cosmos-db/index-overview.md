---
title: Indexação no Azure Cosmos DB
description: Entenda como a indexação funciona em Azure Cosmos DB, tipos diferentes de índices, como intervalos, espaciais e índices compostos com suporte.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: b7349a08b93810dcc3befd6058302d6c4573ab8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019203"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Gerenciar indexação no Azure Cosmos DB – Visão geral
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O Azure Cosmos DB é um banco de dados independente de esquema que permite fazer uma iteração no aplicativo sem a necessidade de gerenciamento de índices ou esquema. Por padrão, o Azure Cosmos DB indexa automaticamente cada propriedade para todos os itens no [contêiner](account-databases-containers-items.md#azure-cosmos-containers) sem precisar definir esquema nem configurar índices secundários.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa os dados e como ele usa índices para melhorar o desempenho da consulta. É recomendável ler esta seção antes de explorar a personalização de [políticas de indexação](index-policy.md).

## <a name="from-items-to-trees"></a>De itens para árvores

Sempre que um item é armazenado em um contêiner, seu conteúdo é projetado como um documento JSON e, depois, convertido em uma representação de árvore. Isso significa que todas as propriedades desse item são representadas como um nó em uma árvore. Um pseudo nó raiz é criado como um pai para todas as propriedades de primeiro nível do item. Os nós folha contêm os valores escalares reais que um item transporta.

Por exemplo, considere este item:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Ele seria representado pela seguinte árvore:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="O item anterior representado como uma árvore" border="false":::

Observe como as matrizes são codificadas na árvore: toda entrada em uma matriz obtém um nó intermediário rotulado com o índice dessa entrada na matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>De árvores para caminhos de propriedade

O motivo pelo qual o Azure Cosmos DB transforma itens em árvores é porque permite que as propriedades sejam referenciadas por seus caminhos nessas árvores. Para obter o caminho para uma propriedade, podemos percorrer a árvore do nó raiz para essa propriedade e concatenar os rótulos de cada nó atravessado.

Vejamos os caminhos para cada propriedade do item de exemplo descrito acima:

- /Locations/0/Country: "Alemanha"
- /Locations/0/City: "Berlim"
- /Locations/1/Country: "França"
- /Locations/1/City: "Paris"
- /Headquarters/Country: "Bélgica"
- /Headquarters/Employees: 250
- /Exports/0/City: "Moscou"
- /Exports/1/City: "Atenas"

Quando um item é gravado, o Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e seu valor correspondente.

## <a name="types-of-indexes"></a><a id="index-types"></a>Tipos de índices

O Azure Cosmos DB atualmente dá suporte a três tipos de índices. Você pode configurar esses tipos de índice ao definir a política de indexação.

### <a name="range-index"></a>Índice de intervalo

O índice de **intervalo** se baseia em uma estrutura ordenada de árvore. O tipo de índice de intervalo é usado para:

- Consultas de igualdade:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Correspondência de igualdade em um elemento de matriz
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Consultas de intervalo:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funciona para `>`, `<`, `>=`, `<=`, `!=`)

- Verificando a presença de uma propriedade:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Funções do sistema de cadeia de caracteres:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- Consultas `ORDER BY`:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- Consultas `JOIN`:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Os índices de intervalo podem ser usados em valores escalares (cadeia de caracteres ou número). A política de indexação padrão para contêineres recém-criados impõe os índices de intervalo para qualquer cadeia de caracteres ou número. Para saber como configurar índices de intervalo, consulte [exemplos de política de indexação de intervalo](how-to-manage-indexing-policy.md#range-index)

### <a name="spatial-index"></a>Índice espacial

Os índices **espaciais** permitem consultas eficientes em objetos geoespaciais, como pontos, linhas, polígonos e multipolígono. Essas consultas usam as palavras-chave ST_DISTANCE, ST_WITHIN, ST_INTERSECTS. Veja a seguir alguns exemplos que usam o tipo de índice espacial:

- Consultas de distância geoespaciais:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Geoespacial em consultas:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Consultas de interseção geoespacial:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Os índices espaciais podem ser usados em objetos do [GeoJSON](./sql-query-geospatial-intro.md) formatados corretamente. Pontos, LineStrings, polígonos e multipolígonos atualmente têm suporte. Para usar esse tipo de índice, defina usando a `"kind": "Range"` propriedade ao configurar a política de indexação. Para saber como configurar índices espaciais, consulte [exemplos de política de indexação espacial](how-to-manage-indexing-policy.md#spatial-index)

### <a name="composite-indexes"></a>Índices compostos

Os índices **compostos** aumentam a eficiência quando você está executando operações em vários campos. O tipo de índice composto é usado para:

- Consultas `ORDER BY` em várias propriedades:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Consultas com um filtro e `ORDER BY`. Essas consultas poderão utilizar um índice composto se a propriedade de filtro for adicionada à cláusula `ORDER BY`.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Consultas com um filtro em duas ou mais propriedades em que pelo menos uma propriedade é um filtro de igualdade

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Desde que um predicado de filtro use um dos tipos de índice, o mecanismo de consulta avaliará isso primeiro antes de verificar o restante. Por exemplo, se você tiver uma consulta SQL como `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Usando o índice, a consulta acima primeiro filtrará as entradas em que firstName = "Andrew". Em seguida, ela passa todas as entradas firstName = "Andrew" por meio de um pipeline subsequente para avaliar o predicado de filtro CONTAINS.

* Você pode acelerar as consultas e evitar verificações de contêiner completas ao usar funções que não usam o índice (por exemplo, CONTAINS). Para isso, adicione predicados de filtro adicionais que usam o índice. A ordem das cláusulas de filtro não é importante. O mecanismo de consulta descobrirá quais predicados são mais seletivos e executará a consulta de acordo.

Para saber como configurar índices compostos, consulte [exemplos de política de indexação composta](how-to-manage-indexing-policy.md#composite-index)

## <a name="querying-with-indexes"></a>Consultar com índices

Os caminhos extraídos ao indexar dados facilitam a pesquisa do índice durante o processamento de uma consulta. Através da correspondência da cláusula `WHERE` de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado de consulta muito rapidamente.

Por exemplo, considere a consulta abaixo: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. O predicado de consulta (filtragem em itens, onde qualquer local tenha "França" como seu país/região) corresponderia ao caminho realçado em vermelho abaixo:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Correspondência de um caminho específico em uma árvore" border="false":::

> [!NOTE]
> Uma cláusula `ORDER BY` ordenada por uma única propriedade *sempre* precisa de um índice de intervalo e falhará se o caminho que ele referencia não tiver um. Da mesma forma, uma consulta `ORDER BY` ordenada por várias propriedades *sempre* precisa de um índice composto.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)