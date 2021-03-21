---
title: Método de detecção de Tradutor
titleSuffix: Azure Cognitive Services
description: Identifique o idioma de um pedaço de texto com o método de detecção do tradutor de serviços cognitivas do Azure.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: cb6660585b5f2b9ab56eaf863f1ec431e5e85109
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895519"
---
# <a name="translator-30-detect"></a>Tradutor 3,0: detectar

Identifica o idioma de uma parte do texto.

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalhos de autenticação</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>Veja <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Tipo de conteúdo</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Cabeçalho de solicitação obrigatório*.<br/>O tamanho do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Observe que você poderá omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta nomeado `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo da solicitação

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`. A detecção de idioma é aplicada ao valor da propriedade `Text`. A detecção automática de idioma funciona melhor com texto de entrada mais longo. Um corpo de solicitação de exemplo é semelhante a:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* Todo o texto incluído na solicitação não pode exceder 50 mil caracteres incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `language`: o código do idioma detectado.

  * `score`: um valor flutuante indicando a confiança no resultado. A pontuação é entre zero e um, e uma pontuação baixa indica uma baixa confiança.

  * `isTranslationSupported`: um valor booliano que será verdadeiro, se o idioma detectado for um dos idiomas com suporte para tradução de texto.

  * `isTransliterationSupported`: um valor booliano que será verdadeiro, se o idioma detectado for um dos idiomas com suporte para transliteração.
  
  * `alternatives`: uma matriz de outros idiomas possíveis. Cada elemento da matriz é outro objeto com as mesmas propriedades listadas acima: `language`, `score`, `isTranslationSupported` e `isTransliterationSupported`.

Um exemplo de resposta JSON é:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar a solicitação. É usado para fins de solução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação. 

<table width="100%">
  <th width="20%">Código de status</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou não é válido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A solicitação não pôde ser autenticada. Verifique se as credenciais estão especificadas e são válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A solicitação não foi autorizada. Verifique os detalhes da mensagem de erro. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram consumidas.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Erro inesperado. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente não disponível. Tente novamente a solicitação. Se o erro persistir, relate-o com: data e hora da falha, identificador da solicitação do cabeçalho de resposta `X-RequestId` e identificador do cliente do cabeçalho de solicitação `X-ClientTraceId`.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na [página de referência do tradutor v3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como recuperar os idiomas compatíveis para a tradução de texto.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```