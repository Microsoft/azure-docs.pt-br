---
title: Início rápido da biblioteca de clientes JavaScript da Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 1b0fa12aa00165ad4ddd02d36383b41bdf74f4d8
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386540"
---
A biblioteca de clientes da Pesquisa na Web do Bing facilita a integração da Pesquisa na Web do Bing ao seu aplicativo Node.js. Neste início rápido, você aprenderá a criar uma instância de um cliente, enviar uma solicitação e imprimir a resposta.

Deseja ver o código agora mesmo? As amostras para as [bibliotecas de clientes da Pesquisa do Bing para JavaScript](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/tree/master/Samples) estão disponíveis no GitHub.

## <a name="prerequisites"></a>Prerequisites
Aqui estão alguns itens de que você poderá precisar antes de executar este início rápido:

* [Node.js 6](https://nodejs.org/en/download/) ou posterior
* Uma chave de assinatura  

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]


## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Vamos começar configurando o ambiente de desenvolvimento para nosso projeto em Node.js.

1. Crie um novo diretório para seu projeto:

    ```console
    mkdir YOUR_PROJECT
    ```

1. Criar um novo arquivo de pacote:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

1. Agora, vamos instalar alguns módulos do Azure e adicioná-los ao `package.json`:

    ```console
    npm install --save @azure/cognitiveservices-websearch
    npm install --save @azure/ms-rest-azure-js
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Criar um projeto e declarar os módulos necessários

No mesmo diretório que seu `package.json`, crie um novo projeto do Node.js usando seu IDE ou editor favorito. Por exemplo: `sample.js`.

Em seguida, copie esse código para seu projeto. Ele carrega os módulos instalados na seção anterior.

```javascript
const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
const WebSearchAPIClient = require('@azure/cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Instancie o cliente

Esse código cria uma instância de um cliente e usando o módulo `@azure/cognitiveservices-websearch`. Insira uma chave de assinatura válida para sua conta do Azure antes de continuar.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Fazer uma solicitação e imprimir os resultados

Use o cliente para enviar uma consulta de pesquisa para Pesquisa na Web do Bing. Se a resposta incluir os resultados para qualquer um dos itens na matriz `properties`, o `result.value` será impresso no console.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Execute o programa

A etapa final é executar o programa!

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você terminar com este projeto, remova sua chave de assinatura do código do programa.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Exemplos de SDK do Node.js de Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>Confira também

* [Referência do SDK do Node do Azure](/javascript/api/@azure/cognitiveservices-websearch/)