---
title: 'Tutorial: criar um analisador personalizado'
titleSuffix: Azure Cognitive Search
description: Saiba como criar um analisador personalizado para aprimorar a qualidade dos resultados da pesquisa no Azure Cognitive Search.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/29/2021
ms.openlocfilehash: f4bde98cfc772f5a80bb52c2e4bc2f5a9c28c78d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99097650"
---
# <a name="tutorial-create-a-custom-analyzer-for-phone-numbers"></a>Tutorial: Criar um analisador personalizado para números de telefone

Os [analisadores](search-analyzers.md) são um componente fundamental de qualquer solução de pesquisa. Para aprimorar a qualidade dos resultados da pesquisa, é importante entender como os analisadores funcionam e afetam esses resultados.

Em alguns casos, como com um campo de texto livre, basta selecionar o [analisador de idioma](index-add-language-analyzers.md) correto para aprimorar os resultados da pesquisa. No entanto, alguns cenários, como pesquisar de modo preciso números de telefone, URLs ou emails, podem exigir o uso de analisadores personalizados.

Este tutorial usa o Postman e as APIs REST do [Azure Cognitive Search](/rest/api/searchservice/) para:

> [!div class="checklist"]
> * Explicar como funcionam os analisadores
> * Definir um analisador personalizado para pesquisar números de telefone
> * Testar como o texto cria tokens do analisador personalizado
> * Criar analisadores separados para indexação e pesquisa para aprimorar ainda mais os resultados

## <a name="prerequisites"></a>Pré-requisitos

Os serviços e as ferramentas a seguir são necessários para este tutorial.

+ [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

## <a name="download-files"></a>Baixar arquivos

O código-fonte deste tutorial está na pasta [custom-analyzers](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) no repositório do GitHub [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

## <a name="1---create-azure-cognitive-search-service"></a>1 – Criar o serviço do Azure Cognitive Search

Para concluir este tutorial, você precisará de um serviço do Azure Cognitive Search, que você pode [criar no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo.

Para a próxima etapa, você precisará saber o nome do seu serviço de pesquisa e sua Chave de API. Se não tiver certeza de como encontrar esses itens, confira este [início rápido](search-create-service-portal.md#get-a-key-and-url-endpoint).


## <a name="2---set-up-postman"></a>2 – Configurar o Postman

Em seguida, inicie o Postman e importe a coleção que você baixou de [Azure-Samples/azure-search-postman-samples](https://github.com/Azure-Samples/azure-search-postman-samples).

Para importar a coleção, acesse **Arquivos** > **Importar** e selecione o arquivo de coleção que deseja importar.

Para cada solicitação, você precisa:

1. Substitua `<YOUR-SEARCH-SERVICE>` pelo nome do serviço de pesquisa.

1. Substituir `<YOUR-ADMIN-API-KEY>` pela chave primária ou secundária do serviço de pesquisa.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="URL e cabeçalho da solicitação do Postman" border="false":::

Se não estiver familiarizado com o Postman, confira [Explorar APIs REST do Azure Cognitive Search](search-get-started-rest.md).

## <a name="3---create-an-initial-index"></a>3 – Criar um índice inicial

Nesta etapa, vamos criar um índice inicial, carregar documentos no índice e consultar os documentos para ver como nossas pesquisas iniciais são executadas.

### <a name="create-index"></a>Criar índice

Vamos começar criando um índice simples chamado `tutorial-basic-index` com dois campos: `id` e `phone_number`. Ainda não definimos um analisador, portanto, o analisador `standard.lucene` será usado por padrão.

Para criar o índice, enviamos a seguinte solicitação:

```http
PUT https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

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
        "name": "phone_number",
        "type": "Edm.String",
        "sortable": false,
        "searchable": true,
        "filterable": false,
        "facetable": false
      }
    ]
  }
```

### <a name="load-data"></a>Carregar dados

Em seguida, carregaremos dados no índice. Em alguns casos, talvez você não tenha controle sobre o formato dos números de telefone ingeridos para que possamos testar diferentes tipos de formatos. O ideal é que uma solução de pesquisa retornará todos os números de telefone correspondentes, independentemente do formato.

Os dados são carregados no índice usando a seguinte solicitação: 

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs/index?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "value": [
      {
        "@search.action": "upload",  
        "id": "1",
        "phone_number": "425-555-0100"
      },
      {
        "@search.action": "upload",  
        "id": "2",
        "phone_number": "(321) 555-0199"
      },
      {  
        "@search.action": "upload",  
        "id": "3",
        "phone_number": "+1 425-555-0100"
      },
      {  
        "@search.action": "upload",  
        "id": "4",  
        "phone_number": "+1 (321) 555-0199"
      },
      {
        "@search.action": "upload",  
        "id": "5",
        "phone_number": "4255550100"
      },
      {
        "@search.action": "upload",  
        "id": "6",
        "phone_number": "13215550199"
      },
      {
        "@search.action": "upload",  
        "id": "7",
        "phone_number": "425 555 0100"
      },
      {
        "@search.action": "upload",  
        "id": "8",
        "phone_number": "321.555.0199"
      }
    ]  
  }
```

Com os dados no índice, estamos prontos para começar a pesquisar.

### <a name="search"></a>Search

Para tornar a pesquisa intuitiva, é melhor não esperar que os usuários formatem consultas de um modo específico. Um usuário pode pesquisar `(425) 555-0100` em qualquer um dos formatos que mostramos acima e ainda esperará que os resultados sejam retornados. Nesta etapa, vamos testar algumas consultas de exemplo para ver como elas são executadas.

Começamos pesquisando `(425) 555-0100`:

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=(425) 555-0100
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  
```

Essa consulta retorna **três dos quatro resultados esperados**, mas também retorna **dois resultados inesperados**:

```json
{
    "value": [
        {
            "@search.score": 0.05634898,
            "phone_number": "+1 425-555-0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425 555 0100"
        },
        {
            "@search.score": 0.05634898,
            "phone_number": "425-555-0100"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "(321) 555-0199"
        },
        {
            "@search.score": 0.020766128,
            "phone_number": "+1 (321) 555-0199"
        }
    ]
}
```

Em seguida, vamos pesquisar um número sem formatação `4255550100`

```http
GET https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/docs?api-version=2019-05-06&search=4255550100
  api-key: <YOUR-ADMIN-API-KEY>
```

Essa consulta é ainda pior, retornando apenas **uma das quatro correspondências corretas**.

```json
{
    "value": [
        {
            "@search.score": 0.6015292,
            "phone_number": "4255550100"
        }
    ]
}
```

Se você considera esses resultados confusos, não está sozinho. Na próxima seção, veremos por que estamos obtendo esses resultados.

## <a name="4---debug-search-results"></a>4 – Depurar resultados da pesquisa

Para entender esses resultados da pesquisa, é importante entender primeiro como funcionam os analisadores. Então podemos testar o analisador padrão usando a API [Analisar Texto](/rest/api/searchservice/test-analyzer) e criar um analisador que atenda às nossas necessidades.

### <a name="how-analyzers-work"></a>Como funcionam os analisadores

Um [analisador](search-analyzers.md) é um componente do [mecanismo de pesquisa de texto completo](search-lucene-query-architecture.md) responsável pelo processamento de texto em cadeias de caracteres de consulta e documentos indexados. Analisadores diferentes manipulam texto de maneiras diferentes, dependendo do cenário. Para esse cenário, precisamos criar um analisador personalizado para números de telefone.

Os analisadores consistem em três componentes:

+ [**Filtros de caractere**](#CharFilters) que removem ou substituem caracteres individuais do texto de entrada.
+ Um [**Criador de token**](#Tokenizers) que quebra o texto de entrada em tokens, que se tornam chaves no índice de pesquisa.
+ [**Filtros de token**](#TokenFilters) que manipulam os tokens produzidos pelo criador de token.

No seguinte diagrama, você pode ver como esses três componentes funcionam em conjunto para indexar uma sentença:

  :::image type="content" source="media/tutorial-create-custom-analyzer/analyzers-explained.png" alt-text="Diagrama do processo do analisador para criar tokens para uma sentença":::

Esses tokens são então armazenados em um índice invertido, que permite pesquisas rápidas de texto completo.  Um índice invertido permite a pesquisa de texto completo mapeando todos os termos exclusivos extraídos durante a análise lexical para os documentos em que eles ocorrem. Você pode ver um exemplo no diagrama abaixo:

  :::image type="content" source="media/tutorial-create-custom-analyzer/inverted-index-explained.png" alt-text="Índice invertido de exemplo":::

Toda a pesquisa fica inativa para pesquisar os termos armazenados no índice invertido. Quando o usuário emite uma consulta:

1. A consulta é analisada e os termos da consulta são analisados.
1. O índice invertido é então examinado em busca de documentos com termos correspondentes.
1. Por fim, os documentos recuperados são classificados pelo [algoritmo de similaridade](index-ranking-similarity.md).

  :::image type="content" source="media/tutorial-create-custom-analyzer/query-architecture-explained.png" alt-text="Similaridade de classificação do diagrama do processo do Analisador":::

Se os termos da consulta não corresponderem aos termos no índice invertido, os resultados não serão retornados. Para saber mais sobre como funcionam as consultas, confira este artigo sobre [pesquisa de texto completo](search-lucene-query-architecture.md).

> [!Note]
> As [consultas de termo parcial](search-query-partial-matching.md) são uma exceção importante a essa regra. Essas consultas (consulta de prefixo, consulta de caractere curinga, consulta regex) ignoram o processo de análise lexical, diferentemente das consultas de termo regular. Os termos parciais são apenas minúsculos antes de serem combinados com relação aos termos no índice. Se um analisador não estiver configurado para dar suporte a esses tipos de consultas, você geralmente receberá resultados inesperados porque os termos de correspondência não existem no índice.

### <a name="test-analyzer-using-the-analyze-text-api"></a>Analisador de testes usando a API de Análise de Texto

O Azure Cognitive Search fornece uma [API de Análise de Texto](/rest/api/searchservice/test-analyzer) que permite testar analisadores para entender como eles processam o texto.

A API de Análise de Texto é chamada usando a seguinte solicitação:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>

  {
    "text": "(425) 555-0100",
    "analyzer": "standard.lucene"
  }
```

A API então retorna uma lista dos tokens extraídos do texto. Você pode ver que o analisador Lucene padrão divide o número de telefone em três tokens separados:

```json
{
    "tokens": [
        {
            "token": "425",
            "startOffset": 1,
            "endOffset": 4,
            "position": 0
        },
        {
            "token": "555",
            "startOffset": 6,
            "endOffset": 9,
            "position": 1
        },
        {
            "token": "0100",
            "startOffset": 10,
            "endOffset": 14,
            "position": 2
        }
    ]
}
```

De modo inverso, o número de telefone `4255550100` formatado sem pontuação é indexado em um token.

```json
{
  "text": "4255550100",
  "analyzer": "standard.lucene"
}
```

```json
{
    "tokens": [
        {
            "token": "4255550100",
            "startOffset": 0,
            "endOffset": 10,
            "position": 0
        }
    ]
}
```

Lembre-se de que os termos de consulta e os documentos indexados são analisados. Pensando nos resultados da pesquisa da etapa anterior, podemos começar a ver por que esses resultados foram retornados.

Na primeira consulta, os números de telefone incorretos foram retornados porque um dos seus termos, `555`, correspondeu a um dos termos que pesquisamos. Na segunda consulta, apenas um número foi retornado porque era o único registro que tinha um termo correspondente `4255550100`.

## <a name="5---build-a-custom-analyzer"></a>5 – Criar um analisador personalizado

Agora que entendemos os resultados que estamos vendo, vamos criar um analisador personalizado para aprimorar a lógica de geração de tokens.

A meta é fornecer uma pesquisa intuitiva em relação aos números de telefone, independentemente do formato em que a consulta ou a cadeia de caracteres indexada está. Para obter esse resultado, especificaremos um [filtro de caractere](#CharFilters), um [criador de token](#Tokenizers) e um [filtro de token](#TokenFilters).

<a name="CharFilters"></a>

### <a name="character-filters"></a>Filtros de caractere

Os filtros de caractere são usados para processar texto antes de serem inseridos no criador. Os usos comuns dos filtros de caractere incluem a filtragem de elementos HTML ou a substituição de caracteres especiais.

Para números de telefone, queremos remover espaços em branco e caracteres especiais, pois nem todos os formatos de número de telefone contêm os mesmos caracteres e espaços especiais.

```json
"charFilters": [
    {
      "@odata.type": "#Microsoft.Azure.Search.MappingCharFilter",
      "name": "phone_char_mapping",
      "mappings": [
        "-=>",
        "(=>",
        ")=>",
        "+=>",
        ".=>",
        "\\u0020=>"
      ]
    }
  ]
```

O filtro acima removerá `-` `(` `)` `+` `.` e espaços da entrada.

|Entrada|Saída|  
|-|-|  
|`(321) 555-0199`|`3215550199`|  
|`321.555.0199`|`3215550199`|

<a name="Tokenizers"></a>

### <a name="tokenizers"></a>Criadores de token

Criadores de token dividem o texto em tokens e descartam alguns caracteres, como pontuação, ao longo do caminho. Em muitos casos, a meta da geração de tokens é dividir uma frase em palavras individuais.

Para este cenário, usaremos uma palavra-chave criador de token, `keyword_v2`, pois queremos capturar o número de telefone como um termo. Observe que essa não é a única maneira de resolver esse problema. Confira a seção [Abordagens alternativas](#Alternate) abaixo.

A palavra-chave criadores sempre produzirá o mesmo texto que foi fornecido como um termo.

|Entrada|Saída|  
|-|-|  
|`The dog swims.`|`[The dog swims.]`|  
|`3215550199`|`[3215550199]`|

<a name="TokenFilters"></a>

### <a name="token-filters"></a>Filtros de token

Os filtros de token filtrarão ou modificarão os tokens gerados pelo criador. Um uso comum de um filtro de token é colocar todos os caracteres em letras minúsculas usando um filtro de token em minúsculas. Outro uso comum é a filtragem de palavras irrelevantes, como `the`, `and`ou `is`.

Embora não seja necessário usar nenhum desses filtros para este cenário, usaremos um filtro de token nGram para permitir pesquisas parciais de números de telefone.

```json
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.NGramTokenFilterV2",
    "name": "custom_ngram_filter",
    "minGram": 3,
    "maxGram": 20
  }
]
```

#### <a name="ngramtokenfilterv2"></a>NGramTokenFilterV2

O [filtro de token de nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html) divide tokens em n-grams de um determinado tamanho com base nos parâmetros `minGram` e `maxGram`.

Para o analisador de telefone, definimos `minGram` como `3` porque essa é a substring mais curta que esperamos que os usuários pesquisem. `maxGram` é definido como `20` para garantir que todos os números de telefone, mesmo com extensões, caibam em um n-grama.

 O efeito colateral incerto de n-gram é que alguns falsos positivos serão retornados. Corrigiremos isso na etapa 7 criando um analisador separado para pesquisas que não incluem o filtro de token n-gram.

|Entrada|Saída|  
|-|-|  
|`[12345]`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`[3215550199]`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

### <a name="analyzer"></a>Analisador

Com nossos filtros de caractere, criador e filtros de token em vigor, estamos prontos para definir o analisador.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_ngram_filter"
    ],
    "charFilters": [
      "phone_char_mapping"
    ]
  }
]
```

|Entrada|Saída|  
|-|-|  
|`12345`|`[123, 1234, 12345, 234, 2345, 345]`|  
|`(321) 555-0199`|`[321, 3215, 32155, 321555, 3215550, 32155501, 321555019, 3215550199, 215, 2155, 21555, 215550, ... ]`|

Observe que qualquer um dos tokens na saída agora pode ser pesquisado. Se nossa consulta incluir qualquer um desses tokens, o número de telefone será retornado.

Com o analisador personalizado definido, recrie o índice para que o analisador personalizado estará disponível para teste na próxima etapa. Para simplificar, a coleção do Postman cria um índice chamado `tutorial-first-analyzer` com o analisador que definimos.

## <a name="6---test-the-custom-analyzer"></a>6 – Testar o analisador personalizado

Depois de criar o índice, agora você pode testar o analisador que criamos usando a seguinte solicitação:

```http
POST https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-first-analyzer/analyze?api-version=2019-05-06
  Content-Type: application/json
  api-key: <YOUR-ADMIN-API-KEY>  

  {
    "text": "+1 (321) 555-0199",
    "analyzer": "phone_analyzer"
  }
```

Você poderá ver a coleção de tokens resultante do número de telefone:

```json
{
    "tokens": [
        {
            "token": "132",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "1321",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        {
            "token": "13215",
            "startOffset": 1,
            "endOffset": 17,
            "position": 0
        },
        ...
        ...
        ...
    ]
}
```

## <a name="7---build-a-custom-analyzer-for-queries"></a>7 – Criar um analisador personalizado para consultas

Depois de fazer algumas consultas de exemplo no índice com o analisador personalizado, você descobrirá que o cancelamento foi aprimorado e que todos os números de telefone correspondentes agora são retornados. No entanto, o filtro de token n-gram faz com que alguns falsos positivos também sejam retornados. Esse é um efeito colateral comum de um filtro de token de n-gram.

Para evitar falsos positivos, criaremos um analisador separado para consulta. Esse analisador será o mesmo que o analisador que já criamos, mas **sem** o `custom_ngram_filter`.

```json
    {
      "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
      "name": "phone_analyzer_search",
      "tokenizer": "custom_tokenizer_phone",
      "tokenFilters": [],
      "charFilters": [
        "phone_char_mapping"
      ]
    }
```

Na definição de índice, especificamos um `indexAnalyzer` e um `searchAnalyzer`.

```json
    {
      "name": "phone_number",
      "type": "Edm.String",
      "sortable": false,
      "searchable": true,
      "filterable": false,
      "facetable": false,
      "indexAnalyzer": "phone_analyzer",
      "searchAnalyzer": "phone_analyzer_search"
    }
```

Com essa alteração, você está pronto. Recrie o índice, indexe os dados e teste as consultas novamente para verificar se a pesquisa funciona conforme o esperado. Se você estiver usando a coleção do Postman, ele criará um terceiro índice chamado `tutorial-second-analyzer`.

<a name="Alternate"></a>

## <a name="alternate-approaches"></a>Abordagens alternativas

O analisador acima foi projetado para maximizar a flexibilidade de pesquisa. No entanto, ele faz isso com o custo de armazenar muitos termos potencialmente não importantes no índice.

O exemplo a seguir mostra um analisador diferente que também pode ser usado para essa tarefa. 

O analisador funciona bem, exceto para dados de entrada, como `14255550100`, o que dificulta a parte do número de telefone logicamente. Por exemplo, o analisador não seria capaz de separar o código do país, `1`, do código de área `425`. Essa discrepância levaria ao número acima não ser retornado se um usuário não incluísse um código de país na pesquisa.

```json
"analyzers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer",
    "name": "phone_analyzer_shingles",
    "tokenizer": "custom_tokenizer_phone",
    "tokenFilters": [
      "custom_shingle_filter"
    ]
  }
],
"tokenizers": [
  {
    "@odata.type": "#Microsoft.Azure.Search.StandardTokenizerV2",
    "name": "custom_tokenizer_phone",
    "maxTokenLength": 4
  }
],
"tokenFilters": [
  {
    "@odata.type": "#Microsoft.Azure.Search.ShingleTokenFilter",
    "name": "custom_shingle_filter",
    "minShingleSize": 2,
    "maxShingleSize": 6,
    "tokenSeparator": ""
  }
]
```

Você pode ver no exemplo abaixo que o número de telefone é dividido em partes que normalmente você esperaria que um usuário pesquisasse.

|Entrada|Saída|  
|-|-|  
|`(321) 555-0199`|`[321, 555, 0199, 321555, 5550199, 3215550199]`|

Dependendo dos seus requisitos, pode ser uma abordagem mais eficiente ao problema.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Para simplificar, este tutorial teve que você criasse três índices. No entanto, é comum excluir e recriar índices durante os estágios iniciais de desenvolvimento. Você pode excluir um índice no portal do Azure ou usando a seguinte chamada à API:

```http
DELETE https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/tutorial-basic-index?api-version=2019-05-06
  api-key: <YOUR-ADMIN-API-KEY>
```

## <a name="takeaways"></a>Observações

Este tutorial demonstrou o processo de criação e teste de um analisador personalizado. Você criou um índice, indexou dados e consultou em relação ao índice para ver quais resultados da pesquisa eram retornados. Então você usou a API de análise de texto para ver o processo de análise lexical em ação.

Embora o analisador definido neste tutorial ofereça uma solução fácil para pesquisar em números de telefone, esse mesmo processo pode ser usado para criar um analisador de telefone personalizado para qualquer cenário que você possa ter.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua assinatura, é uma boa ideia remover os recursos que já não são necessários no final de um projeto. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com a criação de um analisador personalizado, vamos dar uma olhada em todos os diferentes filtros, criadores e analisadores disponíveis para criar uma experiência de pesquisa avançada.

> [!div class="nextstepaction"]
> [Analisadores personalizados no Azure Cognitive Search](index-add-custom-analyzers.md)