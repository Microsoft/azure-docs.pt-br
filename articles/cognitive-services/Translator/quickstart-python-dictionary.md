---
title: 'Início Rápido: Procure palavras com o dicionário bilíngue, Python – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a localizar traduções alternativas e exemplos de uso de um texto especificado usando o Python e a API REST de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c95644dd0573bc7ad6ca78c5454288c811dd5d47
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704393"
---
# <a name="quickstart-look-up-words-with-bilingual-dictionary-using-python"></a>Início Rápido: Procure palavras com o dicionário bilíngue Python

Neste início rápido, você aprenderá a localizar traduções alternativas e exemplos de uso de um texto especificado usando o Python e a API REST de Tradução de Texto.

Este início rápido requer uma [Conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

>[!TIP]
> Caso queira ver todo o código de uma vez, o código-fonte deste exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* Uma chave de assinatura do Azure para a Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto Python usando o IDE ou editor favorito ou crie uma nova pasta na área de trabalho. Copie esse snippet de código no projeto/pasta em um arquivo nomeado `dictionary-lookup.py`.

```python
# -*- coding: utf-8 -*-
import os
import requests
import uuid
import json
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `pip install requests uuid`.

O primeiro comentário manda o interpretador de Python usar a codificação UTF-8. Em seguida, os módulos necessários são importados para ler a chave de assinatura de uma variável de ambiente, construir a solicitação HTTP, criar um identificador exclusivo e manipular a resposta JSON retornada pela API de Tradução de Texto.

## <a name="set-the-subscription-key-base-url-and-path"></a>Definir a chave de assinatura, a URL base e o caminho

Este exemplo tentará ler a chave da sua assinatura de Tradução de Texto da variável de ambiente `TRANSLATOR_TEXT_KEY`. Se você não estiver familiarizado com as variáveis de ambiente,poderá definir `subscriptionKey` como uma cadeia de caracteres e comentar a instrução condicional.

Copie este código em seu projeto:

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key. Then, be sure to delete your "os" import.
# subscriptionKey = 'put_your_key_here'
```

O ponto de extremidade global de Tradução de Texto é definido como o `base_url`. `path` define a rota `dictionary/lookup` e identifica que queremos usar a versão 3 da API.

Os `params` são usados para definir os idiomas de entrada e saída. Neste exemplo, estamos usando inglês e espanhol: `en` e `es`.

>[!NOTE]
> Para saber mais sobre pontos de extremidade, rotas e parâmetros de solicitação, confira [API de Tradução de Texto 3.0: Pesquisa no Dicionário](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/dictionary/lookup?api-version=3.0'
params = '&from=en&to=es'
constructed_url = base_url + path + params
```

## <a name="add-headers"></a>Adicionar cabeçalhos

A maneira mais fácil de autenticar uma solicitação é transmitir sua chave de assinatura como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que usamos neste exemplo. Como alternativa, você pode trocar sua chave de assinatura por um token de acesso e passar o token de acesso como um cabeçalho `Authorization` para validar sua solicitação. Para obter mais informações, consulte [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

Copie este trecho de código no seu projeto:

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre a autenticação com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-find-alternate-translations"></a>Criar uma solicitação para localizar traduções alternativas

Defina a cadeia ou as cadeias de caracteres para a qual você deseja encontrar traduções:

```python
# You can pass more than one object in body.
body = [{
    'text': 'Elephants'
}]
```

Em seguida, vamos criar uma solicitação POST usando o módulo `requests`. Ela usa três argumentos: a URL concatenada, os cabeçalhos de solicitação e o corpo da solicitação:

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>Imprima a resposta

A última etapa é imprimir os resultados. Este trecho de código embeleza os resultados classificando as chaves, definindo o recuo e declarando os separadores de item e chave.

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

É isso; você montou um programa simples que chamará a API de Tradução de Texto e retornará uma resposta JSON. Agora é hora de executar o programa:

```console
python alt-translations.py
```

Se você quiser comparar seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de exemplo

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você embutiu sua chave de assinatura no programa, remova-a quando tiver terminado este início rápido.

## <a name="next-steps"></a>Próximas etapas

Confira a referência da API para saber tudo o que você pode fazer com a API de Tradução de Texto.

> [!div class="nextstepaction"]
> [Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

## <a name="see-also"></a>Consulte também

Saiba como usar a API de Tradução de Texto para:

* [Traduzir o texto](quickstart-python-translate.md)
* [Transliteração de texto](quickstart-python-transliterate.md)
* [Identificar a linguagem pela entrada](quickstart-python-detect.md)
* [Obter uma lista de idiomas com suporte](quickstart-python-languages.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-python-sentences.md)
