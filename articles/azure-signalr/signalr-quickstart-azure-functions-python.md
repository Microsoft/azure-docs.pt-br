---
title: Início Rápido do Serviço do Azure SignalR sem servidor – Python
description: Um guia de início rápido de como usar o Serviço do Azure SignalR e o Azure Functions para criar uma sala de chat usando o Python.
author: anthonychu
ms.author: antchu
ms.date: 12/14/2019
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: a279c1ed16cac69932001146d4108cec19203e22
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536630"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Início Rápido: Criar uma sala de chat com o Azure Functions e o Serviço do SignalR usando Python

O Azure SignalR Service permite adicionar facilmente a funcionalidade em tempo real ao seu aplicativo. O Azure Functions é uma plataforma sem servidor que permite executar seu código sem gerenciar qualquer infraestrutura. Neste início rápido, saiba como usar o SignalR Service e o Functions para criar um aplicativo de chat em tempo real e sem servidor.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido pode ser executado no macOS, Windows ou Linux.

Certifique-se de ter um editor de código, como [Visual Studio Code](https://code.visualstudio.com/), instalado.

Instale [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (versão 2.7.1505 ou superior) para executar aplicativos do Azure Function do Python localmente.

O Azure Functions exige o [Python 3.6 e posterior](https://www.python.org/downloads/). (Confira [Versões do Python compatíveis](/azure/azure-functions/functions-reference-python#python-version))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Entre no portal do Azure em <https://portal.azure.com/> com sua conta do Azure.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Configurar e executar o aplicativo do Azure Functions

1. No navegador em que o portal do Azure é aberto, confirme se a instância do SignalR Service implantada anteriormente foi criada com êxito pesquisando pelo nome na caixa de pesquisa na parte superior do portal. Selecione a instância para abri-la.

    ![Pesquisar a instância do SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selecione **Chaves** para exibir as cadeias de conexão para a instância do SignalR Service.

1. Selecione e copie a cadeia de conexão primária.

    ![Selecione e copie a cadeia de conexão primária.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. No editor de códigos, abra a pasta *src/chat/python* no repositório clonado.

1. Para desenvolver e testar as funções do Python localmente, é necessário trabalhar em um ambiente do Python 3.6 ou 3.7. Execute os seguintes comandos para criar e ativar um ambiente virtual chamado `.venv`.

    **Linux ou macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Renomeie *local.settings.sample.json* como *local.settings.json*.

1. Em **local.settings.json**, cole a cadeia de conexão no valor da configuração **AzureSignalRConnectionString**. Salve o arquivo.

1. As funções Python são organizadas em pastas. Há dois arquivos em cada pasta: *function.json* define as associações usadas na função e *\_\_init\_\_* é o corpo da função. Há duas funções de gatilho HTTP nesse aplicativo de funções:

    - **negociar** – usa a associação de entrada *SignalRConnectionInfo* para gerar e retornar informações de conexão válidas.
    - **mensagens** – recebe uma mensagem de chat no corpo da solicitação e usa a associação de saída *SignalR* para difundir a mensagem a todos os aplicativos cliente conectados.

1. No terminal com o ambiente virtual ativado, verifique se você está na pasta *src/chat/python*. Instale os pacotes de Python necessários usando PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Execute o aplicativo de funções.

    ```bash
    func start
    ```

    ![Executar o aplicativo de função](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou e executou um aplicativo sem servidor em tempo real no VS Code. Em seguida, saiba mais sobre como implantar o Azure Functions do VS Code.

> [!div class="nextstepaction"]
> [Implantar o Azure Functions com o VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
