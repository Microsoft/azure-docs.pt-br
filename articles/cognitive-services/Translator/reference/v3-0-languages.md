---
title: Método de idiomas do Tradutor
titleSuffix: Azure Cognitive Services
description: O método Languages Obtém o conjunto de idiomas com suporte no momento por outras operações do tradutor.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 8471983d24cfed39e6521e19366f6023e519adaf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895468"
---
# <a name="translator-30-languages"></a>Tradutor 3,0: idiomas

Obtém o conjunto de idiomas com suporte no momento por outras operações do tradutor. 

## <a name="request-url"></a>URL de Solicitação

Envie uma solicitação `GET` para:
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros da solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
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
  <th>Descrição</th>
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

  * `name`: nome de exibição do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: nome de exibição do idioma na localidade nativa desse idioma.

  * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

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

  * `name`: nome de exibição do idioma na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: nome de exibição do idioma na localidade nativa desse idioma.

  * `scripts`: lista de scripts dos quais converter. Cada elemento da lista `scripts` tem propriedades:

    * `code`: código que identifica o script.

    * `name`: nome de exibição do script na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: nome de exibição do idioma na localidade nativa do idioma.

    * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

    * `toScripts`: lista de scripts disponíveis nos quais converter o texto. Cada elemento da lista `toScripts` tem propriedades `code`, `name`, `nativeName` e `dir`, conforme descrito anteriormente.

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

  * `name`: nome de exibição do idioma de origem na localidade solicitada por meio do cabeçalho `Accept-Language`.

  * `nativeName`: nome de exibição do idioma na localidade nativa desse idioma.

  * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

  * `translations`: lista de idiomas com traduções alternativas e exemplos para a consulta expressa no idioma de origem. Cada elemento da lista `translations` tem propriedades:

    * `name`: nome de exibição do idioma de destino na localidade solicitada por meio do cabeçalho `Accept-Language`.

    * `nativeName`: nome de exibição do idioma de destino na localidade nativa do idioma de destino.

    * `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.
    
    * `code`: código de idioma que identifica o idioma de destino.

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
  <th>Descrição</th>
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
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>304</td>
    <td>O recurso não foi modificado desde a versão especificada pelos cabeçalhos de solicitação `If-None-Match`.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou não é válido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
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
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```