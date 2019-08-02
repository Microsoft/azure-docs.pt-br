---
title: 'Início Rápido: Sintetizar fala, C# (.NET Core) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em C# no .NET Core no Windows usando o SDK de Fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 7b4a018e38ca625e38dc1658a95d3ce0e677f711
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67467190"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-core"></a>Início Rápido: Sintetização de fala com o SDK de Fala para .NET Core

Guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-csharp-dotnetcore-windows.md) e [tradução de fala](quickstart-translate-speech-dotnetcore-windows.md).

Neste artigo, você cria um aplicativo de console em C# para o .NET Core no Windows usando o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos. Você pode sintetizar em tempo real uma fala a partir de texto no alto-falante do seu PC. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> O .NET Core é uma plataforma .NET multiplataforma de software livre que implementa a especificação [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Você precisa de uma chave de assinatura dos Serviços de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar os Serviços de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK do .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você digite algum texto. Digite uma frase ou algumas palavras. O texto que você digitou é transmitido para os Serviços de Fala e sintetizado em fala, que é reproduzido no alto-falante.

    ![Captura de tela da saída do console após sintetização bem-sucedida](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Captura de tela da saída do console após sintetização bem-sucedida")

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, como sintetizar a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Amostras de memorando de voz](record-custom-voice-samples.md)
