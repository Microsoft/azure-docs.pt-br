---
title: Início Rápido do Serviço do Azure SignalR sem servidor – C#
description: Um guia de início rápido de como usar o Serviço do Azure SignalR e o Azure Functions para criar uma sala de chat usando o C#.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/25/2020
ms.author: zhshang
ms.openlocfilehash: 1f28058a766144ada3326b3ee4ce09ca503d1896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873872"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-c"></a>Início Rápido: criar uma sala de chat com o Azure Functions e o Serviço do SignalR usando C\#

O Azure SignalR Service permite adicionar facilmente a funcionalidade em tempo real ao seu aplicativo. O Azure Functions é uma plataforma sem servidor que permite executar seu código sem gerenciar qualquer infraestrutura. Neste início rápido, saiba como usar o SignalR Service e o Functions para criar um aplicativo de chat em tempo real e sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tiver o Visual Studio 2019 instalado, baixe e use o **Visual Studio 2019 Community Edition** [gratuito](https://www.visualstudio.com/downloads). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

Você também pode executar este tutorial na linha de comando (macOS, Windows ou Linux) usando as [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing), o [SDK do .NET Core](https://dotnet.microsoft.com/download)e seu editor de código favorito.

Se você não tiver uma assinatura do Azure, [crie uma gratuitamente](https://azure.microsoft.com/free/dotnet) antes de começar.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar o aplicativo do Azure Functions

1. Inicie o Visual Studio (ou outro editor de códigos) e abra a solução na pasta *src/chat/csharp* do repositório clonado.

1. No navegador em que o portal do Azure é aberto, confirme se a instância do SignalR Service implantada anteriormente foi criada com êxito pesquisando pelo nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para exibir as cadeias de conexão para a instância do SignalR Service.

1. Selecione e copie a cadeia de conexão primária.

1. De volta ao Visual Studio, no **Gerenciador de Soluções**, renomeie *local.settings.sample.json* para *local.settings.json*.

1. Em *local.settings.json*, cole a cadeia de conexão no valor da configuração **AzureSignalRConnectionString**. Salve o arquivo.

1. Abra *Functions.cs*. Há duas funções de gatilho HTTP nesse aplicativo de funções:

    - **GetSignalRInfo** – usa a associação de entrada `SignalRConnectionInfo` para gerar e retornar informações de conexão válidas.
    - **SendMessage** – recebe uma mensagem de chat no corpo da solicitação e usa a associação de saída *SignalR* para difundir a mensagem a todos os aplicativos cliente conectados.

1. Use uma das opções a seguir para iniciar o aplicativo de Funções do Azure localmente.

    - **Visual Studio**: No menu *Depurar*, selecione *Iniciar depuração* para executar o aplicativo.

        ![Depurar o aplicativo](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **Linha de comando**: Execute o comando a seguir para iniciar o host da função.

        ```bash
        func start
        ```
Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qscsharp)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou e executou um aplicativo sem servidor em tempo real no Visual Studio. Em seguida, saiba mais sobre como desenvolver e implantar o Azure Functions com o Visual Studio.

> [!div class="nextstepaction"]
> [Desenvolver o Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md)

