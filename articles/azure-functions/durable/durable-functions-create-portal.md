---
title: Criar Durable Functions usando o portal do Azure
description: Aprenda a instalar a extensão Durable Functions doa Azure Functions para o desenvolvimento do portal.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
origin.date: 10/23/2018
ms.date: 03/25/2019
ms.author: v-junlch
ms.openlocfilehash: 1c60bd4dae6c279ccff637ff0aa798c48ebec6f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710788"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Criar Durable Functions usando o portal do Azure

A extensão [Funções Duráveis](durable-functions-overview.md) do Azure Functions é fornecida no pacote de NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Esta extensão deve ser instalada em seu aplicativo de função. Este artigo mostra como instalar esse pacote para que você possa desenvolver funções duráveis no portal do Azure.

> [!NOTE]
> 
> * Se você estiver desenvolvendo funções duráveis em C#, deverá considerar o [Desenvolvimento do Visual Studio 2017](durable-functions-create-first-csharp.md).
> * Se você estiver desenvolvendo funções duráveis em JavaScript, você deve considerar o [Desenvolvimento de código do Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de função para hospedar a execução de qualquer função. Um aplicativo de função permite agrupar suas funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Você pode criar um aplicativo .NET ou JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Por padrão, o aplicativo de função criado usa a versão 2.x do Azure Functions Runtime. A extensão Durable Functions funciona nas duas versões 1.x e 2.x do Azure Functions Runtime em C# e versão 2.x no JavaScript. No entanto, os modelos só estão disponíveis ao segmentar a versão 2.x do tempo de execução independentemente da linguagem escolhida.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalar o pacote npm de Durable Functions (apenas JavaScript)

Se você estiver criando Durable Functions do JavaScript, precisará instalar o [pacote npm `durable-functions`](https://www.npmjs.com/package/durable-functions).

1. Selecione o nome do seu aplicativo de funções, seguido pelos **Recursos da Plataforma** e **Ferramentas avançadas (Kudu)**.

   ![Recursos da plataforma funções, escolha Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Dentro do console do Kudu, selecione **Console de depuração** e **CMD**.

   ![Console de depuração do Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Sua estrutura de diretório do arquivo do aplicativo de funções deve ser exibida. Navegue até a pasta `site/wwwroot`. Dela, você pode carregar um arquivo `package.json` arrastando e soltando-o na janela do diretório do arquivo. Há um exemplo `package.json` abaixo:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Carregar o pacote do package.json do Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Após seu `package.json` ser carregado, execute o comando `npm install` do Console de Execução Remota do Kudu.

   ![Executar a instalação do npm do Kudu](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Crie uma função de orquestrador

1. Expanda seu aplicativo de funções e clique no botão **+** ao lado de **Functions**. Se essa for a primeira função em seu aplicativo de funções, selecione **No portal** e depois **Continuar**. Caso contrário, vá para a etapa três.

   ![Página de início rápido de funções no portal do Azure](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Escolha **Mais modelos** e, em seguida, **Concluir e exibir modelos**.

    ![Início Rápido do Functions, escolher mais modelos](./media/durable-functions-create-portal/add-first-function.png)

1. No campo de pesquisa, digite `durable` e escolha o modelo **Durable Functions HTTP starter**.

1. Quando solicitado, selecione **Instalar** para instalar a extensão Azure DurableTask em quaisquer dependências do aplicativo de função. Você só precisa instalar a extensão uma vez para obter um aplicativo de função. Após a instalação ser bem-sucedida, selecione **Continuar**.

    ![Instalar extensões de associação](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Depois de concluir a instalação, nomeie a nova função `HttpStart` e escolha **Criar**. A função criada é usada para iniciar a orquestração.

1. Criar outra função no aplicativo de funções, desta vez usando o modelo **Durable Functions**. Nomeie sua nova função de orquestração `HelloSequence`.

1. Crie uma terceira função denominada `Hello` usando o modelo **Durable Functions Activity**.

## <a name="test-the-durable-function-orchestration"></a>Teste a orquestração de função durável

1. Volte para a função **HttpStart**, escolha **</> Obtenha o URL da função** e **Copie** o URL. Use essa URL para iniciar o **HelloSequence** função.

1. Use uma ferramenta HTTP, como Postman ou cURL, para enviar uma solicitação POST ao URL que você copiou. O exemplo a seguir é um comando cURL que envia uma solicitação POST para a função durável:

    ```bash
    curl -X POST https://{your-function-app-name}.chinacloudsites.cn/api/orchestrators/HelloSequence
    ```

    Neste exemplo, `{your-function-app-name}` é o domínio que é o nome do seu aplicativo de função. A mensagem de resposta contém um conjunto de nós de extremidade do URI que você pode usar para monitorar e gerenciar a execução, que se parece com o exemplo a seguir:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Chame o `statusQueryGetUri` endpoint URI e você verá o status atual da função durável, que pode ser semelhante a este exemplo:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Continue chamando o endpoint `statusQueryGetUri` até que o status seja alterado para **Completed** e você veja uma resposta como o exemplo a seguir:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

A primeira função durável está agora em execução no Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre os padrões comuns de função durável](durable-functions-concepts.md)

<!-- Update_Description: wording update -->
