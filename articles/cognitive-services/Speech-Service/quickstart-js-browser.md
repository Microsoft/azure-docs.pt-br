---
title: 'Início Rápido: Reconhecimento de fala, JavaScript (navegador) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em JavaScript em um navegador usando o SDK de Fala
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: a2884b43268b4c067e6e739f67d2253f8c45a408
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603047"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em JavaScript em um navegador usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá como criar um site usando a associação JavaScript do SDK de Fala de Serviços Cognitivos para transcrever conversão de fala em texto.
O aplicativo baseia-se no SDK de Fala para JavaScript ([baixar versão 1.5.0](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura para o serviço de fala. Veja [Experimentar os Serviços de Fala gratuitamente](get-started.md).
* Um PC ou Mac, com um microfone funcionando.
* Um editor de texto.
* Uma versão atual do Chrome, Microsoft Edge ou Safari.
* Opcionalmente, um servidor Web que dá suporte à hospedagem de scripts PHP.

## <a name="create-a-new-website-folder"></a>Criar uma nova pasta Site

Crie uma nova pasta vazia. Caso deseje hospedar o exemplo em um servidor Web, certifique-se de que ele possa acessar a pasta.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Descompactar o SDK de Fala para JavaScript nessa pasta

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Baixe o SDK de Fala como um [pacote .zip](https://aka.ms/csspeech/jsbrowserpackage) e descompacte-o na pasta recém-criada. Isso resulta no desempacotamento de dois arquivos, `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
O último arquivo é opcional e é útil para depuração no código do SDK.

## <a name="create-an-indexhtml-page"></a>Criar uma página index.html

Crie um novo arquivo na pasta, chamada `index.html`, e abra este arquivo com um editor de texto.

1. Crie o esqueleto do HTML a seguir:

   ```html
   <html>
   <head>
      <title>Speech SDK JavaScript Quickstart</title>
   </head>
   <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
   </body>
   </html>
   ```

1. Adicione o seguinte código da interface do usuário ao seu arquivo, embaixo do primeiro comentário:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Adicionar uma referência ao SDK de Fala

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Conecte manipuladores para o botão de reconhecimento, o resultado de reconhecimento e os campos relacionados à assinatura definidos pelo código da interface do usuário:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Criar a origem do token (opcional)

Caso deseje hospedar a página da Web em um servidor Web, é possível fornecer opcionalmente uma origem do token para seu aplicativo de demonstração.
Dessa forma, sua chave de assinatura nunca sairá do seu servidor, permitindo que seus usuários usem funcionalidades de fala sem inserir nenhum código de autorização por si próprios.

1. Crie um arquivo chamado `token.php`. Neste exemplo, vamos supor seu servidor Web seja compatível com a linguagem de script PHP. Insira o seguinte código:

   [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Edite o arquivo `index.html` e adicione o seguinte código a ele:

   [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> Os tokens de autorização só têm um tempo de vida limitado.
> Este exemplo simplificado não mostra como atualizar os tokens de autorização automaticamente. Como um usuário, é possível recarregar manualmente a página ou pressionar F5 para atualizar.

## <a name="build-and-run-the-sample-locally"></a>Criar e executar o exemplo localmente

Para iniciar o aplicativo, clique duas vezes no arquivo index.html ou abra index.html com seu navegador da Web favorito. Ele apresentará uma GUI simples que permite a você inserir sua chave de assinatura e [região](regions.md) e disparar um reconhecimento usando o microfone.

> [!NOTE]
> Esse método não funciona no navegador do Safari.
> No Safari, a página da web de exemplo precisa ser hospedada em um servidor web. O Safari não permite que os sites carregados de um arquivo local usem o microfone.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Criar e executar o exemplo por meio de um servidor Web

Para iniciar seu aplicativo, abra seu navegador da Web favorito e aponte-o para a URL pública na qual a pasta está hospedada, insira sua [região](regions.md) e dispare um reconhecimento usando o microfone. Se configurado, ele adquirirá um token da origem do token.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de JavaScript no GitHub](https://aka.ms/csspeech/samples)
