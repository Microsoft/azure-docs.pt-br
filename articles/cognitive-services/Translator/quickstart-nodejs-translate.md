---
title: 'Início Rápido: Traduzir texto, Node.js – API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você traduzirá o texto de um idioma para outro usando a API de Tradução de Texto com Node.js.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 06a58bc3d9b6383dd1c4cb6c33c8b24d62d5c851
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705502"
---
# <a name="quickstart-use-the-translator-text-api-to-translate-a-string-with-nodejs"></a>Início Rápido: Usar a API de Tradução de Texto para converter uma cadeia de caracteres com Node.js

Neste início rápido, você aprenderá a converter uma cadeia de texto de inglês para italiano e alemão usando Node.js e a API REST de Tradução de Texto.

Este início rápido requer uma [Conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

>[!TIP]
> Caso queira ver todo o código de uma vez, o código-fonte deste exemplo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* Uma chave de assinatura do Azure para a Tradução de Texto

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto Python usando o editor ou IDE favorito ou crie uma nova pasta com um arquivo chamado `translate-text.js` na área de trabalho. Em seguida, copie este snippet de código no projeto/arquivo:

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `npm install request uuidv4`.

Esses módulos são necessários para construir a solicitação HTTP e criar um identificador exclusivo para o cabeçalho `'X-ClientTraceId'`.

## <a name="set-the-subscription-key"></a>Definir a chave de assinatura

Esse código tentará ler a chave da sua assinatura de Tradução de Texto da variável de ambiente `TRANSLATOR_TEXT_KEY`. Se você não estiver familiarizado com as variáveis de ambiente,poderá definir `subscriptionKey` como uma cadeia de caracteres e comentar a instrução condicional.

Copie este código em seu projeto:

```javascript
/* Checks to see if the subscription key is available
as an environment variable. If you are setting your subscription key as a
string, then comment these lines out.

If you want to set your subscription key as a string, replace the value for
the Ocp-Apim-Subscription-Key header as a string. */
const subscriptionKey = process.env.TRANSLATOR_TEXT_KEY;
if (!subscriptionKey) {
  throw new Error('Environment variable for your subscription key is not set.')
};
```

## <a name="configure-the-request"></a>Configurar a solicitação

O método `request()`, disponibilizado por meio do módulo de solicitação, nos permite passar o método HTTP, a URL, os parâmetros de solicitação, os cabeçalhos e o JSON do corpo como um objeto `options`. Neste trecho de código, configuraremos a solicitação:

>[!NOTE]
> Para saber mais sobre pontos de extremidade, rotas e parâmetros de solicitação, confira [API de Tradução de Texto 3.0: Traduzir](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate).

```javascript
let options = {
    method: 'POST',
    baseUrl: 'https://api.cognitive.microsofttranslator.com/',
    url: 'translate',
    qs: {
      'api-version': '3.0',
      'to': ['de', 'it']
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Hello World!'
    }],
    json: true,
};
```

A maneira mais fácil de autenticar uma solicitação é transmitir sua chave de assinatura como um cabeçalho `Ocp-Apim-Subscription-Key`, que é o que usamos neste exemplo. Como alternativa, você pode trocar sua chave de assinatura por um token de acesso e passar o token de acesso como um cabeçalho `Authorization` para validar sua solicitação.

Se estiver usando uma assinatura de vários serviço cognitivos, você também deve incluir o `Ocp-Apim-Subscription-Region` em seus cabeçalhos de solicitação.

Para obter mais informações, consulte [Autenticação](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="make-the-request-and-print-the-response"></a>Fazer a solicitação e imprimir a resposta

Em seguida, vamos criar a solicitação usando o método `request()`. Ele usa o objeto `options` que criamos na seção anterior como o primeiro argumento e, em seguida, imprime a resposta JSON "embelezada".

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> Neste exemplo, estamos definindo a solicitação HTTP no objeto `options`. No entanto, o módulo de solicitação também dá suporte a métodos de conveniência, como `.post` e `.get`. Para obter mais informações, confira [Métodos de conveniência](https://github.com/request/request#convenience-methods).

## <a name="put-it-all-together"></a>Colocar tudo isso junto

É isso; você montou um programa simples que chamará a API de Tradução de Texto e retornará uma resposta JSON. Agora é hora de executar o programa:

```console
node translate-text.js
```

Se você quiser comparar seu código com o nosso, o exemplo completo está disponível no [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-NodeJS).

## <a name="sample-response"></a>Resposta de exemplo

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
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

Além da detecção de idioma, saiba como usar a API de Tradução de Texto para:

* [Transliteração de texto](quickstart-nodejs-transliterate.md)
* [Identificar a linguagem pela entrada](quickstart-nodejs-detect.md)
* [Obter traduções alternativas](quickstart-nodejs-dictionary.md)
* [Obter uma lista de idiomas com suporte](quickstart-nodejs-languages.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-nodejs-sentences.md)
