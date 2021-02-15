---
title: Método BreakSentence do Tradutor
titleSuffix: Azure Cognitive Services
description: O método BreakSentence do tradutor identifica o posicionamento dos limites de sentença em uma parte do texto.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2da614fe829d0aa82bfa57337baf44491993c68f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895536"
---
# <a name="translator-30-breaksentence"></a>Tradutor 3,0: BreakSentence

Identifica o posicionamento dos limites de frase em uma parte do texto.

## <a name="request-url"></a>URL da solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

| Parâmetro de consulta | Descrição |
| -------| ----------- |
| api-version <img width=200/>   | **Parâmetro de consulta obrigatório**.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`. |
| Linguagem | **Parâmetro de consulta opcional**.<br/>Marcação de idioma que identifica o idioma do texto de entrada. Se um código não for especificado, a detecção automática de idioma será aplicada. |
| Script    | **Parâmetro de consulta opcional**.<br/>Marcação de script identificando o script usado pelo texto de entrada. Se um script não for especificado, o script padrão do idioma será presumido.  | 

Os cabeçalhos de solicitação incluem:

| Cabeçalhos | Descrição |
| ------- | ----------- |
| Cabeçalhos de autenticação <img width=200/>  | **Cabeçalho de solicitação obrigatório**.<br/>Veja <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">Opções disponíveis para autenticação</a>. |
| Tipo de conteúdo | **Cabeçalho de solicitação obrigatório**.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: `application/json`. |
| Content-Length    | **Cabeçalho de solicitação obrigatório**.<br/>O tamanho do corpo da solicitação.  | 
| X-ClientTraceId   | **Opcional**.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Observe que você poderá omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta nomeado `ClientTraceId`.  | 

## <a name="request-body"></a>Corpo da solicitação

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`. Limites de frase são computados para o valor da propriedade `Text`. Um corpo da solicitação de amostra com um texto fica assim:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* O valor de texto de um elemento de matriz não pode exceder 50.000 caracteres, incluindo espaços.
* Todo o texto incluído na solicitação não pode exceder 50 mil caracteres incluindo espaços.
* Se o parâmetro de consulta `language` for especificado, todos os elementos de matriz deverão estar no mesmo idioma. Caso contrário, a detecção automática de idioma será aplicada independentemente a cada elemento da matriz.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `sentLen`: uma matriz de inteiros que representa os comprimentos das frases no elemento de texto. O comprimento da matriz é o número de sentenças, e os valores são o comprimento de cada sentença. 

  * `detectedLanguage`: um objeto que descreve o idioma detectado por meio das seguintes propriedades:

     * `language`: o código do idioma detectado.

     * `score`: um valor flutuante indicando a confiança no resultado. A pontuação é entre zero e um, e uma pontuação baixa indica uma baixa confiança.
     
    Observe que a propriedade `detectedLanguage` só está presente no objeto do resultado quando é solicitada a detecção automática de idioma.

Um exemplo de resposta JSON é:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
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

O exemplo a seguir mostra como obter os limites de frase de uma única frase. O idioma da frase será detectado automaticamente pelo serviço.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```