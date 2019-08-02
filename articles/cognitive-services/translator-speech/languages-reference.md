---
title: Método de Idiomas da API de Tradução de Fala
titleSuffix: Azure Cognitive Services
description: Use o método de Idiomas da API de Tradução de Fala.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4abe6330d0359f7d7c922facecaaf1a8b1fc7174
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446946"
---
# <a name="translator-speech-api-languages"></a>API de Tradução de Fala: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

A API de Tradução de Fala estende continuamente a lista de idiomas compatíveis com seus serviços. Use essa API para descobrir o conjunto de idiomas atualmente disponíveis para uso com o serviço da API de Tradução de Fala.

Exemplos de código demonstrando o uso da API para obter idiomas disponíveis podem ser obtidos no [site Microsoft Translator do GitHub](https://github.com/MicrosoftTranslator).

## <a name="implementation-notes"></a>Notas de implementação

### <a name="get-languages"></a>GET /languages

Um amplo conjunto de idiomas está disponível para transcrever fala, traduzir o texto transcrito e produzir fala sintetizada com base na tradução.

Um cliente usa o parâmetro de consulta `scope` para definir os conjuntos de idiomas de seu interesse.

* **Conversão de fala em texto:** use o parâmetro de consulta `scope=speech` para recuperar o conjunto de idiomas disponíveis para transcrever fala em texto.
* **Tradução de texto:** use o parâmetro de consulta `scope=text` para recuperar o conjunto de idiomas disponíveis para traduzir texto transcrito.
* **Conversão de texto em fala:**  use o parâmetro de consulta `scope=tts` para recuperar o conjunto de idiomas e vozes disponíveis para sintetizar o texto traduzido de volta em fala.

Um cliente pode recuperar vários conjuntos simultaneamente ao especificar uma lista separada por vírgulas de opções. Por exemplo: `scope=speech,text,tts`.

Uma resposta bem-sucedida é um objeto JSON com uma propriedade para cada conjunto solicitado.

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

Uma vez que um cliente pode usar o parâmetro de consulta `scope` para selecionar quais conjuntos de idiomas compatíveis devem ser retornados, uma resposta real pode incluir somente um subconjunto de todas as propriedades mostradas acima.

O valor fornecido com cada propriedade é o seguinte.

### <a name="speech-to-text-speech"></a>Conversão de fala em texto (fala)

O valor associado à propriedade de conversão de fala em texto, `speech`, é um dicionário de pares (chave, valor). Cada chave identifica um idioma compatível com conversão de fala em texto. A chave é o identificador que o cliente passa para a API. O valor associado à chave é um objeto com as seguintes propriedades:

* `name`: nome de exibição do idioma.
* `language`: marca de idioma do idioma escrito associado. Consulte "Transação de texto" abaixo.
Um exemplo é:

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>Tradução de texto (texto)

O valor associado à propriedade `text` também é um dicionário em que cada chave identifica um idioma compatível para a tradução de texto. O valor associado à chave descreve o idioma:

* `name`: nome de exibição do idioma.
* `dir`: direcionalidade, que é `rtl` para idiomas da direita para a esquerda ou `ltr` para idiomas da esquerda para a direita.

Um exemplo é:

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>Conversão de texto em fala (tts)

O valor associado à propriedade de conversão de texto em fala, tts, também é um dicionário em que cada chave identifica uma voz compatível. Atributos de um objeto de voz são:

* `displayName`: nome de exibição para a voz.
* `gender`: gênero da voz (masculino ou feminino).
* `locale`: marca de idioma da voz com submarca de idioma principal e submarca de região.
* `language`: marca de idioma do idioma escrito associado.
* `languageName`: nome de exibição do idioma.
* `regionName`: nome de exibição da região para esse idioma.

Um exemplo é:

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>Localização
O serviço retorna todos os nomes no idioma do cabeçalho 'Accept-Language' para todos os idiomas com suporte em tradução de texto.

### <a name="response-class-status-200"></a>Classe de resposta (Status 200)
Objeto que descreve o conjunto de idiomas compatíveis.

Valor de ModelExample:

Languages { speech (object, optional), text (object, optional), tts (object, optional) }

### <a name="headers"></a>Cabeçalhos

|Cabeçalho|DESCRIÇÃO|Type|
:--|:--|:--|
X-RequestId|Valor gerado pelo servidor para identificar a solicitação e usado para fins de solução de problemas.|string|

### <a name="parameters"></a>parâmetros

|Parâmetro|DESCRIÇÃO|Tipo de Parâmetro|Tipo de Dados|
|:--|:--|:--|:--|
|api-version    |Versão da API solicitada pelo cliente. Os valores permitidos são: `1.0`.|query|string|
|scope  |Conjuntos de idiomas ou vozes compatíveis para retornar ao cliente. Esse parâmetro é especificado como uma lista separada por vírgulas das palavras-chave. As palavras-chave a seguir estão disponíveis:<ul><li>`speech`: fornece o conjunto de idiomas compatíveis para transcrever fala.</li><li>`tts`: fornece o conjunto de vozes compatíveis para conversão de texto em fala.</li><li>`text`: fornece o conjunto de idiomas compatíveis para a tradução de texto.</li></ul>Se um valor não for especificado, o valor de `scope` usará `text` como padrão.|query|string|
|X-ClientTraceId    |Um GUID gerado pelo cliente usado para rastrear uma solicitação. Para facilitar a solução de problemas, os clientes devem fornecer um novo valor com cada solicitação e registrá-lo em log.|cabeçalho|string|
|Idioma aceito    |Alguns dos campos na resposta são nomes de idiomas ou regiões. Use esse parâmetro para definir o idioma no qual os nomes são retornados. O idioma é especificado fornecendo uma marcação de idioma BCP 47 bem formada. Selecione uma marcação na lista de identificadores de idioma retornados com o escopo `text`. Para idiomas sem suporte, os nomes são fornecidos no idioma inglês.<br/>Por exemplo, use o valor `fr` para solicitar nomes em francês ou use o valor `zh-Hant` para solicitar nomes em chinês tradicional.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de status HTTP|Motivo|
|:--|:--|
|400|Solicitação inválida. Verifique os parâmetros de entrada para garantir que sejam válidos. O objeto de resposta inclui uma descrição mais detalhada do erro.|
|429|Solicitações demais.|
|500|Ocorreu um erro. Se o erro persistir, relate-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou com o identificador de solicitação (X-RequestId).|
|503|Servidor temporariamente não disponível. Tente novamente a solicitação. Se o erro persistir, relate-o com o identificador de rastreamento do cliente (X-ClientTraceId) ou com o identificador de solicitação (X-RequestId).|
