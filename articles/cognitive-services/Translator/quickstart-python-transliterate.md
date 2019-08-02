---
title: 'Início Rápido: Transliterar texto, Python – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a transcrever texto (converter) de um script para outro usando Python e a API REST de Tradução de Texto. Neste exemplo, o japonês é transliterado para usar o alfabeto latino.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cafaf67cfaa07d27bf4569efbc7f76196222cc2a
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348739"
---
# <a name="quickstart-use-the-translator-text-api-to-transliterate-text-using-python"></a>Início Rápido: Usar a API de Tradução de Texto para transliterar texto usando Python

Neste início rápido, você aprenderá a transcrever texto (converter) de um script para outro usando Python e a API REST de Tradução de Texto. No exemplo fornecido, o japonês é transliterado para usar o alfabeto latino.

Este início rápido requer uma [Conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

>[!TIP]
> Caso queira ver todo o código de uma vez, o código-fonte deste exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* Uma chave de assinatura do Azure para a Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto usando seu editor ou IDE favorito ou crie uma nova pasta com um arquivo chamado `transliterate-text.py` na área de trabalho. Em seguida, copie este snippet de código no projeto/arquivo:

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
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

O ponto de extremidade global de Tradução de Texto é definido como o `base_url`. `path` define a rota `transliterate` e identifica que queremos usar a versão 3 da API.

Os `params` são usados para definir o idioma de entrada e os scripts de entrada e saída. Neste exemplo, estamos transliterando do japonês para o alfabeto latino.

>[!NOTE]
> Para saber mais sobre pontos de extremidade, rotas e parâmetros de solicitação, confira [API de Tradução de Texto 3.0: Transliterar](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'
params = '&language=ja&fromScript=jpan&toScript=latn'
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

## <a name="create-a-request-to-transliterate-text"></a>Criar uma solicitação para transliterar texto

Defina uma ou mais cadeias de caracteres que você deseja transliterar:

```python
# Transliterate "good afternoon" from source Japanese.
# Note: You can pass more than one object in body.
body = [{
    'text': 'こんにちは'
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
python transliterate-text.py
```

Se você quiser comparar seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python).

## <a name="sample-response"></a>Resposta de exemplo

```json
[
    {
        "script": "latn",
        "text": "konnichiwa"
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
* [Identificar a linguagem pela entrada](quickstart-python-detect.md)
* [Obter traduções alternativas](quickstart-python-dictionary.md)
* [Obter uma lista de idiomas com suporte](quickstart-python-languages.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-python-sentences.md)
