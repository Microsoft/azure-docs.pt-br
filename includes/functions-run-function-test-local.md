---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171696"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

O comando a seguir inicia o aplicativo de funções. O aplicativo é executado usando o mesmo tempo de execução do Azure Functions no Azure.

```bash
func host start --build
```

A opção `--build` é necessária para compilar projetos em C#. Essa opção não é necessária para um projeto em JavaScript.

Quando o host do Functions é iniciado, ele escreve algo parecido com a seguinte saída, que foi truncada por questões de legibilidade:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Copie a URL da função `HttpTrigger` da saída do tempo de execução de função e cole-a na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET retornada pela função local:

![Testar localmente no navegador](./media/functions-run-function-test-local/functions-test-local-browser.png)

Agora que você executou a função localmente, poderá criar o aplicativo de funções e outros recursos necessários no Azure.