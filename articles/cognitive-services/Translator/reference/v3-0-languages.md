---
title: Método de idiomas de API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Use o método Idiomas de API de Tradução de Texto.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 1713a05590f389a6de70d72e8d62237c7521d808
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605111"
---
# <a name="translator-text-api-30-languages"></a>API de Tradução de Texto 3.0: Languages

Obtém o conjunto de idiomas atualmente suportados por outras operações da API de Tradução de Texto. 

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `GET` para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>api-version</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Versão da API solicitada pelo cliente. O valor precisa ser `3.0`.</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>*Parâmetro opcional*.<br/>Uma lista separada por vírgula de nomes que definem o grupo de idiomas a ser retornado. Os nomes de grupo permitidos são: `translation`, `transliteration` e `dictionary`. Se nenhum escopo é fornecido, todos os grupos são retornados, o que é equivalente a passar `scope=translation,transliteration,dictionary`. Para decidir qual conjunto de idiomas compatíveis é apropriado para seu cenário, confira a descrição do [objeto de resposta](#response-body).</td>
  </tr>
</table> 

Os cabeçalhos de solicitação são:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>Idioma aceito</td>
    <td>*Cabeçalho de solicitação opcional*.<br/>O idioma a ser usado para cadeias de caracteres de interface do usuário. Alguns dos campos na resposta são nomes de idiomas ou nomes de regiões. Use esse parâmetro para definir o idioma no qual esses nomes são retornados. O idioma é especificado fornecendo uma marcação de idioma BCP 47 bem formada. Por exemplo, use o valor `fr` para solicitar nomes em francês ou use o valor `zh-Hant` para solicitar nomes em chinês tradicional.<br/>Os nomes são fornecidos em inglês quando um idioma de destino não é especificado ou quando a localização não está disponível.
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Cabeçalho de solicitação opcional*.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação.</td>
  </tr>
</table> 

A autenticação não é necessária para obtenção de recursos de idioma.

## <a name="response-body"></a>Corpo da resposta

Um cliente usa o parâmetro de consulta `scope` para definir os grupos de idiomas de seu interesse.

* `scope=translation` fornece os idiomas compatíveis para traduzir o texto de um idioma em outro;

* `scope=transliteration` fornece as funcionalidades para converter o texto em um idioma de um script em outro;

* `scope=dictionary` fornece os pares de idiomas para os quais as operações `Dictionary` retornam dados.

Um cliente pode recuperar vários grupos simultaneamente especificando uma lista separada por vírgula de nomes. Por exemplo, `scope=translation,transliteration,dictionary` retorna os idiomas compatíveis com todos os grupos.

Uma resposta bem-sucedida é um objeto JSON com uma propriedade para cada grupo solicitado:

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

O valor de cada propriedade é mostrado a seguir.

* Propriedade `translation`

  O valor da propriedade `translation` é um dicionário de pares (chave, valor). Cada chave é uma marcação de linguagem BCP 47. Uma chave identifica um idioma no qual o texto pode ser convertido ou traduzido. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma:

  * `name`: Nome de exibição do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: Nome de exibição do idioma na localidade nativa desse idioma.

  * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

  Um exemplo é:
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* Propriedade `transliteration`

  O valor da propriedade `transliteration` é um dicionário de pares (chave, valor). Cada chave é uma marcação de linguagem BCP 47. Uma chave identifica um idioma no qual o texto pode ser convertido de um script para outro. O valor associado à chave é um objeto JSON com propriedades que descrevem o idioma e seus scripts compatíveis:

  * `name`: Nome de exibição do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: Nome de exibição do idioma na localidade nativa desse idioma.

  * `scripts`: Lista de scripts dos quais converter. Cada elemento da lista `scripts` tem propriedades:

    * `code`: Código que identifica o script.

    * `name`: Nome de exibição do script na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: Nome de exibição do idioma na localidade nativa do idioma.

    * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

    * `toScripts`: Lista de scripts disponíveis nos quais converter o texto. Cada elemento da lista `toScripts` tem propriedades `code`, `name`, `nativeName` e `dir`, conforme descrito anteriormente.

  Um exemplo é:

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* Propriedade `dictionary`

  O valor da propriedade `dictionary` é um dicionário de pares (chave, valor). Cada chave é uma marcação de linguagem BCP 47. A chave identifica um idioma para o qual traduções alternativas e traduções reversas estão disponíveis. O valor é um objeto JSON que descreve o idioma de origem e os idiomas de destino com traduções disponíveis:

  * `name`: Nome de exibição do idioma de origem na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: Nome de exibição do idioma na localidade nativa desse idioma.

  * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

  * `translations`: Lista de idiomas com traduções alternativas e exemplos para a consulta expressa no idioma de origem. Cada elemento da lista `translations` tem propriedades:

    * `name`: Nome de exibição do idioma de destino na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: Nome de exibição do idioma de destino na localidade nativa do idioma de destino.

    * `dir`: Direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.
    
    * `code`: Código de idioma que identifica o idioma de destino.

  Um exemplo é:

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

A estrutura do objeto de resposta não será alterada sem uma alteração na versão da API. Para a mesma versão da API, a lista de idiomas disponíveis pode ser alterada ao longo do tempo, porque o Microsoft Translator estende continuamente a lista de idiomas compatíveis com seus serviços.

A lista de idiomas compatíveis não será alterada com frequência. Para economizar largura de banda de rede e melhorar a capacidade de resposta, um aplicativo cliente deve considerar o armazenamento em cache dos recursos de idioma e da marca da entidade correspondente (`ETag`). Em seguida, o aplicativo cliente pode periodicamente (por exemplo, uma vez a cada 24 horas) consultar o serviço para buscar o último conjunto de idiomas compatíveis. Passar o valor `ETag` atual em um campo de cabeçalho `If-None-Match` permitirá que o serviço otimize a resposta. Se o recurso não tiver sido modificado, o serviço retornará o código de status 304 e um corpo de resposta vazio.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>DESCRIÇÃO</th>
  <tr>
    <td>ETag</td>
    <td>Valor atual da marca da entidade para os grupos solicitados de idiomas compatíveis. Para tornar as solicitações seguintes mais eficientes, o cliente pode enviar o valor `ETag` em um campo de cabeçalho `If-None-Match`.
    </td>
  </tr>
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
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada pelos cabeçalhos de solicitação `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou é inválido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
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

O exemplo a seguir mostra como recuperar os idiomas compatíveis para a tradução de texto.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```

---
