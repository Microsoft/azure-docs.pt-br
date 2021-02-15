---
title: Criar uma função no Azure disparada por mensagens na fila
description: Use o Azure Functions para criar uma função sem servidor que é invocada por uma mensagem enviada para uma fila no Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981579"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função disparada pelo Armazenamento de Filas do Azure

Saiba como criar uma função que é disparada quando as mensagens são enviadas para uma fila do Armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-an-azure-function-app"></a>Criar um Aplicativo de funções do Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

Em seguida, crie uma nova função no novo aplicativo de funções.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Criar uma função disparada por Filas

1. Selecione **Funções** e depois selecione **+ Adicionar** para adicionar uma função.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Escolha o modelo de **gatilho do Armazenamento de Filas do Azure**.

1. Use as configurações conforme especificado na tabela abaixo da imagem.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Aplicativo de funções criado com êxito." border="true":::


    | Configuração | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Exclusivo no aplicativo de funções | O nome dessa função disparada por filas. |
    | **Nome da fila**   | myqueue-items    | Nome da fila à qual se conectar em sua conta de armazenamento. |
    | **Conexão da conta de armazenamento** | AzureWebJobsStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |    

1. Selecione **Criar Função** para criar sua função.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

Em seguida, você pode se conectar à sua conta do Armazenamento do Azure e criar a fila de armazenamento **myqueue-items**.

## <a name="create-the-queue"></a>Criar a fila

1. Em sua função, na página **Visão geral**, selecione o grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Encontre e selecione a conta de armazenamento do grupo de recursos.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Escolha **Filas** e, em seguida, escolha **+ Fila**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. No campo **Nome**, digite `myqueue-items` e, em seguida, selecione **Criar**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

Agora que você tem uma fila de armazenamento, você pode testar a função adicionando uma mensagem à fila.

## <a name="test-the-function"></a>Testar a função

1. De volta ao Portal do Azure, navegue até sua função, expanda os **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Em uma janela separada do navegador, vá para o grupo de recursos no portal do Azure e selecione a conta de armazenamento.

1. Selecione **Filas** e, em seguida, selecione o contêiner **myqueue-items**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Selecione **Adicionar mensagem** e digite "Olá, Mundo!" em **Mensagem de texto**. Selecione **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. Aguarde alguns segundos, depois volte para seus logs de função e verifique se a nova mensagem foi lida da fila.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

1. De volta à sua fila de armazenamento, selecione **Atualizar** e verifique se a mensagem foi processada e se não está mais na fila.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. Para obter mais informações sobre gatilhos de Armazenamento de Filas, consulte [Associações de fila do Armazenamento do Azure Functions](functions-bindings-storage-queue.md).

Agora que você criou sua primeira função, vamos adicionar uma associação de saída à função que grava uma mensagem de volta em outra fila.

> [!div class="nextstepaction"]
> [Adicionar mensagens a uma fila do Armazenamento do Azure usando o Functions](functions-integrate-storage-queue-output-binding.md)
