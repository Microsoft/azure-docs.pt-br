---
title: Usar o JavaScript para criar uma sala de chat com o Azure Functions e o Serviço do SignalR
description: Um guia de início rápido de como usar o Serviço do Azure SignalR e o Azure Functions para criar uma sala de chat usando o JavaScript.
author: sffamily
ms.author: zhshang
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 5e2c9dcd1efc85e1ea3fe6381cbfbff0c5e62fc5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533216"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Início Rápido: Usar o JavaScript para criar uma sala de chat com o Azure Functions e o Serviço do SignalR

O Serviço do Azure SignalR permite que você adicione funcionalidades em tempo real ao seu aplicativo com facilidade, e o Azure Functions é uma plataforma sem servidor que permite que você execute o código sem gerenciar nenhuma infraestrutura. Neste início rápido, você usará o JavaScript para criar um aplicativo de chat sem servidor e em tempo real usando o Serviço do SignalR e o Functions.

## <a name="prerequisites"></a>Pré-requisitos

- Um editor de códigos, como o [Visual Studio Code](https://code.visualstudio.com/)
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing), versão 2 ou posterior. Usado para executar os aplicativos de funções do Azure localmente.
- [Node.js](https://nodejs.org/en/download/), versão 10.x

   > [!NOTE]
   > Os exemplos deverão funcionar com outras versões do Node.js; confira a [documentação de versões do Azure Functions Runtime](../azure-functions/functions-versions.md#languages) para obter mais informações.

> [!NOTE]
> Este início rápido pode ser executado no macOS, Windows ou Linux.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar o aplicativo do Azure Functions

1. No navegador em que o portal do Azure é aberto, confirme se a instância do SignalR Service implantada anteriormente foi criada com êxito pesquisando pelo nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para exibir as cadeias de conexão para a instância do SignalR Service.

1. Selecione e copie a cadeia de conexão primária.

    ![Captura de tela que realça a cadeia de conexão primária.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No editor de códigos, abra a pasta *src/chat/javascript* no repositório clonado.

1. Renomeie *local.settings.sample.json* como *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de conexão no valor da configuração **AzureSignalRConnectionString**. Salve o arquivo.

1. As funções JavaScript são organizadas em pastas. Há dois arquivos em cada pasta: *function.json* define as associações que são usadas na função, e *index.js* é o corpo da função. Há duas funções de gatilho HTTP nesse aplicativo de funções:

    - **negociar** – usa a associação de entrada *SignalRConnectionInfo* para gerar e retornar informações de conexão válidas.
    - **mensagens** – recebe uma mensagem de chat no corpo da solicitação e usa a associação de saída *SignalR* para difundir a mensagem a todos os aplicativos cliente conectados.

1. No terminal, verifique se você está na pasta *src/chat/javascript*. Execute o aplicativo de funções.

    ```bash
    func start
    ```

    ![Criar Serviço SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)
    
Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsjs).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou e executou um aplicativo sem servidor em tempo real no VS Code. Em seguida, saiba mais sobre como implantar o Azure Functions do VS Code.

> [!div class="nextstepaction"]
> [Implantar o Azure Functions com o VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
