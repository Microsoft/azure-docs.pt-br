---
title: Indexando uma fonte de dados do Cosmos DB no Azure Search | Microsoft Docs
description: Este artigo mostra como criar um indexador do Azure Search com o Cosmos DB como fonte de dados.
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 08/10/2017
ms.author: eugenesh
ms.openlocfilehash: 2f1791393b1e59721cc5a1030927cd00d74a5f13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Conectando o Cosmos DB ao Azure Search usando indexadores

Se você desejar implementar uma experiência de pesquisa incrível com seus dados do Cosmos DB, poderá usar um indexador do Azure Search para efetuar pull dos dados em um índice do Azure Search. Neste artigo, mostramos como integrar o Azure Cosmos DB ao Azure Search sem precisar escrever nenhum código para manter a infraestrutura de indexação.

Para configurar um indexador do Cosmos DB, é necessário ter um [serviço do Azure Search](search-create-service-portal.md) e criar um índice, uma fonte de dados e, por fim, o indexador. Você pode criar esses objetos usando o [portal](search-import-data-portal.md), o [SDK do .NET](/dotnet/api/microsoft.azure.search) ou a [API REST](/rest/api/searchservice/) para todas as linguagens não .NET. 

Se você optar pelo portal, o [Assistente para Importação de Dados](search-import-data-portal.md) o orientará na criação de todos esses recursos.

> [!NOTE]
> O Cosmos DB é a próxima geração do DocumentDB. Embora o nome do produto tenha sido alterado, a sintaxe é a mesma de antes. Continue especificando `documentdb`, conforme as instruções deste artigo sobre o indexador. 

> [!TIP]
> Inicie o assistente **Importar dados** no painel do Cosmos DB para simplificar a indexação dessa fonte de dados. Em navegação à esquerda, vá para **Coleções** > **Adicionar Azure Search** para começar.

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Conceitos do indexador do Azure Search
O Azure Search dá suporte à criação e ao gerenciamento de fontes de dados (incluindo o Cosmos DB) e indexadores que operam nessas fontes de dados.

A **fonte de dados** especifica os dados no índice, nas credenciais e nas políticas para identificação de alterações nos dados (como documentos modificados ou excluídos em sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Um **indexador** descreve como os dados vão da fonte de dados para o índice de pesquisa de destino. Um indexador pode ser usado para:

* Executar uma cópia única dos dados para preenchimento de um índice.
* Sincronizar um índice com as alterações da fonte de dados segundo uma agenda. A agenda faz parte da definição do indexador.
* Invocar atualizações sob demanda para um índice, conforme necessário.

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Para criar uma fonte de dados, execute um POST:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

* **name**: escolha um nome para representar o banco de dados do Cosmos DB.
* **type**: deve ser `documentdb`.
* **credentials**:
  
  * **connectionString**: obrigatório. Especifique as informações de conexão do banco de dados do Azure Cosmos DB no seguinte formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **container**:
  
  * **name**: obrigatório. Especifique a ID da coleção do Cosmos DB a ser indexada.
  * **query**: opcional. Você pode especificar uma consulta para nivelar um documento JSON arbitrário, criando um esquema nivelado que a Pesquisa do Azure pode indexar.
* **dataChangeDetectionPolicy**: recomendado. Consulte a seção [Indexando documentos alterados](#DataChangeDetectionPolicy).
* **dataDeletionDetectionPolicy**: opcional. Consulte a seção [Indexando documentos excluídos](#DataDeletionDetectionPolicy).

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para formatar dados indexados
É possível especificar uma consulta do Cosmos DB para mesclar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a serem indexados. 

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Filtrar consulta:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Consulta de mesclagem:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
Se ainda não tiver um, crie um índice de destino da Pesquisa do Azure. Você pode criar um índice usando a [interface do usuário do portal do Azure](search-create-index-portal.md), a [API REST de Criação de Índices](/rest/api/searchservice/create-index) ou a [classe Index](/dotnet/api/microsoft.azure.search.models.index).

O exemplo a seguir cria um índice com um campo de descrição e ID:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou com a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave do documento padrão é a propriedade `_rid` do Cosmos DB, que é renomeada como `rid` no Azure Search. Além disso, os valores `_rid` do Cosmos DB contêm caracteres que são inválidos nas chaves do Azure Search. Por esse motivo, os valores `_rid` são codificados em Base64.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados da Pesquisa do Azure
| TIPO DE DADOS JSON | TIPOS DE CAMPOS DE ÍNDICE DE DESTINO COMPATÍVEIS |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que se parecem com pontos flutuantes |Edm.Double, Edm.String |
| Cadeia de caracteres |Edm.String |
| Matrizes de tipos primitivos, por exemplo, [“a”, “b”, “c”] |Collection(Edm.String) |
| Cadeias de caracteres que se parecem com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo, { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

<a name="CreateIndexer"></a>
## <a name="step-3-create-an-indexer"></a>Etapa 3: Criar um indexador

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador é executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para obter mais detalhes sobre Criar a API do Indexador, consulte [Criar Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Executando o indexador sob demanda
Além de ser executado periodicamente segundo um agendamento, um indexador também pode ser invocado sob demanda:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de Execução for retornada com êxito, isso indica que a invocação do indexador foi agendada, mas o processamento real ocorre de forma assíncrona. 

É possível monitorar o status do indexador no portal ou usando a API Obter Status do Indexador, que descrevemos a seguir. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Obtendo o status do indexador
É possível recuperar o status e o histórico de execução de um indexador:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (de modo que a execução mais recente fique em primeiro lugar).

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Indexando documentos alterados
A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. Atualmente, a única política com suporte é a política `High Water Mark` que usa a propriedade `_ts` (carimbo de data/hora) fornecida pelo Cosmos DB, que é especificada da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

O uso dessa política é altamente recomendável para garantir o bom desempenho do indexador. 

Se estiver usando uma consulta personalizada, garanta que a propriedade `_ts` será projetada pela consulta.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas
O progresso incremental durante a indexação garante que, se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador pode escolher onde ela parou próxima vez que ele é executado, em vez de toda a coleção de zero de índice novamente. Isso é especialmente importante durante a indexação de coleções grandes. 

Para habilitar o progresso incremental usando uma consulta personalizada, certifique-se de que sua consulta classifica os resultados pelo `_ts` coluna. Isso permite periódica seleção apontando que usa o Azure Search para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo se a consulta contiver uma cláusula `ORDER BY [collection alias]._ts`, Azure Search pode não inferir que a consulta é solicitada pelo `_ts`. Você pode informar o Azure Search que os resultados são ordenados usando o `assumeOrderByHighWaterMarkColumn` propriedade de configuração. Para especificar essa dica, crie ou atualize o indexador da seguinte maneira: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Indexando documentos excluídos
Quando as linhas são excluídas da coleção, normalmente, você também deseja excluí-las do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete` (a exclusão recebe algum tipo de marcador), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver usando uma consulta personalizada, garanta que a propriedade referenciada por `softDeleteColumnName` é projetada pela consulta.

O seguinte exemplo cria uma fonte de dados com uma política de exclusão reversível:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Próximas etapas
Parabéns! Você aprendeu a integrar o Azure Cosmos DB ao Azure Search usando o indexador do Cosmos DB.

* Para saber mais sobre o Azure Cosmos DB, consulte a [página de serviço do Cosmos DB](https://azure.microsoft.com/services/documentdb/).
* Para saber mais sobre a Pesquisa do Azure, confira a [página do serviço de Pesquisa](https://azure.microsoft.com/services/search/).
