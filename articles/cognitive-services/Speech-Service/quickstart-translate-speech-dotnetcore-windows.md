---
title: 'Início Rápido: Tradução de fala, C# (.NET Core Windows)'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você criará um aplicativo simples do .NET Core para capturar a fala do usuário, traduzi-la para outro idioma e produzir o texto para a linha de comando. Este guia foi projetado para usuários do Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8ab76a786f11677f5d8f0715b257bd2fb04e50bf
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602805"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-core"></a>Início Rápido: Tradução de fala com o Speech SDK para .NET Core

Guias de início rápido também estão disponíveis para [conversão de fala em texto](quickstart-csharp-dotnet-windows.md) e [conversão de texto em fala](quickstart-text-to-speech-dotnetcore.md).

Neste início rápido, você criará um aplicativo .NET Core simples que captura a fala do usuário do microfone do seu computador, converte a fala e transcreve o texto traduzido para a linha de comando em tempo real. Este aplicativo foi projetado para ser executado no Windows de 64 bits e é criado com o [pacote NuGet do Speech SDK](https://aka.ms/csspeech/nuget) e o Microsoft Visual Studio 2017.

Para obter uma lista completa dos idiomas disponíveis para tradução de fala, confira [suporte ao idioma](language-support.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* [SDK do .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `Program.cs` e substitua todo o código nesse arquivo pelo seguinte.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-dotnetcore/helloworld/Program.cs#code)]

1. No mesmo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` por sua chave de assinatura.

1. Substitua também a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Construa o aplicativo. Na barra de menus, escolha **Compilar** > **Compilar Solução**. O código deve compilar sem erros.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Compilar Solução realçada](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Build bem-sucedido")

1. Inicie o aplicativo. Na barra de menus, escolha **Depurar**  > **Iniciar Depuração** ou pressione **F5**.

    ![Captura de tela do aplicativo do Visual Studio, com a opção Iniciar Depuração realçada](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Iniciar o aplicativo na depuração")

1. Uma janela do console aparece, solicitando que você diga alguma coisa. Fale uma frase ou expressão em inglês. Sua fala será transmitida para o serviço de Fala, traduzida e transcrita para texto, que aparecerá na mesma janela.

    ![Captura de tela da saída do console após tradução bem-sucedida](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "Captura de tela da saída do console após tradução bem-sucedida")


## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler fala de um arquivo de áudio e produzir texto traduzido como fala sintetizada, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de C# no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
