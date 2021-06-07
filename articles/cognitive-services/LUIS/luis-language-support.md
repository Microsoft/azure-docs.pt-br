---
title: Suporte de idioma – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 724b94a44ae289063bbb1c6ebf53b38c0a617b88
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799320"
---
# <a name="language-and-region-support-for-luis"></a>Suporte de idioma e região para o LUIS

LUIS tem uma variedade de recursos dentro do serviço. Nem todos os recursos estão na mesma paridade de idioma. Verifique se os recursos em que você está interessado têm suporte na cultura do idioma de destino. Um aplicativo LUIS é específico à cultura e não pode ser alterado após a definição.

## <a name="multi-language-luis-apps"></a>Aplicativos LUIS com vários idiomas

Se você precisar de um aplicativo de cliente LUIS com vários idiomas, como um chatbot, terá algumas opções. Se o LUIS der suporte a todos os idiomas, desenvolva um aplicativo LUIS para cada idioma. Cada aplicativo LUIS tem uma ID de aplicativo exclusiva e um log de ponto de extremidade. Se você precisar fornecer um entendimento de linguagem para um idioma LUIS não oferecer suporte, você pode usar o [serviço de Tradutor](../Translator/translator-info-overview.md) para converter o expressão em um idioma com suporte, enviar o expressão para o ponto de extremidade do Luis e receber as pontuações resultantes.

## <a name="languages-supported"></a>Idiomas compatíveis

O LUIS compreende declarações nos seguintes idiomas:

| Linguagem |Locale  |  Domínio predefinido | Entidade predefinida | Recomendações da lista de frases | **[Análise de texto](../text-analytics/language-support.md)<br>(Sentimento e<br>Palavras-chave)|
|--|--|:--:|:--:|:--:|:--:|
| Árabe (visualização-árabe moderno padrão) |`ar-AR`|-|-|-|-|
| *[Chinês](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| Holandês |`nl-NL` |✔|-|-|✔|
| Inglês (Estados Unidos) |`en-US` | ✔ | ✔  |✔|✔|
| Francês (Canadá) |`fr-CA` |-|-|-|✔|
| Francês (França) |`fr-FR` |✔| ✔ |✔ |✔|
| Alemão |`de-DE` |✔| ✔ |✔ |✔|
| Guzerate | `gu-IN`|-|-|-|-|
| Híndi | `hi-IN`|-|✔|-|-|
| Italiano |`it-IT` |✔| ✔ |✔|✔|
| *[Japonês](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|Somente frase principal|
| Coreano |`ko-KR` |✔|-|-|Somente frase principal|
| Marati | `mr-IN`|-|-|-|-|
| Português (Brasil) |`pt-BR` |✔| ✔ |✔ |nem todas as subculturas|
| Espanhol (México)|`es-MX` |-|✔|✔|✔|
| Espanhol (Espanha) |`es-ES` |✔| ✔ |✔|✔|
| Tâmil | `ta-IN`|-|-|-|-|
| Télugo | `te-IN`|-|-|-|-|
| Turco | `tr-TR` |✔|✔|-|Sentimento, somente|




O suporte aos idiomas varia para [entidades predefinidas](luis-reference-prebuilt-entities.md) e [domínios predefinidos](luis-reference-prebuilt-domains.md).

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*Notas de suporte ao japonês

 - Como o LUIS não fornece análise sintática e não compreenderá a diferença entre Keigo e japonês informal, será necessário incorporar os diferentes níveis de formalidade como exemplos de treinamento para os seus aplicativos.
     - でございます não é o mesmo que です.
     - です não é o mesmo que だ.

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Idiomas com suporte da API de Fala
Veja [Idiomas com suporte](../speech-service/speech-to-text.md) de Fala para idiomas no modo de ditado de Fala.

### <a name="bing-spell-check-supported-languages"></a>Idiomas com suporte para Verificação Ortográfica do Bing
Veja [idiomas com suporte](../bing-spell-check/language-support.md) da Verificação Ortográfica do Bing para obter uma lista dos idiomas com suporte e status.

## <a name="rare-or-foreign-words-in-an-application"></a>Palavras raras ou estrangeiras em um aplicativo
Na cultura`en-us`, o LUIS aprende a distinguir mais palavras em inglês, incluindo gírias. Na cultura `zh-cn`, o LUIS aprende distinguir a maioria dos caracteres chineses. Se você usar uma palavra rara em `en-us` ou caractere em `zh-cn`, e você verá que o LUIS não parece capaz de distinguir essa palavra ou caractere, você pode adicionar essa palavra ou caractere a um [recurso de lista de frases](luis-how-to-add-features.md). Por exemplo, as palavras fora da cultura do aplicativo, ou seja, as palavras estrangeiras, devem ser adicionadas a um recurso de lista de frases.

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>Idiomas híbridos
Os idiomas híbridos combinam palavras de duas culturas, como inglês e chinês. Não há suporte para esses idiomas no LUIS, pois um aplicativo tem base em uma única cultura.

## <a name="tokenization"></a>Geração de tokens
Para executar o aprendizado de máquina, o LUIS divide uma declaração em [tokens](luis-glossary.md#token) com base na cultura.

|Linguagem|  cada espaço ou caractere especial | nível do caractere|palavras compostas
|--|:--:|:--:|:--:|
|Árabe|✔|||
|Chinês||✔||
|Holandês|✔||✔|
|Inglês (en-us)|✔ |||
|Francês (fr-FR)|✔|||
|Francês (fr-CA)|✔|||
|Alemão|✔||✔|
|Guzerate|✔|||
|Híndi|✔|||
|Italiano|✔|||
|Japonês|||✔
|Coreano||✔||
|Marati|✔|||
|Português (Brasil)|✔|||
|Espanhol (es-ES)|✔|||
|Espanhol (es-MX)|✔|||
|Tâmil|✔|||
|Télugo|✔|||
|Turco|✔|||


### <a name="custom-tokenizer-versions"></a>Versões personalizadas do criador

As seguintes culturas têm versões de criador personalizadas:

|Cultura|Versão|Finalidade|
|--|--|--|
|Alemão<br>`de-de`|1.0.0|Cria tokens palavras dividindo-as usando um criador baseado em Machine Learning que tenta dividir palavras compostas em seus componentes únicos.<br>Se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele será ativado `Ich fahre einen kranken wagen` . Permitir a marcação de `kranken` e de `wagen` forma independente como entidades diferentes.|
|Alemão<br>`de-de`|1.0.2|Cria tokens palavras dividindo-as em espaços.<br> Se um usuário inserir `Ich fahre einen krankenwagen` como um expressão, ele permanecerá como um único token. Portanto, `krankenwagen` é marcado como uma única entidade. |
|Holandês<br>`nl-nl`|1.0.0|Cria tokens palavras dividindo-as usando um criador baseado em Machine Learning que tenta dividir palavras compostas em seus componentes únicos.<br>Se um usuário inserir `Ik ga naar de kleuterschool` como um expressão, ele será ativado `Ik ga naar de kleuter school` . Permitir a marcação de `kleuter` e de `school` forma independente como entidades diferentes.|
|Holandês<br>`nl-nl`|1.0.1|Cria tokens palavras dividindo-as em espaços.<br> Se um usuário inserir `Ik ga naar de kleuterschool` como um expressão, ele permanecerá como um único token. Portanto, `kleuterschool` é marcado como uma única entidade. |


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

[Importe o arquivo como um novo aplicativo](luis-how-to-start-new-app.md), em vez de uma versão. Essa ação significa que o novo aplicativo tem uma ID de aplicativo diferente, mas usa a versão criador especificada no arquivo.
