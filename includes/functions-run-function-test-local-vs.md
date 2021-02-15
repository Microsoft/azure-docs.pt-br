---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056561"
---
1. Para executar sua função, pressione F5 no Visual Studio. Talvez seja necessário habilitar uma exceção de firewall para que as ferramentas possam manipular solicitações HTTP. Os níveis de autorização nunca são impostos quando você executa uma função localmente.

2. Copie a URL da sua função da saída do Azure Functions runtime.

    ![runtime local do Azure](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<YOUR_NAME>` a essa URL e execute a solicitação. A imagem a seguir mostra a resposta no navegador para a solicitação GET local retornada pela função: 

    ![Resposta da função localhost no navegador](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. Pare a depuração pressionando Shift+F5 no Visual Studio.