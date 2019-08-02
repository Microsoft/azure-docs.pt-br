---
title: Indexar blobs JSON no indexador de Blobs do Azure para pesquisa de texto completo – Azure Search
description: Rastrear o conteúdo de texto em blobs JSON do Azure usando o indexador de Blobs do Azure Search. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
ms.date: 05/02/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: f60a41c48b3e78b860dca0e93d399420900dbd46
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485430"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Como indexar blobs JSON usando o indexador de BLOBs do Azure Search
Este artigo mostra como configurar um blob de Azure Search [indexador](search-indexer-overview.md) para extrair o conteúdo estruturado de documentos JSON no armazenamento de BLOBs do Azure e torná-los pesquisáveis no Azure Search. Esse fluxo de trabalho cria um índice de Azure Search e carrega-os com existente texto extraído dos blobs do JSON. 

Você pode usar o [portal](#json-indexer-portal), [APIs REST](#json-indexer-rest), ou [SDK do .NET](#json-indexer-dotnet) para indexar o conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados em um contêiner de blob em uma conta de armazenamento do Azure. Para obter orientação sobre como enviar documentos JSON por push de outras plataformas não Azure, consulte [Importação de dados no Azure Search](search-what-is-data-import.md).

Blobs JSON no armazenamento de BLOBs do Azure são normalmente um único documento JSON ou uma coleção de entidades JSON. Para coleções de JSON, o blob pode ter um **matriz** de elementos JSON bem formados. BLOBs também pode ser compostos por várias entidades JSON individuais separadas por uma nova linha. O indexador de blob no Azure Search pode analisar essa construção, dependendo de como você definir a **parsingMode** parâmetro na solicitação.

JSON de todos os modos de análise (`json`, `jsonArray`, `jsonLines`) agora estão disponíveis. 

> [!NOTE]
> Siga as recomendações de configuração do indexador no [indexação de um-para-muitos](search-howto-index-one-to-many-blobs.md) para a saída de vários documentos de pesquisa de um blob do Azure.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

O método mais fácil para indexação de documentos JSON é usar um assistente no [portal do Microsoft Azure](https://portal.azure.com/). Ao analisar metadados no contêiner de blob do Azure, o assistente [ **Importar dados** ](search-import-data-portal.md) pode criar um índice padrão, mapear os campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

É recomendável usar a mesma assinatura do Azure para o Azure Search e o armazenamento do Azure, preferencialmente na mesma região.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

1. [Entre no Portal do Azure](https://portal.azure.com/).

1. [Criar um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter seus dados. O nível de acesso público pode ser definido para qualquer um dos seus valores válidos.

Será necessário o nome da conta de armazenamento, nome do contêiner e para recuperar os dados em uma chave de acesso a **importar dados** assistente.

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados

Na página de visão geral do serviço Azure Search, você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos ou clicando em **adicionar Azure Search** no **serviço Blob** seção do seu Painel de navegação esquerdo da conta de armazenamento.

   ![Comando Importação de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o Assistente de Importação de Dados")

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **fonte de dados**, a fonte deve ser **Armazenamento de blobs do Azure** com as seguintes especificações:

+ Os **Dados para extração** devem ser *Conteúdo e Metadados*. Esta opção permite que ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ **Modo de análise** deve ser definido como *JSON*, *matriz JSON* ou *linhas JSON*. 

  O *JSON* articula cada blob como um documento de pesquisa única, aparecendo como um item independente nos resultados da pesquisa. 

  *Matriz JSON* é para blobs que contêm dados JSON bem formados – o JSON bem formado corresponde a uma matriz de objetos ou tem uma propriedade que é uma matriz de objetos e você deseja que cada elemento a ser articulada como um documento de pesquisa independente autônomo. Se os blobs são complexos e você não escolher *matriz JSON*, o blob inteiro será ingerido como um único documento.

  *Linhas JSON* é para blobs composto por várias entidades JSON separadas por uma nova linha, onde você deseja que cada entidade em ser articulada como um documento de pesquisa independente autônomo. Se os blobs são complexos e você não escolher *linhas JSON* modo e, em seguida, todo o blob de análise é ingerido como um único documento.
   
+ O **Contêiner de armazenamento** deve especificar sua conta de armazenamento e contêiner ou uma cadeia de caracteres de conexão que apontam para o contêiner. Obtenhas as cadeias de caracteres de conexão na página de portal de serviço Blob.

   ![Definição de fonte de dados de Blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pular a página de "Adicionar a pesquisa cognitiva" no assistente

Adicionar habilidades cognitivas não é necessário para a importação de documentos JSON. A menos que haja uma necessidade específica de [incluir a API de Serviços Cognitivos e transformações](cognitive-search-concept-intro.md) ao seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, primeiro vá para a próxima página.

   ![Botão Próxima página da pesquisa cognitiva](media/search-get-started-portal/next-button-add-cog-search.png)

Dessa página você pode pular para a personalização de índice.

   ![Ignorar etapa de habilidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base nos metadados e pela amostragem dos dados de origem. 

Você pode em massa selecionar atributos clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolher **recuperável** e **pesquisável** para todos os campos que devem ser retornados para um aplicativo cliente e estão sujeitas a processamento da pesquisa de texto completo. Você observará que os inteiros não são texto completo ou difusas pesquisáveis (números são avaliados literalmente e costumam ser úteis em filtros).

Examine a descrição da [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição do índice de blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados do serviço Azure Search. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Search que rastreia uma fonte de dados externa para o conteúdo pesquisável. A saída do assistente para **Importar dados** é um indexador que rastreia sua fonte de dados JSON, extrai o conteúdo pesquisável e importa-o para um índice no Azure Search.

   ![Definição de indexador de blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **Ok** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não vir os dados esperados, você talvez precise definir mais atributos em mais campos. Exclua o índice e indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções para atributos de índice na etapa 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar blobs JSON, seguindo um fluxo de trabalho de três partes comuns a todos os indexadores no Azure Search: criar uma fonte de dados, criar um índice, crie um indexador. Extração de dados do armazenamento de BLOBs ocorre quando você enviar a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice que podem ser consultado. 

Você pode revisar [código de exemplo do REST](#rest-example) no final desta seção que mostra como criar todos os três objetos. Esta seção também contém detalhes sobre [modos de análise de JSON](#parsing-modes), [único blobs](#parsing-single-blobs), [matrizes JSON](#parsing-arrays), e [aninhados matrizes](#nested-json-arrays).

Para indexação de JSON com base no código, use [Postman](search-get-started-postman.md) e a API REST para criar esses objetos:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Ordem das operações requer que você cria e chama os objetos nesta ordem. Em contraste com o fluxo de trabalho de portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviado por meio de **criar indexador** solicitação.

Blobs JSON no armazenamento de BLOBs do Azure são normalmente um único documento JSON ou um "matriz" do JSON. O indexador de blob no Azure Search pode analisar a construção, dependendo de como você definir o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | DESCRIÇÃO | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa blobs JSON como um único bloco de texto. Cada blob JSON torna-se um único documento do Azure Search. | Disponível em ambos os [restante](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por blob | `jsonArray` | Analisa uma matriz JSON no blob, onde cada elemento da matriz se torna um documento do Azure Search separado.  | Disponível em ambos os [restante](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por blob | `jsonLines` | Analisa um blob que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, onde cada entidade se tornará um documento separado do Azure Search. | Disponível em ambos os [restante](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API e [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para o Azure Search (no cabeçalho da POSTAGEM) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar [Postman](search-get-started-postman.md) para enviar solicitações HTTP para o Azure Search.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de pesquisa do Azure
+ Chave de administração de pesquisa do Azure
+ Nome da conta de armazenamento do Azure
+ Chave de conta de armazenamento do Azure

Você pode encontrar esses valores no portal:

1. Nas páginas de portal do Azure Search, copie a URL do serviço de pesquisa da página de visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e, em seguida, copie a chave primária ou secundária (eles são equivalentes).

3. Alternar para as páginas do portal para sua conta de armazenamento. No painel de navegação esquerdo, sob **as configurações**, clique em **chaves de acesso**. Esta página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves para o bloco de notas.

### <a name="2---create-a-data-source"></a>2 - criar uma fonte de dados

Essa etapa fornece informações de conexão de fonte de dados usadas pelo indexador. A fonte de dados é um objeto nomeado no Azure Search que persiste as informações de conexão. Tipo de fonte de dados `azureblob`, determina quais comportamentos de extração de dados são invocados pelo indexador. 

Substitua os valores válidos para o nome do serviço, a chave de administração, a conta de armazenamento e espaços reservados de chave de conta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - criar um índice de pesquisa de destino 

Indexadores são emparelhados com um esquema de índice. Se você estiver usando a API (em vez do portal), prepare um índice com antecedência para que você pode especificá-lo na operação do indexador.

O índice armazena o conteúdo pesquisável no Azure Search. Para criar um índice, forneça um esquema que especifique os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. Se você criar um índice que tem os mesmos nomes de campo e tipos de dados que a origem, o indexador corresponderá os campos de origem e destino, economizando o trabalho de precisar mapear explicitamente os campos.

O exemplo a seguir mostra uma solicitação de [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index). O índice possuirá um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - configurar e executar o indexador

Como com um índice e dados de um origem e o indexador também é um nome de objeto que você cria e reutilizar em um serviço de Azure Search. Uma solicitação totalmente especificada para criar um indexador será semelhante ao seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Configuração do indexador é no corpo da solicitação. Isso requer uma fonte de dados e um índice de destino vazio que já existe no Azure Search. 

Agenda e parâmetros são opcionais. Se você omiti-los, o indexador executa imediatamente, usando `json` como o modo de análise.

Esse indexador específico não inclui mapeamentos de campo. Dentro da definição de indexador, você pode retirar **mapeamentos de campo** se as propriedades do documento JSON de origem correspondam aos campos de seu índice de pesquisa de destino. 


### <a name="rest-example"></a>Exemplo de REST

Esta seção é uma recapitulação de todas as solicitações usado para criar objetos. Para uma discussão das partes do componente, consulte as seções anteriores deste artigo.

### <a name="data-source-request"></a>Solicitação de fonte de dados

Todos os indexadores exigem um objeto de fonte de dados que fornece informações de conexão aos dados existentes. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Solicitação de índice

Todos os indexadores exigem um índice de destino que recebe os dados. O corpo da solicitação define o esquema de índice consiste em campos, atribuídos para dar suporte os comportamentos desejados em um índice pesquisável. Esse índice deve estar vazio quando você executar o indexador. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Solicitação de indexador

Essa solicitação mostra um indexador totalmente especificada. Ele inclui mapeamentos de campo, que foram omitidos nos exemplos anteriores. Lembre-se de que "programação", "parâmetros" e "fieldMappings" são opcionais, desde que exista um padrão disponível. Omitir "agendar" faz com que o indexador a ser executado imediatamente. Omitir "parsingMode" faz com que o índice usar o padrão de "json".

Criar o indexador no Azure Search dispara uma importação de dados. Ele é executado imediatamente e, assim, em um agendamento se você tiver fornecido um.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

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


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usar o SDK do .NET

O SDK do .NET tem paridade completa com a API REST. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

Blobs JSON podem assumir várias formas. O **parsingMode** parâmetro do indexador JSON determina como o conteúdo do blob JSON é analisado e estruturado em um índice de Azure Search:

| parsingMode | DESCRIÇÃO |
|-------------|-------------|
| `json`  | Indexe cada blob como um único documento. Esse é o padrão. |
| `jsonArray` | Escolha este modo se seus blobs consistem em matrizes JSON, e você precisa que cada elemento da matriz se torne um documento separado no Azure Search. |
|`jsonLines` | Escolha este modo se seus blobs consistem em várias entidades JSON, que são separadas por uma nova linha, e você precisa de cada entidade se torne um documento separado no Azure Search. |

Você pode pensar em um documento como um único item nos resultados da pesquisa. Se você quiser que cada elemento na matriz sejam exibidos nos resultados da pesquisa como um item independente, em seguida, use o `jsonArray` ou `jsonLines` opção conforme apropriado.

Dentro da definição de indexador, use opcionalmente [mapeamentos de campo](search-indexer-field-mappings.md) para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Para `jsonArray` análise modo, se existir a matriz como uma propriedade de nível inferior, você pode definir um documento raiz que indica onde a matriz é colocada no blob.

> [!IMPORTANT]
> Quando você usa `json`, `jsonArray` ou `jsonLines` modo de análise, o Azure Search pressupõe que todos os blobs em sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Analisar blobs JSON único

Por padrão, o [indexador de blobs do Azure Search](search-howto-indexing-azure-blob-storage.md) analisa blobs JSON como um único bloco de texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexador de blobs analisa o documento JSON em um único documento do Azure Search. O indexador carrega um índice por correspondência de "texto", "data de publicação" e "marcas" da origem em relação a campos de destino de forma idêntica nomeados e digitados.

Conforme observado, os mapeamentos de campo não são necessários. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem um campo de mapeamento presente na solicitação.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Analisar matrizes JSON

Como alternativa, você pode usar a opção de matriz JSON. Essa opção é útil quando seus blobs contêm uma *matriz de objetos do JSON bem formados*, e você deseja que cada elemento se torne um documento separado do Azure Search. Por exemplo, dado o blob JSON a seguir, você pode popular o índice do Azure Search com três documentos separados, cada um com os campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonArray` analisador. Especificando o analisador certo e ter os dados certos entrada são os apenas dois requisitos específicos de matriz para indexação de blobs do JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, os mapeamentos de campo não são necessários. Dado um índice com nomes idênticos nos campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem uma lista campo de mapeamento explícita.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Analisar matrizes aninhadas
Para JSON matrizes ter elementos aninhados, você pode especificar um `documentRoot` para indicar uma estrutura de vários nível. Por exemplo, se o seu blob tiver esta aparência:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Use esta configuração para indexar a matriz contida na propriedade `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Analisar blobs separados por novas linhas

Se o blob contém várias entidades JSON separadas por uma nova linha, e você deseja que cada elemento se torne um documento separado do Azure Search, você pode escolher a opção de linhas JSON. Por exemplo, dado o blob a seguir (em que há três diferentes entidades JSON), você pode preencher o índice de Azure Search com três documentos separados, cada um com os campos "id" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Para linhas JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonLines` analisador. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Novamente, observe que os mapeamentos de campo podem ser omitido, semelhante ao `jsonArray` modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e destino não estiverem devidamente alinhados, você pode definir uma seção de mapeamento de campo no corpo da solicitação de associações de campo para campo explícitas.

Atualmente, o Azure Search não pode indexar documentos JSON arbitrários diretamente porque ele dá suporte a apenas tipos de dados primitivos, matrizes de cadeia de caracteres e pontos GeoJSON. No entanto, você pode usar **mapeamentos de campo** para separar partes do documento JSON e "elevá-los" para campos de nível superior do documento de pesquisa. Para saber mais sobre conceitos básicos de mapeamentos de campo, consulte [campo mapeamentos de indexadores do Azure Search](search-indexer-field-mappings.md).

Voltando ao nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset` e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "data de publicação" na fonte e campo `date` no índice. Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](https://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.
>
>

## <a name="see-also"></a>Consulte também

+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [Indexação do Armazenamento de Blobs do Azure com o Azure Search](search-howto-index-json-blobs.md)
+ [Indexando blobs CSV com o indexador de blobs do Azure Search](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquisar dados semiestruturados no Armazenamento de Blobs do Azure](search-semi-structured-data.md)
