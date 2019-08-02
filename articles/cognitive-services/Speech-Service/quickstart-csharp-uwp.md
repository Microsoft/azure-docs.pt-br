---
title: 'Início Rápido: Reconhecer fala, C# (UWP) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você criará um aplicativo UWP (Plataforma Universal do Windows) em C# usando o SDK de Fala dos Serviços Cognitivos. Você transcreverá a fala em texto em tempo real por meio do microfone do dispositivo. O aplicativo é criado com o pacote NuGet do SDK de Fala e o Microsoft Visual Studio 2017.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/23/2019
ms.author: lisaweixu
ms.custom: seodec18
ms.openlocfilehash: 25b3474e33351d6365af37d78f442768aba88625
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405918"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer a fala em um aplicativo UWP usando o SDK de Fala

Guias de início rápido também estão disponíveis para a [tradução de texto em fala](quickstart-text-to-speech-csharp-uwp.md), [tradução de fala](quickstart-translate-speech-uwp.md) e [assistente virtual com prioridade do uso de voz](quickstart-virtual-assistant-csharp-uwp.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você desenvolverá um aplicativo UWP (Plataforma Universal do Windows; Windows versão 1709 ou posterior) em C# usando o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos. O programa transcreverá a fala em texto em tempo real por meio do microfone do dispositivo. O aplicativo foi criado com o [Pacote NuGet do SDK de Fala](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017 (qualquer edição).

> [!NOTE]
> A Plataforma Universal do Windows permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. A interface do usuário do aplicativo é definida usando XAML. Abra `MainPage.xaml` no Gerenciador de Soluções. Na exibição XAML do designer, insira o snippet de código XAML a seguir na marca da Grade (entre `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o arquivo de origem code-behind `MainPage.xaml.cs` (encontre-o agrupado em `MainPage.xaml`). Substitua todo o código nesse arquivo pelo código a seguir.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked` desse arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. No manipulador `SpeechRecognitionFromMicrophone_ButtonClicked`, substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela é exibida. Selecione **Habilitar Microfone** e reconheça a solicitação de permissão exibida.

    ![Captura de tela da solicitação de permissão](media/sdk/qs-csharp-uwp-10-access-prompt.png "Iniciar o aplicativo na depuração")

1. Selecione **Reconhecimento de fala com a entrada do microfone** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o Serviço de Fala e transcrita para texto, que será exibida na janela.

    ![Captura de tela da interface do usuário do reconhecimento de fala](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Traduzir discurso](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
