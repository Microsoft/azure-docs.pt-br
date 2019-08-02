---
title: 'Tutorial: Chamar APIs REST dos Serviços Cognitivos em um pipeline de indexação – Azure Search'
description: Veja um exemplo de extração de dados, linguagem natural e processamento de imagem AI na indexação do Azure Search para transformação e extração de dados em blobs JSON usando Postman e a API REST.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: cedcc1be5525cc6932ff168e6549de84fa02a4ca
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669096"
---
# <a name="rest-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>Tutorial do REST: Chamar APIs de Serviços Cognitivos em um pipeline de indexação do Azure Search

Neste tutorial, você aprenderá a mecânica de programação enriquecimento de dados no Azure Search usando *habilidades cognitivas*. As habilidades são apoiadas pelos recursos de processamento de linguagem natural (PLN) e análise de imagem nos Serviços Cognitivos. Por meio da composição do conjunto de qualificações e configuração, você pode extrair texto e representações de texto de um arquivo de imagem ou documento digitalizado. Você também pode detectar a linguagem, entidades, frases-chave e muito mais. O resultado final é um conteúdo mais avançado em um índice do Azure Search, criado com aprimoramentos por IA em um pipeline de indexação. 

Neste tutorial, você deve fazer chamadas da API REST para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar um pipeline de indexação que enriquece dados de exemplo em rota para um índice
> * Aplique as qualificações internas: o reconhecimento de entidade, a detecção de idioma, manipulação de texto e extração de frase-chave
> * Saiba como encadear habilidades mapeando entradas para saídas em um conjunto de qualificações
> * Executar solicitações e analisar resultados
> * Redefinir o índice e indexadores para desenvolvimento futuro

Saída é um índice de pesquisa de texto completo no Azure Search. Você pode aprimorar o índice com outros recursos padrão, como [sinônimos](search-synonyms.md), [perfis de pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [analisadores](search-analyzers.md), e [filtros](search-filters.md).

Este tutorial é executado no serviço gratuito, mas o número de transações gratuitos é limitado a 20 documentos por dia. Se você quiser executar este tutorial mais de uma vez no mesmo dia, use um conjunto de arquivos menor para que você possa encaixar mais execuções.

> [!NOTE]
> À medida que expandir o escopo aumentando a frequência de processamento, adicionando mais documentos ou adicionando mais algoritmos de IA, você precisará [anexar um recurso de Serviços Cognitivos faturável](cognitive-search-attach-cognitive-services.md). As cobranças são geradas ao chamar APIs nos Serviços Cognitivos e para a extração de imagem como parte do estágio de decodificação de documentos no Azure Search. Não há encargos para extração de texto em documentos.
>
> A execução de habilidades integradas é cobrada nos [preços pagos conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existentes. O preço de extração de imagem é descrito na [página de preços do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste tutorial. 

+ [Criar uma conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo. Verifique se que a conta de armazenamento está na mesma região do Azure Search.

+ O [aplicativo da área de trabalho do postman](https://www.getpostman.com/) é usado para fazer chamadas REST ao Azure Search.

+ [Dados de exemplo](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) consistem em um conjunto de pequenos arquivos de tipos diferentes. 

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

O pipeline de enriquecimento extrai de fontes de dados do Azure. Fonte de dados deve originar-se de um tipo de fonte de dados com suporte de um [indexador do Azure Search](search-indexer-overview.md). O Armazenamento de Tabelas do Azure não tem suporte para a pesquisa cognitiva. Para este exercício, usamos o armazenamento de blob para apresentar múltiplos tipos de conteúdo.

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Após o contêiner ser criado, abra-o e selecione **Carregar** na barra de comandos para carregar os arquivos de exemplo que você baixou na etapa anterior.

   ![Arquivos de origem no armazenamento de blobs do Azure](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Depois que os arquivos de exemplo são carregados, obter o nome do contêiner e uma cadeia de caracteres de conexão para o armazenamento de Blob. Você pode fazer isso navegando até você conta de armazenamento no portal do Azure. Em **chaves de acesso**e, em seguida, copie o campo **cadeia de caracteres de Conexão**.

   A cadeia de conexão de armazenamento deve ser uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Existem outras maneiras de especificar a cadeia de caracteres de conexão, como fornecer uma assinatura de acesso compartilhado. Para saber mais sobre as credenciais de fonte de dados, consulte [indexação armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se você não estiver familiarizado com essa ferramenta, consulte [Explorar APIs REST do Azure Search usando Postman](search-get-started-postman.md).

Os métodos de solicitação usados neste tutorial são **POST**, **PUT** e **GET**. As chaves de cabeçalho são "Content-type" definido como "application/json" e "api-key" definido como uma chave de administração de seu serviço Azure Search. O corpo é onde você coloca o conteúdo real de sua chamada. 

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos usando o para Postman fazer quatro chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um conjunto de habilidades, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.


## <a name="create-a-data-source"></a>Criar uma fonte de dados

Agora que os serviços e os arquivos de origem estão preparados, comece montando os componentes do pipeline de indexação. Começar com uma [o objeto de fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) que informa o Azure Search como recuperar dados de origem externa.

No cabeçalho da solicitação, forneça o nome do serviço que você usou ao criar o serviço do Azure Search e a chave de api gerado para o serviço de pesquisa. No corpo da solicitação, especifique a cadeia de conexão e de nome de contêiner do blob.

### <a name="sample-request"></a>Solicitação de Exemplo
```http
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]
```
#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação
```json
{
  "name" : "demodata",
  "description" : "Demo files to demonstrate cognitive search capabilities.",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" :
    "DefaultEndpointsProtocol=https;AccountName=<your account name>;AccountKey=<your account key>;"
  },
  "container" : { "name" : "<your blob container name>" }
}
```
Enviar a solicitação. A ferramenta de teste da web deve retornar um código de status de 201 confirmando êxito. 

Como esta é sua primeira solicitação, verifique o portal do Azure para confirmar a fonte de dados foi criado no Azure Search. Na página de painel do serviço de pesquisa, verifique se a lista de Fontes de Dados tem um novo item. Talvez seja necessário aguardar alguns minutos para que a página do portal atualizar. 

  ![Fontes de dados lado a lado no portal do](./media/cognitive-search-tutorial-blob/data-source-tile.png "lado a lado no portal de fontes de dados")

Se você receber um erro 403 ou 404, verifique a construção da solicitação: `api-version=2019-05-06` deve ser no ponto de extremidade, `api-key` deve estar no cabeçalho após `Content-Type`, e seu valor deve ser válido para um serviço de pesquisa. Você pode reutilizar o cabeçalho para as etapas restantes neste tutorial.

## <a name="create-a-skillset"></a>Criar um conjunto de habilidades

Nesta etapa, você deve definir um conjunto de etapas de enriquecimento que você deseja aplicar aos seus dados. Chamar cada etapa enriquecimento um *habilidade*e o conjunto de etapas de enriquecimento um *conjunto de qualificações*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o conjunto de habilidades:

+ [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ [Divisão de texto](cognitive-search-skill-textsplit.md) para dividir o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave. Extração de frase-chave aceita entradas de 50.000 caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

+ [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave. 

### <a name="sample-request"></a>Solicitação de Exemplo
Antes de fazer esta chamada REST, lembre-se de trocar o nome de serviço e a chave de administrador na solicitação abaixo se a ferramenta não preservar o cabeçalho de solicitação entre chamadas. 

Essa solicitação cria um conjunto de qualificações. Referência ao nome do conjunto de qualificações ```demoskillset``` para o restante deste tutorial.

```http
PUT https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação
```json
{
  "description":
  "Extract entities, detect language and extract key-phrases",
  "skills":
  [
    {
      "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
      "categories": [ "Organization" ],
      "defaultLanguageCode": "en",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "organizations", "targetName": "organizations"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
      "inputs": [
        {
          "name": "text", "source": "/document/content"
        }
      ],
      "outputs": [
        {
          "name": "languageCode",
          "targetName": "languageCode"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "textSplitMode" : "pages",
      "maximumPageLength": 4000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/content"
        },
        {
          "name": "languageCode",
          "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
      "context": "/document/pages/*",
      "inputs": [
        {
          "name": "text", "source": "/document/pages/*"
        },
        {
          "name":"languageCode", "source": "/document/languageCode"
        }
      ],
      "outputs": [
        {
          "name": "keyPhrases",
          "targetName": "keyPhrases"
        }
      ]
    }
  ]
}
```

Enviar a solicitação. A ferramenta de teste da web deve retornar um código de status de 201 confirmando êxito. 

#### <a name="explore-the-request-body"></a>Explore o corpo da solicitação

Observe como a habilidade de extração de frase-chave é aplicada para cada página. Ao definir o contexto como ```"document/pages/*"``` executar este enricher para cada membro da matriz de páginas do documento (para cada página do documento).

Cada uma delas executa no conteúdo do documento. Durante o processamento, o Azure Search danifique cada documento para ler o conteúdo de diferentes formatos de arquivo. Encontrado um texto de origem no arquivo de origem é colocado em um campo gerado ```content```, uma para cada documento. Como tal, defina a entrada como ```"/document/content"```.

Uma representação gráfica do conjunto de qualificações é mostrada abaixo. 

![Entender um conjunto de qualificações](media/cognitive-search-tutorial-blob/skillset.png "entender um conjunto de qualificações")

Saídas podem ser mapeadas para um índice usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

## <a name="create-an-index"></a>Crie um índice

Nesta seção, você define o esquema de índice especificando quais campos serão incluídos no índice de pesquisa e os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | conteúdo   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| nomes de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="sample-request"></a>Solicitação de Exemplo
Antes de fazer esta chamada REST, lembre-se de trocar o nome de serviço e a chave de administrador na solicitação abaixo se a ferramenta não preservar o cabeçalho de solicitação entre chamadas. 

Essa solicitação cria um índice. Use o nome do índice ```demoindex``` para o restante deste tutorial.

```http
PUT https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação

```json
{
  "fields": [
    {
      "name": "id",
      "type": "Edm.String",
      "key": true,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "sortable": true
    },
    {
      "name": "content",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "languageCode",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "keyPhrases",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "filterable": false,
      "facetable": false
    },
    {
      "name": "organizations",
      "type": "Collection(Edm.String)",
      "searchable": true,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```
Enviar a solicitação. A ferramenta de teste da web deve retornar um código de status de 201 confirmando êxito. 

Para saber mais sobre como definir um índice, consulte [Criar Índice (API REST do Azure Search)](https://docs.microsoft.com/rest/api/searchservice/create-index).


## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Crie um indexador, mapear os campos e executar transformações

Até agora, você criou uma fonte de dados, um conjunto de qualificações e um índice. Esses três componentes se tornam parte de um [indexador](search-indexer-overview.md) que reúne cada item em uma única operação de várias fases. Para unir esses em um indexador, você deve definir mapeamentos de campo. 

+ Os fieldMappings são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados depois do conjunto de habilidades, fazendo referência a sourceFieldNames que não existem até a decodificação de documentos ou até que o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

### <a name="sample-request"></a>Solicitação de Exemplo

Antes de fazer esta chamada REST, lembre-se de trocar o nome de serviço e a chave de administrador na solicitação abaixo se a ferramenta não preservar o cabeçalho de solicitação entre chamadas. 

Forneça também o nome do seu indexador. Você pode fazer referência a ele como ```demoindexer``` para o restante deste tutorial.

```http
PUT https://[servicename].search.windows.net/indexers/demoindexer?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```
#### <a name="request-body-syntax"></a>Sintaxe de Corpo da Solicitação

```json
{
  "name":"demoindexer", 
  "dataSourceName" : "demodata",
  "targetIndexName" : "demoindex",
  "skillsetName" : "demoskillset",
  "fieldMappings" : [
    {
      "sourceFieldName" : "metadata_storage_path",
      "targetFieldName" : "id",
      "mappingFunction" :
        { "name" : "base64Encode" }
    },
    {
      "sourceFieldName" : "content",
      "targetFieldName" : "content"
    }
  ],
  "outputFieldMappings" :
  [
    {
      "sourceFieldName" : "/document/organizations",
      "targetFieldName" : "organizations"
    },
    {
      "sourceFieldName" : "/document/pages/*/keyPhrases/*",
      "targetFieldName" : "keyPhrases"
    },
    {
      "sourceFieldName": "/document/languageCode",
      "targetFieldName": "languageCode"
    }
  ],
  "parameters":
  {
    "maxFailedItems":-1,
    "maxFailedItemsPerBatch":-1,
    "configuration":
    {
      "dataToExtract": "contentAndMetadata",
      "imageAction": "generateNormalizedImages"
    }
  }
}
```

Enviar a solicitação. A ferramenta de teste da web deve retornar um código de status de 201 confirmando processamento bem-sucedido. 

Espere essa ação levar vários minutos para ser concluída. Embora o conjunto de dados é pequeno, capacidades analíticas são intensivas na computação. Algumas técnicas, como a análise de imagem, são demoradas.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver problemas ao acessar os dados, as entradas de mapeamento e saídas ou ordem de operações, eles aparecem neste estágio. Para executar novamente o pipeline com as alterações de código ou script, você precisará primeiro descarte de objetos. Para saber mais, confira [Reiniciar e Reexecutar](#reset).

#### <a name="explore-the-request-body"></a>Explore o corpo da solicitação

O script define ```"maxFailedItems"``` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também o ```"dataToExtract":"contentAndMetadata"``` instrução nos parâmetros de configuração. Essa instrução informa o indexador para extrair automaticamente o conteúdo de diferentes formatos de arquivo, como também os metadados relacionados a cada arquivo. 

Quando o conteúdo é extraído, você pode definir ```imageAction``` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração ```"imageAction":"generateNormalizedImages"```, combinada com a Habilidade de OCR e a Habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF), bem como imagens encontradas na fonte de dados, por exemplo um arquivo JPG.

## <a name="check-indexer-status"></a>Checar o status do indexador

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o indexador ainda está em execução, envie a solicitação a seguir para verificar o status do indexador.

```http
GET https://[servicename].search.windows.net/indexers/demoindexer/status?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

A resposta indica se o indexador está em execução. Depois que a indexação é concluída, use outra HTTP GET para o ponto de extremidade STATUS (como acima) para ver os relatórios de erros e avisos que ocorreram durante enriquecimento.  

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, nenhuma entrada de texto dos arquivos JPEG). Você pode analisar a resposta de status para obter informações detalhadas sobre os avisos emitidos durante a indexação.
 
## <a name="query-your-index"></a>Consultar o índice

Depois de terminar de indexação, execute consultas que retornam o conteúdo dos campos individuais. Por padrão, o Azure Search retorna os 50 melhores resultados. Os dados de exemplo serão pequenos para que o padrão funciona bem. No entanto, ao trabalhar com grandes conjuntos de dados, você precisará incluir parâmetros na cadeia de caracteres de consulta para retornar mais resultados. Para obter instruções, consulte [como página de resultados do Azure Search](search-pagination-page-layout.md).

Como uma etapa de verificação, consulte o índice para todos os campos.

```http
GET https://[servicename].search.windows.net/indexes/demoindex?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

A saída é o esquema de índice, com o nome, tipo e atributos de cada campo.

Enviar uma segunda consulta para `"*"` para retornar todo o conteúdo de um único campo, como `organizations`.

```http
GET https://[servicename].search.windows.net/indexes/demoindex/docs?search=*&$select=organizations&api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Repita para campos adicionais: conteúdo, idioma, frases-chave e organizações neste exercício. Você pode retornar vários campos via `$select` usando uma lista delimitada por vírgulas.

Você pode usar GET ou POST, dependendo do tamanho e complexidade de cadeia de caracteres de consulta. Para obter mais informações, consulte a [Consulta usando a API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents).



<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento de pipeline, a abordagem mais prática para iterações de design é excluir os objetos do Azure Search e permitir que seu código para recriá-los. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Reindexar seus documentos com as novas definições:

1. Exclua o índice para remover dados persistentes. Exclua o indexador para recriá-la em seu serviço.
2. Modificar uma definição de conjunto de qualificações e índice.
3. Recrie um índice e um indexador no serviço para executar o pipeline. 

Você pode usar o portal para excluir índices, indexadores e Conjuntos de habilidades.

```http
DELETE https://[servicename].search.windows.net/skillsets/demoskillset?api-version=2019-05-06
api-key: [api-key]
Content-Type: application/json
```

Código de status 204 é retornado na exclusão com êxito.

Como seu código amadurece, talvez queira refinar uma estratégia de reconstrução. Para saber mais, confira [Como recompilar um índice](search-howto-reindex.md).

## <a name="takeaways"></a>Observações

Este tutorial demonstra as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

[Predefinidos habilidades](cognitive-search-predefined-skills.md) foram introduzidas, junto com a definição de conjunto de qualificações e a mecânica de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` no indexador definição é necessária para roteamentos valores enriquecidos do pipeline em um índice de pesquisado em um serviço do Azure Search.

Por fim, você aprendeu como resultados de teste e reinicie o sistema para obter mais iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Nesta versão, há um mecanismo para exibir as construções internas (enriquecidas documentos criados pelo sistema). Você também aprendeu como verificar o status do indexador e quais objetos a serem excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida para limpar depois de um tutorial é excluindo o grupo de recursos que contém o serviço do Azure Search e o serviço de Blob do Azure. Supondo que você colocar ambos os serviços no mesmo grupo, exclua o grupo de recursos agora para excluir permanentemente todo o conteúdo, incluindo os serviços e qualquer conteúdo armazenado que você tenha criado para este tutorial. No portal, o nome do grupo de recurso está na página de Visão geral de cada serviço.

## <a name="next-steps"></a>Próximas etapas

Personalizar ou estender o pipeline com qualificações personalizadas. Criando uma habilidade personalizada e adicionando-a a um conjunto de qualificações permite que você carregue texto ou análise de imagem que você escreve por conta própria. 

> [!div class="nextstepaction"]
> [Exemplo: Como criar uma habilidade personalizada para a pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
