---
title: Atualizar a API da v5 para v7 - API de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Determine quais partes do aplicativo exigem atualizações para usar as APIs v7 da Pesquisa na Web do Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: reference
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 8e5876c9141a3eb85593b12f45b0bde4c7984adf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61431133"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Atualizar a API de Pesquisa na Web do Bing da v5 para v7

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Pesquisa na Web do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de última hora

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade foi alterado de v5 para v7. Por exemplo, https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Os campos a seguir foram adicionados ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`


- Os códigos de erro da v5 foram substituídos pelos possíveis valores `code` e `subCode` a seguir.

|Código|Subcódigo|DESCRIÇÃO
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|O Bing retorna ServerError sempre que ocorre uma das condições de subcódigo. A resposta incluirá esses erros se o código de status HTTP for 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloqueado|O Bing retornará InvalidRequest sempre que qualquer parte da solicitação não for válida. Por exemplo, um parâmetro obrigatório está ausente ou um valor de parâmetro não é válido.<br/><br/>Se o erro for ParameterMissing ou ParameterInvalidValue, o código de status HTTP será 400.<br/><br/>Se o erro for HttpNotAllowed, o código de status HTTP será 410.
|RateLimitExceeded||O Bing retornará RateLimitExceeded sempre que você exceder a cota de consultas por segundo (QPS) ou consultas por mês (QPM).<br/><br/>O Bing retornará código de status HTTP 429 se QPS exceder, e 403 se QPM exceder.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|O Bing retorna InvalidAuthorization quando o Bing não pode autenticar o chamador. Por exemplo, o cabeçalho `Ocp-Apim-Subscription-Key` está ausente ou a chave de assinatura não é válida.<br/><br/>A redundância ocorrerá se você especificar mais de um método de autenticação.<br/><br/>Se o erro for InvalidAuthorization, o código de status HTTP será 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|O Bing retorna InsufficientAuthorization quando o chamador não tem permissões para acessar o recurso. Esse erro pode ocorrer se a chave de assinatura foi desabilitada ou expirou. <br/><br/>Se o erro for InsufficientAuthorization, o código de status HTTP será 403.

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


## <a name="non-breaking-changes"></a>Alterações não relacionadas à falha  

### <a name="headers"></a>Cabeçalhos

- Adição do cabeçalho de solicitação [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma) opcional. Por padrão, o Bing retorna o conteúdo armazenado em cache se disponível. Para impedir que o Bing retorne o conteúdo armazenado em cache, defina o cabeçalho Pragma como no-cache (por exemplo, Pragma: no-cache).

### <a name="query-parameters"></a>Parâmetros de consulta

- Adição do parâmetro de consulta [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount). Use esse parâmetro para especificar o número de respostas que a resposta deve incluir. As respostas são escolhidas com base na classificação. Por exemplo, se você definir esse parâmetro como três (3), a resposta incluirá as três principais respostas classificadas.  

- Adição do parâmetro de consulta [promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote). Use esse parâmetro junto com `answerCount` para incluir explicitamente um ou mais tipos de resposta, seja qual for a classificação. Por exemplo, para promover vídeos e imagens na resposta, defina promote como *vídeos, imagens*. A lista de respostas que você deseja promover não é contada em relação ao limite `answerCount`. Por exemplo, se `answerCount` é 2 e `promote` é definido como *vídeos, imagens*, a resposta pode incluir páginas da Web, notícias, vídeos e imagens.

### <a name="object-changes"></a>Alterações de objeto

- Adição do campo `someResultsRemoved` ao objeto [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer). O campo contém um valor booliano que indica se a resposta excluiu alguns resultados da resposta da Web.  
