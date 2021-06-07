---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d1931614356a313334d712713965346e843a403d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424585"
---
## <a name="invoke-the-function-on-azure"></a>Invocar a função no Azure

Como a função usa um gatilho HTTP, você a invoca fazendo uma solicitação HTTP para sua URL no navegador ou usando uma ferramenta como curl. 

# <a name="browser"></a>[Navegador](#tab/browser)

Copie a **URL de Invocação** completa mostrada na saída do comando de publicação na barra de endereços de um navegador, acrescentando o parâmetro de consulta `&name=Functions`. O navegador deverá exibir uma saída semelhante à que foi exibida quando você executou a função localmente.

![A saída da função executada no Azure em um navegador](./media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Execute [`curl`](https://curl.haxx.se/) com a **URL de Invocação**, acrescentando o parâmetro `&name=Functions`. A saída do comando deverá ser o texto, "Olá, Functions".

![A saída da função executada no Azure usando curl](./media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---
