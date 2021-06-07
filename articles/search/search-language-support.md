---
title: Indexação de vários idiomas para consultas de pesquisa que não estão em inglês
titleSuffix: Azure Cognitive Search
description: Crie um índice que ofereça suporte a conteúdo em vários idiomas e crie consultas com escopo para esse conteúdo.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 627ec77af4e492b4f22404972729cecdb1c40f06
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801597"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Como criar um índice para vários idiomas no Azure Pesquisa Cognitiva

Um requisito importante em um aplicativo de pesquisa multilíngue é a capacidade de pesquisar e recuperar os resultados no próprio idioma do usuário. No Azure Pesquisa Cognitiva, uma maneira de atender aos requisitos de idioma de um aplicativo multilíngue é criar campos dedicados para armazenar cadeias de caracteres em um idioma específico e, em seguida, restringir a pesquisa de texto completo apenas a esses campos no momento da consulta.

+ Em definições de campo, defina um analisador de linguagem que invoca as regras linguísticas do idioma de destino. Para exibir a lista completa de analisadores com suporte, consulte [Adicionar analisadores de idioma](index-add-language-analyzers.md).

+ Na solicitação de consulta, defina parâmetros como escopo pesquisa de texto completo em campos específicos e, em seguida, corte os resultados de todos os campos que não fornecem conteúdo compatível com a experiência de pesquisa que você deseja entregar.

O sucesso dessa técnica depende da integridade do conteúdo do campo. O Azure Pesquisa Cognitiva não converte cadeias de caracteres nem executa a detecção de idioma como parte da execução da consulta. Cabe a você certificar-se de que esses campos contêm as cadeias de caracteres esperadas.

## <a name="define-fields-for-content-in-different-languages"></a>Definir campos para o conteúdo em idiomas diferentes

No Azure Pesquisa Cognitiva, as consultas visam um único índice. Os desenvolvedores que desejam fornecer cadeias de caracteres específicas a um idioma em uma experiência de pesquisa única normalmente definem campos dedicados para armazenar os valores: um campo para cadeias de caracteres em inglês, um para francês e assim por diante.

A propriedade "Analyzer" em uma definição de campo é usada para definir o [analisador de idioma](index-add-language-analyzers.md). Ele será usado para indexação e execução de consulta.

```JSON
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft"
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft"
    },
```

## <a name="build-and-load-an-index"></a>Criar e carregar um índice

Uma etapa intermediária (e talvez óbvia) é que você precisa [criar e preencher o índice](search-get-started-dotnet.md) antes de formular uma consulta. Mencionamos essa etapa aqui para fins de integridade. Uma maneira de determinar a disponibilidade do índice é verificar a lista de índices no [portal](https://portal.azure.com).

> [!TIP]
> Detecção de idioma e tradução de texto têm suporte durante a ingestão de dados por meio do [reenriquecimento de ia](cognitive-search-concept-intro.md) e [habilidades](cognitive-search-working-with-skillsets.md). Se você tiver uma fonte de dados do Azure com conteúdo de idioma misto, poderá experimentar os recursos de detecção e tradução de idioma usando o [Assistente de importação de dados](cognitive-search-quickstart-blob.md).

## <a name="constrain-the-query-and-trim-results"></a>Restringir a consulta e cortar os resultados

Os parâmetros na consulta são usados para limitar a pesquisa em campos específicos e, em seguida, cortar os resultados dos campos não úteis para seu cenário. 

| Parâmetros | Finalidade |
|-----------|--------------|
| **searchFields** | Limita a pesquisa de texto completo à lista de campos nomeados. |
| **$select** | Corta a resposta para incluir apenas os campos especificados. Por padrão, todos os campos recuperáveis são retornados. O parâmetro **$select** permite que você escolha quais deseja retornar. |

Considerando uma meta de restringir a pesquisa para campos que contêm cadeias de caracteres em francês, você usaria **searchFields** para direcionar a consulta em campos que contêm cadeias de caracteres nesse idioma.

Não é necessário especificar o analisador em uma solicitação de consulta. Um analisador de linguagem na definição de campo sempre será usado durante o processamento da consulta. Para consultas que especificam vários campos que chamam diferentes analisadores de idioma, os termos ou frases serão processados independentemente pelos analisadores atribuídos para cada campo.

Por padrão, uma pesquisa retorna todos os campos marcados como recuperáveis. Sendo assim, talvez convenha excluir campos que não estão em conformidade com a experiência de pesquisa específica a um idioma que você deseja fornecer. Especificamente, se você limitou a pesquisa a um campo com cadeias de caracteres em francês, você provavelmente deseja excluir os campos com cadeias de caracteres em inglês dos seus resultados. O uso do parâmetro de consulta **$select** oferece controle sobre quais campos são retornados para o aplicativo de chamada.

#### <a name="example-in-rest"></a>Exemplo em REST

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "animaux acceptés",
    "searchFields": "Tags, Description_fr",
    "select": "HotelName, Description_fr, Address/City, Address/StateProvince, Tags",
    "count": "true"
}
```

#### <a name="example-in-c"></a>Exemplo em C #

```csharp
private static void RunQueries(SearchClient srchclient)
{
    SearchOptions options;
    SearchResults<Hotel> response;

    options = new SearchOptions()
    {
        IncludeTotalCount = true,
        Filter = "",
        OrderBy = { "" }
    };

    options.Select.Add("HotelId");
    options.Select.Add("HotelName");
    options.Select.Add("Description_fr");
    options.SearchFields.Add("Tags");
    options.SearchFields.Add("Description_fr");

    response = srchclient.Search<Hotel>("*", options);
    WriteDocuments(response);
}
```

## <a name="boost-language-specific-fields"></a>Impulsionar campos específicos do idioma

Às vezes, o idioma do agente emissor de uma consulta não é conhecido; nesse caso, a consulta pode ser emitida em todos os campos simultaneamente. A preferência de IA para resultados em uma determinada linguagem pode ser definida usando [perfis de Pontuação](index-add-scoring-profiles.md). No exemplo a seguir, as correspondências encontradas na descrição em inglês serão pontuadas em relação às correspondências em outros idiomas:

```JSON
  "scoringProfiles": [
    {
      "name": "englishFirst",
      "text": {
        "weights": { "description": 2 }
      }
    }
  ]
```

Em seguida, você incluiria o perfil de pontuação na solicitação de pesquisa:

```http
POST /indexes/hotels/docs/search?api-version=2020-06-30
{
  "search": "pets allowed",
  "searchFields": "Tags, Description",
  "select": "HotelName, Tags, Description",
  "scoringProfile": "englishFirst",
  "count": "true"
}
```

## <a name="next-steps"></a>Próximas etapas

+ [Analisadores de linguagem](index-add-language-analyzers.md)
+ [Como funciona a pesquisa de texto completo no Azure Cognitive Search](search-lucene-query-architecture.md)
+ [API REST para pesquisar documentos](/rest/api/searchservice/search-documents)
+ [Visão geral de enriquecimento de ia](cognitive-search-concept-intro.md)
+ [Visão geral do habilidades](cognitive-search-working-with-skillsets.md)