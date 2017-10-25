---
title: "Versão da API REST do Serviço Azure Search 2015-02-28-Preview | Microsoft Docs"
description: "A API do serviço Azure Search Versão 2015-02-28-Preview inclui recursos experimentais como analisadores de linguagem natural e pesquisas do tipo moreLikeThis."
services: search
documentationcenter: na
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/01/2017
ms.author: brjohnst
ms.openlocfilehash: e6ad5c964bfa8421be2706cb4015980e01a271b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-search-service-rest-api-version-2015-02-28-preview"></a>API REST do serviço Azure Search: Versão 2015-02-28-Preview
Este artigo é a documentação de referência para a `api-version=2015-02-28-Preview`. Essa visualização estende a atual versão disponibilizada para o público geral, [api-version=2015-02-28](https://msdn.microsoft.com/library/dn798935.aspx), fornecendo os seguintes recursos experimentais:

* `moreLikeThis` na API [Pesquisar Documentos](#SearchDocs) . Ele localiza outros documentos relevantes para outro documento específico.

Algumas partes adicionais da API REST do `2015-02-28-Preview` são documentadas separadamente. Estão incluídos:

* [Perfis de pontuação](search-api-scoring-profiles-2015-02-28-preview.md)
* [Indexadores](search-api-indexers-2015-02-28-preview.md)

O serviço Azure Search está disponível em várias versões. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes.

## <a name="apis-in-this-document"></a>APIs neste documento
A API do serviço Pesquisa do Azure dá suporte a duas sintaxes de URL para operações de API: simples e OData (consulte [Suporte a OData (API da Pesquisa do Azure)](http://msdn.microsoft.com/library/azure/dn798932.aspx) para obter detalhes). A lista a seguir mostra a sintaxe simples.

[Criar o índice](#CreateIndex)

    POST /indexes?api-version=2015-02-28-Preview

[Atualizar o índice](#UpdateIndex)

    PUT /indexes/[index name]?api-version=2015-02-28-Preview

[Obter o índice](#GetIndex)

    GET /indexes/[index name]?api-version=2015-02-28-Preview

[Listando índices](#ListIndexes)

    GET /indexes?api-version=2015-02-28-Preview

[Obter estatísticas de índice](#GetIndexStats)

    GET /indexes/[index name]/stats?api-version=2015-02-28-Preview

[Analisador de teste](#TestAnalyzer)

    POST /indexes/[index name]/analyze?api-version=2015-02-28-Preview

[Excluir um índice](#DeleteIndex)

    DELETE /indexes/[index name]?api-version=2015-02-28-Preview

[Adicionar, excluir e atualizar dados em um índice](#AddOrUpdateDocuments)

    POST /indexes/[index name]/docs/index?api-version=2015-02-28-Preview

[Pesquisar documentos](#SearchDocs)

    GET /indexes/[index name]/docs?[query parameters]
    POST /indexes/[index name]/docs/search?api-version=2015-02-28-Preview

[Procurar documento](#LookupAPI)

     GET /indexes/[index name]/docs/[key]?[query parameters]

[Contar documentos](#CountDocs)

    GET /indexes/[index name]/docs/$count?api-version=2015-02-28-Preview

[Sugestões](#Suggestions)

    GET /indexes/[index name]/docs/suggest?[query parameters]
    POST /indexes/[index name]/docs/suggest?api-version=2015-02-28-Preview

- - -
<a name="IndexOps"></a>

## <a name="index-operations"></a>Operações de índice
Você pode criar e gerenciar índices no serviço Azure Search por meio de solicitações HTTP simples (POST, GET, PUT, DELETE) em relação a um recurso de índice específico. Para criar um índice, primeiro execute POST para um documento JSON que descreve o esquema de índice. O esquema define os campos do índice, seus tipos de dados e como eles podem ser usados (por exemplo, em pesquisas de texto completo, filtros, classificação ou facetamento). Ele também define os perfis de pontuação, sugestores e outros atributos para configurar o comportamento do índice.

O exemplo a seguir fornece uma ilustração de um esquema usado para pesquisar informações de hotel com o campo Descrição definido em dois idiomas. Observe como os atributos controlam como o campo é usado. Por exemplo, o `hotelId` é usado como a chave do documento (`"key": true`) e é excluído de pesquisas de texto completo (`"searchable": false`).

    {
    "name": "hotels",  
    "fields": [
      {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
      {"name": "baseRate", "type": "Edm.Double"},
      {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
      {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
      {"name": "hotelName", "type": "Edm.String"},
      {"name": "category", "type": "Edm.String"},
      {"name": "tags", "type": "Collection(Edm.String)"},
      {"name": "parkingIncluded", "type": "Edm.Boolean"},
      {"name": "smokingAllowed", "type": "Edm.Boolean"},
      {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
      {"name": "rating", "type": "Edm.Int32"},
      {"name": "location", "type": "Edm.GeographyPoint"}
     ],
     "suggesters": [
      {
       "name": "sg",
       "searchMode": "analyzingInfixMatching",
       "sourceFields": ["hotelName"]
      }
     ]
    }

Depois que o índice for criado, você carregará documentos que populam o índice. Consulte [Adicionar ou Atualizar Documentos](#AddOrUpdateDocuments) para a próxima etapa.

Para um vídeo de introdução à indexação no Azure Search, consulte o [episódio do Cloud Cover do Channel 9 sobre o Azure Search](http://go.microsoft.com/fwlink/p/?LinkId=511509).

<a name="CreateIndex"></a>

## <a name="create-index"></a>Criar o índice
Um índice é o principal meio de organizar e pesquisar documentos no Azure Search, de forma semelhante a como uma tabela organiza registros em um banco de dados. Cada índice tem uma coleção de documentos que estão todos em conformidade com o esquema do índice (nomes de campos, tipos de dados e propriedades), mas os índices também especificam constructos adicionais (sugestores, perfis de pontuação e opções de CORS) que definem outros comportamentos de pesquisa.

Você pode criar um novo índice em um serviço Azure Search usando uma solicitação HTTP POST ou PUT. O corpo da solicitação é um esquema JSON que especifica as informações de configuração e de índice.

    POST https://[service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Como alternativa, você pode usar PUT e especificar o nome do índice no URI. Se o índice não existir, ele será criado.

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]

A criação de um índice determina a estrutura dos documentos armazenados e usados em operações de pesquisa. Popular o índice é uma operação separada. Nessa etapa, você pode usar um [indexador](https://msdn.microsoft.com/library/azure/mt183328.aspx) (disponível para fontes de dados com suporte) ou uma operação [Adicionar, Atualizar ou Excluir Documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx). O índice invertido é gerado quando os documentos são postados.

**Observação**: o número máximo de índices permitidos varia por tipo de preço. O serviço gratuito permite até três índices. O serviço padrão permite 50 índices por serviço de pesquisa. Consulte [Limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para obter detalhes.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Criar Índice** pode ser criada usando um método POST ou PUT. Ao usar POST, você deve fornecer um nome de índice no corpo da solicitação, juntamente com a definição de esquema de índice. Com PUT, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.

A `api-version` é obrigatória. Consulte [Controle de Versão de Serviço de Pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter uma lista das versões disponíveis.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `Content-Type`: obrigatório. Defina-o como `application/json`
* `api-key`: obrigatório. A `api-key` é usada para
* autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Criar Índice** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

<a name="RequestData"></a>
**Sintaxe do Corpo da Solicitação**

O corpo da solicitação contém uma definição de esquema, que inclui a lista de campos de dados em documentos que serão inseridos nesse índice, tipos de dados, atributos, bem como uma lista opcional de perfis de pontuação que são usados para pontuar documentos correspondentes no momento da consulta.

Observe que, para uma solicitação POST, você deve especificar o nome do índice no corpo da solicitação.

Pode haver somente um campo de chave no índice. Ele deve ser um campo de cadeia de caracteres. Esse campo representa o identificador exclusivo para cada documento armazenado no índice.

As principais partes de um índice incluem o seguinte:

* `name`
* `fields` que serão inseridos nesse índice, incluindo nome, tipo de dados e propriedades que definem as ações permitidas nesse campo.
* `suggesters` usados para consultas de preenchimento automático ou digitação antecipada.
* `scoringProfiles` usados para classificação de pontuação de pesquisa personalizada. Consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.
* `analyzers`, `charFilters`, `tokenizers` e `tokenFilters` usados para definir como os seus documentos/consultas são divididos em tokens indexáveis/pesquisáveis. Confira [Análise na Pesquisa do Azure](https://aka.ms//azsanalysis) para obter detalhes.
* `defaultScoringProfile` usado para substituir os comportamentos de pontuação padrão.
* `corsOptions` para permitir consultas entre origens em relação a seu índice.

A sintaxe para estruturar a carga da solicitação é indicada a seguir. Uma solicitação de exemplo é fornecida mais adiante neste tópico.

    {
      "name": (optional on PUT; required on POST) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false,              
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }

**Atributos de índice**

Os atributos a seguir podem ser definidos ao criar um índice. Para obter detalhes sobre pontuação e perfis de pontuação, consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx).

`name` ‒ define o nome do campo.

`type` ‒ define o tipo de dados do campo.

`searchable` ‒ marca o campo como texto completo pesquisável. Isso significa que ele será submetido a análise, como separação de palavras, durante a indexação. Se você definir um campo `searchable` para um valor como "dia ensolarado", internamente ele será dividido nos tokens individuais "dia" e "ensolarado". Isso habilita pesquisas de texto completo para esses termos. Os campos dos tipos `Edm.String` ou `Collection(Edm.String)` são `searchable` por padrão. Campos de outros tipos não podem ser `searchable`.

* **Observação**: os campos `searchable` consomem espaço extra no índice, pois o Azure Search armazenará uma versão com token adicional do valor do campo para pesquisas de texto completo. Se você desejar economizar espaço no índice e não for necessário incluir um campo em pesquisas, defina `searchable` como `false`.

`filterable` - permite que o campo seja referenciado em consultas `$filter`. `filterable` difere de `searchable` da maneira como cadeias de caracteres são tratadas. Campos dos tipos `Edm.String` ou `Collection(Edm.String)` que são `filterable` não são submetidos à quebra de palavras. Portanto, as comparações são apenas para correspondências exatas. Por exemplo, se você definir um campo `f` como "dia ensolarado", `$filter=f eq 'sunny'` não encontrará correspondências, mas `$filter=f eq 'sunny day'` as encontrará. Todos os campos são `filterable` por padrão.

`sortable` ‒ por padrão o sistema classifica os resultados pela pontuação, mas, em muitas experiências, os usuários desejarão classificar por campos nos documentos. Campos do tipo `Collection(Edm.String)` não podem ser `sortable`. Todos os outros campos são `sortable` por padrão.

`facetable`‒ geralmente usado em uma apresentação dos resultados de pesquisa que inclui a contagem de ocorrências por categoria (por exemplo, pesquisar câmeras digitais e ver as ocorrências por marca, por megapixels, por preço etc.). Essa opção não pode ser usada com campos do tipo `Edm.GeographyPoint`. Todos os outros campos são `facetable` por padrão.

* **Observação**: campos do tipo `Edm.String` que são `filterable`, `sortable` ou `facetable` podem ter no máximo 32 KB de comprimento. Isso ocorre porque esses campos são tratados como um único termo de pesquisa, e o comprimento máximo de um termo no Azure Search é 32 KB. Se precisar armazenar mais texto do que isso em um único campo de cadeia de caracteres, você precisará definir explicitamente `filterable`, `sortable` e `facetable` como `false` na definição do índice.
* **Observação**: se um campo não tiver nenhum dos atributos acima definidos como `true` (`searchable`, `filterable`, `sortable` ou `facetable`) o campo será efetivamente excluído do índice invertido. Essa opção é útil para campos que não são usados em consultas, mas são necessários em resultados de pesquisa. A exclusão desses campos do índice melhora o desempenho.

`key` ‒ marca o campo como contendo identificadores exclusivos para documentos no índice. Exatamente um campo deve ser escolhido como o campo `key`, e ele deve ser do tipo `Edm.String`. Campos de chave podem ser usados para pesquisar documentos diretamente por meio da [API de pesquisa](#LookupAPI).

`retrievable` ‒ define se o campo pode ser retornado em um resultado de pesquisa.  Isso é útil quando você quer usar um campo (por exemplo, margem) como mecanismo de filtro, classificação ou pontuação, mas não deseja que o campo seja visível ao usuário final. Esse atributo deve ser `true` for `key` .

`analyzer` - define o nome do analisador a usar para o campo no tempo de pesquisa e de indexação. Para o conjunto de valores permitidos, consulte [Analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção pode ser usada apenas com campos `searchable`, e não pode ser definida com `searchAnalyzer` ou `indexAnalyzer`.  Depois que o analisador for escolhido, ele não poderá ser alterado para o campo.

`searchAnalyzer` - define o nome do analisador usado no tempo de pesquisa para o campo. Para o conjunto de valores permitidos, consulte [Analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção só pode ser usada com campos `searchable` . Ele deve ser definido juntamente com `indexAnalyzer` e não pode ser definido com a opção `analyzer`. Esse analisador pode ser atualizado em um campo existente.

`indexAnalyzer` - define o nome do analisador usado no tempo de indexação para o campo. Para o conjunto de valores permitidos, consulte [Analisadores](https://msdn.microsoft.com/library/mt605304.aspx). Essa opção só pode ser usada com campos `searchable` . Ele deve ser definido juntamente com `searchAnalyzer` e não pode ser definido com a opção `analyzer`. Depois que o analisador for escolhido, ele não poderá ser alterado para o campo.

`suggesters` ‒ define o modo de pesquisa e campos que são a origem do conteúdo para obter sugestões. Consulte [Sugestores](#Suggesters) para obter detalhes.

`scoringProfiles` ‒ define comportamentos de pontuação personalizados que permitem influenciam quais itens aparecem em posição mais elevada nos resultados de pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Consulte [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter mais informações sobre os atributos usados em um perfil de pontuação.

<!-- This is a standalone topic in MSDN -->
<a name="LanguageSupport"></a>
**Suporte ao idioma**

Os campos pesquisáveis são submetidos a análise que, frequentemente, envolve quebra de palavras, normalização do texto e filtragem de termos. Por padrão, os campos pesquisáveis no Azure Search são analisados com o [Analisador Apache Lucene Padrão](http://lucene.apache.org/core/4_9_0/analyzers-common/index.html), que quebra o texto em elementos seguindo as regras de ["Segmentação de texto Unicode"](http://unicode.org/reports/tr29/). Além disso, o analisador padrão converte todos os caracteres em sua forma em letras minúsculas. Documentos indexados e termos de pesquisa são submetidos a análise durante a indexação e o processamento de consultas.

A Pesquisa do Azure oferece suporte a vários idiomas. Cada um desses idiomas requer um analisador de texto não padrão que leva em consideração as características de determinado idioma. O Azure Search oferece dois tipos de analisadores:

* 35 analisadores ativados pela Lucene.
* 50 analisadores apoiados pela tecnologia de processamento de idioma natural proprietária da Microsoft usada no Office e no Bing.

Alguns desenvolvedores talvez prefiram a solução mais familiar, simples e aberta da Lucene. Os analisadores Lucene são mais rápidos, mas os analisadores da Microsoft têm recursos avançados, como derivação, decomposição de palavras (em idiomas como alemão, dinamarquês, holandês, sueco, norueguês, estoniano, finlandês, húngaro, eslovaco) e reconhecimento de identidade (URLs, emails, datas, números). Se possível, você deve executar comparações entre os analisadores da Microsoft e da Lucene para decidir qual é a melhor opção.

***Veja a comparação***

O analisador de inglês da Lucene amplia o analisador padrão. Ele remove possessivos (apóstrofos à direita) de palavras, aplica a lematização conforme o [algoritmo de lematização de Porter](http://tartarus.org/~martin/PorterStemmer/) e remove as [palavras irrelevantes](http://en.wikipedia.org/wiki/Stop_words) do inglês.

Em comparação, o analisador da Microsoft executa a derivação em vez da lematização. Isso significa que ele pode tratar muito melhor as formas irregulares e inflexivas de palavras, o que gera resultados da pesquisa mais relevantes (observe o módulo 7 da [Apresentação MVA da Pesquisa do Azure](http://www.microsoftvirtualacademy.com/training-courses/adding-microsoft-azure-search-to-your-websites-and-apps) para obter mais detalhes).

A indexação com analisadores da Microsoft é, em média, de duas a três vezes mais lenta do que seus equivalentes da Lucene, dependendo do idioma. O desempenho da pesquisa não dever significativamente afetado para consultas de tamanho médio.

***Configuração***

Para cada campo na definição do índice, você pode definir a propriedade `analyzer` como um nome de analisador que especifica o idioma e o fornecedor. O mesmo analisador será aplicado durante a indexação e a pesquisa desse campo.
Por exemplo, você pode ter campos separados para descrições de hotéis em inglês, francês e espanhol, existentes lado a lado no mesmo índice. Use o [parâmetro de consulta 'searchFields'](#SearchQueryParameters) para descrever qual campo específico a um idioma pesquisar em suas consultas. Você pode examinar os exemplos de consultas que incluem a propriedade `analyzer` em [Pesquisar documentos](#SearchDocs). 

***Lista de analisadores***

Veja abaixo uma lista de idiomas com suporte juntamente com nomes de analisador da Lucene e da Microsoft.

<table style="font-size:12">
    <tr>
        <th>Linguagem</th>
        <th>Nome do analisador da Microsoft</th>
        <th>Nome do analisador da Lucene</th>
    </tr>
    <tr>
        <td>Árabe</td>
        <td>ar.microsoft</td>
        <td>ar.lucene</td>        
    </tr>
    <tr>
        <td>Armênia</td>
        <td></td>
        <td>hy.Lucene</td>
      </tr>
    <tr>
        <td>Bangla</td>
        <td>bn.microsoft</td>
        <td></td>
    </tr>
      <tr>
        <td>Basco</td>
        <td></td>
        <td>Eu.Lucene</td>
    </tr>
      <tr>
         <td>Búlgaro</td>
        <td>bg.microsoft</td>
        <td>BG.Lucene</td>
      </tr>
      <tr>
        <td>Catalão</td>
        <td>ca.microsoft</td>
        <td>CA.Lucene</td>          
      </tr>
    <tr>
        <td>Chinês (simplificado)</td>
        <td>zh-Hans.microsoft</td>
        <td>zh-Hans.lucene</td>        
    </tr>
    <tr>
        <td>Chinês (tradicional)</td>
        <td>zh-Hant.microsoft</td>
        <td>zh-Hant.lucene</td>        
    <tr>
    <tr>
        <td>Croata</td>
        <td>hr.microsoft</td>
        <td/></td>
    </tr>
    <tr>
        <td>Tcheco</td>
        <td>cs.microsoft</td>
        <td>cs.lucene</td>        
    </tr>    
    <tr>
        <td>Dinamarquês</td>
        <td>da.Microsoft</td>
        <td>da.lucene</td>        
    </tr>    
    <tr>
        <td>Holandês</td>
        <td>nl.microsoft</td>
        <td>nl.lucene</td>    
    </tr>    
    <tr>
        <td>Inglês</td>        
        <td>en.Microsoft</td>
        <td>en.lucene</td>        
    </tr>
    <tr>
        <td>Estoniano</td>
        <td>et.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Finlandês</td>
        <td>fi.microsoft</td>
        <td>fi.lucene</td>        
    </tr>    
    <tr>
        <td>Francês</td>
        <td>fr.microsoft</td>
        <td>fr.lucene</td>        
    </tr>
    <tr>
        <td>Galego</td>
        <td></td>
        <td>GL.Lucene</td>        
      </tr>
    <tr>
        <td>Alemão</td>
        <td>de.microsoft</td>
        <td>de.lucene</td>        
    </tr>
    <tr>
        <td>Grego</td>
        <td>el.microsoft</td>
        <td>el.lucene</td>        
    </tr>
    <tr>
        <td>Guzerate</td>
        <td>gu.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Hebraico</td>
        <td>he.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Híndi</td>
        <td>hi.microsoft</td>
        <td>hi.lucene</td>        
    </tr>
    <tr>
        <td>Húngaro</td>        
        <td>hu.Microsoft</td>
        <td>hu.lucene</td>
    </tr>
    <tr>
        <td>Islandês</td>
        <td>is.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Indonésio (Bahasa)</td>
        <td>id.microsoft</td>
        <td>id.lucene</td>        
    </tr>
    <tr>
        <td>Irlandês</td>
        <td></td>
          <td>GA.Lucene</td>
    </tr>
    <tr>
        <td>Italiano</td>
        <td>it.microsoft</td>
        <td>it.lucene</td>        
    </tr>
    <tr>
        <td>Japonês</td>
        <td>ja.microsoft</td>
        <td>ja.lucene</td>

    </tr>
    <tr>
        <td>Kannada</td>
        <td>ka.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Coreano</td>
        <td>ko.Microsoft</td>
        <td>ko.lucene</td>
    </tr>
    <tr>
        <td>Letão</td>        
        <td>lv.microsoft</td>
        <td>lv.lucene</td>    
    </tr>
    <tr>
        <td>Lituano</td>
        <td>lt.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaiala</td>
        <td>ml.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Malaio (latino)</td>
        <td>ms.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Marati</td>
        <td>mr.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Norueguês</td>
        <td>nb.microsoft</td>
        <td>no.lucene</td>        
    </tr>
      <tr>
        <td>Persa</td>
        <td></td>
        <td>FA.Lucene</td>        
      </tr>
    <tr>
        <td>Polonês</td>
        <td>pl.Microsoft</td>
        <td>pl.lucene</td>        
    </tr>
    <tr>
        <td>Português (Brasil)</td>
        <td>pt-Br.microsoft</td>
        <td>pt-Br.lucene</td>        
    </tr>
    <tr>
        <td>Português (Portugal)</td>
        <td>pt-Pt.microsoft</td>        
        <td>pt-Pt.lucene</td>
    </tr>
    <tr>
        <td>Punjabi</td>
        <td>pa.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Romeno</td>
        <td>ro.microsoft</td>
        <td>ro.lucene</td>
    </tr>
    <tr>
        <td>Russo</td>
        <td>ru.microsoft</td>
        <td>ru.lucene</td>    
    </tr>
    <tr>
        <td>Sérvio (cirílico)</td>
        <td>sr-cyrillic.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Sérvio (latino)</td>
        <td>sr-latin.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Eslovaco</td>
        <td>sk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Esloveno</td>
        <td>sl.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Espanhol</td>
        <td>es.microsoft</td>
        <td>es.lucene</td>
    </tr>
    <tr>
        <td>Sueco</td>
        <td>sv.microsoft</td>
        <td>sv.lucene</td>
    </tr>

    <tr>
        <td>Tâmil</td>
        <td>ta.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Télugo</td>
        <td>te.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Tailandês</td>
        <td>th.microsoft</td>
        <td>th.lucene</td>
    </tr>
    <tr>
        <td>Turco</td>
        <td>tr.microsoft</td>
        <td>tr.lucene</td>        
    </tr>
    <tr>
        <td>Ucraniano</td>
        <td>uk.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Urdu</td>
        <td>ur.microsoft</td>
        <td></td>
    </tr>
    <tr>
        <td>Vietnamita</td>
        <td>vi.microsoft</td>
        <td></td>
    </tr>
    <td colspan="3">Além disso, o Azure Search fornece configurações do analisador que não são específicas de idiomas</td>
    <tr>
        <td>Dobra ASCII padrão</td>
        <td>standardasciifolding.Lucene</td>
        <td>
        <ul>
            <li>Segmentação de texto Unicode (Criador de Token Padrão)</li>
            <li>Filtro de dobra ASCII ‒ converte caracteres Unicode que não pertencem ao conjunto dos primeiros 127 caracteres ASCII em seus equivalentes em ASCII. Isso é útil para remover os sinais diacríticos.</li>
        </ul>
        </td>
    </tr>
</table>

Todos os analisadores com nomes anotados com <i>lucene</i> são da plataforma de [analisadores de idiomas do Apache Lucene](http://lucene.apache.org/core/4_9_0/analyzers-common/overview-summary.html). Mais informações sobre o filtro de dobra ASCII podem ser encontradas [aqui](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html).

**Sugestores**

Um `suggester` define quais campos em um índice são usados para oferecer suporte a auto-completar em pesquisas. Normalmente, cadeias de caracteres de pesquisa parciais são enviadas à [API de sugestões](#Suggestions) enquanto o usuário está digitando uma consulta de pesquisa, e a API retorna um conjunto de frases sugeridas. Um sugestor que você define no índice determina quais campos são usados para construir os termos de pesquisa de preenchimento automático. Consulte [Sugestores](#Suggesters) para obter detalhes de configuração.

**Perfis de pontuação**

`scoringProfile` ‒ define comportamentos de pontuação personalizados que permitem influenciam quais itens aparecem em posição mais elevada nos resultados de pesquisa. Perfis de pontuação são compostos de funções e pesos de campos. Para usá-las, você pode especificar um perfil por nome na sequência de consulta.

Um padrão de pontuação perfil funciona nos bastidores para calcular uma pontuação para cada item em um conjunto de resultados de pesquisa. Você pode usar o perfil de pontuação interno e sem o nome. Como alternativa, defina `defaultScoringProfile` para usar um perfil personalizado como padrão, chamado sempre que um perfil personalizado não for especificado na sequência de consulta.

Consulte [Adicionar perfis de pontuação para um índice de pesquisa (API de REST de serviço de pesquisa do Azure)](search-api-scoring-profiles-2015-02-28-preview.md) para obter detalhes.

**Opções de CORS**

O Javascript do lado do cliente não pode chamar APIs por padrão, pois o navegador impedirá todas as solicitações entre origens. Habilite o CORS (Compartilhamento de Recursos entre Origens) definindo o atributo `corsOptions` para permitir consultas entre origens em seu índice. Observe que apenas APIs de consulta dão suporte a CORS por motivos de segurança. As seguintes opções podem ser definidas para CORS:

* `allowedOrigins` (obrigatório): essa é uma lista de origens às quais será concedido acesso ao índice. Isso significa que qualquer código Javascript fornecido por essas origens poderá consultar seu índice (supondo que ele forneça a chave de API correta). Cada origem normalmente tem o formato `protocol://fully-qualified-domain-name:port` , embora a porta muitas vezes seja omitida. Consulte [este artigo](http://go.microsoft.com/fwlink/?LinkId=330822) para obter mais detalhes.
  * Se você quiser permitir o acesso a todas as origens, inclua `*` como um único item na matriz `allowedOrigins`. Observe que **essa não é uma prática recomendável para serviços de pesquisa de produção.** No entanto, pode ser útil para fins de depuração ou de desenvolvimento.
* `maxAgeInSeconds` (opcional): navegadores usam esse valor para determinar a duração (em segundos) para respostas de simulação de CORS de cache. Esse deve ser um inteiro não negativo. Quanto maior for esse valor, melhor será o desempenho, porém, mais tempo levará para que as alterações de política CORS entrem em vigor. Se ele não for definido, uma duração padrão de cinco minutos será usada.

<a name="CreateUpdateIndexExample"></a>
**Exemplo de Corpo de Solicitação**

    {
      "name": "hotels",  
      "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String"},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean"},
        {"name": "smokingAllowed", "type": "Edm.Boolean"},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
      ],
      "suggesters": [
        {
          "name": "sg",
          "searchMode": "analyzingInfixMatching",
          "sourceFields": ["hotelName"]
        }
      ]
    }

**Resposta**

Para uma solicitação bem-sucedida: "201 Criado".

Por padrão, o corpo da resposta conterá o JSON para a definição de índice que foi criada. Se o cabeçalho da solicitação `Prefer` for definido como `return=minimal`, o corpo da resposta estará vazio, e o código de status de êxito será "204 Sem Conteúdo" em vez de "201 Criado". Isso ocorre independentemente de PUT ou POST ter sido usado para criar o índice.

**Comentários**

Atualmente, há suporte limitado para atualizações de esquema de índice. Atualmente, não há suporte às atualizações de esquema que exigem reindexação, como a alteração de tipos de campo. Embora os campos existentes não possam ser alterados ou excluídos, novos campos podem ser adicionados a um índice existente a qualquer momento. Quando um novo campo é adicionado, todos os documentos existentes no índice automaticamente têm um valor nulo para esse campo. Nenhum espaço de armazenamento adicional será consumido até que novos documentos sejam adicionados ao índice.

<a name="Suggesters"></a>

## <a name="suggesters"></a>Sugestores
O recurso de sugestões de pesquisa do Azure é um recurso de consulta de digitação antecipada ou preenchimento automático, fornecendo uma lista de possíveis condições de pesquisa em resposta às entradas parciais de cadeia de caracteres inseridas em uma caixa de pesquisa. Você já deve ter notado sugestões de consulta ao usar mecanismos de pesquisa da web comercial: digitando ".NET" no Bing produz uma lista de termos de ".NET 4.5", ".NET Framework 3.5", e assim por diante. Ao usar o serviço de pesquisa da API REST, a implementação de sugestões em um aplicativo personalizado de Pesquisa do Azure requer o seguinte:

* Habilite sugestões adicionando uma construção de **sugestor** em seu índice, fornecendo o nome, o modo de pesquisa e uma lista de campos para os quais a digitação antecipada é chamada. Por exemplo, se você especificar "cityName" como um campo de origem, digite uma cadeia de caracteres de pesquisa parcial de "Sea" resultará em "Seattle", "Seaside" e "Seatac" (todos os três são nomes de cidades real) oferecidos como sugestões de consulta para o usuário.
* Chame sugestões chamando o [API de sugestões](#Suggestions) no código do aplicativo. Normalmente, cadeias de caracteres de pesquisa parciais são enviadas ao serviço enquanto o usuário está digitando uma consulta de pesquisa, e a API retorna um conjunto de frases sugeridas.

Este artigo explica como configurar um **sugestor**. Você também deve examinar o [API sugestões](#Suggestions) para obter detalhes sobre como um sugestor é usado.

**Uso**

`Suggesters` são criados no índice e funcionam melhor quando usados para sugerir documentos específicos, em vez de termos ou frases flexíveis. Os campos de melhores candidatos são títulos, nomes e outras frases relativamente curtas que podem identificar um item. Os campos repetitivos, como categorias e marcas, ou campos muito longos, como campos de comentários ou descrições, são menos eficazes.

Como parte da definição do índice, você pode adicionar um único sugestor à coleção de `suggesters` . Propriedades que definem um sugestor incluem o seguinte:

* `name`: o nome do sugestor. Use o nome do sugestor ao chamar a API de `suggest` .
* `searchMode`: a estratégia usada para pesquisar frases candidatas. O único modo com suporte atualmente é `analyzingInfixMatching`, que executa a correspondência flexível de frases no início ou no meio da frase.
* `sourceFields`: uma lista de um ou mais campos que são a fonte do conteúdo para obter sugestões. Somente os campos dos tipos `Edm.String` e `Collection(Edm.String)` podem ser fontes de sugestões. Somente os campos que não têm um analisador de idioma personalizado definido podem ser usados.

**Exemplo de sugestor**

Um sugestor faz parte do índice. Apenas um sugestor pode existir na coleção do `suggesters` na versão atual, junto com a coleção de campos e `scoringProfiles`.

        {
          "name": "hotels",
          "fields": [
             . . .
           ],
          "suggesters": [
            {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields: ["hotelName", "category"]
            }
          ],
          "scoringProfiles": [
             . . .
          ]
        }

> [!NOTE]
> Se você usou a versão de visualização pública do Azure Search, `suggesters` substitui uma propriedade booliana antiga (`"suggestions": false`) que dava suporte apenas a sugestões de prefixo para cadeias de caracteres curtas (3-25 caracteres). Sua substituição, `suggesters`, dá suporte à correspondências infixas que localizam os termos correspondentes no início ou no meio do conteúdo do campo, com melhor tolerância para erros em cadeias de caracteres de pesquisa. Começando com a versão disponível, esta é a única implementação da API de sugestões. A propriedade `suggestions` mais antiga que foi introduzida na `api-version=2014-07-31-Preview` continua funcionando nessa versão, mas não na versão `2015-02-28` ou posteriores da Pesquisa do Azure.
> 
> 

<a name="UpdateIndex"></a>

## <a name="update-index"></a>Atualizar o índice
Você pode atualizar um índice existente no Azure Search usando uma solicitação HTTP PUT. As atualizações podem incluir adição de novos campos ao esquema existente, modificação de opções de CORS e modificação de perfis de pontuação. Confira [Adicionar perfis de pontuação](https://msdn.microsoft.com/library/azure/dn798928.aspx) para saber mais. Especifique o nome do índice a ser atualizado no URI da solicitação:

    PUT https://[search service url]/indexes/[index name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Importante:** o suporte para atualizações de esquema de índice é limitado às operações que não exigem a recriação do índice de pesquisa. Atualmente, não há suporte às atualizações de esquema que exigem reindexação, como a alteração de tipos de campo. Novos campos podem ser adicionados a qualquer momento, embora os campos existentes não possam ser alterados nem excluídos. O mesmo se aplica a `suggesters`. Novos campos podem ser adicionados a um sugestor no momento em que os campos são adicionados, mas os campos não podem ser removidos dos `suggesters` e campos existentes não podem ser adicionados a `suggesters`.

Ao se adicionar um novo campo a um índice, todos os documentos existentes no índice automaticamente terão um valor nulo para esse campo. Nenhum espaço de armazenamento adicional será consumido até que novos documentos sejam adicionados ao índice.

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Atualizar Índice** é criada usando HTTP PUT. Com PUT, o nome do índice é parte da URL. Se o índice não existir, ele será criado. Se já existir, ele será atualizado para a nova definição.

O nome do índice deve estar em letras minúsculas, começar com uma letra ou número, não conter barras ou pontos e ter menos de 128 caracteres. Depois de iniciar o nome do índice com uma letra ou número, o restante do nome pode incluir qualquer letra, número e traços, desde que os traços não sejam consecutivos.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `Content-Type`: obrigatório. Defina-o como `application/json`
* `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Atualizar Índice** deve incluir um cabeçalho `api-key` definido como sua chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Sintaxe de corpo da solicitação**

Ao se atualizar um índice existente, o corpo deve incluir a definição de esquema original, além de novos campos que você está adicionando, bem como os perfis de pontuação modificados, sugestores e as opções de CORS, se houver. Se não estiver modificando os perfis de pontuação e as opções de CORS, você deverá incluir os originais de quando o índice foi criado. Em geral, o melhor padrão a ser usado para atualizações é recuperar a definição do índice com GET, modificá-la e atualizá-la com PUT.

A sintaxe do esquema usada para criar um índice é reproduzida aqui para sua conveniência. Consulte [Criar Índice](#CreateIndex) para obter mais detalhes.

    {
      "name": (optional) "name_of_index",
      "fields": [
        {
          "name": "name_of_field",
          "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
          "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
          "filterable": true (default) | false,
          "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
          "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
          "key": true | false (default, only Edm.String fields can be keys),
          "retrievable": true (default) | false, 
          "analyzer": "name of the analyzer used for search and indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
          "searchAnalyzer": "name of the search analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
          "indexAnalyzer": "name of the indexing analyzer" (only if 'searchAnalyzer' is set and 'analyzer' is not set)
        }
      ],
      "suggesters": [
        {
          "name": "name of suggester",
          "searchMode": "analyzingInfixMatching" (other modes may be added in the future),
          "sourceFields": ["field1", "field2", ...]
        }
      ],
      "scoringProfiles": [
        {
          "name": "name of scoring profile",
          "text": (optional, only applies to searchable fields) {
            "weights": {
              "searchable_field_name": relative_weight_value (positive numbers),
              ...
            }
          },
          "functions": (optional) [
            {
              "type": "magnitude | freshness | distance | tag",
              "boost": # (positive number used as multiplier for raw score != 1),
              "fieldName": "...",
              "interpolation": "constant | linear (default) | quadratic | logarithmic",
              "magnitude": {
                "boostingRangeStart": #,
                "boostingRangeEnd": #,
                "constantBoostBeyondRange": true | false (default)
              },
              "freshness": {
                "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
              },
              "distance": {
                "referencePointParameter": "...", (parameter to be passed in queries to use as reference location, see "scoringParameter" for syntax details)
                "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
              },
              "tag": {
                "tagsParameter": "..." (parameter to be passed in queries to specify list of tags to compare against target field, see "scoringParameter" for syntax details)
              }
            }
          ],
          "functionAggregation": (optional, applies only when functions are specified)
            "sum (default) | average | minimum | maximum | firstMatching"
        }
      ],
      "analyzers":(optional)[ ... ],
      "charFilters":(optional)[ ... ],
      "tokenizers":(optional)[ ... ],
      "tokenFilters":(optional)[ ... ],
      "defaultScoringProfile": (optional) "...",
      "corsOptions": (optional) {
        "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
        "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
      }
    }


**Resposta**

Para uma solicitação bem-sucedida: "204 sem Conteúdo".

Por padrão, o corpo da resposta estará vazio. No entanto, se o cabeçalho `Prefer` da solicitação for definido como `return=representation`, o corpo da resposta conterá o JSON para a definição de índice que foi atualizada. Nesse caso, o código de status de êxito será "200 OK".

**Atualizando a definição de índice com analisadores personalizados**

Quando um analisador, um criador de token, um filtro de token ou um filtro de char é definido, ele não pode ser modificado. Outros novos poderão ser adicionados a um índice existente apenas se o sinalizador `allowIndexDowntime` estiver definido como true na solicitação de atualização de índice: 

`PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true`

Observe que essa operação deixará seu índice offline por pelo menos alguns segundos, fazendo com que suas solicitações de indexação e de consulta falhem. O desempenho e a disponibilidade de gravação do índice podem ser prejudicados por vários minutos após o índice ser atualizado, ou por mais tempo em caso de índices muito grandes.

<a name="ListIndexes"></a>

## <a name="list-indexes"></a>Listar Índices
A operação **Listar Índices** retorna uma lista dos índices existentes no momento em seu serviço Azure Search.

    GET https://[service name].search.windows.net/indexes?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Listar Índices** pode ser criada usando o método GET.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Listar Índices** deve incluir uma `api-key` definida como uma chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

Aqui está um exemplo de corpo de resposta:

    {
      "value": [
        {
          "name": "Books",
          "fields": [
            {"name": "ISBN", ...},
            ...
          ]
        },
        {
          "name": "Games",
          ...
        },
        ...
      ]
    }

Observe que você pode filtrar a resposta para apenas as propriedades em que está interessado. Por exemplo, se você quiser apenas uma lista de nomes de índice, use a opção de consulta OData `$select` :

    GET /indexes?api-version=2015-02-28-Preview&$select=name

Nesse caso, a resposta do exemplo anterior seria exibida da seguinte maneira:

    {
      "value": [
        {"name": "Books"},
        {"name": "Games"},
        ...
      ]
    }

Essa é uma técnica útil para economizar largura de banda se você tiver muitos índices em seu serviço de pesquisa.

<a name="GetIndex"></a>

## <a name="get-index"></a>Obter o índice
A operação **Obter Índice** obtém a definição de índice do Azure Search.

    GET https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Obter Índice** pode ser criada usando o método GET.

O [nome do índice] na solicitação URI especifica qual índice deve ser retornado da coleção de índices.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Obter Índice** deve incluir uma `api-key` definida como uma chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

Consulte o exemplo de JSON em [Criando e atualizando um índice](#CreateUpdateIndexExample) para obter um exemplo da carga de resposta.

<a name="DeleteIndex"></a>

## <a name="delete-index"></a>Excluir o índice
A operação **Excluir Índice** remove um índice e os documentos associados de seu serviço Azure Search. Você pode obter o nome do índice por meio do painel de serviço no Portal  do Azure ou por meio da API. Consulte [Listar índices](#ListIndexes) para obter detalhes.

    DELETE https://[service name].search.windows.net/indexes/[index name]?api-version=[api-version]
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Excluir Índice** pode ser criada usando o método DELETE.

O [nome do índice] no URI da solicitação especifica qual índice deve ser excluído da coleção de índices.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Excluir Índice** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 204 Sem Conteúdo é retornado para uma resposta bem-sucedida.

<a name="GetIndexStats"></a>

## <a name="get-index-statistics"></a>Obter estatísticas de índice
A operação **Obter Estatísticas do Índice** retorna uma contagem de documentos do Azure Search para o índice atual, além do uso do armazenamento.

    GET https://[service name].search.windows.net/indexes/[index name]/stats?api-version=[api-version]
    api-key: [admin key]

> [!NOTE]
> As estatísticas de tamanho de armazenamento e contagem de documentos são coletadas a cada poucos minutos, não em tempo real. Portanto, talvez as estatísticas retornadas por essa API não reflitam as alterações causadas por operações de indexação recentes.
> 
> 

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Obter Estatísticas do Índice** pode ser criada usando o método GET.

O [nome do índice] no URI da solicitação instrui o serviço a retornar as estatísticas de índice para o índice especificado.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Obter Estatísticas de Índice** deve incluir um `api-key` definido como uma chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

O corpo da resposta está no seguinte formato:

    {
      "documentCount": number,
      "storageSize": number (size of the index in bytes)
    }

<a name="TestAnalyzer"></a>

## <a name="test-analyzer"></a>Analisador de teste
O **Analisar API** mostra como um analisador divide o texto em tokens.

    POST https://[service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. A solicitação **Analisar API** pode ser criada usando o método POST.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Analisar API** deve incluir uma `api-key` definida como uma chave de administração (em vez de uma chave de consulta).

Você também precisará do nome do índice e do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

    {
      "text": "Text to analyze",
      "analyzer": "analyzer_name"
    }

ou o

    {
      "text": "Text to analyze",
      "tokenizer": "tokenizer_name",
      "tokenFilters": (optional) [ "token_filter_name" ],
      "charFilters": (optional) [ "char_filter_name" ]
    }

`analyzer_name`, `tokenizer_name`, `token_filter_name` e `char_filter_name` precisam ser nomes válidos de analisadores, criadores de token, filtros de token e filtros de char predefinidos ou personalizados para o índice. Para saber mais sobre o processo de análise léxica, consulte [Análise na Pesquisa do Azure](https://aka.ms/azsanalysis).

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

O corpo da resposta está no seguinte formato:

    {
      "tokens": [
        {
          "token": string (token),
          "startOffset": number (index of the first character of the token),
          "endOffset": number (index of the last character of the token),
          "position": number (position of the token in the input text)
        },
        ...
      ]
    }

**Analisar exemplo de API**

**Solicitação**

    {
      "text": "Text to analyze",
      "analyzer": "standard"
    }

**Resposta**

    {
      "tokens": [
        {
          "token": "text",
          "startOffset": 0,
          "endOffset": 4,
          "position": 0
        },
        {
          "token": "to",
          "startOffset": 5,
          "endOffset": 7,
          "position": 1
        },
        {
          "token": "analyze",
          "startOffset": 8,
          "endOffset": 15,
          "position": 2
        }
      ]
    }

- - -
<a name="DocOps"></a>

## <a name="document-operations"></a>Operações de documento
Na Pesquisa do Azure, um índice é armazenado na nuvem e preenchido usando documentos JSON que você carrega no serviço. Todos os documentos que você carrega formam o corpus de seus dados de pesquisa. Documentos contêm campos, alguns dos quais são indexados em termos de pesquisa ao serem carregados. O segmento de URL `/docs` na API do Azure Search representa a coleção de documentos em um índice. Todas as operações executadas na coleção, como carregar, mesclar, excluir ou consultar documentos, ocorrem no contexto de um único índice. Portanto, as URLs para essas operações sempre começarão com `/indexes/[index name]/docs` para um nome de índice específico.

O código do aplicativo deve gerar documentos JSON a serem carregados no Azure Search ou é possível usar um [indexador](https://msdn.microsoft.com/library/dn946891.aspx) para carregar documentos, caso a fonte de dados seja o Banco de Dados SQL do Azure ou o Azure Cosmos DB. Normalmente, os índices são preenchidos por meio de um único conjunto de dados que você fornece.

Você deve planejar ter um documento para cada item que deseja pesquisar. Um aplicativo de aluguel de filmes pode ter um documento por filme, um aplicativo de vitrine pode ter um documento por SKU, um aplicativo de cursos online pode ter um documento por curso, uma empresa de pesquisa pode ter um documento para cada artigo acadêmico em seu repositório e assim por diante.

Os documentos consistem em um ou mais campos. Os campos podem conter texto indexado pelo Azure Search em termos de pesquisa, bem como valores não indexados ou que não são de texto, que podem ser usadas em filtros ou perfis de pontuação. Os nomes, tipos de dados e recursos de pesquisa com suporte para cada campo são determinados pelo esquema de índice. Um dos campos em cada esquema de índice deve ser designado como uma ID, e cada documento deve ter um valor para o campo de ID que identifique de forma exclusiva o documento no índice. Todos os outros campos de documento são opcionais e usarão como padrão um valor nulo, se o valor não for especificado. Observe que os valores nulos não ocupam espaço no índice de pesquisa.

Para poder carregar documentos, você precisa já ter criado o índice no serviço. Consulte [Criar o índice](#CreateIndex) para obter detalhes sobre essa primeira etapa.

<a name="AddOrUpdateDocuments"></a>

## <a name="add-update-or-delete-documents"></a>Adicionar, Atualizar ou Excluir Documentos
Você pode carregar, mesclar, mesclar ou carregar ou excluir documentos de um índice especificado usando HTTP POST. Para um grande número de atualizações, recomenda-se o processamento em lotes de documentos (até 1000 documentos ou aproximadamente 16 MB por lote).

    POST https://[service name].search.windows.net/indexes/[index name]/docs/index?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

**Solicitação**

HTTPS é necessário para todas as solicitações de serviço. Você pode carregar, mesclar, mesclar ou carregar ou excluir documentos de um índice especificado usando HTTP POST.

O URI da solicitação inclui o [nome do índice], especificando em qual índice postar documentos. Só é possível postar documentos em um índice por vez.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `Content-Type`: obrigatório. Defina-o como `application/json`
* `api-key`: obrigatório. A `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo de seu serviço. A solicitação **Adicionar Documentos** deve incluir um cabeçalho de `api-key` definido como sua chave de administração (em vez de uma chave de consulta).

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

O corpo da solicitação contém um ou mais documentos a serem indexados. Os documentos são identificados por uma chave exclusiva. Cada documento está associado uma ação: carregar, mesclar, mesclar ou carregar ou excluir. As solicitações de carregamento devem incluir os dados do documento como um conjunto de pares de chave/valor.

    {
      "value": [
        {
          "@search.action": "upload (default) | merge | mergeOrUpload | delete",
          "key_field_name": "unique_key_of_document", (key/value pair for key field from index schema)
          "field_name": field_value (key/value pairs matching index schema)
            ...
        },
        ...
      ]
    }

> [!NOTE]
> As chaves de documento só podem conter letras, números, traços ("-"), sublinhados ("_") e sinais de igual ("="). Para obter mais detalhes, veja [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx).
> 
> 

**Ações do documento**

* `upload`: uma ação de carregamento é semelhante a um "upsert", em que o documento será inserido se for novo e será atualizado/substituído se ele existir. Observe que todos os campos são substituídos no caso da atualização.
* `merge`: a mesclagem atualiza um documento existente com os campos especificados. Se o documento não existir, a mesclagem falhará. Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. Isso inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contiver um campo "marcas" com o valor `["budget"]` e você executar uma mesclagem com o valor `["economy", "pool"]` para "marcas", o valor final do campo "marcas" final será `["economy", "pool"]`. Ele **não** será `["budget", "economy", "pool"]`.
* `mergeOrUpload`: se comportará como `merge` se já existir um documento com a chave especificada no índice. Se o documento não existir, se comportará como `upload` com um novo documento.
* `delete`: a exclusão remove o documento especificado do índice. Observe que todos os campos que você especificar em uma operação `delete` , exceto o campo de chave, serão ignorados. Se você quiser remover um campo individual de um documento, use `merge` em vez disso e apenas defina o campo explicitamente como `null`.

**Resposta**

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, indicando que todos os itens foram indexados com êxito. Isso é indicado pela propriedade `status` sendo definida como verdadeira para todos os itens, bem como a propriedade `statusCode` sendo definida como 201 (para documentos carregados recentemente) ou 200 (para documentos mesclados ou excluídos):

    {
      "value": [
        {
          "key": "unique_key_of_new_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 201
        },
        {
          "key": "unique_key_of_merged_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        },
        {
          "key": "unique_key_of_deleted_document",
          "status": true,
          "errorMessage": null,
          "statusCode": 200
        }
      ]
    }  

O código de status 207 (Multi-Status) será retornado quando pelo menos um item não tiver sido indexado com êxito. Os itens que não foram indexados têm o campo `status` definido como falso. As propriedades `errorMessage` e `statusCode` indicarão o motivo do erro de indexação:

    {
      "value": [
        {
          "key": "unique_key_of_document_1",
          "status": false,
          "errorMessage": "The search service is too busy to process this document. Please try again later.",
          "statusCode": 503
        },
        {
          "key": "unique_key_of_document_2",
          "status": false,
          "errorMessage": "Document not found.",
          "statusCode": 404
        },
        {
          "key": "unique_key_of_document_3",
          "status": false,
          "errorMessage": "Index is temporarily unavailable because it was updated with the 'allowIndexDowntime' flag set to 'true'. Please try again later.",
          "statusCode": 422
        }
      ]
    }  

A tabela a seguir explica os vários códigos de status de cada documento que podem ser retornados na resposta. Observe que alguns indicam problemas com a solicitação em si, enquanto outros indicam condições de erro temporárias. Você deve testar o último novamente após um atraso.

<table style="font-size:12">
    <tr>
        <th>Código de status</th>
        <th>Significado</th>
        <th>Com nova tentativa</th>
        <th>Observações</th>
    </tr>
    <tr>
        <td>200</td>
        <td>O documento foi modificado ou excluído com êxito.</td>
        <td>n/d</td>
        <td>As operações de exclusão são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotentes</a>. Ou seja, mesmo se não existir uma chave de documento no índice, a tentativa de uma operação de exclusão com essa chave resultará em um código de status 200.</td>
    </tr>
    <tr>
        <td>201</td>
        <td>O documento foi criado com êxito.</td>
        <td>n/d</td>
        <td></td>
    </tr>
    <tr>
        <td>400</td>
        <td>Ocorreu um erro no documento que o impediu de ser indexado.</td>
        <td>Não</td>
        <td>A mensagem de erro na resposta indicará o que há de errado com o documento.</td>
    </tr>
    <tr>
        <td>404</td>
        <td>Não foi possível mesclar o documento porque a chave especificada não existe no índice.</td>
        <td>Não</td>
        <td>Esse erro não ocorre em carregamentos, uma vez que eles criam novos documentos e não ocorre em exclusões porque elas são <a href="https://en.wikipedia.org/wiki/Idempotence">idempotentes</a>.</td>
    </tr>
    <tr>
        <td>409</td>
        <td>Foi detectado um conflito de versão durante a tentativa de indexar um documento.</td>
        <td>Sim</td>
        <td>Isso pode acontecer quando você está tentando indexar o mesmo documento mais de uma vez simultaneamente.</td>
    </tr>
    <tr>
        <td>422</td>
        <td>O índice está temporariamente indisponível porque ele foi atualizado com o sinalizador 'allowIndexDowntime' definido como 'verdadeiro'.</td>
        <td>Sim</td>
        <td></td>
    </tr>
    <tr>
        <td>503</td>
        <td>O serviço de pesquisa está temporariamente indisponível, possivelmente devido a uma carga pesada.</td>
        <td>Sim</td>
        <td>Neste caso, seu código deverá ser colocado em espera antes de uma nova tentativa, sob o risco de prolongar a indisponibilidade do serviço.</td>
    </tr>
</table> 

**Observação**: se o seu código de cliente encontra com frequência uma resposta 207, um motivo possível para isso é que o sistema está sobrecarregado. Você pode confirmar isso verificando a propriedade `statusCode` para 503. Se esse for o caso, será recomendável a ***limitação de solicitações de indexação***. Caso contrário, se a indexação de tráfego não diminuir, o sistema poderá começar a rejeitar todas as solicitações com erros 503.

O código de status 429 indica que você excedeu sua cota no número de documentos por índice. Você deve criar um novo índice ou atualizar para limites de capacidade mais altos.

**Exemplo:**

    {
      "value": [
        {
          "@search.action": "upload",
          "hotelId": "1",
          "baseRate": 199.0,
          "description": "Best hotel in town",
          "description_fr": "Meilleur hôtel en ville",
          "hotelName": "Fancy Stay",
          "category": "Luxury",
          "tags": ["pool", "view", "wifi", "concierge"],
          "parkingIncluded": false,
          "smokingAllowed": false,
          "lastRenovationDate": "2010-06-27T00:00:00Z",
          "rating": 5,
          "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
          "@search.action": "upload",
          "hotelId": "2",
          "baseRate": 79.99,
          "description": "Cheapest hotel in town",
          "description_fr": "Hôtel le moins cher en ville",
          "hotelName": "Roach Motel",
          "category": "Budget",
          "tags": ["motel", "budget"],
          "parkingIncluded": true,
          "smokingAllowed": true,
          "lastRenovationDate": "1982-04-28T00:00:00Z",
          "rating": 1,
          "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
          "@search.action": "merge",
          "hotelId": "3",
          "baseRate": 279.99,
          "description": "Surprisingly expensive",
          "lastRenovationDate": null
        },
        {
          "@search.action": "delete",
          "hotelId": "4"
        }
      ]
    }
- - -
<a name="SearchDocs"></a>

## <a name="search-documents"></a>Pesquisar Documentos
Uma operação **Search** é emitida como uma solicitação GET ou POST e especifica parâmetros que fornecem os critérios para a seleção de documentos correspondentes.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usar POST em vez de GET**

Quando você usa o HTTP GET para chamar a API de **Search** , é preciso estar ciente de que o comprimento da URL da solicitação não pode exceder 8 KB. Isso costuma ser suficiente para a maioria dos aplicativos. No entanto, alguns aplicativos geram consultas muito grandes ou expressões de filtro OData. Para esses aplicativos, usar HTTP POST é uma opção melhor, pois permite filtros e consultas maiores que o GET. Com o POST, o número de termos ou cláusulas em uma consulta é o fator limitante, não o tamanho da consulta processada, uma vez que o limite de tamanho da solicitação POST é quase 16 MB.

> [!NOTE]
> Embora o limite de tamanho da solicitação POST seja muito grande, consultas de pesquisa e expressões de filtro não podem ser arbitrariamente complexos. Confira a [Sintaxe de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) e a [Sintaxe de expressão OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de consulta e filtro de pesquisa.
> 
> 

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Pesquisar** pode ser criada usando os métodos GET ou POST.

O URI da solicitação especifica qual índice deve ser consultado para todos os documentos que correspondem aos parâmetros. Parâmetros são especificados na cadeia de consulta no caso de solicitações GET e no corpo da solicitação no caso de solicitações POST.

Como prática recomendada ao criar solicitações GET, lembre-se de [codificar na URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) os parâmetros de consulta específicos ao chamar a API REST diretamente. Para operações de **Pesquisa** , isso inclui:

* `$filter`
* `facet`
* `highlightPreTag`
* `highlightPostTag`
* `search`
* `moreLikeThis`

A codificação de URL é recomendada apenas nos parâmetros da consulta acima. Se você inadvertidamente codificar na URL a cadeia de caracteres de consulta inteira (tudo após o ?), as solicitações serão interrompidas.

Além disso, a codificação de URL só é necessária ao se chamar a API REST diretamente usando GET. Nenhuma codificação de URL é necessária ao chamar a **Pesquisa** usando POST, ou ao usar a [biblioteca cliente .NET](https://msdn.microsoft.com/library/dn951165.aspx), que processa a codificação de URL para você.

<a name="SearchQueryParameters"></a>
**Parâmetros de consulta**

**Search** aceita vários parâmetros que fornecem critérios de consulta e especificam o comportamento da pesquisa. Você fornece esses parâmetros na cadeia de consulta da URL ao chamar **Pesquisar** via GET e como propriedades JSON no corpo da solicitação ao chamar **Pesquisar** via POST. A sintaxe para alguns parâmetros é ligeiramente diferente entre GET e POST. Essas diferenças são indicadas, como aplicável, abaixo:

`search=[string]` (opcional) ‒ o texto a ser pesquisado. Todos os campos `searchable` são pesquisados por padrão, a menos que `searchFields` sejam especificados. Ao pesquisar os campos `searchable`, é gerado um token para o próprio texto de pesquisa. Assim, vários termos podem ser separados por um espaço em branco (por exemplo: `search=hello world`). Para corresponder a qualquer termo, use `*` (isso pode ser útil para consultas de filtros boolianos). A omissão desse parâmetro tem o mesmo efeito que sua definição como `*`. Consulte [Sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) para obter informações específicas sobre a sintaxe de pesquisa.

* **Observação**: os resultados às vezes podem ser surpreendentes ao se consultar sobre campos `searchable`. O criador de token inclui lógica para lidar com casos comuns para texto em inglês, como apóstrofos, vírgulas em números etc. Por exemplo, `search=123,456` corresponderá a um único termo, 123,456, em vez de cada um do termos 123 e 456, já que as vírgulas são usadas como separadores de milhar para números grandes em inglês. Por esse motivo, recomendamos o uso de espaços em branco em vez de pontuação para separar os termos do parâmetro `search`.

`searchMode=any|all` (opcional; o padrão é `any`) ‒ se for necessária a correspondência de algum dos termos de pesquisa ou de todos eles para que o documento seja contado como uma correspondência.

`searchFields=[string]` (opcional) ‒ a lista de nomes de campos separados por vírgulas para pesquisar o texto especificado. Os campos de destino devem ser marcados como `searchable`.

`queryType=simple|full` (opcional, o padrão é `simple`) - quando definido como texto de pesquisa "simples", é interpretado usando uma linguagem de consulta simples que permite símbolos como +, * e "". As consultas são avaliadas em todos os campos pesquisáveis (ou nos campos indicados em `searchFields`) em cada documento por padrão. Quando o tipo de consulta for definido como `full` , o texto de pesquisa será interpretado usando a linguagem de consulta Lucene, que permite pesquisas de campo específicos e ponderadas. Confira [Sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) e [Sintaxe de consulta Lucene](https://msdn.microsoft.com/library/mt589323.aspx) para obter informações específicas sobre sintaxes de pesquisa. 

> [!NOTE]
> A pesquisa do intervalo na linguagem de consulta Lucene não tem suporte a $filter, que oferece funcionalidade semelhante.
> 
> 

`moreLikeThis=[key]` (opcional) **Importante:** esse recurso só está disponível na `2015-02-28-Preview`. Essa opção não pode ser usada em uma consulta que contém o parâmetro de pesquisa de texto `search=[string]`. O parâmetro `moreLikeThis` localiza os documentos que são semelhantes ao documento especificado pela chave do documento. Quando é feita uma solicitação de pesquisa com `moreLikeThis`, uma lista de termos de pesquisa é gerada com base na frequência e raridade dos termos no documento de origem. Esses termos são usados para fazer a solicitação. Por padrão, o conteúdo de todos os campos `searchable` será considerado, a menos que `searchFields` seja usado para restringir quais campos são pesquisados.  

`$skip=#` (opcional) ‒ o número de resultados da pesquisa a serem ignorados. Não pode ser superior a 100.000. Se você precisar examinar os documentos em sequência, mas não puder usar `$skip` devido a essa limitação, considere o uso de `$orderby` em uma chave totalmente ordenada e `$filter` com um intervalo de consulta em vez disso.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `skip`, em vez de `$skip`.
> 
> 

`$top=#` (opcional) ‒ o número de resultados da pesquisa a serem recuperados. Isso pode ser usado em conjunto com `$skip` para implementar a paginação de cliente dos resultados da pesquisa.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `top`, em vez de `$top`.
> 
> 

`$count=true|false` (opcional; o padrão é `false`) ‒ especifica se é necessário buscar a contagem total de resultados. Essa é a contagem de todos os documentos que correspondem aos parâmetros `search` e `$filter`, ignorando `$top` e `$skip`. A definição desse valor como `true` pode afetar o desempenho. Observe que a contagem retornada é uma aproximação.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `count`, em vez de `$count`.
> 
> 

`$orderby=[string]` (opcional) ‒ uma lista de expressões separadas por vírgulas para classificar os resultados. Cada expressão pode ser um nome de campo ou uma chamada para a função `geo.distance()` . Cada expressão pode ser seguida de `asc` para indicar a ordem crescente e `desc` para indicar a ordem decrescente. O padrão é a ordem crescente. Os empates serão resolvidos pelas pontuações de correspondência de documentos. Se nenhum `$orderby` for especificado, a ordem de classificação padrão será decrescente de acordo com a pontuação de correspondência dos documentos. Há um limite de 32 cláusulas para `$orderby`.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `orderby`, em vez de `$orderby`.
> 
> 

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado, todos os campos marcados como recuperáveis no esquema serão incluídos. Você pode solicitar explicitamente todos os campos ao definir esse parâmetro como `*`.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `select`, em vez de `$select`.
> 
> 

`facet=[string]` (zero ou mais) ‒ um campo de acordo com o qual o facetamento deve ser realizado. Opcionalmente, a cadeia de caracteres pode conter parâmetros para personalizar o facetamento expressado como pares separados por vírgulas `name:value` . Os parâmetros válidos são:

* `count` (número máximo de termos de faceta; o padrão é 10). Não há um máximo, mas os valores mais altos incorrerão em uma penalidade de desempenho correspondente, principalmente se o campo facetado contiver um grande número de termos exclusivos.
  * Por exemplo: `facet=category,count:5` obtém as cinco principais categorias nos resultados de faceta.  
  * **Observação**: se o parâmetro `count` for menor do que o número de termos exclusivos, os resultados poderão não ser precisos. Isso ocorre devido à maneira como as consultas de facetamento são distribuídas entre fragmentos. O aumento de `count` geralmente aumenta a precisão da contagem de termos, mas isso prejudica o desempenho.
* `sort` (uma das `count` para classificar em ordem *decrescente* por contagem, `-count` para classificar em ordem *crescente* por contagem, `value` para classificar em ordem *crescente* por valor ou `-value` para classificar em ordem *decrescente* por valor)
  * Por exemplo: `facet=category,count:3,sort:count` obtém as três principais categorias nos resultados de faceta em ordem decrescente pelo número de documentos com o nome de cada cidade. Por exemplo, se as três principais categorias forem Orçamento, Motel e Luxo, Orçamento tiver cinco ocorrências, Motel tiver seis e Luxo tiver quatro, as classificações, em ordem, serão Motel, Orçamento, Luxo.
  * Por exemplo: `facet=rating,sort:-value` produz todas as classificações possíveis, em ordem decrescente por valor. Por exemplo, se as classificações forem de 1 a 5, as classificações serão ordenadas como 5, 4, 3, 2, 1, independentemente de quantos documentos corresponderem a cada classificação.
* `values` (valores numéricos delimitados por barras verticais ou valores `Edm.DateTimeOffset` que especificam um conjunto dinâmico de valores de entrada de faceta)
  * Por exemplo: `facet=baseRate,values:10|20` produz três classificações: uma para a taxa de base 0 até 10, mas sem incluir esse valor, uma para 10 até 20, mas sem incluir esse valor e outra para 20 ou mais.
  * Por exemplo: `facet=lastRenovationDate,values:2010-02-01T00:00:00Z` produz duas classificações: uma para hotéis reformados antes de fevereiro de 2010 e outra para hotéis reformados em ou depois de 1º de fevereiro de 2010.
* `interval` (intervalo inteiro maior que 0 para números ou `minute`, `hour`, `day`, `week`, `month`, `quarter`, `year` para valores de tempo de data)
  * Por exemplo: `facet=baseRate,interval:100` produz classificações com base em intervalos de taxa de base de tamanho 100. Por exemplo, se as taxas de base estiverem todas entre US$ 60 e US$ 600, haverá classificações para 0-100, 100-200, 200-300, 300-400, 400-500 e 500-600.
  * Por exemplo: `facet=lastRenovationDate,interval:year` produz uma classificação para cada ano em que os hotéis foram reformados.
* `timeoffset` ([+-]hh:mm, [+-]hhmm ou [+-]hh) `timeoffset` é opcional. Ele só pode ser combinado com a opção `interval` e somente quando aplicado a um campo do tipo `Edm.DateTimeOffset`. O valor especifica o deslocamento de hora em relação ao UTC para compensar ao definir limites de tempo.
  * Por exemplo: `facet=lastRenovationDate,interval:day,timeoffset:-01:00` usa o dia limite que inicia à 01:00:00 UTC (meia-noite no fuso horário de destino)
* **Observação**: `count` e `sort` podem ser combinados na mesma especificação de faceta, mas não podem ser combinados a `interval` ou a `values` e `interval` e `values` não podem ser combinados juntos.
* **Observação**: as facetas de intervalo de data e hora serão calculadas com base na hora UTC, caso `timeoffset` não seja especificado. Por exemplo: para `facet=lastRenovationDate,interval:day`, o limite de dia começa à 00:00:00 UTC. 

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `facets`, em vez de `facet`. Além disso, especifique-o como uma matriz JSON de cadeias de caracteres em que cada cadeia é uma expressão de faceta separada.
> 
> 

`$filter=[string]` (opcional) ‒ uma expressão de pesquisa estruturada na sintaxe de OData padrão.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `filter`, em vez de `$filter`.
> 
> 

`highlight=[string]` (opcional) ‒ realça um conjunto de nomes de campo separados por vírgulas usado para realçar ocorrências. Somente campos `searchable` podem ser usados para realçar ocorrências.

`highlightPreTag=[string]` (opcional, o padrão é `<em>`) ‒ uma marca de cadeia de caracteres que é anexada ao início para realçar ocorrências. Deve ser definida com `highlightPostTag`.

> [!NOTE]
> Ao chamar **Pesquisa** usando GET, caracteres reservados na URL deverão ser codificados por percentual (por exemplo, %23, em vez de #).
> 
> 

`highlightPostTag=[string]` (opcional, o padrão é `</em>`) ‒ uma marca de cadeia de caracteres que é anexada para realçar ocorrências. Deve ser definida com `highlightPreTag`.

> [!NOTE]
> Ao chamar **Pesquisa** usando GET, caracteres reservados na URL deverão ser codificados por percentual (por exemplo, %23, em vez de #).
> 
> 

`scoringProfile=[string]` (opcional) ‒ o nome de um perfil de pontuação para avaliar pontuações de correspondência de documentos correspondentes para classificar os resultados.

`scoringParameter=[string]` (zero ou mais) ‒ indica o valor de cada parâmetro definido em uma função de pontuação (por exemplo, `referencePointParameter`) usando o formato `name-value1,value2,...`.

* Por exemplo, se o perfil de pontuação definir uma função com um parâmetro chamado "mylocation", a opção de cadeia de consulta será `&scoringParameter=mylocation--122.2,44.8`. O primeiro traço separa o nome da lista de valores, enquanto o segundo traço faz parte do primeiro valor (longitude, neste exemplo).
* Para parâmetros de pontuação, como as marcações de aumento que podem conter vírgulas, você pode liberar todos esses valores na lista usando aspas simples. Se os próprios valores contiverem aspas simples, você poderá liberá-las ao duplicar.
  * Por exemplo, se você tiver um parâmetro de aumento de marcação chamado "mytag" e quiser aumentá-lo nos valores de marcação "Hello, O'Brien" e "Smith", a opção de cadeia de consulta será `&scoringParameter=mytag-'Hello, O''Brien',Smith`. Observe que as aspas são necessárias apenas para os valores que contêm vírgulas.

> [!NOTE]
> Ao chamar **Pesquisa** usando POST, esse parâmetro será chamado de `scoringParameters`, em vez de `scoringParameter`. Além disso, especifique-o como uma matriz de cadeias de caracteres JSON, na qual cada cadeia é um par de nome `name-values` .
> 
> 

`minimumCoverage` (opcional, o padrão até 100)-um número entre 0 e 100, indicando a porcentagem do índice deve ser coberto por uma consulta de pesquisa, para que a consulta seja relatada como sucesso. Por padrão, o índice inteiro deve estar disponível ou `Search` retornará o código de status HTTP 503. Se você definir `minimumCoverage` e `Search` for bem-sucedido, retornará HTTP 200 e incluirá um valor de `@search.coverage` na resposta indicando a porcentagem do índice que foi incluído na consulta.

> [!NOTE]
> Definir esse parâmetro para um valor inferior a 100 pode ser útil para garantir a disponibilidade de pesquisa até mesmo para serviços com apenas uma réplica. No entanto, não existe a garantia de que todos os documentos correspondentes estejam presentes nos resultados da pesquisa. Se rechamada da pesquisa é mais importante para o seu aplicativo do que a disponibilidade, é melhor deixar `minimumCoverage` em seu valor padrão de 100.
> 
> 

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, o `api-version` é especificado como um parâmetro de consulta na URL, independentemente de você chamar **Pesquisa** com GET ou POST.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Pesquisar** pode especificar uma chave de administração ou a chave de consulta para `api-key`.

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Para GET: Nenhum.

Para POST:

    {
      "count": true | false (default),
      "facets": [ "facet_expression_1", "facet_expression_2", ... ],
      "filter": "odata_filter_expression",
      "highlight": "highlight_field_1, highlight_field_2, ...",
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 100),
      "moreLikeThis": "document_key" (mutually exclusive with "search" parameter),
      "orderby": "orderby_expression",
      "scoringParameters": [ "scoring_parameter_1", "scoring_parameter_2", ... ],
      "scoringProfile": "scoring_profile_name",
      "search": "simple_query_expression",
      "searchFields": "field_name_1, field_name_2, ...",
      "searchMode": "any" (default) | "all",
      "select": "field_name_1, field_name_2, ...",
      "skip": # (default 0),
      "top": #
    }

**Continuação de respostas de pesquisa parciais**

Às vezes, a pesquisa do Azure não pode retornar todos os resultados solicitados em uma única resposta de pesquisa. Isso pode ocorrer por diferentes motivos, como quando a consulta solicita muitos documentos não especificando `$top` ou especificando um valor grande demais para `$top`. Nesses casos, a pesquisa do Azure incluirá a anotação `@odata.nextLink` no corpo da resposta e também `@search.nextPageParameters`, se for uma solicitação POST. Você pode usar os valores dessas anotações para formular outra solicitação Pesquisar para obter a próxima parte da resposta da pesquisa. Isso é chamado de ***continuação*** da solicitação Pesquisar original e as anotações geralmente são chamadas de ***tokens de continuação***. Consulte [o exemplo a seguir](#SearchResponse) para obter detalhes sobre a sintaxe dessas anotações e onde elas aparecem no corpo da resposta. 

Os motivos pelos quais a pesquisa do Azure pode retornar os tokens de continuação são específicos de implementação e estão sujeitos a alteração. Clientes robustos devem sempre estar prontos para lidar com casos em que são retornados menos documentos do que o esperado e um token de continuação é incluído para continuar recuperando os documentos. Observe também que você deve usar o mesmo método HTTP que a solicitação original para continuar. Por exemplo, se você tiver enviado uma solicitação GET, quaisquer solicitações de continuação que você enviar também deverão usar GET (e da mesma forma para POST).

<a name="SearchResponse"></a>
**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      "@odata.count": # (if $count=true was provided in the query),
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "@search.facets": { (if faceting was specified in the query)
        "facet_field": [
          {
            "value": facet_entry_value (for non-range facets),
            "from": facet_entry_value (for range facets),
            "to": facet_entry_value (for range facets),
            "count": number_of_documents
          }
        ],
        ...
      },
      "@search.nextPageParameters": { (request body to fetch the next page of results if not all results could be returned in this response and Search was called with POST)
        "count": ... (value from request body if present),
        "facets": ... (value from request body if present),
        "filter": ... (value from request body if present),
        "highlight": ... (value from request body if present),
        "highlightPreTag": ... (value from request body if present),
        "highlightPostTag": ... (value from request body if present),
        "minimumCoverage": ... (value from request body if present),
        "moreLikeThis": ... (value from request body if present),
        "orderby": ... (value from request body if present),
        "scoringParameters": ... (value from request body if present),
        "scoringProfile": ... (value from request body if present),
        "search": ... (value from request body if present),
        "searchFields": ... (value from request body if present),
        "searchMode": ... (value from request body if present),
        "select": ... (value from request body if present),
        "skip": ... (page size plus value from request body if present),
        "top": ... (value from request body if present minus page size),
      },
      "value": [
        {
          "@search.score": document_score (if a text query was provided),
          "@search.highlights": {
            field_name: [ subset of text, ... ],
            ...
          },
          key_field_name: document_key,
          field_name: field_value (retrievable fields or specified projection),
          ...
        },
        ...
      ],
      "@odata.nextLink": (URL to fetch the next page of results if not all results could be returned in this response; Applies to both GET and POST)
    }

**Exemplos:**

Você pode encontrar exemplos adicionais na página [Sintaxe de expressão OData para o Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx) .

1)    Pesquisar o índice classificado em ordem decrescente por data.

    GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "orderby": "lastRenovationDate desc" }

2)    Em uma pesquisa facetada, pesquisar o índice e recuperar facetas para categorias, classificação, marcas, bem como itens com baseRate em intervalos específicos:

    GET /indexes/hotels/docs?search=test&facet=category&facet=rating&facet=tags&facet=baseRate,values:80|150|220&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "test", "facets": [ "category", "rating", "tags", "baseRate,values:80|150|220" ] }

3)    Usando um filtro, restringir os resultados de consulta facetados anteriores depois que o usuário clicar na classificação 3 e na categoria "Motel":

    GET /indexes/hotels/docs?search=test&facet=tags&facet=baseRate,values:80|150|220&$filter=rating eq 3 and category eq 'Motel'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "test", "facets": [ "tags", "baseRate,values:80|150|220" ], "filter": "rating eq 3 and category eq 'Motel'" }

4) Em uma pesquisa facetada, definir um limite superior para termos exclusivos retornados em uma consulta. O padrão é 10, mas você pode aumentar ou diminuir esse valor usando o parâmetro `count` no atributo `facet`:

    GET /indexes/hotels/docs?search=test&facet=city,count:5&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview  {    "search": "test",    "facets": [ "city,count:5" ]  }

5)    Pesquisar o índice em campos específicos. Por exemplo, um campo específico do idioma:

    GET /indexes/hotels/docs?search=hôtel&searchFields=description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "hôtel", "searchFields": "description_fr" }

6) Pesquisar o índice em vários campos. Por exemplo, você pode armazenar e consultar campos pesquisáveis em vários idiomas, todos no mesmo índice.  Se descrições em inglês e francês coexistirem no mesmo documento, você poderá retornar qualquer uma das descrições ou todas elas nos resultados da consulta:

    GET /indexes/hotels/docs?search=hotel&searchFields=description,description_fr&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview  {    "search": "hotel",    "searchFields": "description, description_fr"  }

Observe que você pode consultar apenas um índice por vez. Não crie vários índices para cada idioma, a menos que você planeje consultar um de cada vez.

7)    Paginar ‒ obtenha a primeira página de itens (o tamanho de página é 10):

    GET /indexes/hotels/docs?search=*&$skip=0&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "skip": 0, "top": 10 }

8)    Paginar ‒ obtenha a segunda página de itens (o tamanho de página é 10):

    GET /indexes/hotels/docs?search=*&$skip=10&$top=10&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "skip": 10, "top": 10 }

9)    Recuperar um conjunto específico de campos:

    GET /indexes/hotels/docs?search=*&$select=hotelName,description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview { "search": "*", "select": "hotelName, description" }

10)  Recupere documentos que correspondem a uma expressão de filtro específica:

    GET /indexes/hotels/docs?$filter=(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {  "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'" }

11) Pesquisar o índice e retornar fragmentos com realces de ocorrências

    GET /indexes/hotels/docs?search=something&highlight=description&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "highlight": "description" }

12) Pesquisar o índice e retornar documentos classificados de mais próximo a mais distante em relação a um local de referência

    GET /indexes/hotels/docs?search=something&$orderby=geo.distance(location, geography'POINT(-122.12315 47.88121)')&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "orderby": "geo.distance(location, geography'POINT(-122.12315 47.88121)')" }

13) Pesquisar o índice, supondo que haja um perfil de pontuação chamado "geo" com duas funções de pontuação de distância, uma definindo um parâmetro chamado "currentLocation" e uma definindo um parâmetro chamado "lastLocation"

    GET /indexes/hotels/docs?search=something&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&scoringParameter=lastLocation--121.499,44.2113&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "something",   "scoringProfile": "geo",   "scoringParameters": [ "currentLocation--122.123,44.77233", "lastLocation--121.499,44.2113" ] }

14) Localizar documentos no índice usando a [sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx). Esta consulta retorna hotéis em que os campos de pesquisa contêm os termos "conforto" e "local", mas não "motel":

    GET /indexes/hotels/docs?search=comfort +location -motel&searchMode=all&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "comfort +location -motel",   "searchMode": "all" }

Observe o uso de `searchMode=all` acima. Incluir esse parâmetro substitui o padrão de `searchMode=any`, garantindo que `-motel` significa "E NÃO" em vez de "OU NÃO". Sem `searchMode=all`, você obtém "OU NÃO", que  expande em vez de restringir os resultados de pesquisa, e isso pode ser contraintuitivo para alguns usuários.

15) Localizar documentos no índice usando a [sintaxe de consulta lucene](https://msdn.microsoft.com/library/mt589323.aspx). Essa consulta retorna hotéis onde o campo de categoria contém o termo "orçamento" e todos os campos pesquisáveis com a frase "renovado recentemente". Os documentos com a frase "renovado recentemente" apresentam uma classificação superior como um resultado do valor de aumento de termo (3)

    GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2015-02-28-Preview&querytype=full

    POST /indexes/hotels/docs/search?api-version=2015-02-28-Preview {   "search": "category:budget AND \"recently renovated\"^3",   "queryType": "full",   "searchMode": "all" }

<a name="LookupAPI"></a>

## <a name="lookup-document"></a>Procurar documento
A operação **Pesquisar Documento** recupera um documento do Azure Search. Isso é útil quando um usuário clica em um resultado de pesquisa específico e você deseja pesquisar detalhes específicos sobre esse documento.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/[key]?[query parameters]
    api-key: [admin or query key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Pesquisar Documento** pode ser criada da maneira a seguir.

    GET /indexes/[index name]/docs/key?[query parameters]

Como alternativa, você pode usar a sintaxe tradicional de OData para pesquisa de chave:

    GET /indexes('[index name]')/docs('[key]')?[query parameters]

O URI da solicitação inclui um [nome de índice] e uma [chave], especificando qual documento deve ser recuperado do índice. Você pode obter apenas um documento por vez. Use **Pesquisar** para obter vários documentos em uma única solicitação.

**Parâmetros de consulta**

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado ou se for definido como `*`, todos os campos marcados como recuperáveis no esquema serão incluídos na projeção.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, a `api-version` é especificada como um parâmetro de consulta.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Pesquisar Documento** pode especificar uma chave de administração ou a chave de consulta para `api-key`.

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      field_name: field_value (fields matching the default or specified projection)
    }

**Exemplo**

Pesquisar o documento que tem a chave '2'

    GET /indexes/hotels/docs/2?api-version=2015-02-28-Preview

Pesquisar o documento que tem a chave '3' usando a sintaxe de OData:

    GET /indexes('hotels')/docs('3')?api-version=2015-02-28-Preview

<a name="CountDocs"></a>

## <a name="count-documents"></a>Contar documentos
A operação **Contar Documentos** recupera uma contagem do número de documentos em um índice de pesquisa. A sintaxe de `$count` faz parte do protocolo OData.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/$count?api-version=[api-version]
    Accept: text/plain
    api-key: [admin or query key]

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Contar Documentos** pode ser criada usando o método GET.

O [nome do índice] no URI da solicitação instrui o serviço a retornar uma contagem de todos os itens da coleção de documentos do índice especificado.

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação obrigatórios e opcionais.

* `Accept`: esse valor deve ser definido como `text/plain`.
* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Contar Documentos** pode especificar uma chave de administração ou a chave de consulta para `api-key`.

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Nenhuma.

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

O corpo da resposta contém o valor da contagem como um inteiro formatado como texto sem formatação.

<a name="Suggestions"></a>

## <a name="suggestions"></a>Sugestões
A operação **Sugestões** recupera sugestões com base na entrada de pesquisa parcial. Ela é normalmente usada em caixas de pesquisa para fornecer sugestões de digitação antecipada à medida que os usuários inserem termos de pesquisa.

Solicitações de sugestão têm por objetivo sugerir documentos de destino, assim, o texto sugerido poderá ser repetido se a mesma pesquisa de entrada corresponder a vários documentos candidatos. Você pode usar `$select` para recuperar outros campos de documento (inclusive a chave do documento) para determinar qual documento é a fonte de cada sugestão.

Uma operação **Suggestions** é emitida como uma solicitação GET ou POST.

    GET https://[service name].search.windows.net/indexes/[index name]/docs/suggest?[query parameters]
    api-key: [admin or query key]

    POST https://[service name].search.windows.net/indexes/[index name]/docs/suggest?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin or query key]

**Quando usar POST em vez de GET**

Quando você usar HTTP GET para chamar a API de **Sugestões** , será preciso estar ciente de que o comprimento da URL da solicitação não pode exceder 8 KB. Isso costuma ser suficiente para a maioria dos aplicativos. No entanto, alguns aplicativos geram consultas muito grandes, especificamente expressões de filtro OData. Para esses aplicativos, usar HTTP POST é uma opção melhor, pois permite filtros maiores que o GET. Com o POST, o número de cláusulas em um filtro é o fator limitante, não o tamanho da cadeia de caracteres de filtro não processada, uma vez que o limite de tamanho da solicitação POST é quase 16 MB.

> [!NOTE]
> Embora o limite de tamanho da solicitação POST seja muito grande, expressões de filtro não podem ser arbitrariamente complexos. Confira [Sintaxe de expressão OData](https://msdn.microsoft.com/library/dn798921.aspx) para obter mais informações sobre as limitações de complexidade de filtro.
> 
> 

**Solicitação**

HTTPS é necessário para as solicitações de serviço. A solicitação **Sugestões** pode ser criada usando os métodos GET ou POST.

O URI da solicitação especifica o nome do índice a ser consultado. Parâmetros, como o termo de pesquisa inserido parcialmente, são especificados na cadeia de consulta no caso de solicitações GET e no corpo da solicitação no caso de solicitações POST.

Como prática recomendada ao criar solicitações GET, lembre-se de [codificar na URL](https://msdn.microsoft.com/library/system.uri.escapedatastring.aspx) os parâmetros de consulta específicos ao chamar a API REST diretamente. Para as operações **Sugestões** , isso inclui:

* `$filter`
* `highlightPreTag`
* `highlightPostTag`
* `search`

A codificação de URL é recomendada apenas nos parâmetros da consulta acima. Se você inadvertidamente codificar na URL a cadeia de caracteres de consulta inteira (tudo após o ?), as solicitações serão interrompidas.

Além disso, a codificação de URL só é necessária ao se chamar a API REST diretamente usando GET. Nenhuma codificação de URL é necessária ao chamar **Sugestões** usando POST, ou ao usar a [biblioteca de clientes .NET](https://msdn.microsoft.com/library/dn951165.aspx), que processa a codificação de URL para você.

**Parâmetros de consulta**

**Suggestions** aceita vários parâmetros que fornecem critérios de consulta e também especificam o comportamento da pesquisa. Você fornece esses parâmetros na cadeia de consulta da URL ao chamar **Suggestions** via GET e como propriedades JSON no corpo da solicitação ao chamar **Sugestões** via POST. A sintaxe para alguns parâmetros é ligeiramente diferente entre GET e POST. Essas diferenças são indicadas, como aplicável, abaixo:

`search=[string]` ‒ o texto de pesquisa a ser usado para sugerir consultas. Deve ter pelo menos 1 e não mais que 100 caracteres.

`highlightPreTag=[string]` (opcional) ‒ uma cadeia de caracteres de marca que é anexada no início para pesquisar ocorrências. Deve ser definida com `highlightPostTag`.

> [!NOTE]
> Ao chamar **Sugestões** usando GET, os caracteres reservados na URL deverão ser codificados por percentual (por exemplo, %23, em vez de #).
> 
> 

`highlightPostTag=[string]` (opcional) ‒ uma cadeia de caracteres de marca que é anexada para pesquisar ocorrências. Deve ser definida com `highlightPreTag`.

> [!NOTE]
> Ao chamar **Sugestões** usando GET, os caracteres reservados na URL deverão ser codificados por percentual (por exemplo, %23, em vez de #).
> 
> 

`suggesterName=[string]` ‒ o nome do sugestor conforme especificado na coleção `suggesters` que faz parte da definição do índice. Um `suggester` determina quais campos são examinados em busca de termos de consulta sugeridos. Consulte [Sugestores](#Suggesters) para obter detalhes.

`fuzzy=[boolean]` (opcional, padrão = falso) ‒ quando definido como verdadeiro, essa API encontrará sugestões mesmo que haja um caractere ausente ou substituído no texto de pesquisa. Embora isso proporcione uma experiência melhor em alguns cenários, prejudica o desempenho, pois pesquisas com sugestões difusas são mais lentas e consumem mais recursos.

`searchFields=[string]` (opcional) ‒ a lista de nomes de campos separados por vírgulas para pesquisar o texto de pesquisa especificado. Os campos de destino devem ser habilitados para sugestões.

`$top=#` (opcional, padrão = 5) ‒ o número de sugestões a serem recuperadas. Deve ser um número entre 1 e 100.

> [!NOTE]
> Ao chamar **Suggestions** usando POST, esse parâmetro será chamado de `top` em vez de `$top`.
> 
> 

`$filter=[string]` (opcional) par uma expressão que filtra os documentos considerados para sugestões.

> [!NOTE]
> Ao chamar **Suggestions** usando POST, esse parâmetro será chamado de `filter` em vez de `$filter`.
> 
> 

`$orderby=[string]` (opcional) ‒ uma lista de expressões separadas por vírgulas para classificar os resultados. Cada expressão pode ser um nome de campo ou uma chamada para a função `geo.distance()` . Cada expressão pode ser seguida de `asc` para indicar a ordem crescente e `desc` para indicar a ordem decrescente. O padrão é a ordem crescente. Há um limite de 32 cláusulas para `$orderby`.

> [!NOTE]
> Ao chamar **Suggestions** usando POST, esse parâmetro será chamado de `orderby` em vez de `$orderby`.
> 
> 

`$select=[string]` (opcional) ‒ uma lista de campos separados por vírgulas a serem recuperados. Se não for especificado, somente a chave do documento e o texto de sugestão serão retornados. Você pode solicitar explicitamente todos os campos ao definir esse parâmetro para `*`.

> [!NOTE]
> Ao chamar **Suggestions** usando POST, esse parâmetro será chamado de `select` em vez de `$select`.
> 
> 

`minimumCoverage` (opcional, o padrão é 80) - um número entre 0 e 100 que indica a porcentagem do índice que deve ser coberto por uma consulta de sugestões para que a consulta a seja relatada como sucesso. Por padrão, pelo menos 80% do índice deve estar disponível ou `Suggest` retornará o código de status HTTP 503. Se você definir `minimumCoverage` e `Suggest` for bem-sucedido, retornará HTTP 200 e incluirá um valor de `@search.coverage` na resposta indicando a porcentagem do índice que foi incluído na consulta.

> [!NOTE]
> Definir esse parâmetro para um valor inferior a 100 pode ser útil para garantir a disponibilidade de pesquisa até mesmo para serviços com apenas uma réplica. No entanto, não há garantias de que todas as sugestões de correspondência estejam presentes nos resultados. Se a rechamada for mais importante para seu aplicativo do que a disponibilidade, é melhor não diminuir `minimumCoverage` para abaixo de seu valor padrão de 80.
> 
> 

`api-version=[string]` (obrigatório). A versão de visualização é `api-version=2015-02-28-Preview`. Consulte [Controle de versão de serviço de pesquisa](http://msdn.microsoft.com/library/azure/dn864560.aspx) para obter detalhes e versões alternativas.

Observação: para essa operação, o `api-version` é especificado como um parâmetro de consulta na URL, independentemente de você chamar **Suggestions** com GET ou POST.

**Cabeçalhos da solicitação**

A lista a seguir descreve os cabeçalhos de solicitação necessários e opcionais

* `api-key`: a `api-key` é usada para autenticar a solicitação para o serviço de pesquisa. É um valor de cadeia de caracteres exclusivo para a URL do serviço. A solicitação **Suggestions** pode especificar uma chave de administração ou a chave de consulta como a `api-key`.

Você também precisará do nome de serviço para criar a URL da solicitação. Você pode obter o nome do serviço e a `api-key` por meio do painel de serviço no Portal do Azure. Consulte [Criar um serviço Azure Search no portal](search-create-service-portal.md) para obter ajuda sobre a navegação na página.

**Corpo da solicitação**

Para GET: Nenhum.

Para POST:

    {
      "filter": "odata_filter_expression",
      "fuzzy": true | false (default),
      "highlightPreTag": "pre_tag",
      "highlightPostTag": "post_tag",
      "minimumCoverage": # (% of index that must be covered to declare query successful; default 80),
      "orderby": "orderby_expression",
      "search": "partial_search_input",
      "searchFields": "field_name_1, field_name_2, ...",
      "select": "field_name_1, field_name_2, ...",
      "suggesterName": "suggester_name",
      "top": # (default 5)
    }

**Resposta**

Código de status: 200 OK é retornado para uma resposta bem-sucedida.

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
        },
        ...
      ]
    }

Se a opção de projeção for usada para recuperar campos, eles serão incluídos em cada elemento da matriz:

    {
      "@search.coverage": # (if minimumCoverage was provided in the query),
      "value": [
        {
          "@search.text": "...",
          "<key field>": "..."
          <other projected data fields>
        },
        ...
      ]
    }

**Exemplo**

Recuperar cinco sugestões, em que a entrada de pesquisa parcial é 'lux'

    GET /indexes/hotels/docs/suggest?search=lux&$top=5&suggesterName=sg&api-version=2015-02-28-Preview

    POST /indexes/hotels/docs/suggest?api-version=2015-02-28-Preview
    {
      "search": "lux",
      "top": 5,
      "suggesterName": "sg"
    }
