---
title: Método de BreakSentence de API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Use o método BreakSentence de API do Translator texto.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: de11d493c1a42b67aeab6502f719bcee1adc6e99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60879133"
---
# <a name="translator-text-api-30-breaksentence"></a>API de Tradução de Texto 3.0: BreakSentence

Identifica o posicionamento dos limites de frase em uma parte do texto.

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro de consulta obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
  <tr>
    <td>Linguagem</td>
    <td>*Parâmetro de consulta opcional*.<br/>Marcação de idioma que identifica o idioma do texto de entrada. Se um código não for especificado, a detecção automática de idioma será aplicada.</td>
  </tr>
  <tr>
    <td>script</td>
    <td>*Parâmetro de consulta opcional*.<br/>Marcação de script identificando o script usado pelo texto de entrada. Se um script não for especificado, o script padrão do idioma será presumido.</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>Cabeçalhos de autenticação</td>
    <td><em>Cabeçalho de solicitação obrigatório</em>.<br/>Veja <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Opções disponíveis para autenticação</a>.</td>
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

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`. Limites de frase são computados para o valor da propriedade `Text`. Um corpo da solicitação de amostra com um texto fica assim:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento de matriz não pode exceder dez mil caracteres incluindo espaços.
* Todo o texto incluído na solicitação não pode exceder 50 mil caracteres incluindo espaços.
* Se o parâmetro de consulta `language` for especificado, todos os elementos de matriz deverão estar no mesmo idioma. Caso contrário, a detecção automática de idioma será aplicada independentemente a cada elemento da matriz.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: Uma matriz de inteiros que representa os comprimentos das frases no elemento de texto. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença. 

  * `detectedLanguage`: um objeto que descreve o idioma detectado por meio das seguintes propriedades:

     * `language`: O código do idioma detectado.

     * `score`: um valor flutuante indicando a confiança no resultado. A pontuação é entre zero e um, e uma pontuação baixa indica uma baixa confiança.
     
    Observe que a propriedade `detectedLanguage` só está presente no objeto do resultado quando é solicitada a detecção automática de idioma.

Um exemplo de resposta JSON é:

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar a solicitação. É usado para fins de solução de problemas.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

Veja a seguir os possíveis códigos de status HTTP retornados por uma solicitação. 

<table width="100%">
  <th width="20%">Código de status</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>200</td>
    <td>Sucesso.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou é inválido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
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
    <td>O chamador está enviando muitas solicitações.</td>
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

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns que podem ser encontrados na [página de referência da API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter os limites de frase de uma única frase. O idioma da frase será detectado automaticamente pelo serviço.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

