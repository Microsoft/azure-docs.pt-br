---
title: 'Tutorial: Indexar dados semiestruturados em blobs JSON'
titleSuffix: Azure Cognitive Search
description: Saiba como indexar e pesquisar blobs JSON do Azure semiestruturados usando APIs REST da Pesquisa Cognitiva do Azure e o Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746130"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Tutorial: Indexar blobs JSON do Armazenamento do Azure usando a REST

A Pesquisa Cognitiva do Azure pode indexar matrizes e documentos JSON no armazenamento de blobs do Azure usando um [indexador](search-indexer-overview.md) que faz leitura de dados semiestruturados. Dados semi-estruturados contêm marcas ou marcações que separam o conteúdo dentro dos dados. Eles dividem a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados estruturados formalmente que aderem a um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado por campo.

Este tutorial usa o Postman e as [APIs REST de Pesquisa](/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma fonte de dados da Pesquisa Cognitiva do Azure para um contêiner de blobs do Azure
> * Criar um índice da Pesquisa Cognitiva do Azure para conter conteúdo pesquisável
> * Configurar e executar um indexador para ler o contêiner e extrair conteúdo pesquisável do armazenamento de blobs do Azure
> * Pesquisar no índice que você acabou de criar

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](../storage/common/storage-account-create.md)
+ [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados usados neste tutorial. Baixe e descompacte este arquivo em sua própria pasta. Para este tutorial, os dados são provenientes do [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) convertidos para JSON.

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas e o Armazenamento de Blobs do Azure para fornecer os dados. 

Se possível, crie os dois na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Começar com o Armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ Criar Recurso**.

1. Pesquise *conta de armazenamento* e selecione a oferta Conta de Armazenamento da Microsoft.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Criar conta de armazenamento" border="false":::

1. Na guia Informações Básicas, os itens a seguir são obrigatórios. Aceite os padrões para todo o restante.

   + **Grupo de recursos**. Selecione um grupo existente ou crie um, mas use o mesmo grupo para todos os serviços, de modo que você possa gerenciá-los em conjunto.

   + **Nome da conta de armazenamento**. Se acreditar que possa ter vários recursos do mesmo tipo, use o nome para desfazer a ambiguidade por tipo e região, por exemplo, *blobstoragewestus*. 

   + **Local**. Se possível, escolha a mesma localização usada para a Pesquisa Cognitiva do Azure e os Serviços Cognitivos. Uma única localização anula os encargos de largura de banda.

   + **Tipo de Conta**. Escolha o padrão, *StorageV2 (Uso Geral v2)* .

1. Clique em **Examinar + Criar** para criar o serviço.

1. Após a criação, clique em **Ir para o recurso** para abrir a página Visão Geral.

1. Clique em serviço **Blobs**.

1. [Crie um contêiner de Blob](../storage/blobs/storage-quickstart-blobs-portal.md) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Carregar na barra de comandos" border="false":::

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Carregar arquivos" border="false":::

Após o upload ser concluído, os arquivos devem aparecer em sua própria subpasta dentro do contêiner de dados.

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro recurso é o Azure Cognitive Search, que pode ser [criado no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo. 

Assim como o Armazenamento de Blobs do Azure, reserve um momento para coletar a chave de acesso. Além disso, quando começar a estruturar as solicitações, você precisará fornecer a chave de API de administração e o ponto de extremidade usados para autenticar cada solicitação.

### <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="Obter um ponto de extremidade HTTP e uma chave de acesso" border="false":::

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="2---set-up-postman"></a>2 – Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se você não estiver familiarizado com essa ferramenta, confira [Criar um índice de pesquisa usando APIs REST](search-get-started-rest.md).

Os métodos de solicitação para cada chamada neste tutorial são **POST** e **GET**. Você fará três chamadas à API ao serviço de pesquisa para criar uma fonte de dados, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.

Em Cabeçalhos, defina "Content-Type" como `application/json` e `api-key` como a chave de API de administração do serviço da Pesquisa Cognitiva do Azure. Depois de definir os cabeçalhos, você poderá usá-los para cada solicitação neste exercício.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL e cabeçalho da solicitação do Postman" border="false":::

Os URIs precisam especificar uma api-version e cada chamada de consulta deve retornar uma mensagem **201 Criado**. A versão de API disponível em geral para o uso de matrizes JSON é `2020-06-30`.

## <a name="3---create-a-data-source"></a>3 – Criar uma fonte de dados

A [API de Criação de Fonte de Dados](/rest/api/searchservice/create-data-source) cria um objeto do Azure Cognitive Search que especifica quais dados serão indexados.

1. Defina o ponto de extremidade dessa chamada como `https://[service name].search.windows.net/datasources?api-version=2020-06-30`. Substitua `[service name]` pelo nome do serviço de pesquisa. 

1. Copie o JSON a seguir no corpo da solicitação.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Substitua a cadeia de conexão por uma cadeia de caracteres válida para a sua conta.

1. Substitua "[nome do contêiner de blob]" pelo contêiner criado para os dados de exemplo. 

1. Enviar a solicitação. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 – Criar um índice

A segunda chamada é à [API de Criação de Índice](/rest/api/searchservice/create-index), criando um índice da Pesquisa Cognitiva do Azure que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e seus atributos.

1. Defina o ponto de extremidade dessa chamada como `https://[service name].search.windows.net/indexes?api-version=2020-06-30`. Substitua `[service name]` pelo nome do serviço de pesquisa.

1. Copie o JSON a seguir no corpo da solicitação.

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
      ]
    }
   ```

1. Enviar a solicitação. A resposta deve ser semelhante a:

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 – Criar e executar um indexador

Um indexador se conecta à fonte de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece uma agenda para automatizar a atualização de dados. A API REST é [Criar Indexador](/rest/api/searchservice/create-indexer).

1. Defina o URI dessa chamada como `https://[service name].search.windows.net/indexers?api-version=2020-06-30`. Substitua `[service name]` pelo nome do serviço de pesquisa.

1. Copie o JSON a seguir no corpo da solicitação.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Enviar a solicitação. A solicitação é processada imediatamente. Quando a resposta voltar, você terá um índice que é pesquisável em texto completo. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 – Pesquisar os arquivos JSON

Você poderá iniciar a pesquisa assim que o primeiro documento for carregado.

1. Altere o verbo para **GET**.

1. Defina o URI dessa chamada como `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true`. Substitua `[service name]` pelo nome do serviço de pesquisa.

1. Enviar a solicitação. Esta é uma consulta de pesquisa de texto completo não especificada que retorna todos os campos marcados como recuperáveis no índice, juntamente com uma contagem de documentos. A resposta deve ser semelhante a:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Adicione o parâmetro de consulta `$select` para limitar os resultados a menos campos: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`.  Nesta consulta, 100 documentos são correspondentes, mas por padrão, o Azure Cognitive Search retorna apenas 50 nos resultados.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Consulta parametrizada" border="false":::

1. Um exemplo de uma consulta mais complexa será `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que apenas retorna resultados em que o parâmetro MinimumAge é superior ou igual a 30 e MaximumAge é inferior a 75. Substitua a expressão `$select` pela expressão `$filter`.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Pesquisa semi-estruturada" border="false":::

Use também operadores lógicos (and, or e not) e operadores de comparação (eq, ne, gt, lt, ge e le). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas. Para obter mais informações e exemplos, confira [Criar uma consulta simples](search-query-simple-examples.md).

> [!NOTE]
> O parâmetro `$filter` funciona somente com metadados que foram marcadas como filtráveis na criação do índice.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Use o portal para excluir índices, indexadores e fontes de dados. Ou então, use **DELETE** e forneça URLs para cada objeto. O comando a seguir exclui um indexador.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

Código de status 204 é retornado na exclusão com êxito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os conceitos básicos da indexação de blobs do Azure, vamos examinar mais de perto a configuração do indexador para blobs JSON no Armazenamento do Azure.

> [!div class="nextstepaction"]
> [Configurar a indexação de blob JSON](search-howto-index-json-blobs.md)