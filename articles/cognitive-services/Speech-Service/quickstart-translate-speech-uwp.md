---
title: 'Início Rápido: Traduzir fala, C# (UWP)-serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você criará um aplicativo simples do UWP (Plataforma Universal do Windows) para capturar a fala do usuário, traduzi-la para outro idioma e produzir o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.openlocfilehash: 71f8ecfaa11538cf43284d2aafff61cbf9655c0e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607774"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Início Rápido: Tradução de fala com o Speech SDK para C# (UWP)

Guias de início rápido também estão disponíveis para a [tradução de fala em fala](quickstart-csharp-uwp.md), [texto em fala](quickstart-text-to-speech-csharp-uwp.md) e o [assistente virtual que tem como prioridade o uso de voz](quickstart-virtual-assistant-csharp-uwp.md).

Neste início rápido, você criará um aplicativo UPW (Plataforma Universal do Windows) simples que captura a fala do usuário do microfone do seu computador, converte a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado em Windows de 64 bits e é criado com o [pacote NuGet do SDK de fala](https://aka.ms/csspeech/nuget) e Microsoft Visual Studio 2017 ou posterior.

Para obter uma lista completa dos idiomas disponíveis para tradução de fala, confira [suporte ao idioma](language-support.md).

> [!NOTE]
> A UWP permite desenvolver aplicativos que executam em qualquer dispositivo com suporte para Windows 10, incluindo PCs, Xbox, Surface Hub e outros dispositivos.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) ou posterior
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. A interface do usuário do aplicativo é definida usando XAML. Abra `MainPage.xaml` no Gerenciador de Soluções. Na exibição XAML do designer, insira o seguinte snippet XAML entre `<Grid>` e `</Grid>`.

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Abra o arquivo de origem code-behind `MainPage.xaml.cs` (encontre-o agrupado em `MainPage.xaml`). Substitua todo o código nesse arquivo pelo código a seguir.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. No manipulador `SpeechTranslationFromMicrophone_ButtonClicked` desse arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. No manipulador `SpeechTranslationFromMicrophone_ButtonClicked`, substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve todas as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, selecione **Build** > **Build Solution**. Agora, o código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-uwp-08-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, selecione **Depurar**  > **Inicie a depuração**, ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-uwp-09-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela é exibida. Selecione **Habilitar Microfone** e reconheça a solicitação de permissão exibida.

    ![Captura de tela da solicitação de permissão](media/sdk/qs-csharp-uwp-10-access-prompt.png "Iniciar o aplicativo na depuração")

1. Selecione **Reconhecimento de fala com a entrada do microfone** e fale uma frase ou uma oração em inglês no microfone do dispositivo. Sua fala será transmitida para o serviço de Fala e transcrita para texto, que será exibida na janela.

    ![Captura de tela da interface do usuário do reconhecimento de fala](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
