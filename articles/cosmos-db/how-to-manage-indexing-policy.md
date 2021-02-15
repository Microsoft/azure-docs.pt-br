---
title: Gerenciar políticas de indexação no Azure Cosmos DB
description: Saiba como gerenciar políticas de indexação, incluir ou excluir uma propriedade da indexação, como definir a indexação usando diferentes SDKs de Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 11/02/2020
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d52f8c59e83a4aae8724100770965f756a439fb
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015684"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Gerenciar políticas de indexação no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

No Azure Cosmos DB, os dados são indexados seguindo [políticas de indexação](index-policy.md) que são definidas para cada contêiner. A política de indexação padrão para contêineres recém-criados impõe os índices de intervalo para qualquer cadeia de caracteres ou número. Essa política pode ser substituída por sua própria política de indexação personalizada.

> [!NOTE]
> O método de atualização das políticas de indexação descritas neste artigo se aplica somente à API do SQL (Core) do Azure Cosmos DB. Saiba mais sobre a indexação na [API do Azure Cosmos DB para MongoDB e a](mongodb-indexing.md) [indexação secundária no Azure Cosmos DB API do Cassandra.](cassandra-secondary-index.md)

## <a name="indexing-policy-examples"></a>Exemplos de política de indexação

Aqui estão alguns exemplos de políticas de indexação mostradas em [seu formato JSON](index-policy.md#include-exclude-paths), que é como elas são expostas no portal do Azure. Os mesmos parâmetros podem ser definidos por meio da CLI do Azure ou qualquer SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a><a id="range-index"></a>Política de recusa para excluir seletivamente alguns caminhos de propriedade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Essa política de indexação é equivalente à seguinte que define manualmente ```kind``` , ```dataType``` e ```precision``` com seus valores padrão. Essas propriedades não são mais necessárias para definir explicitamente e você deve omiti-las de sua política de indexação totalmente (como mostrado no exemplo acima).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Política de aceitação para incluir seletivamente alguns caminhos de propriedade

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Essa política de indexação é equivalente à seguinte que define manualmente ```kind``` , ```dataType``` e ```precision``` com seus valores padrão. Essas propriedades não são mais necessárias para definir explicitamente e você deve omiti-las de sua política de indexação totalmente (como mostrado no exemplo acima).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> Geralmente, é recomendável usar uma política de indexação de **recusa** para permitir que Azure Cosmos DB indexe proativamente qualquer nova propriedade que possa ser adicionada ao seu modelo de dados.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a><a id="spatial-index"></a>Como usar um índice espacial em apenas um caminho de propriedade específico

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
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a><a id="composite-index"></a>Exemplos de política de indexação composta

Além de incluir ou excluir caminhos para as propriedades individuais, você também pode especificar um índice composto. Se quiser executar uma consulta que tem uma `ORDER BY` cláusula para várias propriedades, será necessário um [índice composto](index-policy.md#composite-indexes) com essas propriedades. Além disso, os índices compostos terão um benefício de desempenho para consultas que têm vários filtros ou um filtro e uma cláusula ORDER BY.

> [!NOTE]
> Os caminhos compostos têm um implícito `/?` , pois apenas o valor escalar nesse caminho é indexado. `/*`Não há suporte para o curinga em caminhos compostos. Você não deve especificar `/?` ou `/*` em um caminho composto.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Índice composto definido para (nome asc, idade desc):

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
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

O índice composto acima em nome e idade é necessário para consulta #1 e #2 de consulta:

Consulta nº 1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Consulta nº 2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Esse índice composto beneficiará a consulta #3 e #4 de consulta e otimizará os filtros:

#3 de consulta:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 de consulta:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Índice composto definido para (nome ASC, idade ASC) e (nome ASC, idade DESC):

Você pode definir vários índices compostos diferentes dentro da mesma política de indexação.

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
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Índice composto definido para (nome ASC, idade ASC):

É opcional para especificar a ordem. Se não for especificada, a ordem é crescente.

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
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Como excluir todos os caminhos de propriedade, mas manter a indexação ativa

Essa política pode ser usada em situações em que o [recurso TTL (vida útil)](time-to-live.md) está ativo, mas nenhum índice adicional é necessário (para usar Azure Cosmos DB como um armazenamento de chave-valor puro).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Nenhuma indexação

Esta política desativará a indexação. Se `indexingMode` é definido como `none` , você não pode definir um TTL no contêiner.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Atualizando política de indexação

No Azure Cosmos DB, a política de indexação pode ser atualizada usando qualquer um dos métodos abaixo:

- do portal do Azure
- usando a CLI do Azure
- usando o PowerShell
- usando um dos SDKs

Uma [atualização de política de indexação](index-policy.md#modifying-the-indexing-policy) dispara uma transformação de índice. O andamento dessa transformação também pode ser acompanhado dos SDKs.

> [!NOTE]
> Ao atualizar a política de indexação, as gravações para Azure Cosmos DB não serão interrompidas. Saiba mais sobre as [transformações de indexação](index-policy.md#modifying-the-indexing-policy)

## <a name="use-the-azure-portal"></a>Usar o portal do Azure

Contêineres do Azure Cosmos armazenam sua política de indexação como um documento JSON que o portal do Azure lhe permite editar diretamente.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Crie uma nova conta do Cosmos ou selecione uma conta existente.

1. Abra o painel do **Data Explorer** e selecione o contêiner no qual deseja trabalhar.

1. Clique em **Escala e Configurações**.

1. Modifique o documento JSON de política indexação (veja exemplos [abaixo](#indexing-policy-examples))

1. Clique em **Salvar** quando terminar.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Gerenciar a indexação usando portal do Azure":::

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

Para criar um contêiner com uma política de indexação personalizada, consulte [criar um contêiner com uma política de índice personalizada usando a CLI](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Usar o PowerShell

Para criar um contêiner com uma política de indexação personalizada, consulte [criar um contêiner com uma política de índice personalizada usando o PowerShell](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> Usar o SDK do .NET

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

O `DocumentCollection` objeto do [SDK do .net v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) expõe uma `IndexingPolicy` propriedade que permite alterar `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths` .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Para acompanhar o progresso de transformação de índice, passe um objeto `RequestOptions` que define a propriedade `PopulateQuotaInfo` para `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

O `ContainerProperties` objeto do [SDK do .net v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (consulte [este guia de início rápido](create-sql-api-dotnet.md) sobre seu uso) expõe uma `IndexingPolicy` propriedade que permite alterar `IndexingMode` e adicionar ou remover `IncludedPaths` e `ExcludedPaths` .

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Para acompanhar o progresso da transformação do índice, passe um `RequestOptions` objeto que define a `PopulateQuotaInfo` propriedade para `true` e, em seguida, recupere o valor do `x-ms-documentdb-collection-index-transformation-progress` cabeçalho de resposta.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

Ao definir uma política de indexação personalizada ao criar um novo contêiner, a API Fluent V3's do SDK permite que você escreva essa definição de maneira concisa e eficiente:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Usar o SDK do Java

O objeto `DocumentCollection` do [SDK do Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (confira [este Início Rápido](create-sql-api-java.md) sobre seu uso) expõe os métodos `getIndexingPolicy()` e `setIndexingPolicy()`. O objeto `IndexingPolicy` que eles manipulam permite que você altere o modo de indexação e adicione ou remova caminhos incluídos ou excluídos.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Para acompanhar o progresso da transformação do índice em um contêiner, passe um objeto `RequestOptions` que solicita as informações de cota a serem populadas, depois recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Usar o SDK do Node.js

A interface `ContainerDefinition` do [SDK do Node.js](https://www.npmjs.com/package/@azure/cosmos) (veja [este Início Rápido](create-sql-api-nodejs.md) quanto ao seu uso) expõe uma propriedade `indexingPolicy` que permite que você altere o `indexingMode` e adicione ou remova `includedPaths` e `excludedPaths`.

Recuperar os detalhes do contêiner

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Definir o modo de indexação como consistente

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Adicionar caminho incluído, incluindo um índice espacial

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Adicionar caminho excluído

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Atualizar o contêiner com alterações

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Para acompanhar o progresso da transformação do índice em um contêiner, passe um objeto `RequestOptions` que define a propriedade `populateQuotaInfo` para `true` e depois recupere o valor do cabeçalho de resposta `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Usar o SDK do Python

# <a name="python-sdk-v3"></a>[SDK do Python v3](#tab/pythonv3)

Ao usar o [SDK V3 do Python](https://pypi.org/project/azure-cosmos/) (consulte este guia de [início rápido](create-sql-api-python.md) sobre seu uso), a configuração do contêiner é gerenciada como um dicionário. Desse dicionário, é possível acessar a política de indexação e todos os seus atributos.

Recuperar os detalhes do contêiner

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Definir o modo de indexação como consistente

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Definir uma política de indexação com um caminho incluído e um índice espacial

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definir uma política de indexação com um caminho excluído

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Adicionar um índice composto

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Atualizar o contêiner com alterações

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[SDK do Python v4](#tab/pythonv4)

Ao usar o [SDK do Python v4](https://pypi.org/project/azure-cosmos/), a configuração do contêiner é gerenciada como um dicionário. Desse dicionário, é possível acessar a política de indexação e todos os seus atributos.

Recuperar os detalhes do contêiner

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Definir o modo de indexação como consistente

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Definir uma política de indexação com um caminho incluído e um índice espacial

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Definir uma política de indexação com um caminho excluído

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Adicionar um índice composto

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Atualizar o contêiner com alterações

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

Recuperar o progresso da transformação do índice dos cabeçalhos de resposta
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre indexação nos artigos a seguir:

- [Visão geral da indexação](index-overview.md)
- [Política de indexação](index-policy.md)
