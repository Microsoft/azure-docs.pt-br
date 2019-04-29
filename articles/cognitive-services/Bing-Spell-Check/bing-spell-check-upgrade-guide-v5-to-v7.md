---
title: Atualizar API de Verificação Ortográfica do Bing v5 para v7
titlesuffix: Azure Cognitive Services
description: Identifica as partes do aplicativo que você precisa atualizar para usar a versão 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: b19681fd1ad70985bccd375931f5d5801f153aa8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60895782"
---
# <a name="spell-check-api-upgrade-guide"></a>Guia de atualização de API de Verificação Ortográfica

Este guia de atualização identifica as alterações entre a versão 5 e a versão 7 da API de Verificação Ortográfica do Bing. Use este guia para ajudá-lo a identificar as partes do aplicativo que você precisa atualizar para usar a versão 7.

## <a name="breaking-changes"></a>Alterações de última hora

### <a name="endpoints"></a>Pontos de extremidade

- O número de versão do ponto de extremidade foi alterado de v5 para v7. Por exemplo, `https://api.cognitive.microsoft.com/bing/v7.0/spellcheck`.

### <a name="error-response-objects-and-error-codes"></a>Objetos de resposta de erro e códigos de erro

- Todas as solicitações com falha agora devem incluir um objeto `ErrorResponse` no corpo da resposta.

- Os campos a seguir foram adicionados ao objeto `Error`.  
  - `subCode`&mdash;Particiona o código de erro em buckets discretos, se possível
  - `moreDetails`&mdash;Informações adicionais sobre o erro descrito no campo `message`
   

- Os códigos de erro da v5 foram substituídos pelos possíveis valores `code` e `subCode` a seguir.  
  
|Código|Subcódigo|DESCRIÇÃO
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

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Requisitos de uso e exibição](./UseAndDisplayRequirements.md)
