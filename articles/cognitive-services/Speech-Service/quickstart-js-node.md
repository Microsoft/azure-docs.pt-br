---
title: 'Início Rápido: Reconhecer fala, Node.js – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o SDK de Fala para Node.js. Quando terminar, você pode usar o microfone do computador para transcrever a conversão de fala em texto em tempo real.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605124"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Início Rápido: Reconhecer fala com o SDK de Fala para Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Este artigo mostra como criar um projeto do Node.js usando a associação JavaScript do SDK de Fala dos Serviços Cognitivos do Azure para transcrever fala em texto.
O aplicativo se baseia no [SDK de Fala para JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura do Azure para o serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Uma versão atual do [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Criar um novo projeto

Crie uma pasta e inicialize o projeto:

```sh
npm init -f
```

Esse comando inicializa os arquivos **package.json** com valores padrão. Você provavelmente desejará editar esse arquivo mais tarde.

## <a name="install-the-speech-sdk"></a>Instalar o SDK de Fala

Adicione o SDK de Fala ao projeto do Node.js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Esse comando baixa e instala a versão mais recente do SDK de Fala e quaisquer pré-requisitos necessários do **npmjs**. O SDK é instalado no diretório `node_modules` dentro da pasta do projeto.

## <a name="use-the-speech-sdk"></a>Usar o SDK de Fala

Crie um novo arquivo na pasta, chamada `index.js`, e abra esse arquivo com um editor de texto.

> [!NOTE]
> No Node.js, o SDK de Fala não dá suporte ao tipo de dados de **Arquivo** ou ao microfone. Ambos têm suporte apenas em navegadores. Em vez disso, use a interface do **Stream** para o SDK de Fala por meio de `AudioInputStream.createPushStream()` ou de `AudioInputStream.createPullStream()`.

Neste exemplo, usamos a interface `PushAudioInputStream`.

Adicione esse código JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Execute o exemplo

Para abrir o aplicativo, adapte `YourSubscriptionKey`, `YourServiceRegion` e `YourAudioFile.wav` à sua configuração. Em seguida, execute-o chamando este comando:

```sh
node index.js
```

Ele dispara um reconhecimento usando o nome de arquivo fornecido. E ele apresenta a saída no console.

Este exemplo é a saída quando você executa `index.js` após atualizar a chave de assinatura e usar o arquivo `whatstheweatherlike.wav`:

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de Fala com o Visual Studio Code

Também é possível executar o exemplo do Visual Studio Code. Siga estas etapas para instalar, abrir e executar o início rápido:

1. Inicie o Visual Studio Code. Selecione **Abrir Pasta**. Em seguida, navegue até a pasta de início rápido.

   ![Abrir pasta](media/sdk/qs-js-node-01-open_project.png)

1. Abra um terminal no Visual Studio Code.

   ![Uma janela do terminal](media/sdk/qs-js-node-02_open_terminal.png)

1. Execute `npm` para instalar as dependências.

   ![npm install](media/sdk/qs-js-node-03-npm_install.png)

1. Agora você está pronto para abrir `index.js`e definir um ponto de interrupção.

   ![index.js com um ponto de interrupção na linha 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Para iniciar a depuração, selecione F5 ou **Depurar/Iniciar depuração** no menu.

   ![O menu de depuração](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando um ponto de interrupção é atingido, é possível inspecionar a pilha de chamadas e variáveis.

   ![Depurador](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. Qualquer saída é mostrada na janela do console de depuração.

   ![Console de depuração](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Node.js no GitHub](https://aka.ms/csspeech/samples)
