---
title: Suporte de idioma – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 26127f9f6ed718e33a77b986f2edb0d2dc81b2c1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563565"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para o LUIS

LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS com vários idiomas

Se você precisar de um aplicativo de cliente LUIS com vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, desenvolva um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisa fornecer o reconhecimento de idioma para um idioma ao qual o LUIS não oferece suporte, use a [API de Tradução da Microsoft](../Translator/translator-info-overview.md) para traduzir a declaração para um idioma com suporte, envie a declaração para o ponto de extremidade do LUIS e receba as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas compatíveis

O LUIS compreende declarações nos seguintes idiomas:

| Idioma |Localidade  |  Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | \**[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglês americano |`en-US` | ✔ | ✔  |✔|✔|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |-|  -   |-|✔|
| Francês (França) |`fr-FR` |-| ✔ |✔ |✔|
| Francês (Canadá) |`fr-CA` |-|   -   |-|✔|
| Alemão |`de-DE` |-| ✔ |✔ |✔|
| Italiano |`it-IT` |-| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|Somente frase principal|
| Coreano |`ko-KR` |-|   -   |-|Somente frase principal|
| Português (Brasil) |`pt-BR` |-| ✔ |✔ |nem todas as subculturas|
| Espanhol (Espanha) |`es-ES` |-| ✔ |✔|✔|
| Espanhol (México)|`es-MX` |-|  -   |✔|✔|
| Turco | `tr-TR` |-|-|-|Sentimento, somente|


O suporte aos idiomas varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md).

### <a name="chinese-support-notes"></a>*Notas de suporte ao chinês

 - Na cultura `zh-cn`, o LUIS espera o conjunto de caracteres de chinês simplificado em vez do conjunto de caracteres tradicional.
 - Os nomes de intenções, entidades, recursos e expressões regulares podem estar em caracteres chineses ou romanos.
 - Consulte a [referência de domínios pré-criados](luis-reference-prebuilt-domains.md) para obter informações sobre quais domínios pré-criados têm suporte na `zh-cn` cultura.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*Notas de suporte ao japonês

 - Como o LUIS não fornece análise sintática e não compreenderá a diferença entre Keigo e japonês informal, será necessário incorporar os diferentes níveis de formalidade como exemplos de treinamento para os seus aplicativos.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

### <a name="text-analytics-support-notes"></a>\*\*Notas de suporte a análises de texto
A análise de texto inclui análise de sentimento e entidade pré-compilada keyPhrase. Só há suporte para subculturas em português: `pt-PT` e `pt-BR`. Todas as outras culturas têm suporte no nível de cultura principal. Saiba mais sobre os [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) da Análise de Texto.

### <a name="speech-api-supported-languages"></a>Idiomas com suporte da API de Fala
Veja [Idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode) de Fala para idiomas no modo de ditado de Fala.

### <a name="bing-spell-check-supported-languages"></a>Idiomas com suporte para Verificação Ortográfica do Bing
Veja [idiomas com suporte](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages) da Verificação Ortográfica do Bing para obter uma lista dos idiomas com suporte e status.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras em um aplicativo
Na cultura`en-us`, o LUIS aprende a distinguir mais palavras em inglês, incluindo gírias. Na cultura `zh-cn`, o LUIS aprende distinguir a maioria dos caracteres chineses. Se você usar uma palavra rara em `en-us` ou caractere em `zh-cn`, e você verá que o LUIS não parece capaz de distinguir essa palavra ou caractere, você pode adicionar essa palavra ou caractere a um [recurso de lista de frases](luis-how-to-add-features.md). Por exemplo, as palavras fora da cultura do aplicativo, ou seja, as palavras estrangeiras, devem ser adicionadas a um recurso de lista de frases. Essa lista de frases deve ser marcada como não intercambiável, para indicar que o conjunto de palavras raras forma uma classe a qual o LUIS deve aprender a reconhecer, mas que não são sinônimos nem intercambiáveis.

### <a name="hybrid-languages"></a>Idiomas híbridos
Os idiomas híbridos combinam palavras de duas culturas, como inglês e chinês. Não há suporte para esses idiomas no LUIS, pois um aplicativo tem base em uma única cultura.

## <a name="tokenization"></a>Geração de tokens
Para executar o aprendizado de máquina, o LUIS divide uma declaração em [tokens](luis-glossary.md#token) com base na cultura.

|Idioma|  cada espaço ou caractere especial | nível do caractere|palavras compostas|[Entidade indexada retornada](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|Chinês||✔||✔|
|Holandês|||✔|✔|
|Inglês (en-us)|✔ ||||
|Francês (fr-FR)|✔||||
|Francês (fr-CA)|✔||||
|Alemão|||✔|✔|
|Italiano|✔||||
|Japonês||||✔|
|Coreano||✔||✔|
|Português (Brasil)|✔||||
|Espanhol (es-ES)|✔||||
|Espanhol (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>Versões personalizadas do criador

As seguintes culturas têm versões de criador personalizadas:

|Cultura|Versão|Finalidade|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Cria tokens palavras dividindo-as usando um criador baseado em Machine Learning que tenta dividir palavras compostas em seus componentes únicos.<br>Se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele será `Ich fahre einen kranken wagen`ativado. Permitir a marcação de `kranken` e `wagen` de forma independente como entidades diferentes.|
|Alemão<br>`de-de`|1.0.2|Cria tokens palavras dividindo-as em espaços.<br> se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele permanecerá como um único token. Portanto `krankenwagen` , é marcado como uma única entidade. |

### <a name="migrating-between-tokenizer-versions"></a>Migrando entre versões do criador
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

A geração de tokens ocorre no nível do aplicativo. Não há suporte para geração de tokens no nível de versão. 

[Importe o arquivo como um novo aplicativo](luis-how-to-start-new-app.md#import-an-app-from-file), em vez de uma versão. Essa ação significa que o novo aplicativo tem uma ID de aplicativo diferente, mas usa a versão criador especificada no arquivo. 