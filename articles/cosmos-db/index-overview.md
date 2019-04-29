---
title: Indexação no Azure Cosmos DB
description: Entenda como funciona a indexação no Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 3bb8913725acf04f71aba8b4c4350235f2c44dfb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61051849"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indexação no Azure Cosmos DB – visão geral

O Azure Cosmos DB é um banco de dados independente de esquema permite que você itere em seu aplicativo sem ter que lidar com o gerenciamento de esquema ou índice. Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades para todos os itens em sua [contêiner](databases-containers-items.md#azure-cosmos-containers) sem a necessidade de definir qualquer esquema ou índices secundários de configurar.

O objetivo deste artigo é explicar como o Azure Cosmos DB indexa dados e como ele usa índices para melhorar o desempenho da consulta. É recomendável passar por essa seção antes de explorar como personalizar [políticas de indexação](index-policy.md).

## <a name="from-items-to-trees"></a>De itens de árvores

Sempre que um item é armazenado em um contêiner, seu conteúdo é projetado como um documento JSON, então convertido em uma representação em árvore. Isso significa que todas as propriedades de um item é representada como um nó em uma árvore. Um nó de raiz de pseudo é criado como um pai para todas as propriedades de primeiro nível do item. Os nós folha contêm os valores escalares reais realizados por um item.

Por exemplo, considere este item:

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

Ele seria representado pela árvore do seguinte:

![O item anterior, representado como uma árvore](./media/index-overview/item-as-tree.png)

Observe como as matrizes são codificadas na árvore: cada entrada em uma matriz obtém um nó intermediário rotulado com o índice da entrada de dentro da matriz (0, 1 etc.).

## <a name="from-trees-to-property-paths"></a>Das árvores de caminhos de propriedade

O motivo por que o Azure Cosmos DB transforma itens em árvores é porque ela permite que propriedades sejam referenciadas por seus caminhos dentro essas árvores. Para obter o caminho para uma propriedade, podemos percorrer a árvore do nó raiz para essa propriedade e concatenar os rótulos de cada nó atravessado.

Aqui estão os caminhos para cada propriedade do item de exemplo descrito acima:

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

Quando um item é escrito, o Azure Cosmos DB indexa efetivamente o caminho de cada propriedade e seu valor correspondente.

## <a name="index-kinds"></a>Tipos de índice

Atualmente, o Azure Cosmos DB dá suporte a dois tipos de índices:

O **intervalo** tipo de índice é usado para:

- consultas de igualdade: `SELECT * FROM container c WHERE c.property = 'value'`
- consultas de intervalo: `SELECT * FROM container c WHERE c.property > 'value'` (funciona para `>`, `<`, `>=`, `<=`, `!=`)
- `ORDER BY` consultas: `SELECT * FROM container c ORDER BY c.property`
- `JOIN` consultas: `SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'`

Índices de intervalo podem ser usados em valores escalares (cadeia de caracteres ou número).

O **espacial** tipo de índice é usado para:

- consultas de distância geoespaciais: `SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40`
- geoespacial dentro de consultas: `SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })`

Índices espaciais podem ser usados em corretamente formatado [GeoJSON](geospatial.md) objetos. Atualmente, há suporte para pontos, LineStrings e polígonos.

## <a name="querying-with-indexes"></a>Consultar com índices

Os caminhos extraídos do durante a indexação de dados facilitam a pesquisar o índice quando uma consulta de processamento. Correspondendo a `WHERE` cláusula de uma consulta com a lista de caminhos indexados, é possível identificar os itens que correspondem ao predicado de consulta muito rapidamente.

Por exemplo, considere a seguinte consulta: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. O predicado da consulta (filtragem de itens, onde qualquer local tem "França" como seu país) seria corresponder ao caminho realçado em vermelho abaixo:

![Um caminho específico dentro de uma árvore de correspondência](./media/index-overview/matching-path.png)

> [!NOTE]
> Uma `ORDER BY` cláusula *sempre* precisa de um intervalo de índice e falhará se o caminho que faz referência a ele não tiver um.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos seguintes artigos:

- [Política de indexação](index-policy.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
