---
title: Processar eventos do Barramento de Serviço por meio da Grade de Eventos usando os Aplicativos Lógicos do Azure
description: Este artigo fornece etapas para processar os eventos do Barramento de Serviço por meio da Grade de Eventos usando os Aplicativos Lógicos do Azure.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998955"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>Tutorial: Responder aos eventos do Barramento de Serviço do Azure recebidos por meio da Grade de Eventos do Azure usando os Aplicativos Lógicos do Azure
Neste tutorial, você aprenderá a responder aos eventos do Barramento de Serviço do Azure recebidos por meio da Grade de Eventos do Azure usando os Aplicativos Lógicos do Azure. 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>Receber mensagens usando os Aplicativos Lógicos
Nesta etapa, você criará um aplicativo lógico do Azure que recebe eventos do Barramento de Serviço por meio da Grade de Eventos do Azure. 

1. No portal do Azure, crie um aplicativo lógico.
    1. Selecione **+ Criar um recurso**, selecione **Integração** e, em seguida, selecione **Aplicativo Lógico**. 
    2. Na página **Aplicativo Lógico – Criar**, insira um **nome** para o aplicativo lógico.
    3. Selecione sua **assinatura** do Azure. 
    4. Selecione **Usar existente** para o **Grupo de recursos** e selecione o grupo de recursos que você usou para outros recursos (como a função do Azure, o namespace do Barramento de Serviço) criados anteriormente. 
    5. Selecione o **Local** para o aplicativo lógico. 
    6. Selecione **Examinar + criar**. 
    1. Na página **Examinar + criar**, selecione **Criar** para criar o aplicativo lógico. 
1. Na página **Designer de Aplicativos Lógicos**, selecione **Aplicativo Lógico em Branco** em **Modelos**. 
1. No designer, siga as seguintes etapas:
    1. Pesquise **Grade de Eventos**. 
    2. Selecione **Quando ocorrer um evento de recurso – Grade de Eventos do Azure**. 

        ![Designer de Aplicativos Lógicos – selecione o gatilho da Grade de Eventos](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Selecione **Entrar**, insira suas credenciais do Azure e selecione **Permitir o acesso**. 
5. Na página **Quando ocorrer um evento de recurso**, siga as seguintes etapas:
    1. Selecione sua assinatura do Azure. 
    2. Para **Tipo de recurso**, selecione **Microsoft.ServiceBus.Namespaces**. 
    3. Para **Nome de recurso**, selecione seu namespace do Barramento de Serviço. 
    4. Selecione **Adicionar novo parâmetro** e selecione **Filtro de sufixo**. 
    5. Para **Filtro de sufixo**, insira o nome da sua segunda assinatura do tópico do Barramento de Serviço. 
        ![Designer de Aplicativos Lógicos – configurar eventos](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Selecione **+ Nova Etapa** no designer e siga as etapas abaixo:
    1. Pesquise **Barramento de Serviço**.
    2. Selecione **Barramento de Serviço** na lista. 
    3. Selecione **Receber mensagens** na lista **Ações**. 
    4. Selecione **Receber mensagens de uma assinatura do tópico (bloqueio de inspeção)** . 

        ![Designer de Aplicativos Lógicos – ação de recebimento de mensagens](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Insira um **nome para a conexão**. Por exemplo:  **Receber mensagens da assinatura do tópico** e selecione o namespace do Barramento de Serviço. 

        ![Designer de Aplicativos Lógicos – selecione o namespace do Barramento de Serviço](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Selecione **RootManageSharedAccessKey** e selecione **Criar**.

        ![Designer de Aplicativos Lógicos – selecione a chave de acesso compartilhada](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. Selecione seu **tópico** e sua **assinatura**. 
    
        ![Captura de tela que mostra onde você seleciona seu tópico e sua assinatura.](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Selecione **+ Nova Etapa** e siga as etapas abaixo: 
    1. Selecione **Barramento de Serviço**.
    2. Selecione **Concluir a mensagem em uma assinatura de tópico** na lista de ações. 
    3. Selecione seu **tópico** do Barramento de Serviço.
    4. Selecione a segunda **assinatura** do tópico.
    5. Para **Lock token da mensagem**, selecione **Lock token** em **Conteúdo dinâmico**. 

        ![Designer de Aplicativos Lógicos – concluir a mensagem](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Selecione **Salvar** na barra de ferramentas do Designer de Aplicativos Lógicos para salvar o aplicativo lógico. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="Salvar aplicativo lógico":::
1. Se você ainda não enviou mensagens de teste para o tópico, siga as instruções descritas na seção [Enviar mensagens ao tópico do Barramento de Serviço](#send-messages-to-the-service-bus-topic) para enviar mensagens ao tópico. 
1. Alterne para a página **Visão geral** do seu aplicativo lógico. Você verá as execuções do aplicativo lógico no **Histórico de execuções** das mensagens enviadas. Pode levar alguns minutos para que você veja as execuções do aplicativo lógico. Selecione **Atualizar** na barra de ferramentas para atualizar a página. 

    ![Designer de Aplicativos Lógicos – execuções de aplicativo lógico](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. Selecione uma execução de aplicativo lógico para ver os detalhes. Observe que ela processou cinco mensagens no loop for. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="Detalhes da execução de aplicativo lógico":::    

## <a name="troubleshoot"></a>Solucionar problemas
Se você não vir nenhuma invocação depois de aguardar e atualizar a página por algum tempo, siga estas etapas: 

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