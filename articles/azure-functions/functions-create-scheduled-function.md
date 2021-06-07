---
title: Criar uma função no Azure que é executada em uma agenda
description: Saiba como usar o portal do Azure para criar uma função que é executada com base em um agendamento que você define.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035182"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>Criar uma função no portal do Azure que é executado em uma agenda

Saiba como usar o portal do Azure para criar uma função que é executada sem [servidor](https://azure.microsoft.com/solutions/serverless/) no Azure com base em uma agenda que você define.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

+ Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Seu novo aplicativo de funções está pronto para uso. Em seguida, você criará uma função no novo aplicativo de funções.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Aplicativo de funções criado com êxito." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Criar uma função disparada por temporizador

1. No seu aplicativo de funções, escolha **Funções** e selecione **+ Adicionar**. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Adicione uma função no portal do Azure." border="true":::

1. Selecione o modelo **Gatilho de timer**. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Selecione o gatilho de timer no portal do Azure." border="true":::

1. Configure o novo gatilho com as configurações especificadas na tabela abaixo da imagem e, depois, selecione **Criar Função**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Captura de tela mostra a nova página de função com o modelo de gatilho de temporizador selecionado." border="true":::
    
    | Configuração | Valor sugerido | Descrição |
    |---|---|---|
    | **Nome** | Padrão | Define o nome da sua função disparada por temporizador. |
    | **Agenda** | 0 \*/1 \* \* \* \* | Uma [expressão CRON](functions-bindings-timer.md#ncrontab-expressions) de seis campos que agenda sua função para ser executada a cada minuto. |

## <a name="test-the-function"></a>Testar a função

1. Em sua função, selecione **Codificar + Testar** e expanda os logs.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Teste o gatilho de timer no portal do Azure." border="true":::

1. Verifique a execução, exibindo as informações gravadas nos logs.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Veja o gatilho de timer no portal do Azure." border="true":::

Agora você altera o agendamento da função para que ela seja executada uma vez por hora em vez de uma vez por minuto.

## <a name="update-the-timer-schedule"></a>Atualizar o agendamento do temporizador

1. Em sua função, selecione **Integração**. É aqui que você define as associações de entrada e saída de sua função e também define o agendamento. 

1. Selecione **Timer (myTimer)** .

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Atualize o agendamento do timer no portal do Azure." border="true":::

1. Atualize o valor de **Agendamento** para `0 0 */1 * * *` e, em seguida, selecione **Salvar**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Atualize o agendamento do temporizador da função no portal do Azure." border="true":::

Agora você tem uma função que é executada uma vez a cada hora, na hora.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada segundo um agendamento. Para obter mais informações sobre gatilhos de temporizador, confira [Agendar a execução de código com o Azure Functions](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
