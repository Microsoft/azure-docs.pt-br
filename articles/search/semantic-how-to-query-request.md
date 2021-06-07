---
title: Criar uma consulta semântica
titleSuffix: Azure Cognitive Search
description: Defina um tipo de consulta semântica para anexar os modelos de aprendizado profundo ao processamento de consultas, à intenção de referência e ao contexto como parte da classificação e da relevância da pesquisa.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654655"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Criar uma consulta para legendas semânticas no Pesquisa Cognitiva

> [!IMPORTANT]
> A pesquisa semântica está em visualização pública, disponível por meio da API REST de visualização e portal do Azure. Os recursos de visualização são oferecidos no estado em que se encontram, sob [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Esses recursos são faturáveis. Para obter mais informações, consulte [disponibilidade e preços](semantic-search-overview.md#availability-and-pricing).

Neste artigo, saiba como formular uma solicitação de pesquisa que usa a classificação semântica e retorna legendas semânticas (e, opcionalmente, [respostas semânticas](semantic-answers.md)), com destaques sobre os termos e frases mais relevantes. As legendas e as respostas são retornadas em consultas formuladas usando o tipo de consulta "Semantic".

As legendas e as respostas são extraídas textualmente do texto no documento de pesquisa. O subsistema semântico determina qual parte do seu conteúdo tem as características de uma legenda ou resposta, mas não compõe novas frases ou frases. Por esse motivo, o conteúdo que inclui explicações ou definições funciona melhor para pesquisa semântica.

## <a name="prerequisites"></a>Pré-requisitos

+ Um serviço de pesquisa em uma camada Standard (S1, S2, S3), localizado em uma destas regiões: Norte EUA Central, oeste dos EUA, oeste dos EUA 2, leste dos EUA 2, Europa Setentrional Europa Ocidental. Se você tiver um serviço S1 ou superior existente em uma dessas regiões, poderá solicitar acesso sem precisar criar um novo serviço.

+ Acesso à visualização da pesquisa semântica: [inscrever-se](https://aka.ms/SemanticSearchPreviewSignup)

+ Um índice de pesquisa existente que contém conteúdo em inglês

+ Um cliente de pesquisa para enviar consultas

  O cliente de pesquisa deve dar suporte a APIs REST de visualização na solicitação de consulta. Você pode usar o [postmaster](search-get-started-rest.md), o [Visual Studio Code](search-get-started-vs-code.md)ou o código que faz chamadas REST para as APIs de visualização. Você também pode usar o [Gerenciador de pesquisa](search-explorer.md) no portal do Azure para enviar uma consulta semântica.

+ Uma [solicitação de consulta](/rest/api/searchservice/preview-api/search-documents) deve incluir a opção semântica e outros parâmetros descritos neste artigo.

## <a name="whats-a-semantic-query"></a>O que é uma consulta semântica?

No Pesquisa Cognitiva, uma consulta é uma solicitação parametrizada que determina o processamento da consulta e a forma da resposta. Uma *consulta semântica* adiciona parâmetros que invocam o modelo de reclassificação semântica que pode avaliar o contexto e o significado dos resultados correspondentes, promover correspondências mais relevantes na parte superior e retornar respostas e legendas semânticas.

A solicitação a seguir é representativa de uma consulta semântica mínima (sem respostas).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Assim como acontece com todas as consultas no Pesquisa Cognitiva, a solicitação visa a coleção de documentos de um único índice. Além disso, uma consulta semântica passa da mesma sequência de análise, análise, verificação e Pontuação como uma consulta não semântica. 

A diferença está em relevância e pontuação. Conforme definido nesta versão de visualização, uma consulta semântica é aquela cujos *resultados* são reclassificados usando um modelo de linguagem semântica, fornecendo uma maneira de trazer as correspondências consideradas mais relevantes pelo classificador semânticos, em vez das pontuações atribuídas pelo algoritmo de classificação de similaridade padrão.

Somente as principais correspondências 50 dos resultados iniciais podem ser classificadas semanticamente e todas as legendas de inclusão na resposta. Opcionalmente, você pode especificar um **`answer`** parâmetro na solicitação para extrair uma resposta em potencial. Para obter mais informações, consulte [respostas semânticas](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Consultar com o Gerenciador de pesquisa

O [Gerenciador de pesquisa](search-explorer.md) foi atualizado para incluir opções para consultas semânticas. Essas opções se tornam visíveis no portal depois de concluir as seguintes etapas:

1. [Inscreva-se](https://aka.ms/SemanticSearchPreviewSignup) e Admittance de seu serviço de pesquisa no programa de visualização

1. Abra o portal com esta sintaxe: `https://portal.azure.com/?feature.semanticSearch=true`

As opções de consulta incluem opções para habilitar consultas semânticas, searchFields e correção ortográfica. Você também pode colar os parâmetros de consulta necessários na cadeia de caracteres de consulta.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Opções de consulta no Gerenciador de pesquisa" border="true":::

## <a name="query-using-rest"></a>Consultar usando REST

Use os [documentos de pesquisa (visualização REST)](/rest/api/searchservice/preview-api/search-documents) para formular a solicitação programaticamente.

Uma resposta inclui legendas e realce automáticas. Se você quiser a resposta para incluir correção ortográfica ou respostas, adicione um **`speller`** parâmetro opcional ou **`answers`** na solicitação.

O exemplo a seguir usa o índice Hotéis-Sample-index para criar uma solicitação de consulta semântica com respostas semânticas e legendas:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

A tabela a seguir resume os parâmetros de consulta usados em uma consulta semântica para que você possa vê-los de forma holística. Para obter uma lista de todos os parâmetros, consulte [Pesquisar documentos (visualização REST)](/rest/api/searchservice/preview-api/search-documents)

| Parâmetro | Tipo | Descrição |
|-----------|-------|-------------|
| queryType | String | Os valores válidos incluem simples, completo e semântico. Um valor de "Semantic" é necessário para consultas semânticas. |
| queryLanguage | String | Necessário para consultas semânticas. Atualmente, apenas "en-US" é implementado. |
| searchFields | String | Uma lista delimitada por vírgulas de campos pesquisáveis. Especifica os campos em que a classificação semântica ocorre, a partir da qual as legendas e as respostas são extraídas. </br></br>Em contraste com tipos de consulta simples e completos, a ordem na qual os campos são listados determina a precedência. Para obter mais instruções de uso, consulte [Step 2: Set searchFields](#searchfields). |
| verificador ortográfico | String | Parâmetro opcional, não específico de consultas semânticas, que corrige termos incorretos de ortografia antes que eles atinjam o mecanismo de pesquisa. Para obter mais informações, consulte [Adicionar correção ortográfica a consultas](speller-how-to-add.md). |
| respectivas |String | Parâmetros opcionais que especificam se as respostas semânticas são incluídas no resultado. Atualmente, apenas "extração" é implementada. As respostas podem ser configuradas para retornar um máximo de cinco. O padrão é um. Este exemplo mostra uma contagem de três respostas: "extracçãoy \| Count3". Para obter mais informações, consulte [retornar respostas semânticas](semantic-answers.md).|

### <a name="formulate-the-request"></a>Formular a solicitação

Esta seção percorre os parâmetros de consulta necessários para a pesquisa semântica.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Etapa 1: definir QueryType e queryLanguage

Adicione os parâmetros a seguir ao restante. Ambos os parâmetros são obrigatórios.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

O queryLanguage deve ser consistente com quaisquer [analisadores de idioma](index-add-language-analyzers.md) atribuídos às definições de campo no esquema de índice. Se queryLanguage for "en-US", qualquer analisador de linguagem também deverá ser uma variante em inglês ("en. Microsoft" ou "en. Lucene"). Qualquer analisador independente de linguagem, como palavra-chave ou simples, não tem conflito com valores de queryLanguage.

Em uma solicitação de consulta, se você também estiver usando a [correção ortográfica](speller-how-to-add.md), o queryLanguage definido se aplicará igualmente ao verificador ortográfico, às respostas e às legendas. Não há nenhuma substituição para partes individuais. 

Embora o conteúdo em um índice de pesquisa possa ser composto em vários idiomas, a entrada da consulta provavelmente está em um. O mecanismo de pesquisa não verifica a compatibilidade do queryLanguage, do analisador de linguagem e do idioma em que o conteúdo é composto, portanto, certifique-se de fazer o escopo das consultas de forma adequada para evitar a produção de resultados incorretos.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Etapa 2: definir searchFields

O parâmetro searchFields é usado para identificar passagens a serem avaliadas para "similaridade semântica" para a consulta. Para a versão prévia, não recomendamos deixar searchFields em branco, pois o modelo requer uma dica sobre quais campos são os mais importantes para serem processados.

A ordem de searchFields é fundamental. Se você já usa searchFields no código existente para consultas do Lucene simples ou completas, visite novamente esse parâmetro para verificar a ordem dos campos ao alternar para um tipo de consulta semântica.

Para dois ou mais searchFields:

+ Inclua somente campos de cadeia de caracteres e campos de cadeia de caracteres de nível superior em coleções. Se você estiver incluindo campos que não são de cadeia de caracteres ou campos de nível inferior em uma coleção, não haverá erro, mas esses campos não serão usados na classificação semântica.

+ O primeiro campo deve ser sempre conciso (como um título ou nome), idealmente menos de 25 palavras.

+ Se o índice tiver um campo de URL que seja textual (legível por humanos `www.domain.com/name-of-the-document-and-other-details` , como, e não com foco no computador, como `www.domain.com/?id=23463&param=eis` ), coloque-o em segundo lugar na lista (ou primeiro se não houver nenhum campo de título conciso).

+ Siga esses campos por campos descritivos em que a resposta a consultas semânticas pode ser encontrada, como o conteúdo principal de um documento.

Se apenas um campo for especificado, use um campo descritivo em que a resposta a consultas semânticas possa ser encontrada, como o conteúdo principal de um documento. 

#### <a name="step-3-remove-orderby-clauses"></a>Etapa 3: remover cláusulas orderBy

Remova todas as cláusulas orderBy, se existirem em uma solicitação existente. A pontuação semântica é usada para ordenar os resultados e, se você incluir lógica de classificação explícita, um erro HTTP 400 será retornado.

#### <a name="step-4-add-answers"></a>Etapa 4: Adicionar respostas

Opcionalmente, adicione "respostas" se desejar incluir processamento adicional que forneça uma resposta. As respostas (e legendas) são extraídas das passagens encontradas nos campos listados em searchFields. Certifique-se de incluir campos ricos no conteúdo no searchFields para obter as melhores respostas em uma resposta. Para obter mais informações, consulte [como retornar respostas semânticas](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Etapa 5: adicionar outros parâmetros

Defina quaisquer outros parâmetros que você deseja na solicitação. Parâmetros como [Verificador ortográfico](speller-how-to-add.md), [Select](search-query-odata-select.md)e Count melhoram a qualidade da solicitação e a legibilidade da resposta.

Opcionalmente, você pode personalizar o estilo de realce aplicado às legendas. As legendas aplicam formatação de realce sobre passagens de chave no documento que resumem a resposta. O padrão é `<em>`. Se desejar especificar o tipo de formatação (por exemplo, plano de fundo amarelo), você poderá definir highlightPreTag e highlightPostTag.

## <a name="evaluate-the-response"></a>Avaliar a resposta

Assim como acontece com todas as consultas, uma resposta é composta de todos os campos marcados como recuperáveis ou apenas dos campos listados no parâmetro SELECT. Ele inclui a pontuação de relevância original e também pode incluir uma contagem ou resultados em lote, dependendo de como você formulau a solicitação.

Em uma consulta semântica, a resposta tem elementos adicionais: uma nova Pontuação de relevância classificada semanticamente, legendas em texto sem formatação e com destaques e, opcionalmente, uma resposta.

Em um aplicativo cliente, você pode estruturar a página de pesquisa para incluir uma legenda como a descrição da correspondência, em vez de todo o conteúdo de um campo específico. Isso é útil quando campos individuais são muito densos para a página de resultados da pesquisa.

A resposta para a consulta de exemplo acima retorna a seguinte correspondência como a seleção superior. As legendas são retornadas automaticamente, com texto sem formatação e versões realçadas. As respostas são omitidas do exemplo porque não foi possível determinar uma determinada consulta e corpus.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Próximas etapas

Lembre-se de que a classificação semântica e as respostas são criadas sobre um conjunto de resultados inicial. Qualquer lógica que melhora a qualidade dos resultados iniciais será postergada para a pesquisa semântica. Como uma próxima etapa, examine os recursos que contribuem para resultados iniciais, incluindo analisadores que afetam como as cadeias de caracteres são indexadas, perfis de pontuação que podem ajustar os resultados e o algoritmo de relevância padrão.

+ [Analisadores para processamento de texto](search-analyzers.md)
+ [Algoritmo de classificação de similaridade](index-similarity-and-scoring.md)
+ [Perfis de pontuação](index-add-scoring-profiles.md)
+ [Visão geral da pesquisa semântica](semantic-search-overview.md)
+ [Algoritmo de classificação semântica](semantic-ranking.md)
