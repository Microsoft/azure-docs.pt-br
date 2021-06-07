---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99809551"
---
## <a name="run-the-function-in-azure"></a>Executar a função no Azure

1. De volta na área **Azure: Funções** na barra lateral, expanda sua assinatura, o novo aplicativo de funções e **Funções**. Clique com o botão direito do mouse (Windows) ou clique em <kbd>CTRL -</kbd> (macOS) na função `HttpExample` e escolha **Função Executar Agora...** .

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar a função agora no Azure por meio do Visual Studio Code":::

1. Em **Insira o corpo da solicitação**, você verá o valor do corpo da mensagem de solicitação igual a `{ "name": "Azure" }`. Clique em ENTER para enviar essa mensagem de solicitação à função.  

1. Quando a função é executada no Azure e retorna uma resposta, uma notificação é gerada no Visual Studio Code.
