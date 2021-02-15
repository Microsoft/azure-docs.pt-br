---
title: Solucionar problemas de consulta ao usar o Azure Cosmos DB
description: Saiba como identificar, diagnosticar e solucionar problemas de consulta do SQL do Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/02/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 6875fc53a651b89fcfe88d3217ff86bd21204f6c
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524273"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Solucionar problemas de consulta ao usar o Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo percorre uma abordagem geral recomendada para a solução de problemas de consultas no Azure Cosmos DB. Embora você não deva considerar as etapas descritas neste artigo uma defesa completa contra possíveis problemas de consulta, incluímos as dicas de desempenho mais comuns aqui. Você deve usar este artigo como um local de início para solucionar problemas de consultas lentas ou dispendiosas na API de núcleo do Azure Cosmos DB Core (SQL). Você também pode usar os [logs de diagnóstico](cosmosdb-monitor-resource-logs.md) para identificar consultas que são lentas ou que consomem quantidades significativas de taxa de transferência. Se você estiver usando a API do Azure Cosmos DB para MongoDB, deverá usar [a API do Azure Cosmos DB para o guia de solução de problemas de consulta do MongoDB](mongodb-troubleshoot-query.md)

As otimizações de consulta no Azure Cosmos DB são amplamente categorizadas da seguinte maneira:

- Otimizações que reduzem o preço de RU (unidade de solicitação) da consulta
- Otimizações que apenas reduzem a latência

Se você reduzir a carga de RU de uma consulta, normalmente diminuirá a latência também.

Este artigo fornece exemplos que você pode recriar usando o conjunto de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Problemas comuns do SDK

Antes de ler este guia, é útil considerar problemas comuns do SDK que não estão relacionados ao mecanismo de consulta.

- Siga estas [dicas de desempenho do SDK](performance-tips.md).
    - [Guia de solução de problemas do SDK do .NET](troubleshoot-dot-net-sdk.md)
    - [Guia de solução de problemas do SDK do Java](troubleshoot-java-sdk-v4-sql.md)
- O SDK permite definir um `MaxItemCount` para suas consultas, mas você não pode especificar uma contagem mínima de itens.
    - O código deve lidar com qualquer tamanho de página, de zero a `MaxItemCount`.
- Às vezes, as consultas podem ter páginas vazias mesmo quando há resultados em uma página futura. Os motivos para isso podem ser:
    - O SDK pode estar fazendo várias chamadas de rede.
    - A consulta pode estar demorando um longo tempo para recuperar os documentos.
- Todas as consultas têm um token de continuação que permitirá que a consulta continue. Certifique-se de esvaziar a consulta completamente. Saiba mais sobre como [lidar com várias páginas de resultados](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Obter métricas de consulta

Quando você otimiza uma consulta no Azure Cosmos DB, a primeira etapa é sempre [obter as métricas de consulta](profile-sql-api-query.md) para sua consulta. Essas métricas também estão disponíveis por meio do portal do Azure. Depois de executar a consulta no Data Explorer, as métricas de consulta ficam visíveis ao lado da guia **Resultados**:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Como obter métricas de consulta" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Depois de obter as métricas de consulta, compare a **Contagem de Documentos Recuperados** com a **Contagem de Documentos de Saída** para sua consulta. Use essa comparação para identificar as seções relevantes que serão examinadas neste artigo.

A **Contagem de Documentos Recuperados** é o número de documentos que o mecanismo de consulta precisava carregar. A **Contagem de Documentos de Saída** é o número de documentos que foram necessários para os resultados da consulta. Se a **Contagem de Documentos Recuperada** for significativamente maior do que a **Contagem de Documentos de Saída**, haverá pelo menos uma parte da sua consulta que não conseguiu usar um índice e que precisava fazer uma verificação.

Veja as seções a seguir para entender as otimizações de consulta relevantes para seu cenário.

### <a name="querys-ru-charge-is-too-high"></a>O preço de RU da consulta é muito alto

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>A Contagem de Documentos Recuperados é significativamente maior que a Contagem de Documentos de Saída

- [Incluir os caminhos necessários na política de indexação.](#include-necessary-paths-in-the-indexing-policy)

- [Entender quais funções do sistema usam o índice.](#understand-which-system-functions-use-the-index)

- [Melhorar a execução da função do sistema de cadeia de caracteres.](#improve-string-system-function-execution)

- [Entender quais consultas de agregação usam o índice.](#understand-which-aggregate-queries-use-the-index)

- [Otimizar consultas que têm um filtro e uma cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Otimizar expressões JOIN usando uma subconsulta.](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>A Contagem de Documentos Recuperados é aproximadamente igual à Contagem de Documentos de Saída

- [Minimizar consultas entre partições.](#minimize-cross-partition-queries)

- [Otimizar consultas que têm filtros em várias propriedades.](#optimize-queries-that-have-filters-on-multiple-properties)

- [Otimizar consultas que têm um filtro e uma cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>O preço de RU da consulta é aceitável, mas a latência ainda é muito alta

- [Aprimorar a proximidade.](#improve-proximity)

- [Aumentar a taxa de transferência provisionada.](#increase-provisioned-throughput)

- [Aumentar MaxConcurrency.](#increase-maxconcurrency)

- [Aumentar MaxBufferedItemCount.](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas em que a Contagem de Documentos Recuperados excede a Contagem de Documentos de Saída

 A **Contagem de Documentos Recuperados** é o número de documentos que o mecanismo de consulta precisava carregar. A **Contagem de Documentos de Saída** é o número de documentos retornados pela consulta. Se a **Contagem de Documentos Recuperada** for significativamente maior do que a **Contagem de Documentos de Saída**, haverá pelo menos uma parte da sua consulta que não conseguiu usar um índice e que precisava fazer uma verificação.

Veja um exemplo de consulta de verificação que não foi totalmente servida pelo índice:

Consulta:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Métricas de consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

A **Contagem de Documentos Recuperados** (60.951) é significativamente maior do que a **Contagem de Documentos de Saída** (7), o que implica que essa consulta resultou em uma verificação de documento. Nesse caso, a função do sistema [UPPER()](sql-query-upper.md) não usa um índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Incluir os caminhos necessários na política de indexação

Sua política de indexação deve abranger todas as propriedades incluídas em cláusulas `WHERE`, cláusulas `ORDER BY`, `JOIN` e a maioria das funções do sistema. Os caminhos desejados especificados na política de índice devem corresponder às propriedades nos documentos JSON.

> [!NOTE]
> As propriedades na política de indexação do Azure Cosmos DB diferenciam maiúsculas de minúsculas

Se você executar a seguinte consulta simples no conjunto de dados de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json), você observará um preço de RU muito mais baixo quando a propriedade na cláusula `WHERE` for indexada:

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Política de indexação:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Preço da RU:** 409,51 RUs

#### <a name="optimized"></a>Otimizado

Política de indexação atualizada:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Preço da RU:** 2,98 RUs

Você pode adicionar propriedades à política de indexação a qualquer momento, sem efeito na disponibilidade de gravação ou leitura. Você pode [acompanhar o progresso da transformação do índice](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Entender quais funções do sistema usam o índice

A maioria das funções do sistema usa índices. Aqui está uma lista de algumas funções de cadeia de caracteres comuns que usam índices:

- StartsWith
- Contém
- RegexMatch
- Esquerda
- Substring-mas somente se a primeira num_expr for 0

Veja a seguir algumas funções comuns do sistema que não usam o índice e que devem carregar cada documento quando usadas em uma `WHERE` cláusula:

| **Função do sistema**                     | **Ideias para otimização**             |
| --------------------------------------- |------------------------------------------------------------ |
| Superior/inferior                         | Em vez de usar a função do sistema para normalizar dados para comparações, normalize o uso de maiúsculas e minúsculas após a inserção. Uma consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` se torna ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Calcule a hora atual antes da execução da consulta e use esse valor de cadeia de caracteres na `WHERE` cláusula. |
| Funções matemáticas (não agregadas) | Se você precisar computar um valor frequentemente em sua consulta, considere armazenar o valor como uma propriedade em seu documento JSON. |

Quando usado na `SELECT` cláusula, funções ineficientes do sistema não afetarão como as consultas podem usar índices.

### <a name="improve-string-system-function-execution"></a>Melhorar a execução da função do sistema de cadeia de caracteres

Para algumas funções do sistema que usam índices, você pode melhorar a execução da consulta adicionando uma `ORDER BY` cláusula à consulta. 

Mais especificamente, qualquer função do sistema cuja carga de RU aumenta à medida que a cardinalidade da propriedade aumenta pode se beneficiar da existência da `ORDER BY` consulta. Essas consultas fazem uma verificação de índice, portanto, ter os resultados da consulta classificados pode tornar a consulta mais eficiente.

Essa otimização pode melhorar a execução para as seguintes funções do sistema:

- StartsWith (em que não diferencia maiúsculas de minúsculas = verdadeiro)
- StringEquals (em que não diferencia maiúsculas de minúsculas = verdadeiro)
- Contém
- RegexMatch
- EndsWith

Por exemplo, considere a consulta abaixo com `CONTAINS` . `CONTAINS` usará índices, mas às vezes, mesmo depois de adicionar o índice relevante, você ainda poderá observar uma cobrança de RU muito alta ao executar a consulta abaixo.

Consulta original:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

Você pode melhorar a execução da consulta adicionando `ORDER BY` :

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

A mesma otimização pode ajudar nas consultas com filtros adicionais. Nesse caso, é melhor também adicionar propriedades com filtros de igualdade à `ORDER BY` cláusula.

Consulta original:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

Você pode melhorar a execução da consulta adicionando `ORDER BY` e [um índice composto](index-policy.md#composite-indexes) para (c.Name, c. cidade):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Entender quais consultas de agregação usam o índice

Na maioria dos casos, as funções do sistema de agregação no Azure Cosmos DB usarão o índice. No entanto, dependendo dos filtros ou cláusulas adicionais em uma consulta de agregação, o mecanismo de consulta poderá ser necessário para carregar um grande número de documentos. Normalmente, o mecanismo de consulta aplicará primeiro os filtros de igualdade e de intervalo. Depois de aplicar esses filtros, o mecanismo de consulta poderá avaliar filtros adicionais e recorrer ao carregamento de documentos restantes para computar a agregação, se necessário.

Por exemplo, considerando essas duas consultas de exemplo, a consulta com um filtro de função de sistema de igualdade e um de `CONTAINS` geralmente será mais eficiente do que uma consulta com apenas um filtro de função de sistema `CONTAINS`. Isso ocorre porque o filtro de igualdade é aplicado primeiro e usa o índice antes que os documentos precisem ser carregados para o filtro de `CONTAINS` mais custoso.

Consulta apenas com o filtro de `CONTAINS` – preço de RU mais alto:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Consulta com o filtro de igualdade e o filtro de `CONTAINS` – preço de RU mais baixo:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

Aqui estão exemplos adicionais de consultas de agregação que não usarão totalmente o índice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Consultas com funções do sistema que não usam o índice

Você deve consultar a [página da função de sistema relevante](sql-query-system-functions.md) para ver se ela usa o índice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Consultas de agregação com UDFs (funções definidas pelo usuário)

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Consultas com GROUP BY

O preço de RU de consultas com `GROUP BY` aumentará conforme a cardinalidade das propriedades na cláusula `GROUP BY` aumentar. Na consulta abaixo, por exemplo, o preço de RU da consulta aumentará conforme o número de descrições exclusivas aumentar.

O preço de RU de uma função de agregação com uma cláusula `GROUP BY` será maior do que o preço de RU de uma função de agregação sozinha. Neste exemplo, o mecanismo de consulta deve carregar todos os documentos que correspondam ao filtro de `c.foodGroup = "Sausages and Luncheon Meats"` para o qual é esperado que o preço de RU seja alto.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Se você planeja executar com frequência as mesmas consultas de agregação, pode ser mais eficiente criar uma exibição materializada em tempo real com o [feed de alterações do Azure Cosmos DB](change-feed.md) do que executar consultas individuais.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Otimizar consultas que têm um filtro e uma cláusula ORDER BY

Embora as consultas que têm um filtro e uma cláusula `ORDER BY` normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas de um índice composto. Além de modificar a política de indexação, você deve adicionar todas as propriedades no índice composto à cláusula `ORDER BY`. Essa alteração na consulta garantirá que ela use o índice composto.  Você pode observar o impacto executando uma consulta no conjunto de dados de [nutrição](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json):

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Política de indexação:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Preço da RU:** 44,28 RUs

#### <a name="optimized"></a>Otimizado

Consulta atualizada (inclui ambas as propriedades na cláusula `ORDER BY`):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Política de indexação atualizada:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Preço da RU:** 8,86 RUs

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Otimizar expressões JOIN usando uma subconsulta

Subconsultas de vários valores podem otimizar expressões `JOIN` enviando predicados por push após cada expressão select-many em vez de todas as junções cruzadas na cláusula `WHERE`.

Considere esta consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Preço da RU:** 167,62 RUs

Para essa consulta, o índice corresponderá a qualquer documento que tenha uma marca com o nome `infant formula`, `nutritionValue` maior que 0 e `amount` maior que 1. A expressão `JOIN` aqui executará o produto cruzado de todos os itens de matrizes de porção, nutrientes e marcas para cada documento correspondente antes de qualquer filtro ser aplicado. A cláusula `WHERE` aplicará o predicado de filtro em cada tupla de `<c, t, n, s>`.

Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ele será expandido para tuplas de 1 x 10 x 10 x 10 (ou seja, 1.000). O uso de subconsultas aqui pode ajudar a filtrar itens de matriz unidos antes de se juntar à próxima expressão.

Essa consulta é equivalente à anterior, mas usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Preço da RU:** 22,17 RUs

Suponha que apenas um item na matriz de marcas corresponda ao filtro e que haja cinco itens tanto para as matrizes de nutrientes quanto para as matrizes de porção. As expressões `JOIN` se expandirão para 1 x 1 x 5 x 5 = 25 itens, em oposição a 1.000 itens na primeira consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas em que a Contagem de Documentos Recuperados é igual à Contagem de Documentos de Saída

Se a **Contagem de Documentos Recuperados** for aproximadamente igual à **Contagem de Documentos de Saída**, o mecanismo de consulta não precisará verificar muitos documentos desnecessários. Para muitas consultas, como as que usam a palavra-chave `TOP`, a **Contagem de Documentos Recuperados** pode a exceder **Contagem de Documentos de Saída** em 1. Você não precisa se preocupar com isso.

### <a name="minimize-cross-partition-queries"></a>Minimizar consultas entre partições

O Azure Cosmos DB usa [particionamento](partitioning-overview.md) para escalar contêineres individuais à medida que as necessidades de armazenamento de dados e da Unidade de Solicitação aumentam. Cada partição física tem um índice separado e independente. Se a consulta tiver um filtro de igualdade que corresponda à chave de partição do contêiner, você precisará verificar apenas o índice da partição relevante. Essa otimização reduz o número total de RUs que a consulta requer.

Se você tiver um grande número de RUs provisionados (mais de 30.000) ou uma grande quantidade de dados armazenados (mais de aproximadamente 100 GB), provavelmente terá um contêiner grande o suficiente para ver uma redução significativa nos preços de RU de consulta.

Por exemplo, se você criar um contêiner com a chave de partição foodGroup, as seguintes consultas precisarão verificar apenas uma partição física:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

As consultas que têm um filtro `IN` com a chave de partição verificarão apenas as partições físicas relevantes e não de "fan-out":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

As consultas que têm filtros de intervalo na chave de partição ou que não têm filtros na chave de partição precisarão "realizar fan-out" e verificar o índice de cada partição física para obter os resultados:

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Otimizar consultas que têm filtros em várias propriedades

Embora as consultas que têm um filtro em várias propriedades normalmente usem um índice de intervalo, elas serão mais eficientes se puderem ser servidas de um índice composto. Para pequenas quantidades de dados, essa otimização não terá um impacto significativo. No entanto, ela pode ser útil para grandes quantidades de dados. Você só pode otimizar, no máximo, um filtro de não igualdade por índice composto. Se a consulta tiver vários filtros de não igualdade, escolha um deles para usar o índice composto. O restante continuará a usar índices de intervalo. O filtro de não igualdade deve ser definido por último no índice composto. [Saiba mais sobre os índices compostos](index-policy.md#composite-indexes).

Aqui estão alguns exemplos de consultas que podem ser otimizadas com um índice composto:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Este é o índice composto relevante:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Otimizações que reduzem a latência da consulta

Em muitos casos, o preço de RU pode ser aceitável quando a latência de consulta ainda é muito alta. As seções a seguir fornecem uma visão geral das dicas para reduzir a latência da consulta. Se você executar a mesma consulta várias vezes no mesmo conjunto de mesmos, ele normalmente terá a mesma cobrança de RU a cada vez. Mas a latência de consulta pode variar entre as execuções de consulta.

### <a name="improve-proximity"></a>Aprimora a proximidade

As consultas executadas de uma região diferente da conta do Azure Cosmos DB terão uma latência maior do que se fossem executadas dentro da mesma região. Por exemplo, se você estiver executando código em seu computador desktop, deverá esperar que a latência seja de dezenas ou centenas de milissegundos maior (ou mais) do que se a consulta vier de uma máquina virtual na mesma região do Azure que o Azure Cosmos DB. É simples [distribuir dados globalmente no Azure Cosmos DB](distribute-data-globally.md) para garantir que você possa trazer seus dados mais perto do seu aplicativo.

### <a name="increase-provisioned-throughput"></a>Aumentar a taxa de transferência provisionada

No Azure Cosmos DB, sua taxa de transferência provisionada é medida em RUs (unidades de solicitação). Imagine que você tenha uma consulta que consome 5 RUs de taxa de transferência. Por exemplo, se você provisionar 1.000 RUs, poderá executar essa consulta 200 vezes por segundo. Se você tentar executar a consulta quando não houver taxa de transferência suficiente disponível, o Azure Cosmos DB retornará um erro HTTP 429. Qualquer um dos SDKs da API do núcleo atual (SQL) repetirá automaticamente essa consulta depois de esperar por um curto período de tempo. As solicitações restritas demoram mais, portanto, aumentar a taxa de transferência provisionada pode aprimorar a latência da consulta. Você pode observar o [número total de solicitações restritas](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) na folha **Métricas** do portal do Azure.

### <a name="increase-maxconcurrency"></a>Aumentar MaxConcurrency

As consultas paralelas funcionam consultando várias partições em paralelo. Porém, os dados de uma coleção particionada individual são buscados em série com relação à consulta. Então, se você definir MaxConcurrency para o número de partições, terá mais chance de conseguir uma consulta com o melhor desempenho, desde que todas as outras condições do sistema permaneçam as mesmas. Se você não souber o número de partições, poderá definir MaxConcurrency (ou MaxDegreesOfParallelism em versões mais antigas do SDK) para um número alto. O sistema escolherá o mínimo (número de partições, entrada fornecida pelo usuário) como o grau máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumentar MaxBufferedItemCount

As consultas destinam-se a buscar previamente resultados enquanto o lote atual de resultados está sendo processado pelo cliente. A busca prévia ajuda a aprimorar a latência geral de uma consulta. Definir MaxBufferedItemCount limita o número de resultados com busca prévia. Se você definir esse valor como o número esperado de resultados retornados (ou um número mais alto), a consulta poderá obter o máximo benefício da busca prévia. Se você definir esse valor como -1, o sistema determinará automaticamente o número de itens para armazenar em buffer.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos para obter informações sobre como medir RUs por consulta, obter estatísticas de execução para ajustar suas consultas e muito mais:

* [Obter métricas de execução de consulta SQL usando o SDK do .NET](profile-sql-api-query.md)
* [Ajustando o desempenho da consulta com o Azure Cosmos DB](./sql-api-query-metrics.md)
* [Dicas de desempenho para o SDK do .NET](performance-tips.md)
* [Dicas de desempenho para o SDK do Java v4](performance-tips-java-sdk-v4-sql.md)