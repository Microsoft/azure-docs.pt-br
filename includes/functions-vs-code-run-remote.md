---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 55a75651b724a4fe975f655958e36fbd40e35db7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748242"
---
## <a name="run-the-function-in-azure"></a>Executar a função no Azure

1. De volta na área **Azure: Functions** na barra lateral, expanda o novo aplicativo de funções em sua assinatura. Expanda **Funções**, clique com o botão direito do mouse (Windows) ou pressione <kbd>Ctrl</kbd> e clique (macOS) em **HttpExample** e escolha **Copiar URL de função**.

    ![Copiar a URL da função para o novo gatilho HTTP](./media/functions-vs-code-run-remote/function-copy-endpoint-url.png)

1. Cole essa URL para a solicitação HTTP na barra de endereços do navegador, adicione a cadeia de caracteres de consulta `name` como `?name=Functions` no final desta URL e, em seguida, execute a solicitação. A URL que chama a função HTTP disparada deve estar no seguinte formato:

    ```http
    http://<FUNCTION_APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions
    ```

    O seguinte exemplo mostra a resposta no navegador à solicitação GET remota retornada pela função:

    ![Resposta da função no navegador](./media/functions-vs-code-run-remote/functions-test-remote-browser.png)
