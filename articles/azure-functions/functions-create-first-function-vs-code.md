---
title: Criar sua primeira função no Azure usando o Visual Studio Code
description: Crie e publique uma função disparada por HTTP simples no Azure usando a extensão Azure Functions no Visual Studio Code.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, computação, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fcf9f1d6420dbbde359d386bc3b67a0866aca30d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444619"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Criar sua primeira função usando o Visual Studio Code

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web.

Neste artigo, você aprenderá a usar a [extensão Azure Functions para Visual Studio Code] para criar e testar uma função "hello world" no computador local usando o Microsoft Visual Studio Code. Em seguida, você publicará o código de função no Azure do Visual Studio Code.

![Código do Azure Functions em um projeto do Visual Studio](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

No momento, a extensão é compatível com as funções C#, JavaScript e Java. Com o Python ela é compatível em Visualização. As etapas neste artigo e no artigo seguinte são compatíveis somente com as funções JavaScript e C#. Para saber como usar o Visual Studio Code para criar e publicar funções do Python, veja [Implantar Python no Azure Functions](https://code.visualstudio.com/docs/python/tutorial-azure-functions). Para saber como usar o Visual Studio Code para criar e publicar funções do PowerShell, veja [Criar sua primeira função do PowerShell no Azure](functions-create-first-function-powershell.md). 

Atualmente, a extensão está em versão prévia. Para obter mais informações, consulte a página da extensão [Extensão Azure Functions para Visual Studio Code].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Instale a versão 2.x do [Azure Functions Core Tools](functions-run-local.md#v2).

* Instale os requisitos específicos para a linguagem de programação escolhida:

    | Linguagem | Requisito |
    | -------- | --------- |
    | **C#** | [Extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> | 
 
    <sup>*</sup>Active LTS e versões LTS de manutenção (8.11.1 e 10.14.1 recomendadas).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Após verificar se a função foi executada corretamente no computador local, é hora de publicar o projeto no Azure.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>Executar a função no Azure

1. Copie a URL do gatilho de HTTP do painel **Saída**. Assim como anteriormente, certifique-se de adicionar o valor de cadeia de caracteres de consulta `?name=<yourname>` a essa URL e execute a solicitação.

    A URL que chama a função HTTP disparada deve estar no seguinte formato:

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

1. Cole essa nova URL para a solicitação HTTP na barra de endereços do navegador. O exemplo a seguir mostra a resposta no navegador à solicitação GET remota retornada pela função: 

    ![Resposta da função no navegador](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Próximas etapas

Você usou o Visual Studio Code para criar um aplicativo de funções com uma função disparada por HTTP simples. No próximo artigo, você pode expandir essa função adicionando uma associação de saída. Essa associação grava a cadeia de caracteres da solicitação HTTP em uma mensagem em uma fila do Armazenamento de Filas do Azure. O próximo artigo também mostra como limpar esses novos recursos do Azure, removendo o grupo de recursos que você criou.

> [!div class="nextstepaction"]
> [Adicionar uma associação de fila do Armazenamento do Azure à sua função](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Extensão Azure Functions para Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
