---
title: 'Início Rápido: Analisar uma imagem remota – REST, Node.js'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você analisará uma imagem usando a API da Pesquisa Visual Computacional com Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7659196463d8b1f7affdb4019045116d94a06f97
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605897"
---
# <a name="quickstart-analyze-a-remote-image-using-the-computer-vision-rest-api-with-nodejs"></a>Início Rápido: Analisar uma imagem remota usando a API REST da Pesquisa Visual Computacional com o Node.js

Neste início rápido, você analisará uma imagem armazenada remotamente para extrair recursos visuais usando a API REST de Pesquisa Visual Computacional. Com o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa), é possível extrair recursos visuais com base no conteúdo de imagem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- É necessário ter o [Node.js](https://nodejs.org) 4.x ou posterior instalado.
- É necessário ter o [npm](https://www.npmjs.com/) instalado.
- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar o exemplo, siga estas etapas:

1. Instale o pacote npm [`request`](https://www.npmjs.com/package/request).
   1. Abra uma janela de prompt de comando como administrador.
   1. Execute o comando a seguir:

      ```console
      npm install request
      ```

   1. Depois que o pacote for instalado com êxito, feche a janela do prompt de comando.

1. Copie o código a seguir em um editor de texto.
1. Faça as alterações a seguir no código quando necessário:
    1. Substitua o valor de `subscriptionKey` pela sua chave de assinatura.
    1. Substitua o valor de `uriBase` pela URL do ponto de extremidade para o método [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) da região do Azure em que você adquiriu suas chaves de assinatura, se necessário.
    1. Outra opção é substituir o valor de `imageUrl` pela URL de uma imagem diferente que você deseja analisar.
    1. Opcionalmente, substitua o valor do parâmetro de solicitação `language` por uma linguagem diferente.
1. Salve o código como um arquivo com uma extensão `.js`. Por exemplo, `analyze-image.js`.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `node` para executar o arquivo. Por exemplo, `node analyze-image.js`.

```javascript
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase =
    'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg';

// Request parameters.
const params = {
    'visualFeatures': 'Categories,Description,Color',
    'details': '',
    'language': 'en'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta com êxito é retornada em JSON. O exemplo analisa e exibe uma resposta bem-sucedida na janela do prompt de comando, semelhante ao exemplo a seguir:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Grey",
    "dominantColorBackground": "Green",
    "dominantColors": [
      "Grey",
      "Green"
    ],
    "accentColor": "4D5E2F",
    "isBwImg": false
  },
  "requestId": "81b4e400-e3c1-41f1-9020-e6871ad9f0ed",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o arquivo e, em seguida, desinstale o pacote `request` npm. Para desinstalar o pacote, siga estas etapas:

1. Abra uma janela de prompt de comando como administrador.
2. Execute o comando a seguir:

   ```console
   npm uninstall request
   ```

3. Depois que o pacote for desinstalado com êxito, feche a janela do prompt de comando.

## <a name="next-steps"></a>Próximas etapas

Explore as APIs de Pesquisa Visual Computacional usadas para analisar uma imagem, detectar celebridades e marcos, criar uma miniatura e extrair textos manuscritos e impressos. Para testar rapidamente a API da Pesquisa Visual Computacional, experimente o [Abrir o console de teste de API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Explorar a API da Pesquisa Visual Computacional](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
