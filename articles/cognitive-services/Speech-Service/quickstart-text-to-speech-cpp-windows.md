---
title: 'Início Rápido: Sintetizar fala, C++ (Windows) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar fala em C++ no Windows Desktop usando o SDK de Fala
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: dfad425d6da2e3dabec138ef4f108957ebea7ee9
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602908"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início Rápido: Sintetizar fala em C++ no Windows usando o SDK de Fala

Guias de início rápido também estão disponíveis para [reconhecimento de fala](quickstart-cpp-windows.md) e [tradução de fala](quickstart-translate-speech-cpp-windows.md).

Neste artigo, você criará um aplicativo de console C++ para o Windows. Você usa os serviços Cognitivos do [SDK de Fala](speech-sdk.md) para sintetizar fala do texto em tempo real e reproduzir a fala no alto-falante do seu PC. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

O recurso descrito neste artigo está disponível no [SDK de Fala 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Para obter uma lista completa dos idiomas/vozes disponíveis para síntese de fala, confira [suporte ao idioma](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos Serviços de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar os Serviços de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução include inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Uma janela do console aparece, solicitando que você digite algum texto. Digite uma frase ou algumas palavras. O texto que você digitou é transmitido para os Serviços de Fala e sintetizado em fala, que é reproduzido no alto-falante.

   ![Captura de tela da saída do console após a síntese bem-sucedida](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, como salvar a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar fontes de voz](how-to-customize-voice-font.md)
- [Amostras de memorando de voz](record-custom-voice-samples.md)
