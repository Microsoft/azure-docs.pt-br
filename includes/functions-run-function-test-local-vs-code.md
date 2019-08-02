---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88a4fe8b9f0b477ed851a03742a9957c08b7cbf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455166"
---
## <a name="run-the-function-locally"></a>Executar a função localmente

As Ferramentas Principais do Azure Functions permitem executar um projeto do Azure Functions no seu computador de desenvolvimento local.

1. Para testar sua função, defina um ponto de interrupção no código da função e pressione F5 para iniciar o projeto de aplicativo de funções. A saída do Core Tools é exibida no painel **Terminal**.

1. No painel **Terminal**, copie o ponto de extremidade de URL da sua função disparada por HTTP. Essa URL inclui a chave de função, que é passada para o parâmetro de consulta `code`.

    ![Saída local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `?name=<yourname>` a essa URL e execute a solicitação. A execução é pausada quando o ponto de interrupção é atingido.

1. Quando você continua a execução, o exemplo a seguir mostra a resposta no navegador à solicitação GET:

    ![Resposta da função localhost no navegador](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Para interromper a depuração, pressione Shift + F5.