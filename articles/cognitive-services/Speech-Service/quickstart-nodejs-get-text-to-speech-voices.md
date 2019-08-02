---
title: 'Início Rápido: Listar vozes de conversão de texto em fala, Node.js – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/um ponto de extremidade usando o Node.js. A lista é retornada como JSON, e a disponibilidade de voz varia por região.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d64f59b6b4439d79d64ee92cf23676ab1275c45a
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602994"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-nodejs"></a>Início Rápido: Obter a lista de vozes de conversão de texto em fala usando o Node.js

Neste início rápido, você aprenderá a obter a lista completa de vozes padrão e neurais para uma região/um ponto de extremidade usando o Node.js. A lista é retornada como JSON, e a disponibilidade de voz varia por região. Para obter uma lista das regiões com suporte, confira [regiões](regions.md).

Este início rápido exige uma [Conta de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso dos Serviços de Fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](get-started.md) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Node 8.12.x ou posterior](https://nodejs.org/en/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha uma gratuitamente!](get-started.md).

## <a name="create-a-project-and-require-dependencies"></a>Criar um projeto e exigir dependências

Crie um novo projeto Node.js usando seu editor ou IDE favorito. Em seguida, copie esse trecho de código para seu projeto em um arquivo denominado `get-voices.js`.

```javascript
// Requires request and request-promise for HTTP requests
// e.g. npm install request request-promise
const rp = require('request-promise');
// Requires fs to write the list of languages to a file
const fs = require('fs');
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `npm install request request-promise`.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Esta função troca sua chave de assinatura dos Serviços de Fala por um token de acesso usando o ponto de extremidade `issueToken`.

Esta amostra pressupõe que a sua assinatura dos Serviços de Fala esteja na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `uri`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código em seu projeto:

```javascript
// Gets an access token.
function getAccessToken(subscriptionKey) {
    let options = {
        method: 'POST',
        uri: 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken',
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey
        }
    }
    return rp(options);
}
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

Na próxima seção, criaremos a função para obter a lista de vozes e salvar a saída JSON no arquivo.

## <a name="make-a-request-and-save-the-response"></a>Faça uma solicitação e salve a resposta

Aqui você criará a solicitação e salvará a lista de vozes retornadas. Este exemplo supõe que você esteja usando o endpoint do West US. Se o seu recurso estiver registrado em uma região diferente, atualize o `uri`. Para obter mais informações, consulte [regiões dos Serviços de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, adicione cabeçalhos obrigatórios para a solicitação. Finalmente, você fará uma solicitação ao serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta será gravada no arquivo.

```javascript
function textToSpeech(accessToken) {
    let options = {
        method: 'GET',
        baseUrl: 'https://westus.tts.speech.microsoft.com/',
        url: 'cognitiveservices/voices/list',
        headers: {
            'Authorization': 'Bearer ' + accessToken,
            'Content-Type': 'application/json'
        }
    }

    let request = rp(options)
        .on('response', (response) => {
            if (response.statusCode === 200) {
                request.pipe(fs.createWriteStream('voices.json'));
                console.log('\nYour file is ready.\n')
            }
        });
    return request;
}
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

Você está quase lá. A última etapa é criar uma função assíncrona. Essa função lerá sua chave de assinatura de uma variável de ambiente, obterá um token, aguardará a conclusão da solicitação e, em seguida, gravará a resposta JSON no arquivo.

Caso não esteja familiarizado com as variáveis de ambiente ou prefira testar sua chave de assinatura embutida em código como uma cadeia de caracteres, substitua `process.env.SPEECH_SERVICE_KEY` pela chave de assinatura como uma cadeia de caracteres.

```javascript
// Use async and await to get the token before attempting
// to convert text to speech.
async function main() {
    // Reads subscription key from env variable.
    // You can replace this with a string containing your subscription key. If
    // you prefer not to read from an env variable.
    // e.g. const subscriptionKey = "your_key_here";
    const subscriptionKey = process.env.SPEECH_SERVICE_KEY;
    if (!subscriptionKey) {
        throw new Error('Environment variable for your subscription key is not set.')
    };
    try {
        const accessToken = await getAccessToken(subscriptionKey);
        await textToSpeech(accessToken);
    } catch (err) {
        console.log(`Something went wrong: ${err}`);
    }
}

main()
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até o diretório do projeto e execute:

```console
node get-voices.js
```

## <a name="clean-up-resources"></a>Limpar recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NodeJS)

## <a name="see-also"></a>Consulte também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar fontes de voz personalizada](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
