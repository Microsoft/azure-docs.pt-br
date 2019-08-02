---
title: 'Início Rápido: Reconhecer fala, C++ (Windows) – Serviços da API de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C++ no Windows Desktop usando o SDK de Fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d63d1c96077642e660e2272cbd8c2ee1250b1471
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606486"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C++ no Windows usando o SDK de Fala

Guias de início rápido também estão disponíveis para [conversão de texto em fala](quickstart-text-to-speech-cpp-windows.md) e [tradução de fala](quickstart-translate-speech-cpp-windows.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo de console C++ para o Windows. Você usará o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos para transcrever a conversão de fala em texto em tempo real do microfone do seu computador. O aplicativo é criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos Serviços de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar os Serviços de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o arquivo de origem *helloworld.cpp*. Substitua todo o código abaixo da instrução include inicial (`#include "stdafx.h"` ou `#include "pch.h"`) pelo seguinte:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-cpp-windows-06-build.png)

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

   ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Uma janela do console aparece, solicitando que você diga alguma coisa. Fale uma frase ou expressão em inglês. Sua fala será transmitida para os Serviços de Fala e transcrita para texto, que aparecerá na mesma janela.

   ![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
