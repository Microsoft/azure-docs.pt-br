---
title: Método de Transliteração de API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Use o método de Transliteração de API de Tradução de Texto.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 4811e40f87962ecde75c7b2bb14e1d033e82ad44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605093"
---
# <a name="translator-text-api-30-transliterate"></a>API de Tradução de Texto 3.0: Transliterate

Converte texto em um idioma de um script em outro script.

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>api-version</td>
    <td>*Parâmetro obrigatório*.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
  <tr>
    <td>Linguagem</td>
    <td>*Parâmetro obrigatório*.<br/>Especifica o idioma do texto a converter de um script para outro. Idiomas possíveis estão listados no escopo de `transliteration` obtido consultando o serviço para os seus [idiomas compatíveis](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o script usado pelo texto de entrada. Pesquisa [linguagens compatíveis](./v3-0-languages.md) usando o escopo `transliteration` para localizar os scripts de entrada disponíveis para a linguagem selecionada.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parâmetro necessário*.<br/>Especifica o script de saída. Pesquisa [linguagens compatíveis](./v3-0-languages.md) usando o escopo `transliteration` para localizar os scripts de saída disponíveis para a combinação selecionada de linguagem de entrada e script de entrada.</td>
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

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres denominada `Text`, que representa a cadeia de caracteres a converter.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 10 elementos.
* O valor de texto de um elemento de matriz não pode exceder mil caracteres incluindo espaços.
* Todo o texto incluído na solicitação não pode exceder 5.000 caracteres incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada elemento na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `text`: uma cadeia de caracteres que é o resultado da conversão da cadeia de caracteres de entrada no script de saída.
  
  * `script`: uma cadeia de caracteres especificando o script usado na saída.

Um exemplo de resposta JSON é:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
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

O exemplo a seguir mostra como converter duas cadeias de caracteres japonesas em japonês romanizado.

# <a name="curltabcurl"></a>[curl](#tab/curl)

O conteúdo JSON para a solicitação neste exemplo:

```
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se você estiver usando cURL em uma janela de linha de comando que não seja compatível com caracteres Unicode, pegue o seguinte conteúdo JSON e salve-o em um arquivo denominado `request.txt`. Salve o arquivo com a codificação `UTF-8`.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```

---
