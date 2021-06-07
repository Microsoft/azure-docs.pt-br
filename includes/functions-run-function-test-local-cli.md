---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283177"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

1. Execute sua função iniciando o host de runtime do Azure Functions local da pasta *LocalFunctionProj*:

    ```
    func start
    ```

    Perto do fim da saída, devem aparecer as seguintes linhas: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Se HttpExample não aparece conforme mostrado acima, é provável que você tenha iniciado o host fora da pasta raiz do projeto. Nesse caso, use **Ctrl**+**C** para parar o host, navegue até a pasta raiz do projeto e execute o comando anterior novamente.

1. Copie a URL da função `HttpExample` dessa saída para um navegador e acrescente a cadeia de caracteres de consulta `?name=<YOUR_NAME>`, fazendo com que a URL completa seja `http://localhost:7071/api/HttpExample?name=Functions`. O navegador exibirá uma mensagem de resposta que retorna o valor da cadeia de consulta. O terminal em que você iniciou seu projeto também mostra a saída do log conforme você faz solicitações.

1. Quando você concluir, use **Ctrl**+**C** e escolha `y` para interromper o host de funções.
