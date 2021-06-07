---
title: Atualizar a API de Pesquisa de Imagem do Bing da v5 para v7
titleSuffix: Azure Cognitive Services
description: Este guia de atualização descreve as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Imagem do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96342168"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Guia de atualização da API v7 de Pesquisa de Imagem do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa de Imagem do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de quebra

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade foi alterado de v5 para v7. Por exemplo, https: \/ /API.cognitive.Microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Os campos a seguir foram adicionados ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`


- Os códigos de erro da v5 foram substituídos pelos possíveis valores `code` e `subCode` a seguir.

|Código|Subcódigo|Descrição
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retornará ServerError sempre que ocorrer qualquer uma das condições do subcódigo. A resposta incluirá esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retornará InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP será 410.
|RateLimitExceeded||O Bing retornará RateLimitExceeded sempre que você exceder a cota de consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>O Bing retornará código de status HTTP 429 se QPS exceder, e 403 se QPM exceder.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Isso pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

- Os códigos a seguir mapeiam os códigos de erro anteriores para os novos códigos. Se você obteve uma dependência nos códigos de erro da v5, atualize o código adequadamente.

|Código da versão 5|Code.subCode da versão 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Desabilitado|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloqueado|InvalidRequest.Blocked



### <a name="query-parameters"></a>Parâmetros de consulta

- Renomeado o parâmetro de consulta `modulesRequested` para [módulos](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Renomeadas as Anotações para Marcações. Consulte o parâmetro de consulta [modules](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) para Marcações.  

- Alterada a lista de mercados com suporte do valor do filtro ShoppingSources para apenas en-US. Consulte [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Alterações de insights de imagem

- Renomeado o campo `annotations` de [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para `imageTags`.  

- Renomeado o objeto `AnnotationModule` para [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule).  

- Renomeado o objeto `Annotation` para [Tag](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) e renomeado o campo `confidence`.  

- Renomeado o campo `insightsSourcesSummary` do objeto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) para `insightsMetadata`.  

- Renomeado o objeto `InsightsSourcesSummary` para [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata).  

- Adicionado o ponto de extremidade `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Use esse ponto de extremidade para solicitar insights de imagem em vez do ponto de extremidade /images/search. Consulte [Insights de Imagem](./image-insights.md).

- Os parâmetros de consulta a seguir agora são válidos apenas com o ponto de extremidade `/images/details`.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [módulos](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [carro](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [CT](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Renomeado o objeto `ImageInsightsResponse` para [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

- Alterado os tipos de dados dos seguintes campos no objeto [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights).  

    -   Alterado o tipo do campo `relatedCollections` de `ImageGallery[]` para [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule).  

    -   Alterado o tipo do campo `pagesIncluding` de `Image[]` para [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterado o tipo do campo `relatedSearches` de `Query[]` para [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule).  

    -   Alterado o tipo do campo `recipes` de `Recipe[]` para [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule).  

    -   Alterado o tipo do campo `visuallySimilarImages` de `Image[]` para [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Alterado o tipo do campo `visuallySimilarProducts` de `ProductSummaryImage[]` para [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule).  

    -   Removido o objeto `ProductSummaryImage` e movido os campos relacionados ao produto para o objeto [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image). O objeto `Image` inclui os campos relacionados ao produto somente quando a imagem é incluída como parte de produtos visualmente similar em uma resposta de insight de imagem.  

    -   Alterado o tipo do campo `recognizedEntityGroups` de `RecognizedEntityGroup[]` para [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule).  

-   Renomeado o campo `categoryClassification` de [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) para `annotations` e alterado o tipo para `AnnotationsModule`.  

### <a name="images-answer"></a>Resposta de imagens

-   Removidos os campos displayShoppingSourcesBadges e displayRecipeSourcesBadges de [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Renomeado o campo `nextOffsetAddCount` de [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) para `nextOffset`. A maneira como o deslocamento é utilizado também alterou. Anteriormente, você definiu o parâmetro de consulta [offset](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) para o valor `nextOffsetAddCount`, mais o valor de deslocamento anterior, mais o número de imagens no resultado. Agora, você define `offset` para o valor `nextOffset`.  


## <a name="non-breaking-changes"></a>Alterações não relacionadas à falha

### <a name="query-parameters"></a>Parâmetros de consulta

- Adicionado Transparente como um possível valor do filtro [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype). O filtro Transparente retorna apenas imagens com um fundo transparente.

- Adicionado o valor do filtro Qualquer como uma possível [licença](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license). O filtro Qualquer retorna apenas imagens que estão sob licença.

- Adicionados os parâmetros de consulta [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) e [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize). Use esses filtros para retornar imagens dentro de um intervalo de tamanhos de arquivo.  

- Adicionados os parâmetros de consulta [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth), [minWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth). Use esses filtros para retornar imagens dentro de um intervalo de alturas e larguras.  

### <a name="object-changes"></a>Alterações de objeto

- Adicionados os campos `description` e `lastUpdated` para o objeto [Offer](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer).  

- Adicionado o campo `name` para o objeto [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery).  

- Adicionado `similarTerms` para o objeto [imagens](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images). Este campo contém uma lista de termos que são semelhantes em significado à cadeia de caracteres de consulta do usuário.