---
title: 'Tutorial: Indexação de dados semiestruturados em blobs JSON – Azure Search'
description: Saiba como indexar e pesquisar blobs JSON do Azure semiestruturados usando APIs REST do Azure Search e o Postman.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3184b839087944d8d4335927810ec31d8876866e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485330"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Tutorial do REST: Indexar e pesquisar dados semiestruturados (blobs JSON) no Azure Search

O Azure Search pode indexar matrizes e documentos JSON no armazenamento de blobs do Azure usando um [indexador](search-indexer-overview.md) que faz leitura de dados semiestruturados. Dados semi-estruturados contêm marcas ou marcações que separam o conteúdo dentro dos dados. Eles dividem a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados estruturados formalmente que aderem a um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado por campo.

Neste tutorial, use as [APIs REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/) e um cliente REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma fonte de dados do Azure Search para um contêiner de blobs do Azure
> * Criar um índice do Azure Search para conter conteúdo pesquisável
> * Configurar e executar um indexador para ler o contêiner e extrair conteúdo pesquisável do armazenamento de blobs do Azure
> * Pesquisar no índice que você acabou de criar

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste início rápido. 

[Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial. 

[Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo.

[Aplicativo da área de trabalho do Postman](https://www.getpostman.com/) para enviar solicitações para o Azure Search.

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados usados neste tutorial. Baixe e descompacte este arquivo em sua própria pasta. Para este tutorial, os dados são provenientes do [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) convertidos para JSON.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   ![Carregar arquivos](media/search-semi-structured-data/clinicalupload.png "Upload files")

Após o upload ser concluído, os arquivos devem aparecer em sua própria subpasta dentro do contêiner de dados.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [Explorar APIs REST do Azure Search usando Postman](search-get-started-postman.md).

O método de solicitação para cada chamada neste tutorial é **POST**. As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e sua "chave de administrador" (a chave de administrador é um espaço reservado para sua chave primária de pesquisa) respectivamente. O corpo é onde você coloca o conteúdo real de sua chamada. Dependendo do cliente que você está usando, talvez haja algumas variações em como você cria sua consulta, mas essas são as noções básicas.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos usando o Postman fazer três chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.

Cadeias de caracteres de consulta devem especificar uma versão de API, e cada chamada de consulta deve retornar **201 criado**. A versão de API disponível em geral para o uso de matrizes JSON é `2019-05-06`.

Execute as seguintes três chamadas à API do cliente REST.

## <a name="create-a-data-source"></a>Criar uma fonte de dados

A [API de Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) cria um objeto de Azure Search que especifica quais dados indexar.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa. 

Para essa chamada, o corpo da solicitação deve incluir o nome da sua conta de armazenamento, a chave de conta de armazenamento e o nome do contêiner de blob. A chave de conta de armazenamento pode ser encontrada no Portal do Azure dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é mostrada na imagem a seguir:

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/storagekeys.png)

Substitua `[storage account name]`, `[storage account key]` e `[blob container name]` no corpo da chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Crie um índice
    
A segunda chamada é [API de Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-indexer), criando um índice do Azure Search que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e seus atributos.

A URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e execute a consulta.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

Um indexador conecta a fonte de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece um planejamento para automatizar a atualização de dados. A API REST é [Criar Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

A URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e envie a solicitação. A solicitação é processada imediatamente. Quando a resposta voltar, você terá um índice que é pesquisável em texto completo.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Pesquisar os arquivos JSON

Você poderá iniciar a pesquisa assim que o primeiro documento for carregado. Para essa tarefa, use o [**Gerenciador de pesquisa**](search-explorer.md) no portal.

No portal do Azure, abra a página **Visão Geral** do o serviço de pesquisa e localize o índice criado na lista **Índices**.

Escolha o índice que você acabou de criar. 

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definida pelo usuário

Como antes, os dados podem ser consultados de várias maneiras: pesquisa de texto completo, propriedades do sistema ou metadados definidos pelo usuário. As propriedades do sistema e os metadados definidos pelo usuário podem ser pesquisados com o parâmetro `$select` apenas se forem marcados como **recuperáveis** durante a criação do índice de destino. Os parâmetros no índice não podem ser alterados depois que são criados. No entanto, parâmetros adicionais podem ser adicionados.

Um exemplo de uma consulta básica é `$select=Gender,metadata_storage_size`, que limita o retorno para esses dois parâmetros.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que retorna apenas resultados em que o parâmetro MinimumAge é maior que ou igual a 30 e o MaximumAge é menor que 75.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/metadatashort.png)

Se você quiser experimentar e tentar algumas consultas adicionais por conta própria, fique à vontade para fazer isso. Saiba que você pode usar operadores lógicos (and, or, not) e operadores de comparação (eq, ne, gt, lt, ge, le). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas.

O parâmetro `$filter` funciona somente com metadados que foram marcadas como filtráveis na criação do índice.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de fazer a limpeza depois de um tutorial é excluindo o grupo de recursos que contém o serviço Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Há várias abordagens e várias opções para indexar blobs JSON. Como próxima etapa, analise e teste as diferentes opções para ver o que funciona melhor para seu cenário.

> [!div class="nextstepaction"]
> [Como indexar blobs JSON usando o indexador de Blobs do Azure Search](search-howto-index-json-blobs.md)
