---
title: 'Início Rápido: Obter lista de idiomas compatíveis, Python – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você obterá uma lista dos idiomas compatíveis com tradução, transliteração e pesquisa em dicionário, além de exemplos que usam a API de Tradução de Texto com Python.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 9ff7dbd86efe56472b7e31b34310bcfdbbf95109
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705454"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>Início Rápido: Usar a API de Tradução de Texto para obter uma lista dos idiomas com suporte usando o Python

Neste início rápido, você aprenderá a fazer uma solicitação GET que retorna uma lista dos idiomas com suporte usando o Python e a API REST de Tradução de Texto.

>[!TIP]
> Caso queira ver todo o código de uma vez, o código-fonte deste exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto Python usando seu IDE ou editor favorito. Em seguida, copie esse trecho de código para seu projeto em um arquivo denominado `get-languages.py`.

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

## <a name="set-the-base-url-and-path"></a>Definir a URL base e o caminho

O ponto de extremidade global de Tradução de Texto é definido como o `base_url`. `path` define a rota `languages` e identifica que queremos usar a versão 3 da API.

>[!NOTE]
> Para saber mais sobre pontos de extremidade, rotas e parâmetros de solicitação, confira [API de Tradução de Texto 3.0: idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>Adicionar cabeçalhos

A solicitação para obter linguagens com suporte não requer autenticação. Defina o `Content-type` como `application/json` e adicione `X-ClientTraceId` para identificar sua solicitação com exclusividade.

Copie este trecho de código no seu projeto:

```python
headers = {
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus parâmetros de solicitação. [Saiba mais sobre a autenticação com a assinatura de vários serviços](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>Criar uma solicitação para obter uma lista dos idiomas com suporte

Vamos criar uma solicitação GET usando o módulo `requests`. Ele usa dois argumentos: a URL concatenada e os cabeçalhos de solicitação:

```python
request = requests.get(constructed_url, headers=headers)
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
python get-languages.py
```

Se você quiser comparar seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de exemplo

Localize a abreviação do país/região nesta [lista de idiomas](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

Este exemplo foi truncado para mostrar um trecho do resultado:

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
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
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
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
* [Obter traduções alternativas](quickstart-python-dictionary.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-python-sentences.md)
