---
title: Adicionar verificação ortográfica
titleSuffix: Azure Cognitive Search
description: Anexe a correção ortográfica ao pipeline de consulta para corrigir erros de grafia em termos de consulta antes de executar a consulta.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625901"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Adicionar verificação ortográfica a consultas no Pesquisa Cognitiva

> [!IMPORTANT]
> A correção ortográfica está em visualização pública, disponível apenas por meio da API REST de visualização. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Durante a inicialização da visualização inicial, não há nenhum encargo para o verificador ortográfico. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

Você pode melhorar a RECALL, corrigindo os termos da consulta de pesquisa individual antes que eles atinjam o mecanismo de pesquisa. O parâmetro do **Verificador ortográfico** tem suporte para todos os tipos de consulta: [simples](query-simple-syntax.md), [completo](query-lucene-syntax.md)e a nova opção [semântica](semantic-how-to-query-request.md) atualmente em visualização pública.

## <a name="prerequisites"></a>Pré-requisitos

+ Um índice de pesquisa existente, contendo conteúdo em inglês. Atualmente, a correção ortográfica não funciona com [sinônimos](search-synonyms.md). Evite usá-lo em índices que especificam um mapa de sinônimos em qualquer definição de campo.

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve dar suporte a APIs REST de visualização na solicitação de consulta. Você pode usar o [postmaster](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)ou código que você modificou para fazer chamadas REST para as APIs de visualização.

+ [Uma solicitação de consulta](/rest/api/searchservice/preview-api/search-documents) que usa a correção ortográfica tem "API-Version = 2020-06 -30-Preview", "verificador ortográfico = léxico" e "queryLanguage = en-US".

  O queryLanguage é necessário para o verificador ortográfico e, atualmente, "en-US" é o único valor válido.

> [!Note]
> O parâmetro do verificador ortográfico está disponível em todas as camadas, nas mesmas regiões que fornecem pesquisa semântica. Você não precisa se inscrever para acessar esse recurso de visualização. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

## <a name="spell-correction-with-simple-search"></a>Correção ortográfica com pesquisa simples

O exemplo a seguir usa o índice de exemplo de hotéis internos para demonstrar a correção ortográfica em uma consulta de texto de forma livre simples. Sem a correção ortográfica, a consulta retorna zero resultados. Com a correção, a consulta retorna um resultado para o Resort orientado por família da Johnson.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>Correção ortográfica com Lucene completo

A correção ortográfica ocorre em termos de consulta individuais que passam por análise, e é por isso que você pode usar o parâmetro verificador ortográfico com algumas consultas do Lucene, mas não outras.

+ Formulários de consulta incompatíveis que ignoram a análise de texto incluem: curinga, Regex, difuso
+ Os formulários de consulta compatíveis incluem: pesquisa em campo, proximidade, aumento de termo

Este exemplo usa a pesquisa de campo no campo Categoria, com a sintaxe Lucene completa e um termo de consulta digitado incorretamente. Ao incluir o verificador ortográfico, a digitação em "Suiite" é corrigida e a consulta é realizada com sucesso.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>Correção ortográfica com pesquisa semântica

Essa consulta, com erros de digitação em cada termo, exceto uma, passa por correções ortográficas para retornar resultados relevantes. Para saber mais, consulte [criar uma consulta semântica](semantic-how-to-query-request.md).

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>Considerações sobre o idioma

O parâmetro queryLanguage necessário para o verificador ortográfico deve ser consistente com quaisquer [analisadores de idioma](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. 

+ queryLanguage determina quais léxicos são usados para verificação ortográfica e também é usado como uma entrada para o [algoritmo de classificação semântica](semantic-answers.md) se você estiver usando "QueryType = semântico".

+ Os analisadores de linguagem são usados durante a indexação e a execução da consulta para localizar documentos correspondentes no índice de pesquisa. Um exemplo de uma definição de campo que usa um analisador de linguagem é `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"` .

Para obter melhores resultados ao usar o verificador ortográfico, se queryLanguage for "en-US", qualquer analisador de idioma também deverá ser uma variante em inglês ("en. Microsoft" ou "en. Lucene").

> [!NOTE]
> Analisadores independentes de linguagem (como palavra-chave, simples, padrão, parada, espaço em branco ou `standardasciifolding.lucene` ) não entram em conflito com as configurações de queryLanguage.

Em uma solicitação de consulta, o queryLanguage que você define aplica-se igualmente ao verificador ortográfico, às respostas e às legendas. Não há nenhuma substituição para partes individuais.

Embora o conteúdo em um índice de pesquisa possa ser composto em vários idiomas, a entrada da consulta provavelmente está em um. O mecanismo de pesquisa não verifica a compatibilidade do queryLanguage, do analisador de linguagem e do idioma em que o conteúdo é composto, portanto, certifique-se de fazer o escopo das consultas de forma adequada para evitar a produção de resultados incorretos.

## <a name="next-steps"></a>Próximas etapas

+ [Criar uma consulta semântica](semantic-how-to-query-request.md)
+ [Criar uma consulta básica](search-query-create.md)
+ [Usar sintaxe de consulta Lucene completa](query-Lucene-syntax.md)
+ [Usar sintaxe de consulta simples](query-simple-syntax.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)