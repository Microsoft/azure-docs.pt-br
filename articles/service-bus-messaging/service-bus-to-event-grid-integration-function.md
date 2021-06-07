---
title: Processar eventos do Barramento de Serviço por meio da Grade de Eventos usando o Azure Functions
description: Este artigo fornece etapas para processar os eventos do Barramento de Serviço por meio da Grade de Eventos usando o Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95818418"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Tutorial: Responder aos eventos do Barramento de Serviço do Azure recebidos por meio da Grade de Eventos do Azure usando o Azure Functions
Neste tutorial, você aprenderá a responder a eventos do Barramento de Serviço do Azure que são recebidos por meio da Grade de Eventos do Azure usando o Azure Functions e os Aplicativos Lógicos do Azure. 

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um namespace do Barramento de Serviço
> * Preparar um aplicativo de exemplo para enviar as mensagens
> * Enviar mensagens para o tópico do Barramento de Serviço
> * Receber mensagens usando os Aplicativos Lógicos
> * Configurar uma função de teste no Azure
> * Conecte a função e o namespace através da Grade de Eventos
> * Receber mensagens usando o Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Pré-requisitos adicionais
Instale o [Visual Studio 2019](https://www.visualstudio.com/vs) e inclua a carga de trabalho de **Desenvolvimento do Azure**. Essa carga de trabalho inclui o **Azure Function Tools**, que você precisa para criar, compilar e implantar projetos do Azure Functions no Visual Studio. 

## <a name="deploy-the-function-app"></a>Implantar o aplicativo de funções 

>[!NOTE]
> Para saber mais sobre como criar e implantar um aplicativo do Azure Functions, confira [Desenvolver o Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md)

1. Abra o arquivo **ReceiveMessagesOnEvent.cs** por meio do projeto **FunctionApp1** da solução **SBEventGridIntegration.sln**. 
1. Substitua `<SERCICE BUS NAMESPACE - CONNECTION STRING>` pela cadeia de conexão para o namespace do Barramento de Serviço. Ele deverá ser o mesmo que você usou no arquivo **Program.cs** do projeto **MessageSender** na mesma solução. 
1. Clique com o botão direito do mouse em **FunctionApp1** e selecione **Publicar**. 
1. Na página **Publicar**, selecione **Iniciar**. Essas etapas podem ser diferentes do que você vê, mas o processo de publicação deve ser semelhante. 
1. No assistente de **Publicação**, na página **Destino**, selecione **Azure** em **Destino**. 
1. Na página **Destino específico**, selecione **Aplicativo de Funções do Azure (Windows)** . 
1. Na página **Instância do Functions**, selecione **Criar uma função do Azure**. 
1. Na página **Aplicativo de Funções (Windows)** , siga estas etapas:
    1. insira um **nome** para o aplicativo de funções.
    1. Selecione uma **assinatura** do Azure.
    1. Selecione um **grupo de recursos** existente ou crie um grupo de recursos. Para este tutorial, selecione o grupo de recursos que tem o namespace do Barramento de Serviço. 
    1. Selecione um **tipo de plano** para o Serviço de Aplicativo.
    1. Selecione um **local**. Selecione a mesma localização do namespace do Barramento de Serviço. 
    1. Escolha um **Armazenamento do Azure** existente ou selecione **Novo** para criar uma conta de armazenamento a ser usada pelo aplicativo de funções. 
    1. Selecione **Criar** para criar o aplicativo de funções. 
1. De volta à página **Instância do Functions** do assistente de **Publicação**, selecione **Concluir**. 
1. Na página **Publicar** do Visual Studio, selecione **Publicar** para publicar o aplicativo do Functions no Azure. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Publicar aplicativo do Functions":::    
1. Na Janela de **Saída**, confira as mensagens de build e publicação e confirme se os dois foram bem-sucedidos. 
1. Agora, na página **Publicar**, escolha **Gerenciar no portal do Azure**. 
1. No portal do Azure, na página **Aplicativo de Funções**, selecione **Functions** no menu à esquerda e confirme se você vê duas funções: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Duas funções para o manipulador do gatilho da Grade de Eventos e do gatilho HTTP":::

    > [!NOTE]
    > A `EventGridTriggerFunction` usa um [gatilho da Grade de Eventos](../azure-functions/functions-bindings-event-grid-trigger.md) e a `HTTPTriggerFunction` usa um [gatilho HTTP](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Selecione **EventGridTriggerFunction** na lista. Recomendamos que você use o gatilho da Grade de Eventos com o Azure Functions, pois ele oferece alguns benefícios em relação ao uso do gatilho HTTP. Para obter detalhes, confira [Função do Azure como um manipulador de eventos para os eventos da Grade de Eventos](../event-grid/handler-functions.md).
1. Na página **Função** da **EventGridTriggerFunction**, selecione **Monitor** no menu à esquerda. 
1. Selecione **Configurar** para configurar o Application Insights a fim de capturar o log de invocação. Use essa página para monitorar as execuções de função após o recebimento de eventos do Barramento de Serviço por meio da Grade de Eventos. 
1. Na página **Application Insights**, insira um nome para o recurso, escolha uma **localização** para o recurso e selecione **OK**. 
1. Selecione a função **EventGridTriggerFunction** na parte superior (menu de navegação estrutural) para navegar de volta até a página **Função**. 
1. Confirme se você está na página **Monitor**. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Página Monitor da função antes das invocações da função":::
    
    Mantenha essa página aberta em uma guia do navegador da Web. Você a atualizará para ver as invocações dessa função posteriormente.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Conecte a função e o namespace através da Grade de Eventos
Nesta seção, você une a função e o namespace do Barramento de Serviço usando o portal do Azure. 

Para criar uma assinatura da Grade de Eventos do Azure, siga as etapas a seguir:

1. No portal do Azure, vá para seu namespace e, em seguida, no painel esquerdo, selecione **Eventos**. A janela do namespace é aberta, com duas assinaturas da Grade de Eventos exibidas no painel direito. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Barramento de Serviço – página de eventos":::
2. Selecione **+ Assinatura de Evento** na barra de ferramentas. 
3. Na página **Criar Assinatura de Eventos**, faça o seguinte:
    1. Insira um **nome** para a assinatura. 
    2. Insira um **nome** para o **tópico do sistema**. Os tópicos do sistema são tópicos criados para recursos do Azure, como a conta do Armazenamento do Azure e o Barramento de Serviço do Azure. Para saber mais sobre os tópicos do sistema, confira [Visão geral dos tópicos do sistema](../event-grid/system-topics.md).
    2. Escolha **Função do Azure** em **Tipo de Ponto de Extremidade** e clique em **Selecionar um ponto de extremidade**. 

        ![Barramento de Serviço – Assinatura da Grade de Eventos](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Na página **Selecionar Função do Azure**, escolha a assinatura, o grupo de recursos, o aplicativo de funções, o slot e a função e selecione **Confirmar seleção**. 

        ![Função – selecionar o ponto de extremidade](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Na página **Criar Assinatura de Evento**, alterne para a guia **Filtros** e execute as seguintes tarefas:
        1. Selecione **Habilitar filtragem por assunto**
        2. Insira o nome da assinatura do tópico do Barramento de Serviço (**S1**) criado anteriormente.
        3. Selecione o botão **Criar**. 

            ![Filtro de assinatura de eventos](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Alterne para a guia **Assinaturas de Evento** da página **Eventos** e confirme se você vê a assinatura de evento na lista.

    ![Assinatura de eventos na lista](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Monitorar o aplicativo de funções
As mensagens que você enviou para o tópico do Barramento de Serviço anteriormente são encaminhadas para a assinatura (S1). A Grade de Eventos encaminha as mensagens na assinatura para a função do Azure. Nesta etapa do tutorial, você confirmará se a função foi invocada e verá as mensagens informativas registradas em log. 

1. Na página do aplicativo de funções do Azure, alterne para a guia **Monitor** caso ela ainda não esteja ativa. Você deverá ver uma entrada para cada mensagem postada no tópico do Barramento de Serviço. Se você não as vir, atualize a página depois de aguardar alguns minutos. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Página Monitor da função após as invocações":::
2. Selecione a invocação na lista para ver os detalhes. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Detalhes de invocação da função" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Você também pode usar a guia **Logs** da página **Monitor** para ver as informações de registro em log conforme as mensagens são enviadas. Pode haver algum atraso, então aguarde alguns minutos para ver as mensagens registradas. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Logs da função" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Solucionar problemas
Se você não vir nenhuma invocação da função depois de aguardar e atualizar a página por algum tempo, siga estas etapas: 

1. Confirme se as mensagens chegaram no tópico do Barramento de Serviço. Confira o contador **mensagens de entrada** na página **Tópico do Barramento de Serviço**. Nesse caso, executei o aplicativo **MessageSender** duas vezes e, portanto, vejo dez mensagens (cinco mensagens para cada execução).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Página Tópico do Barramento de Serviço – mensagens de entrada":::    
1. Confirme se não há **nenhuma mensagem ativa** na assinatura do Barramento de Serviço. 
    Se você não vir nenhum evento nessa página, verifique se a página **Assinatura do Barramento de Serviço** não mostra nenhuma **Contagem de mensagens ativas**. Se o número desse contador for maior que zero, as mensagens da assinatura não serão encaminhadas para a função de manipulador (manipulador de assinaturas de evento) por algum motivo. Verifique se você configurou a assinatura de evento corretamente. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Contagem de mensagens ativas na assinatura do Barramento de Serviço":::    
1. Você também verá os **eventos entregues** na página **Eventos** do namespace do Barramento de Serviço. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Página Eventos – eventos entregues" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Você também verá que os eventos são entregues na página **Assinatura de Evento**. Acesse essa página selecionando a assinatura de evento na página **Eventos**. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Página Assinatura de evento – eventos entregues":::
    


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [Grade de Eventos do Azure](../event-grid/index.yml).
* Saiba mais sobre o [Azure Functions](../azure-functions/index.yml).
* Saiba mais sobre o [recurso de Aplicativos Lógicos do Serviço de Aplicativo do Azure](../logic-apps/index.yml).
* Saiba mais sobre o [Barramento de Serviço do Azure](/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png