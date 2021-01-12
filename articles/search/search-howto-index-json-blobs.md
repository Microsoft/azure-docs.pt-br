---
title: Pesquisar em BLOBs JSON
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs JSON do Azure para conteúdo de texto usando o indexador de blob Pesquisa Cognitiva do Azure. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 1fc6c7086917f2bcd6e4991d2dac37ea24cbfa83
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116374"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar BLOBs JSON usando um indexador de blob no Azure Pesquisa Cognitiva

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) de blob pesquisa cognitiva do Azure para extrair o conteúdo estruturado de documentos JSON no armazenamento de BLOBs do Azure e torná-lo pesquisável no Azure pesquisa cognitiva. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva do Azure e o carrega com o texto existente extraído de BLOBs JSON. 

Você pode usar o [portal](#json-indexer-portal), [APIs REST](#json-indexer-rest), ou [SDK do .NET](#json-indexer-dotnet) para indexar o conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados em um contêiner de BLOB em uma conta de armazenamento do Azure. Para obter diretrizes sobre o envio de documentos JSON de outras plataformas que não são do Azure, consulte [importação de dados no azure pesquisa cognitiva](search-what-is-data-import.md).

Os BLOBs JSON no armazenamento de BLOBs do Azure normalmente são um único documento JSON (o modo de análise é `json` ) ou uma coleção de entidades JSON. Para coleções, o blob poderia ter uma **matriz** de elementos JSON bem formados (o modo de análise é `jsonArray` ). Os BLOBs também podem ser compostos por várias entidades JSON individuais separadas por uma nova linha (o modo de análise é `jsonLines` ). O parâmetro **parsingMode** na solicitação determina as estruturas de saída.

> [!NOTE]
> Para obter mais informações sobre como indexar vários documentos de pesquisa de um único BLOB, consulte [indexação de um para muitos](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

O método mais fácil para indexação de documentos JSON é usar um assistente no [portal do Microsoft Azure](https://portal.azure.com/). Ao analisar metadados no contêiner de blob do Azure, o assistente [**Importar dados**](search-import-data-portal.md) pode criar um índice padrão, mapear os campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

É recomendável usar a mesma região ou local para o Azure Pesquisa Cognitiva e o armazenamento do Azure para latência mais baixa e para evitar encargos de largura de banda.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

[Entre no portal do Azure](https://portal.azure.com/) e [crie um contêiner de BLOBs](../storage/blobs/storage-quickstart-blobs-portal.md) para conter seus dados. O nível de acesso público pode ser definido como qualquer um de seus valores válidos.

Você precisará do nome da conta de armazenamento, do nome do contêiner e de uma chave de acesso para recuperar seus dados no assistente de **importação de dados** .

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados 

Na página Visão geral do serviço de pesquisa, você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="Comando importar dados no portal" border="false":::

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **fonte de dados**, a fonte deve ser **Armazenamento de blobs do Azure** com as seguintes especificações:

+ Os **Dados para extração** devem ser *Conteúdo e Metadados*. Esta opção permite que ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ O **modo de análise** deve ser definido como *JSON*, *matriz JSON* ou *linhas JSON*. 

  O *JSON* articula cada blob como um documento de pesquisa única, aparecendo como um item independente nos resultados da pesquisa. 

  A *matriz JSON* é para BLOBs que contêm dados JSON bem formados – o JSON bem formado corresponde a uma matriz de objetos ou tem uma propriedade que é uma matriz de objetos e você deseja que cada elemento seja articulado como um documento de pesquisa independente e autônomo. Se os blobs são complexos e você não escolher *matriz JSON*, o blob inteiro será ingerido como um único documento.

  *As linhas JSON* são para BLOBs compostos por várias entidades JSON separadas por uma nova linha, em que você deseja que cada entidade seja articulada como um documento de pesquisa independente autônomo. Se os BLOBs forem complexos e você não escolher o modo de análise de *linhas JSON* , todo o blob será ingerido como um único documento.
   
+ O **Contêiner de armazenamento** deve especificar sua conta de armazenamento e contêiner ou uma cadeia de caracteres de conexão que apontam para o contêiner. Obtenhas as cadeias de caracteres de conexão na página de portal de serviço Blob.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Definição de fonte de dados de Blob" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-ignorar a página "conteúdo enriquecer" no assistente

Adicionar habilidades cognitivas (ou enriquecimento) não é um requisito de importação. A menos que você tenha uma necessidade específica de [Adicionar o enriquecimento de ia](cognitive-search-concept-intro.md) ao seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, clique nos botões azuis na parte inferior da página para "Avançar" e "ignorar".

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e por amostragem dos dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolha **recuperável** e **pesquisável** para cada campo que deve ser retornado a um aplicativo cliente e sujeito ao processamento de pesquisa de texto completo. Você observará que inteiros não são de texto completo ou de pesquisa difusa (os números são avaliados de forma idêntica e geralmente são úteis em filtros).

Examine a descrição de [atributos de índice](/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Definição do índice de blob" border="false":::

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados em seu serviço de Pesquisa Cognitiva do Azure. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure pesquisa cognitiva que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **importação de dados** é um indexador que rastreia sua fonte de dados JSON, extrai conteúdo pesquisável e importa-o para um índice no Azure pesquisa cognitiva.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Definição de indexador de blob" border="false":::

Clique em **Ok** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não vir os dados esperados, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções de atributos de índice na etapa 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar BLOBs JSON, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Pesquisa Cognitiva: criar uma fonte de dados, criar um índice, criar um indexador. A extração de dados do armazenamento de BLOBs ocorre quando você envia a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice passível de consulta. 

Você pode examinar o [código de exemplo REST](#rest-example) no final desta seção que mostra como criar todos os três objetos. Esta seção também contém detalhes sobre os [modos de análise JSON](#parsing-modes), [BLOBs únicos](#parsing-single-blobs), [matrizes JSON](#parsing-arrays)e [matrizes aninhadas](#nested-json-arrays).

Para a indexação JSON baseada em código, use o [postmaster](search-get-started-rest.md) ou [Visual Studio Code](search-get-started-vs-code.md) e a API REST para criar esses objetos:

+ [index](/rest/api/searchservice/create-index)
+ [fonte de dados](/rest/api/searchservice/create-data-source)
+ [indexador](/rest/api/searchservice/create-indexer)

A ordem das operações requer que você crie e chame objetos nesta ordem. Em contraste com o fluxo de trabalho do portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviados por meio da solicitação **criar indexador** .

Os BLOBs JSON no armazenamento de BLOBs do Azure normalmente são um único documento JSON ou uma "matriz" JSON. O indexador de blob no Azure Pesquisa Cognitiva pode analisar qualquer construção, dependendo de como você define o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa blobs JSON como um único bloco de texto. Cada blob JSON se torna um único documento Pesquisa Cognitiva do Azure. | Geralmente disponível na API [REST](/rest/api/searchservice/indexer-operations) e no SDK do [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) . |
| Múltiplos por blob | `jsonArray` | Analisa uma matriz JSON no BLOB, em que cada elemento da matriz se torna um documento do Azure Pesquisa Cognitiva separado.  | Geralmente disponível na API [REST](/rest/api/searchservice/indexer-operations) e no SDK do [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) . |
| Múltiplos por blob | `jsonLines` | Analisa um blob que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, em que cada entidade se torna um documento separado Pesquisa Cognitiva do Azure. | Geralmente disponível na API [REST](/rest/api/searchservice/indexer-operations) e no SDK do [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) . |

### <a name="1---assemble-inputs-for-the-request"></a>1-montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para o Azure Pesquisa Cognitiva (no cabeçalho da POSTAgem) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar uma [ferramenta de teste de API da Web](search-get-started-rest.md) para enviar solicitações HTTP para o Azure pesquisa cognitiva.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de Pesquisa Cognitiva do Azure
+ Chave de administração de Pesquisa Cognitiva do Azure
+ Nome da conta de armazenamento do Azure
+ Chave de conta de armazenamento do Azure

Você pode encontrar esses valores no Portal:

1. Nas páginas do portal para Pesquisa Cognitiva do Azure, copie a URL do serviço de pesquisa da página Visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e copie a chave primária ou secundária (elas são equivalentes).

3. Alterne para as páginas do portal da sua conta de armazenamento. No painel de navegação à esquerda, em **configurações**, clique em **chaves de acesso**. Essa página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves para o bloco de notas.

### <a name="2---create-a-data-source"></a>2-criar uma fonte de dados

Esta etapa fornece informações de conexão de fonte de dados usadas pelo indexador. A fonte de dados é um objeto nomeado no Azure Pesquisa Cognitiva que persiste as informações de conexão. O tipo de fonte de dados, `azureblob` , determina quais comportamentos de extração de dados são invocados pelo indexador. 

Substitua os valores válidos para o nome do serviço, a chave de administração, a conta de armazenamento e os espaços reservados da chave de conta.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3-criar um índice de pesquisa de destino 

Indexadores são emparelhados com um esquema de índice. Se você estiver usando a API (em vez do portal), prepare um índice com antecedência para que você pode especificá-lo na operação do indexador.

O índice armazena conteúdo pesquisável no Pesquisa Cognitiva do Azure. Para criar um índice, forneça um esquema que especifique os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. Se você criar um índice que tem os mesmos nomes de campo e tipos de dados que a origem, o indexador corresponderá os campos de origem e destino, economizando o trabalho de precisar mapear explicitamente os campos.

O exemplo a seguir mostra uma solicitação de [Criar índice](/rest/api/searchservice/create-index). O índice possuirá um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4-configurar e executar o indexador

Assim como ocorre com um índice e uma fonte de dados, e o indexador também é um objeto nomeado que você cria e reutiliza em um serviço de Pesquisa Cognitiva do Azure. Uma solicitação totalmente especificada para criar um indexador pode ter a seguinte aparência:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

A configuração do indexador está no corpo da solicitação. Ele requer uma fonte de dados e um índice de destino vazio que já exista no Azure Pesquisa Cognitiva. 

Agendamento e parâmetros são opcionais. Se você omiti-los, o indexador será executado imediatamente, usando `json` como o modo de análise.

Esse indexador específico não inclui mapeamentos de campo. Dentro da definição do indexador, você pode deixar os **mapeamentos de campo** se as propriedades do documento JSON de origem corresponderem aos campos do índice de pesquisa de destino. 


### <a name="rest-example"></a>Exemplo de REST

Esta seção é uma recapitulação de todas as solicitações usadas para criar objetos. Para obter uma discussão sobre partes do componente, consulte as seções anteriores neste artigo.

### <a name="data-source-request"></a>Solicitação de fonte de dados

Todos os indexadores exigem um objeto de fonte de dados que fornece informações de conexão para os dados existentes. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Solicitação de índice

Todos os indexadores exigem um índice de destino que recebe os dados. O corpo da solicitação define o esquema de índice, que consiste em campos, atribuídos para dar suporte aos comportamentos desejados em um índice pesquisável. Esse índice deve estar vazio quando você executa o indexador. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Solicitação do indexador

Essa solicitação mostra um indexador totalmente especificado. Ele inclui mapeamentos de campo, que foram omitidos nos exemplos anteriores. Lembre-se de que "Schedule", "Parameters" e "fieldMappings" são opcionais, desde que haja um padrão disponível. Omitir "Schedule" faz com que o indexador seja executado imediatamente. Omitir "parsingMode" faz com que o índice use o padrão "JSON".

A criação do indexador no Azure Pesquisa Cognitiva dispara a importação de dados. Ele é executado imediatamente e depois de um agendamento, se você tiver fornecido um.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usar o SDK do .NET

O SDK do .NET tem paridade total com a API REST. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [azure.search.documents. indexes. Models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. indexes. Models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) 
+ [azure.search.documents. indexes. Models. SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 
+ [azure.search.documents. indexes. Models. SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

Os BLOBs JSON podem assumir vários formulários. O parâmetro **parsingMode** no indexador JSON determina como o conteúdo do blob JSON é analisado e estruturado em um índice de pesquisa cognitiva do Azure:

| parsingMode | Descrição |
|-------------|-------------|
| `json`  | Indexe cada blob como um único documento. Este é o padrão. |
| `jsonArray` | Escolha esse modo se os BLOBs consistirem em matrizes JSON e você precisar que cada elemento da matriz se torne um documento separado no Azure Pesquisa Cognitiva. |
|`jsonLines` | Escolha esse modo se seus BLOBs consistirem em várias entidades JSON, que são separadas por uma nova linha, e você precisa que cada entidade se torne um documento separado no Pesquisa Cognitiva do Azure. |

Você pode pensar em um documento como um único item nos resultados da pesquisa. Se você quiser que cada elemento na matriz apareça nos resultados da pesquisa como um item independente, use a `jsonArray` `jsonLines` opção ou conforme apropriado.

Dentro da definição de indexador, use opcionalmente [mapeamentos de campo](search-indexer-field-mappings.md) para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Para `jsonArray` o modo de análise, se a matriz existir como uma propriedade de nível inferior, você poderá definir uma raiz do documento indicando onde a matriz é colocada no BLOB.

> [!IMPORTANT]
> Quando você usa o `json` , `jsonArray` ou o `jsonLines` modo de análise, o Azure pesquisa cognitiva pressupõe que todos os BLOBs em sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analisar BLOBs JSON únicos

Por padrão, o [indexador de blob pesquisa cognitiva do Azure](search-howto-indexing-azure-blob-storage.md) analisa os BLOBs JSON como uma única parte do texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

O indexador de blob analisa o documento JSON em um único documento Pesquisa Cognitiva do Azure. O indexador carrega um índice por correspondência de "texto", "data de publicação" e "marcas" da origem em relação a campos de destino de forma idêntica nomeados e digitados.

Conforme observado, os mapeamentos de campo não são necessários. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem um campo de mapeamento presente na solicitação.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analisar matrizes JSON

Como alternativa, você pode usar a opção de matriz JSON. Essa opção é útil quando os BLOBs contêm uma *matriz de objetos JSON bem formados* e você deseja que cada elemento se torne um documento separado do Azure pesquisa cognitiva. Por exemplo, dado o blob JSON a seguir, você pode preencher o índice de Pesquisa Cognitiva do Azure com três documentos separados, cada um com os campos "ID" e "texto".  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonArray` analisador. Especificar o analisador correto e ter a entrada de dados correta são os únicos dois requisitos específicos de matriz para indexação de BLOBs JSON.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Novamente, os mapeamentos de campo não são necessários. Dado um índice com nomes idênticos nos campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem uma lista campo de mapeamento explícita.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analisar matrizes aninhadas
Para matrizes JSON com elementos aninhados, você pode especificar um `documentRoot` para indicar uma estrutura de vários níveis. Por exemplo, se o seu blob tiver esta aparência:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Use esta configuração para indexar a matriz contida na propriedade `level2`:

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Analisar BLOBs separados por novas linhas

Se o seu blob contiver várias entidades JSON separadas por uma nova linha e você quiser que cada elemento se torne um documento separado do Azure Pesquisa Cognitiva, você poderá optar pela opção de linhas JSON. Por exemplo, considerando o blob a seguir (em que há três entidades JSON diferentes), você pode preencher o índice de Pesquisa Cognitiva do Azure com três documentos separados, cada um com os campos "ID" e "texto".

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Para linhas JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonLines` analisador. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Novamente, observe que os mapeamentos de campo podem ser omitidos, semelhante ao `jsonArray` modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e destino não estiverem devidamente alinhados, você pode definir uma seção de mapeamento de campo no corpo da solicitação de associações de campo para campo explícitas.

Atualmente, o Azure Pesquisa Cognitiva não pode indexar documentos JSON arbitrários diretamente porque ele dá suporte apenas a tipos de dados primitivos, matrizes de cadeia de caracteres e pontos geojson. No entanto, você pode usar **mapeamentos de campo** para separar partes do documento JSON e "elevá-los" para campos de nível superior do documento de pesquisa. Para saber mais sobre os mapeamentos de campo básicos, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).

Voltando ao nosso documento JSON de exemplo:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset` e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "data de publicação" na fonte e campo `date` no índice. Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](https://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.
>

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, dê sua opinião em [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Se precisar de ajuda para usar o recurso existente, poste sua pergunta em [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Consulte também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Indexando o armazenamento de BLOBs do Azure com o Azure Pesquisa Cognitiva](search-howto-index-json-blobs.md)
+ [Indexando BLOBs CSV com o indexador de blob Pesquisa Cognitiva do Azure](search-howto-index-csv-blobs.md)
+ [Tutorial: pesquisar dados semiestruturados do armazenamento de BLOBs do Azure](search-semi-structured-data.md)