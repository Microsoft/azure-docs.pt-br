---
title: Conectar-se aos Hubs de Eventos do Azure
description: Criar tarefas e fluxos de trabalho automatizados que monitoram e gerenciam eventos usando os hubs de eventos do Azure e os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/23/2019
tags: connectors
ms.openlocfilehash: 198a5da63ed90937c53f7f12f3559f15100e8f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88031297"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Monitorar, receber e enviar eventos com Hubs de Eventos do Azure e Aplicativos Lógicos do Azure

Este artigo mostra como é possível monitorar e gerenciar eventos enviados para os [Hubs de Eventos do Azure](../event-hubs/event-hubs-about.md) a partir de um aplicativo lógico com o conector de Hubs de Eventos do Azure. Dessa forma, é possível aplicativos lógicos que automatizam tarefas e fluxos de trabalho para verificar, enviar e receber eventos do Hub de Eventos. Para obter informações técnicas específicas do conector, consulte a [referência do conector dos hubs de eventos do Azure](/connectors/eventhubs/) </a> .

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Um [namespace de hubs de eventos do Azure e um hub de eventos](../event-hubs/event-hubs-create.md)

* O aplicativo lógico no qual você deseja acessar o Hub de Eventos. Para iniciar seu aplicativo lógico com um gatilho de hubs de eventos do Azure, você precisa de um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>Verifique as permissões e obtenha a cadeia de conexão

Para garantir que seu aplicativo lógico possa acessar seu hub de eventos, verifique suas permissões e obtenha a cadeia de conexão para seu namespace de hubs de eventos.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Acesse o *namespace* de seus Hubs de Eventos, não um Hub de Eventos específico. 

1. No menu namespace, em **configurações**, selecione **políticas de acesso compartilhado**. Em **declarações**, verifique se você tem permissões de **Gerenciamento** para esse namespace.

   ![Gerenciar permissões para seu namespace do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

1. Se quiser manualmente inserir suas informações de conexão mais tarde, obtenha a cadeia de caracteres de conexão para o namespace dos Hubs de Eventos.

   1. Em **Política**, escolha **RootManageSharedAccessKey**.

   1. Localize a cadeia de conexão da chave primária. Escolha o botão de cópia e salve a cadeia de conexão para uso posterior.

      ![Copie a cadeia de conexão do namespace dos Hubs de Eventos](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > Para confirmar se a cadeia de conexão está associada ao namespace de Hubs de Eventos ou a um hub de eventos específico, certifique-se de que a cadeia de conexão não tenha o parâmetro `EntityPath` . Se você encontrar esse parâmetro, a cadeia de conexão servirá para uma "entidade" de Hub de Eventos específica e não será a cadeia de caracteres correta a ser usada com seu aplicativo lógico.

1. Agora continue com o [Adicionar um gatilho de Hubs de Eventos](#add-trigger) ou [Adicionar uma ação de Hubs de Eventos](#add-action).

<a name="add-trigger"></a>

## <a name="add-event-hubs-trigger"></a>Adicionar gatilho dos Hubs de Eventos

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico e inicia a execução do fluxo de trabalho do aplicativo.

Este exemplo mostra como é possível iniciar um fluxo de trabalho de aplicativo lógico quando novos eventos são enviados para o Hub de Eventos. 

> [!NOTE]
> Todos os gatilhos de Hub de eventos são gatilhos *de sondagem longa* , o que significa que o gatilho processa todos os eventos e, em seguida, aguarda 30 segundos por partição para que mais eventos apareçam em seu hub de eventos. Portanto, se o gatilho estiver configurado com quatro partições, esse atraso poderá levar até dois minutos antes que o gatilho termine de sondar todas as partições. Se nenhum evento for recebido dentro desse atraso, a execução do gatilho será ignorada. Caso contrário, o gatilho continuará lendo os eventos até que o Hub de Eventos esteja vazio. A próxima pesquisa de gatilhos acontecerá com base no intervalo de recorrência que você especificar nas propriedades do gatilho.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. Na caixa de pesquisa, insira "hubs de eventos" como filtro. Na lista de gatilhos, selecione este gatilho: **quando os eventos estão disponíveis no Hub de eventos-hubs de eventos**

   ![Selecionar gatilho](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

1. Se for solicitado-lhe a fornecer detalhes da conexão, [crie sua conexão de Hubs de Eventos agora](#create-connection). 

1. No gatilho, forneça informações sobre o Hub de eventos que você deseja monitorar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** . A seleção de um parâmetro adiciona essa propriedade ao cartão de gatilho.

   ![Propriedades do gatilho](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de Eventos** | Sim | O nome do hub de eventos que você deseja monitorar |
   | **Tipo de conteúdo** | Não | O tipo de conteúdo do evento. O padrão é `application/octet-stream`. |
   | **Nome do grupo de consumidor** | Não | O [nome do grupo de consumidores do hub de eventos](../event-hubs/event-hubs-features.md#consumer-groups) a ser usado para ler eventos. Se não especificado, será usado o grupo de consumidores padrão. |
   | **Contagem de eventos máxima** | Não | O número máximo de eventos. O gatilho retorna entre um e o número de eventos especificado por essa propriedade. |
   | **Intervalo** | Sim | Um inteiro positivo que descreve com que frequência o fluxo de trabalho é executado com base na frequência |
   | **Frequência** | Sim | A unidade de tempo para a recorrência |
   ||||

   **Propriedades adicionais**

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Esquema de conteúdo** | Não | O esquema de conteúdo JSON para os eventos a serem lidos do hub de eventos. Por exemplo, se você especificar o esquema de conteúdo, poderá disparar o aplicativo lógico somente para os eventos que correspondam ao esquema. |
   | **Chave de participação mínima** | Não | Insira a ID de [partição](../event-hubs/event-hubs-features.md#partitions) mínima para leitura. Por padrão, todas as partições são lidas. |
   | **Chave de participação máxima** | Não | Insira a ID de [participação](../event-hubs/event-hubs-features.md#partitions) para leitura. Por padrão, todas as partições são lidas. |
   | **Fuso horário** | Não | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita diferença UTC. Selecione o fuso horário que você deseja aplicar. <p>Para obter mais informações, consulte [criar e executar tarefas e fluxos de trabalho recorrentes com o aplicativo lógico do Azure](../connectors/connectors-native-recurrence.md). |
   | **Hora de início** | Não | Forneça uma hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você selecionar um fuso horário<p>- ou -<p>AAAA-MM-DDThh:mm:ssZ se você não selecionar um fuso horário<p>Para obter mais informações, consulte [criar e executar tarefas e fluxos de trabalho recorrentes com o aplicativo lógico do Azure](../connectors/connectors-native-recurrence.md). |
   ||||

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

1. Agora, continue a adicionar uma ou mais ações ao aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho. 

   Por exemplo, para filtrar eventos com base em um valor específico, como uma categoria, você pode adicionar uma condição para que a ação **enviar evento** envie somente os eventos que atendem à sua condição. 

<a name="add-action"></a>

## <a name="add-event-hubs-action"></a>Adicionar ação dos Hubs de Eventos

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Para este exemplo, o aplicativo lógico começa com um gatilho de Hubs de Eventos que verifica novos eventos no Hub de Eventos.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou ação, escolha **nova etapa**.

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "hubs de eventos" como filtro.
Na lista ações, selecione esta ação: **enviar evento-hubs de eventos**

   ![Selecione a ação "enviar evento"](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

1. Se for solicitado-lhe a fornecer detalhes da conexão, [crie sua conexão de Hubs de Eventos agora](#create-connection). 

1. Na ação, forneça informações sobre os eventos que você deseja enviar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** . A seleção de um parâmetro adiciona essa propriedade ao cartão de ação.

   ![Selecione o nome do Hub de Eventos e forneça o conteúdo do evento](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

   | Propriedade | Obrigatório | Descrição |
   |----------|----------|-------------|
   | **Nome do Hub de Eventos** | Sim | O Hub de eventos para o qual você deseja enviar o evento |
   | **Conteúdo** | Não | O conteúdo do evento que você quer enviar |
   | **Propriedades** | Não | As propriedades e os valores do aplicativo a serem enviados |
   | **Chave de partição** | Não | A ID da [partição](../event-hubs/event-hubs-features.md#partitions) para onde enviar o evento |
   ||||

   Por exemplo, você pode enviar a saída de seu gatilho de hubs de eventos para outro hub de eventos:

   ![Exemplo de evento Send](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action-example.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Conectar ao Hub de Eventos

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. Quando for solicitado a fornecer informações de conexão, forneça estes detalhes:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | <*nome da conexão*> | O nome a criar para a conexão |
   | **Namespace de hubs de eventos** | Sim | <*hubs de eventos-namespace*> | Selecione o namespace do Hubs de Eventos que você quer usar. |
   |||||  

   Por exemplo:

   ![Criar conexão do Hub de Eventos](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   Para inserir manualmente a cadeia de conexão, selecione **inserir manualmente as informações de conexão**. 
   Saiba [como localizar a cadeia de conexão](#permissions-connection-string).

2. Selecione a política de Hubs de Eventos a ser usada, se ainda não estiver selecionada. Escolha **Criar**.

   ![Criar conexão do Hub de Eventos, parte 2](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. Depois de criar a conexão, continue com o [gatilho Adicionar Hubs de Eventos](#add-trigger) ou [ação Adicionar Hubs de Eventos](#add-action).

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/eventhubs/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
