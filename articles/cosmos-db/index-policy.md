---
title: Políticas de indexação no Azure Cosmos DB
description: Saiba como configurar e alterar a política de indexação padrão para indexação automática e melhor desempenho no Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: thweiss
ms.openlocfilehash: 01e3e1f1c9bffee0604de1260e8e466f5b1d229d
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467871"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Políticas de indexação no Azure Cosmos DB

No Azure Cosmos DB, cada contêiner tem uma política de indexação que determina como os itens do contêiner devem ser indexados. A política de indexação padrão para contêineres recém-criados indexa cada propriedade de cada item, impondo índices de intervalo para qualquer cadeia de caracteres ou número e índices espaciais para qualquer objeto geojson do tipo Point. Isso permite que você obtenha alto desempenho de consulta sem precisar pensar na indexação e no gerenciamento de índice antecipadamente.

Em algumas situações, talvez você queira substituir esse comportamento automático para atender melhor às suas necessidades. Você pode personalizar a política de indexação de um contêiner definindo seu *modo*de indexação e incluir ou excluir os *caminhos de propriedade*.

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo se aplica somente à API do SQL (Core) do Azure Cosmos DB.

## <a name="indexing-mode"></a>Modo de indexação

O Azure Cosmos DB dá suporte a dois modos de indexação:

- **Consistentee**: Se a política de indexação de um contêiner estiver definida como consistente, o índice será atualizado de forma síncrona à medida que você criar, atualizar ou excluir itens. Isso significa que a consistência de suas consultas de leitura será a [consistência configurada para a conta](consistency-levels.md).

- **Nenhum**: Se a política de indexação de um contêiner estiver definida como nenhum, a indexação será efetivamente desabilitada nesse contêiner. Isso é normalmente usado quando um contêiner é usado como um repositório de chave-valor puro sem a necessidade de índices secundários. Ele também pode ajudar a acelerar as operações de inserção em massa.

## <a name="including-and-excluding-property-paths"></a>Incluindo e excluindo caminhos de propriedade

Uma política de indexação personalizada pode especificar caminhos de propriedade que são explicitamente incluídos ou excluídos da indexação. Ao otimizar o número de caminhos que são indexados, você pode diminuir a quantidade de armazenamento usada pelo seu contêiner e melhorar a latência de operações de gravação. Esses caminhos são definidos seguindo [o método descrito na seção visão geral](index-overview.md#from-trees-to-property-paths) da indexação com as seguintes adições:

- um caminho que leva a um valor escalar (cadeia de caracteres ou número) termina com`/?`
- os elementos de uma matriz são abordados em `/[]` conjunto por meio da `/0`notação (em vez de, `/1` etc.)
- o `/*` curinga pode ser usado para corresponder qualquer elemento abaixo do nó

Dando o mesmo exemplo novamente:

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- o `headquarters`caminho `employees` do é`/headquarters/employees/?`
- o `locations`' `country` caminho ' é`/locations/[]/country/?`
- o caminho para qualquer coisa `headquarters` em é`/headquarters/*`

Quando um caminho é incluído explicitamente na política de indexação, ele também precisa definir quais tipos de índice devem ser aplicados a esse caminho e para cada tipo de índice, o tipo de dados ao qual este índice se aplica:

| Tipo de índice | Tipos de dados de destino permitidos |
| --- | --- |
| Intervalo | Cadeia de caracteres ou número |
| Espacial | Ponto, LineString ou polígono |

Por exemplo, `/headquarters/employees/?` poderíamos incluir o caminho e especificar que um `Range` índice deve ser aplicado nesse caminho para ambos os `String` valores e `Number` .

### <a name="includeexclude-strategy"></a>Estratégia de inclusão/exclusão

Qualquer política de indexação deve incluir o caminho `/*` raiz como um caminho incluído ou excluído.

- Inclua o caminho raiz para excluir seletivamente os caminhos que não precisam ser indexados. Essa é a abordagem recomendada, pois ela permite que Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo.
- Exclua o caminho raiz para incluir seletivamente os caminhos que precisam ser indexados.

- Para caminhos com caracteres regulares que incluem: caracteres alfanuméricos e _ (sublinhado), você não precisa escapar da cadeia de caracteres do caminho em volta de aspas duplas (por exemplo, "/Path/?"). Para caminhos com outros caracteres especiais, você precisa escapar da cadeia de caracteres de caminho em aspas duplas (por exemplo\", "/\"Path-ABC/?"). Se você espera caracteres especiais em seu caminho, pode escapar de cada caminho para segurança. Funcionalmente não faz nenhuma diferença se você escapa de todos os caminhos, e não apenas aqueles com caracteres especiais.

- A propriedade do sistema "ETag" é excluída da indexação por padrão, a menos que a ETag seja adicionada ao caminho incluído para indexação.

Consulte [esta seção](how-to-manage-indexing-policy.md#indexing-policy-examples) para obter exemplos de política de indexação.

## <a name="composite-indexes"></a>Índices compostos

Consulta se `ORDER BY` duas ou mais propriedades exigem um índice composto. Atualmente, os índices compostos são utilizados apenas por várias `ORDER BY` consultas. Por padrão, nenhum índice composto é definido, portanto, você deve [Adicionar índices compostos](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) conforme necessário.

Ao definir um índice composto, você especifica:

- Dois ou mais caminhos de propriedade. A sequência na qual os caminhos de propriedade são definidos é importante.
- A ordem (crescente ou decrescente).

As seguintes considerações são usadas ao usar índices compostos:

- Se os caminhos do índice composto não corresponderem à sequência das propriedades na cláusula ORDER BY, o índice composto não poderá dar suporte à consulta

- A ordem dos caminhos de índice composto (crescente ou decrescente) também deve corresponder à ordem na cláusula ORDER BY.

- O índice composto também dá suporte a uma cláusula ORDER BY com a ordem oposta em todos os caminhos.

Considere o exemplo a seguir em que um índice composto é definido nas propriedades a, b e c:

| **Índice composto**     | **Consulta `ORDER BY` de exemplo**      | **Com suporte do índice?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

Você deve personalizar a política de indexação para que possa atender a `ORDER BY` todas as consultas necessárias.

## <a name="modifying-the-indexing-policy"></a>Modificando a política de indexação

A política de indexação de um contêiner pode ser atualizada a qualquer momento [usando o portal do Azure ou um dos SDKs com suporte](how-to-manage-indexing-policy.md). Uma atualização para a política de indexação dispara uma transformação do índice antigo para o novo, que é executado online e em vigor (portanto, nenhum espaço de armazenamento adicional é consumido durante a operação). O índice antigo da política é transformado com eficiência na nova política sem afetar a disponibilidade de gravação ou a taxa de transferência provisionada no contêiner. A transformação de índice é uma operação assíncrona e o tempo necessário para concluir depende da taxa de transferência provisionada, do número de itens e de seu tamanho. 

> [!NOTE]
> Enquanto a reindexação está em andamento, as consultas podem não retornar todos os resultados correspondentes e farão isso sem retornar erros. Isso significa que os resultados da consulta podem não ser consistentes até que a transformação do índice seja concluída. É possível acompanhar o progresso da transformação de índice [usando um dos SDKs](how-to-manage-indexing-policy.md).

Se o modo da nova política de indexação for definido como consistente, nenhuma outra alteração de política de indexação poderá ser aplicada enquanto a transformação do índice estiver em andamento. Uma transformação índice em execução pode ser cancelada definindo o modo da política de indexação como None (o que descartará imediatamente o índice).

## <a name="indexing-policies-and-ttl"></a>Políticas de indexação e TTL

O [recurso TTL (vida útil)](time-to-live.md) requer que a indexação esteja ativa no contêiner em que está ativada. Isso significa que:

- Não é possível ativar o TTL em um contêiner em que o modo de indexação está definido como nenhum,
- Não é possível definir o modo de indexação como None em um contêiner em que TTL está ativado.

Para cenários em que nenhum caminho de propriedade precisa ser indexado, mas o TTL é necessário, você pode usar uma política de indexação com:

- um modo de indexação definido como consistente e
- nenhum caminho incluído e
- `/*`como o único caminho excluído.

## <a name="obsolete-attributes"></a>Atributos obsoletos

Ao trabalhar com políticas de indexação, você pode encontrar os seguintes atributos que agora são obsoletos:

- `automatic`é um booliano definido na raiz de uma política de indexação. Ele agora é ignorado e pode ser definido como `true`, quando a ferramenta que você está usando exigir.
- `precision`é um número definido no nível de índice para caminhos incluídos. Ele agora é ignorado e pode ser definido como `-1`, quando a ferramenta que você está usando exigir.
- `hash`é um tipo de índice que agora é substituído pelo tipo de intervalo.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

- [Visão geral de indexação](index-overview.md)
- [Como gerenciar a política de indexação](how-to-manage-indexing-policy.md)
