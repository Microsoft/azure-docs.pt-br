---
title: 'Início Rápido: Sintetizar fala, .NET Framework (Windows) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode sintetizar fala do texto e ouvir a fala no seu alto-falante em tempo real.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 352d3f5f34bd46d8ee1ba2ed45dcc28af2b430ef
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605025"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Início Rápido: Sintetizar fala com o SDK de Fala para .NET Framework (Windows)

Guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-dotnet-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetframework-windows.md).

Use este guia para criar um aplicativo de console de conversão de fala em texto usando o .NET Framework para Windows e o SDK de Fala. Quando terminar, você pode sintetizar fala do texto e ouvir a fala no seu alto-falante em tempo real.

Para ver uma demonstração rápida (sem compilar o projeto do Visual Studio você mesmo, conforme mostrado abaixo):

Obter a versão mais recente dos [Exemplos do SDK de Fala dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-speech-sdk) do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este projeto, você precisará de:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Um alto-falante (ou um fone de ouvido) disponíveis.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua o código gerado automaticamente por este exemplo:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Localize e substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura do Serviço de Fala.

1. Localize e substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura. Por exemplo, se você estiver usando a versão de avaliação gratuita, a região é `westus`.

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnet-windows-08-build.png "Build bem-sucedido")

1. Na barra de menus, selecione **Depurar** > **Inicie a depuração**, ou pressione **F5** para iniciar o aplicativo.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console será exibida, solicitando que você digite texto. Digite uma frase ou algumas palavras. O texto que você digitou é transmitido para os Serviços de Fala e sintetizado em fala, que é reproduzido no alto-falante.

    ![Captura de tela da saída do console após o reconhecimento bem-sucedido](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Saída do console após o reconhecimento bem-sucedido")

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Amostras de memorando de voz](record-custom-voice-samples.md)
