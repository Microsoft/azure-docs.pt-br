---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: eae828d03431dd339c5399d8db8c6e46141ab11b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075314"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O Visual Studio Code integra-se ao [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) para permitir que você execute esse projeto em seu computador de desenvolvimento local antes da publicação no Azure.

1. Para chamar sua função, pressione <kbd>F5</kbd> para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**. Seu aplicativo é iniciado no painel **Terminal**. Você pode ver o ponto de extremidade de URL de sua função disparada por HTTP localmente.

    ![Saída do VS Code de função local](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Se você tiver problemas com a execução no Windows, verifique se o terminal padrão do Visual Studio Code não está definido como **bash WSL**.

1. Com o Core Tools em execução, acesse a área **Azure: Funções**. Em **Funções**, expanda **Projeto Local** > **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Executar a função agora por meio do Visual Studio Code":::
    
1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função. 

   Em vez disso, você poderia ter enviado uma solicitação HTTP GET para o endereço `http://localhost:7071/api/HttpExample` em um navegador da Web.

1. Quando a função é executada localmente e retorna uma resposta, uma notificação é gerada no Visual Studio Code. As informações sobre a execução da função são mostradas no painel **Terminal**.

1. Pressione <kbd>Ctrl + C</kbd> para parar o Core Tools e desconectar o depurador.
