---
title: "Configurar o monitor de eventos com Hubs de Evento do Azure para Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Monitorar fluxos de dados para receber eventos e enviar eventos para Aplicativos Lógicos do Azure com Hubs de Evento do Azure"
services: logic-apps
keywords: fluxo de dados, monitor de eventos, hubs de eventos
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Monitorar, receber e enviar eventos com o conector do Hubs de Eventos

Para configurar um monitor de eventos para que seu aplicativo lógico possa detectar eventos, receber eventos e enviar eventos, conecte-se a um [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs) de seu aplicativo lógico. Saiba mais sobre [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md).

## <a name="requirements"></a>Requisitos

* Você precisa ter um [Namespace dos Hubs de Eventos e o Hub de Eventos](../event-hubs/event-hubs-create.md) no Azure. Saiba [como criar um namespace dos Hubs de Eventos e um Hub de Eventos](../event-hubs/event-hubs-create.md). 

* Para usar [qualquer conector](https://docs.microsoft.com/azure/connectors/apis-list) em seu aplicativo lógico, você precisa criar primeiro um aplicativo lógico. Saiba [como criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Verifique as permissões de namespace dos Hubs de Eventos e encontre a cadeia de conexão

Para seu aplicativo lógico acessar qualquer serviço, você precisa criar uma [*conexão*](./connectors-overview.md) entre seu aplicativo lógico e o serviço, se você ainda não fez isso. Essa conexão autoriza seu aplicativo lógico a acessar os dados.
Para seu aplicativo lógico acessar o Hub de Eventos, você precisa ter permissões de **Gerenciamento** e a cadeia de conexão para seu namespace de Hubs de Eventos.

Para verificar suas permissões e obter a cadeia de conexão, execute estas etapas.

1.  Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2.  Acesse o *namespace* de seus Hubs de Evento, não o Hub de Eventos específico. Na folha do namespace, em **Configurações**, escolha **Políticas de acesso compartilhado**. Em **Declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

    ![Gerenciar permissões para seu namespace do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Para copiar a cadeia de conexão para o namespace de Hubs de Eventos, escolha **RootManageSharedAccessKey**. Ao lado de sua cadeia de conexão de chave primária, escolha o botão de cópia.

    ![Copie a cadeia de conexão do namespace dos Hubs de Eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > Para confirmar se a cadeia de conexão está associada ao namespace dos seus Hubs de Eventos ou com um Hub de Eventos específico, verifique na cadeia de conexão o parâmetro `EntityPath`. Se você encontrar esse parâmetro, a cadeia de conexão servirá para uma "entidade" de Hub de Eventos específica e não será a cadeia de caracteres correta a ser usada com seu aplicativo lógico.

4.  Agora, quando você receber uma solicitação por credenciais após a adição de um gatilho dos Hubs de Evento ou uma ação para o seu aplicativo lógico, você pode se conectar ao namespace de seus Hubs de Eventos. Dê um nome à sua conexão, insira a cadeia de conexão que você copiou e escolha **Criar**.

    ![Insira a cadeia de conexão para o namespace dos Hubs de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Depois de criar a conexão, o nome da conexão deverá aparecer no gatilho ou ação dos Hubs de Evento. 
    Continue com as outras etapas em seu aplicativo lógico.

    ![Conexão do namespace dos Hubs de Eventos criado](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Iniciar o fluxo de trabalho quando seu Hub de Eventos receber novos eventos

Um [*gatilho*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) é um evento que inicia um fluxo de trabalho em seu aplicativo lógico. Para iniciar um fluxo de trabalho quando novos eventos forem enviados ao seu Hub de Eventos, execute estas etapas para adicionar o gatilho que detecta esse evento.

1.  No [portal do Azure](https://portal.azure.com "portal do Azure"), acesse seu aplicativo lógico existente ou crie um aplicativo lógico em branco.

2.  Na caixa de pesquisa do Designer de Aplicativo Lógico, insira `event hubs` para o seu filtro. Selecione esse gatilho: **quando os eventos estiverem disponíveis no Hub de Eventos**

    ![Selecionar o gatilho quando seu Hub de Eventos receber novos eventos](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Se você ainda não tiver uma conexão para o namespace de seus Hubs de Eventos, receberá uma solicitação para criar essa conexão. Dê um nome à sua conexão e insira a cadeia de conexão para seu namespace de Hubs de Eventos. 
    Se for necessário, saiba [como localizar a cadeia de conexão](#permissions-connection-string).

    ![Insira a cadeia de conexão para o namespace dos Hubs de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    Depois de criar a conexão, as configurações para o gatilho **Quando um evento estiver disponível em um Hub de Eventos** aparecer.

    ![Configurações do gatilho quando seu Hub de Eventos receber novos eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  Insira ou selecione o nome do Hub de Eventos que você deseja monitorar. Selecione a frequência e o intervalo da frequência com que você deseja verificar o Hub de Eventos.

    > [!TIP]
    > Para selecionar, opcionalmente, um grupo de consumidores para ler eventos, escolha **Mostrar opções avançadas**. 

    ![Especifique o Hub de Evento ou grupo de consumidores](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    Agora, você definiu um gatilho para iniciar um fluxo de trabalho para seu aplicativo lógico. 
    Seu aplicativo lógico verifica o Hub de Eventos especificado com base no agendamento definido. 
    Se o seu aplicativo encontrar novos eventos no Hub de Eventos, o gatilho executará outras ações ou gatilhos em seu aplicativo lógico.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>Enviar eventos para seu Hub de Eventos de seu aplicativo lógico

Uma [*ação*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) é uma tarefa executada pelo fluxo de trabalho do aplicativo lógico. Depois de adicionar um gatilho ao seu aplicativo lógico, você poderá adicionar uma ação para executar operações com os dados gerados por esse gatilho. Para enviar um evento para seu Hub de Eventos de seu aplicativo lógico, execute estas etapas.

1.  No Designer de Aplicativo Lógico, no gatilho de seu aplicativo lógico, escolha **Nova etapa** > **Adicionar uma ação**.

    ![Escolha "Nova etapa", em seguida, "Adicionar uma ação"](./media/connectors-create-api-azure-event-hubs/add-action.png)

    Agora você pode localizar e selecionar uma ação para execução. 
    Embora você possa selecionar qualquer ação, neste exemplo, queremos que a ação Hubs de Eventos envie eventos.

2.  Na caixa de pesquisa, digite `event hubs` como seu filtro.
Selecione esta ação: **Enviar evento**

    ![Selecione a ação "Hubs de eventos - Enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  Insira os detalhes necessários para o evento, como o nome do Hub de Eventos no qual você deseja enviar o evento. Insira outros detalhes opcionais sobre o evento, como o conteúdo desse evento.

    > [!TIP]
    > Para especificar, opcionalmente, a partição do Hub de Eventos para onde enviar o evento, escolha **Mostrar opções avançadas de**. 

    ![Insira o nome do Hub de Eventos e detalhes opcionais do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  Salve suas alterações.

    ![Salve seu aplicativo lógico](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    Agora você configurou uma ação para enviar eventos de seu aplicativo lógico. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/eventhubs/). 

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

*  [Encontrar outros conectores de Aplicativos Lógicos do Azure](./apis-list.md)