---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: bff2f05a95faf9c475189cb5a8003cb7fd9f69be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701375"
---
1. Para executar sua função, pressione <kbd>F5</kbd> no Visual Studio. Talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP. Os níveis de autorização nunca são impostos quando você executa uma função localmente.

2. Copie a URL da sua função da saída do Azure Functions runtime.

    ![runtime local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<YOUR_NAME>` a essa URL e execute a solicitação. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função: 

    ![Resposta da função localhost no navegador](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pare a depuração pressionando <kbd>Shift</kbd>+<kbd>F5</kbd> no Visual Studio.
