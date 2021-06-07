---
title: Adicionar mensagens a uma fila do Armazenamento do Azure usando o Functions
description: Use o Azure Functions para criar uma função sem servidor que é invocada por uma solicitação HTTP e cria uma mensagem em uma fila do Armazenamento do Azure.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674194"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Adicionar mensagens a uma fila do Armazenamento do Azure usando o Functions

No Azure Functions, as associações de entrada e saída fornecem uma maneira declarativa para criar dados a partir de serviços externos disponíveis ao seu código. Neste guia de início rápido, é possível usar uma associação de saída para criar uma mensagem em uma fila quando uma função é disparada por uma solicitação HTTP. Use o contêiner do Armazenamento do Azure para exibir as mensagens em fila que sua função cria.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Siga as instruções em [Criar sua primeira função no portal do Azure](./functions-get-started.md) e não realize a etapa **Limpar recursos**. Este início rápido cria o aplicativo de funções e a função que você usa aqui.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Adicionar uma associação de saída

Nesta seção, você usa a interface do usuário do portal para adicionar uma associação de saída de armazenamento de filas para a função criada anteriormente. Essa associação permite escrever o mínimo possível de código para criar uma mensagem em uma fila. Você não precisa escrever códigos para tarefas como abrir uma conexão de armazenamento, criar uma fila ou obter uma referência a uma fila. O Azure Functions runtime e a associação de saída da fila cuidam dessas tarefas para você.

1. No portal do Azure, abra a página de aplicativo de funções do aplicativo de funções criado por você em [Criar sua primeira função no portal do Azure](./functions-get-started.md). Para abrir a página, pesquise e selecione **Aplicativo de Funções**. Em seguida, selecione seu aplicativo de funções.

1. Selecione seu aplicativo de funções e depois a função que você criou no guia de início rápido anterior.

1. Selecione **Integração** e escolha **+ Adicionar saída**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Crie uma associação de saída para sua função." border="true":::

1. Selecione o tipo de associação **Armazenamento de Filas do Azure** e adicione as configurações conforme especificado na tabela após esta captura de tela: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Adicione uma associação de saída de Armazenamento de Filas a uma função no portal do Azure." border="true":::
    
    | Configuração      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do parâmetro de mensagem** | outputQueueItem | O nome do parâmetro de associação de saída. | 
    | **Nome da fila**   | outqueue  | Nome da fila à qual se conectar em sua conta de armazenamento. |
    | **Conexão da conta de armazenamento** | AzureWebJobsStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |

1. Clique em **OK** para adicionar a associação.

Agora que você tem uma associação de saída definida, você precisa atualizar o código para usar a associação para adicionar mensagens a uma fila.  

## <a name="add-code-that-uses-the-output-binding"></a>Adicionar o código que usa a associação de saída

Nesta seção, você adiciona o código que grava uma mensagem na fila de saída. A mensagem inclui o valor que é passado para o gatilho HTTP na cadeia de consulta. Por exemplo, se a cadeia de consulta incluir `name=Azure`, a mensagem da fila será *Nome transmitido para a função: Azure*.

1. Na sua função, selecione **Codificar + Testar** para exibir o código de função no editor.

1. Atualize o código de função, dependendo da linguagem de programação de sua função:

    # <a name="c"></a>[C\#](#tab/csharp)

    Adicione um parâmetro **outputQueueItem** à assinatura do método, conforme mostrado no exemplo a seguir.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    No corpo da função, imediatamente antes da instrução `return`, adicione o código que usa o parâmetro para criar uma mensagem da fila.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Adicione código que usa a associação de saída no objeto `context.bindings` para criar uma mensagem da fila. Adicione esse código antes da instrução `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Selecione **Salvar** para salvar as alterações.

## <a name="test-the-function"></a>Testar a função

1. Depois que as alterações de código forem salvas, selecione **Testar**.
1. Confirme se o teste corresponde à imagem abaixo e selecione **Executar**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Teste a associação de Armazenamento de Filas no portal do Azure." border="true":::

    Observe que o **Corpo da solicitação** contém o valor de `name`*Azure*. Esse valor aparece na mensagem da fila que é criada quando a função é invocada.
    
    Como uma alternativa para selecionar **Executar** aqui, você pode chamar a função inserindo uma URL em um navegador e especificando o valor `name` na cadeia de consulta. O método de navegador é mostrado no [guia de início rápido anterior](./functions-get-started.md).

1. Confira os logs para verificar se a função foi bem-sucedida. 

Uma nova fila denominada **outqueue** é criada na sua conta de armazenamento pelo runtime do Functions quando a associação de saída é usada pela primeira vez. Você usará a conta de armazenamento para verificar se a fila e uma mensagem nela foram criadas.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Localizar a conta de armazenamento conectada ao AzureWebJobsStorage


1. Navegue até o aplicativo de funções e selecione **Configuração**.

1. Em **Configurações do aplicativo**, selecione **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Captura de tela mostra a página de configuração com AzureWebJobsStorage selecionado." border="true":::

1. Localize e anote o nome da conta.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Localize a conta de armazenamento conectada ao AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Examinar a fila de saída

1. No grupo de recursos do aplicativo de funções, selecione a conta de armazenamento sendo usada para este início rápido.

1. Em **Serviço Fila**, selecione **Filas** e selecione a fila denominada **outqueue**. 

   A fila contém a mensagem que a associação de saída de fila criou quando você executou a função disparada por HTTP. Se você tiver invocado a função com o valor `name` padrão do *Azure*, a mensagem da fila será *Nome transmitido à função: Azure*.

1. Execute a função novamente e você verá uma nova mensagem exibida na fila.  

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você adicionou uma associação de saída a uma função existente. Para obter mais informações sobre associação a Armazenamento de Filas, consulte [Associações de fila do Armazenamento do Azure Functions](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
